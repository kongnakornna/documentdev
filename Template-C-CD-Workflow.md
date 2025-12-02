
## การออกแบบ CI/CD Workflow สอดคล้องกับ Git Flow
- ระบบ CI/CD ที่ออกแบบนี้จะรองรับ Git Flow โดยมี 3 environments หลัก (Development, Staging, Production) และใช้ GitHub Actions เป็นตัวอย่าง[^1][^2]

## สถาปัตยกรรม CI/CD Overview

```
┌─────────────────────────────────────────────────────────────┐
│                     GIT FLOW BRANCHES                        │
├──────────────┬──────────────┬──────────────┬────────────────┤
│   Feature    │   Develop    │   Release    │     Main       │
│   Branches   │              │   Branches   │  (Production)  │
└──────┬───────┴──────┬───────┴──────┬───────┴────────┬───────┘
       │              │              │                │
       ▼              ▼              ▼                ▼
┌──────────────┬──────────────┬──────────────┬────────────────┐
│   CI: Test   │  CI + Deploy │  CI + Deploy │  CI + Deploy   │
│   Only       │  to Dev      │  to Staging  │  to Production │
└──────────────┴──────────────┴──────────────┴────────────────┘
       │              │              │                │
       ▼              ▼              ▼                ▼
┌──────────────┬──────────────┬──────────────┬────────────────┐
│  No Deploy   │ Development  │   Staging    │   Production   │
│              │ Environment  │ Environment  │  Environment   │
└──────────────┴──────────────┴──────────────┴────────────────┘
```


## Environment Configuration

### Environment Matrix

| Environment | Branch | Auto Deploy | Approval Required | Database | URL |
| :-- | :-- | :-- | :-- | :-- | :-- |
| **Development** | `develop` | ✅ Yes | ❌ No | Dev DB | `dev.car-service.com` |
| **Staging** | `release/*` | ✅ Yes | ❌ No | Staging DB | `staging.car-service.com` |
| **Production** | `main` | ⚠️ Manual | ✅ Yes (2 approvers) | Prod DB | `car-service.com` |

[^6][^3]

## Complete GitHub Actions Workflow

### Main CI/CD Pipeline

**.github/workflows/ci-cd.yml**

```yaml
name: Car Service CI/CD Pipeline

on:
  push:
    branches:
      - main
      - develop
      - 'release/**'
  pull_request:
    branches:
      - main
      - develop

env:
  REGISTRY: docker.io
  NESTJS_IMAGE: your-org/car-service-nestjs
  SPRING_IMAGE: your-org/car-service-spring
  FRONTEND_IMAGE: your-org/car-service-frontend
  NODE_VERSION: '18'
  JAVA_VERSION: '17'

jobs:
  # ==================== DETECT CHANGES ====================
  detect-changes:
    runs-on: ubuntu-latest
    outputs:
      nestjs: ${{ steps.filter.outputs.nestjs }}
      spring: ${{ steps.filter.outputs.spring }}
      frontend: ${{ steps.filter.outputs.frontend }}
      infra: ${{ steps.filter.outputs.infra }}
    steps:
      - uses: actions/checkout@v4
      
      - uses: dorny/paths-filter@v2
        id: filter
        with:
          filters: |
            nestjs:
              - 'backend/nestjs-api/**'
            spring:
              - 'backend/spring-boot-api/**'
            frontend:
              - 'frontend/react-app/**'
            infra:
              - 'infrastructure/**'

  # ==================== NESTJS CI ====================
  nestjs-ci:
    needs: detect-changes
    if: needs.detect-changes.outputs.nestjs == 'true'
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
          cache-dependency-path: backend/nestjs-api/package-lock.json

      - name: Install dependencies
        working-directory: backend/nestjs-api
        run: npm ci

      - name: Lint code
        working-directory: backend/nestjs-api
        run: npm run lint

      - name: Run unit tests
        working-directory: backend/nestjs-api
        run: npm run test:cov

      - name: Run e2e tests
        working-directory: backend/nestjs-api
        run: npm run test:e2e

      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          files: ./backend/nestjs-api/coverage/coverage-final.json
          flags: nestjs
          name: nestjs-coverage

      - name: SonarCloud Scan
        uses: SonarSource/sonarcloud-github-action@master
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
        with:
          projectBaseDir: backend/nestjs-api

  # ==================== SPRING BOOT CI ====================
  spring-boot-ci:
    needs: detect-changes
    if: needs.detect-changes.outputs.spring == 'true'
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup Java
        uses: actions/setup-java@v4
        with:
          java-version: ${{ env.JAVA_VERSION }}
          distribution: 'temurin'
          cache: 'maven'

      - name: Build with Maven
        working-directory: backend/spring-boot-api
        run: mvn clean install -DskipTests

      - name: Run unit tests
        working-directory: backend/spring-boot-api
        run: mvn test

      - name: Run integration tests
        working-directory: backend/spring-boot-api
        run: mvn verify

      - name: Generate test report
        if: always()
        uses: dorny/test-reporter@v1
        with:
          name: Spring Boot Tests
          path: backend/spring-boot-api/target/surefire-reports/*.xml
          reporter: java-junit

      - name: Code coverage
        working-directory: backend/spring-boot-api
        run: mvn jacoco:report

  # ==================== FRONTEND CI ====================
  frontend-ci:
    needs: detect-changes
    if: needs.detect-changes.outputs.frontend == 'true'
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
          cache-dependency-path: frontend/react-app/package-lock.json

      - name: Install dependencies
        working-directory: frontend/react-app
        run: npm ci

      - name: Lint code
        working-directory: frontend/react-app
        run: npm run lint

      - name: Run tests
        working-directory: frontend/react-app
        run: npm test -- --coverage --watchAll=false

      - name: Build production
        working-directory: frontend/react-app
        run: npm run build
        env:
          VITE_API_URL: ${{ secrets.API_URL }}

      - name: Upload build artifacts
        uses: actions/upload-artifact@v3
        with:
          name: frontend-build
          path: frontend/react-app/dist
          retention-days: 7

  # ==================== SECURITY SCANNING ====================
  security-scan:
    needs: [detect-changes]
    if: |
      needs.detect-changes.outputs.nestjs == 'true' ||
      needs.detect-changes.outputs.spring == 'true' ||
      needs.detect-changes.outputs.frontend == 'true'
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Run Trivy vulnerability scanner
        uses: aquasecurity/trivy-action@master
        with:
          scan-type: 'fs'
          scan-ref: '.'
          format: 'sarif'
          output: 'trivy-results.sarif'

      - name: Upload Trivy results to GitHub Security
        uses: github/codeql-action/upload-sarif@v2
        with:
          sarif_file: 'trivy-results.sarif'

  # ==================== BUILD & PUSH DOCKER IMAGES ====================
  build-images:
    needs: [nestjs-ci, spring-boot-ci, frontend-ci, detect-changes]
    if: |
      always() &&
      (github.ref == 'refs/heads/develop' || 
       github.ref == 'refs/heads/main' ||
       startsWith(github.ref, 'refs/heads/release/'))
    runs-on: ubuntu-latest

    strategy:
      matrix:
        service:
          - name: nestjs
            path: backend/nestjs-api
            image: ${{ env.NESTJS_IMAGE }}
            condition: needs.detect-changes.outputs.nestjs == 'true'
          - name: spring
            path: backend/spring-boot-api
            image: ${{ env.SPRING_IMAGE }}
            condition: needs.detect-changes.outputs.spring == 'true'
          - name: frontend
            path: frontend/react-app
            image: ${{ env.FRONTEND_IMAGE }}
            condition: needs.detect-changes.outputs.frontend == 'true'

    steps:
      - name: Checkout code
        if: ${{ matrix.service.condition }}
        uses: actions/checkout@v4

      - name: Set up Docker Buildx
        if: ${{ matrix.service.condition }}
        uses: docker/setup-buildx-action@v3

      - name: Login to Docker Hub
        if: ${{ matrix.service.condition }}
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: Extract metadata
        if: ${{ matrix.service.condition }}
        id: meta
        uses: docker/metadata-action@v5
        with:
          images: ${{ matrix.service.image }}
          tags: |
            type=ref,event=branch
            type=sha,prefix={{branch}}-
            type=semver,pattern={{version}}
            type=raw,value=latest,enable={{is_default_branch}}

      - name: Build and push
        if: ${{ matrix.service.condition }}
        uses: docker/build-push-action@v5
        with:
          context: ${{ matrix.service.path }}
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}
          cache-from: type=registry,ref=${{ matrix.service.image }}:buildcache
          cache-to: type=registry,ref=${{ matrix.service.image }}:buildcache,mode=max

  # ==================== DEPLOY TO DEVELOPMENT ====================
  deploy-dev:
    needs: [build-images]
    if: github.ref == 'refs/heads/develop'
    runs-on: ubuntu-latest
    environment:
      name: development
      url: https://dev.car-service.com

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup kubectl
        uses: azure/setup-kubectl@v3
        with:
          version: 'latest'

      - name: Configure kubectl
        run: |
          echo "${{ secrets.KUBE_CONFIG_DEV }}" | base64 -d > kubeconfig
          export KUBECONFIG=kubeconfig

      - name: Deploy to Kubernetes
        run: |
          kubectl set image deployment/nestjs-api \
            nestjs-api=${{ env.NESTJS_IMAGE }}:develop-${{ github.sha }} \
            -n development
          
          kubectl set image deployment/spring-api \
            spring-api=${{ env.SPRING_IMAGE }}:develop-${{ github.sha }} \
            -n development
          
          kubectl set image deployment/frontend \
            frontend=${{ env.FRONTEND_IMAGE }}:develop-${{ github.sha }} \
            -n development

      - name: Wait for rollout
        run: |
          kubectl rollout status deployment/nestjs-api -n development
          kubectl rollout status deployment/spring-api -n development
          kubectl rollout status deployment/frontend -n development

      - name: Run smoke tests
        run: |
          curl -f https://dev.car-service.com/health || exit 1

      - name: Notify Slack
        if: always()
        uses: 8398a7/action-slack@v3
        with:
          status: ${{ job.status }}
          text: 'Deployment to Development: ${{ job.status }}'
          webhook_url: ${{ secrets.SLACK_WEBHOOK }}

  # ==================== DEPLOY TO STAGING ====================
  deploy-staging:
    needs: [build-images]
    if: startsWith(github.ref, 'refs/heads/release/')
    runs-on: ubuntu-latest
    environment:
      name: staging
      url: https://staging.car-service.com

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Extract version
        id: version
        run: |
          VERSION=$(echo ${GITHUB_REF#refs/heads/release/})
          echo "version=$VERSION" >> $GITHUB_OUTPUT

      - name: Setup kubectl
        uses: azure/setup-kubectl@v3

      - name: Configure kubectl
        run: |
          echo "${{ secrets.KUBE_CONFIG_STAGING }}" | base64 -d > kubeconfig
          export KUBECONFIG=kubeconfig

      - name: Deploy to Kubernetes
        run: |
          # Update deployment with version tags
          kubectl set image deployment/nestjs-api \
            nestjs-api=${{ env.NESTJS_IMAGE }}:${{ steps.version.outputs.version }} \
            -n staging
          
          kubectl set image deployment/spring-api \
            spring-api=${{ env.SPRING_IMAGE }}:${{ steps.version.outputs.version }} \
            -n staging
          
          kubectl set image deployment/frontend \
            frontend=${{ env.FRONTEND_IMAGE }}:${{ steps.version.outputs.version }} \
            -n staging

      - name: Run database migrations
        run: |
          kubectl run migration-${{ github.sha }} \
            --image=${{ env.NESTJS_IMAGE }}:${{ steps.version.outputs.version }} \
            --restart=Never \
            --command -- npm run migration:run \
            -n staging

      - name: Wait for rollout
        run: |
          kubectl rollout status deployment/nestjs-api -n staging
          kubectl rollout status deployment/spring-api -n staging
          kubectl rollout status deployment/frontend -n staging

      - name: Run integration tests
        run: |
          npm run test:integration:staging

      - name: Performance test
        run: |
          docker run --rm -i grafana/k6 run - < ./tests/load-test.js

  # ==================== DEPLOY TO PRODUCTION ====================
  deploy-production:
    needs: [build-images]
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    environment:
      name: production
      url: https://car-service.com

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup kubectl
        uses: azure/setup-kubectl@v3

      - name: Configure kubectl
        run: |
          echo "${{ secrets.KUBE_CONFIG_PROD }}" | base64 -d > kubeconfig
          export KUBECONFIG=kubeconfig

      - name: Create backup
        run: |
          # Backup current deployment
          kubectl get deployment -n production -o yaml > backup-deployment.yaml
          
          # Backup database
          ./scripts/backup-database.sh

      - name: Deploy with blue-green strategy
        run: |
          # Deploy to green environment
          kubectl apply -f infrastructure/kubernetes/green/ -n production
          
          # Wait for green to be healthy
          kubectl wait --for=condition=available --timeout=300s \
            deployment/nestjs-api-green -n production

      - name: Run smoke tests on green
        run: |
          curl -f https://green.car-service.com/health || exit 1

      - name: Switch traffic to green
        run: |
          kubectl patch service nestjs-api \
            -p '{"spec":{"selector":{"version":"green"}}}' \
            -n production

      - name: Monitor for 5 minutes
        run: |
          sleep 300
          # Check error rates, response times
          ./scripts/check-metrics.sh

      - name: Cleanup old blue deployment
        run: |
          kubectl delete deployment -l version=blue -n production

      - name: Create Git tag
        run: |
          git tag -a v${{ github.run_number }} -m "Production release ${{ github.run_number }}"
          git push origin v${{ github.run_number }}

      - name: Notify team
        uses: 8398a7/action-slack@v3
        with:
          status: ${{ job.status }}
          text: |
            🚀 Production Deployment Successful
            Version: v${{ github.run_number }}
            Commit: ${{ github.sha }}
          webhook_url: ${{ secrets.SLACK_WEBHOOK }}

  # ==================== ROLLBACK (Manual Trigger) ====================
  rollback:
    if: github.event_name == 'workflow_dispatch'
    runs-on: ubuntu-latest
    environment:
      name: production

    steps:
      - name: Rollback to previous version
        run: |
          kubectl rollout undo deployment/nestjs-api -n production
          kubectl rollout undo deployment/spring-api -n production
          kubectl rollout undo deployment/frontend -n production

      - name: Restore database
        run: |
          ./scripts/restore-database.sh ${{ github.event.inputs.backup_id }}
```


## Environment-Specific Configurations

### Development Environment

**infrastructure/kubernetes/dev/deployment.yaml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nestjs-api
  namespace: development
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nestjs-api
      env: dev
  template:
    metadata:
      labels:
        app: nestjs-api
        env: dev
    spec:
      containers:
      - name: nestjs-api
        image: your-org/car-service-nestjs:develop-latest
        env:
        - name: NODE_ENV
          value: "development"
        - name: DB_HOST
          value: "postgres-dev.default.svc.cluster.local"
        - name: REDIS_HOST
          value: "redis-dev.default.svc.cluster.local"
        - name: LOG_LEVEL
          value: "debug"
        resources:
          requests:
            memory: "256Mi"
            cpu: "100m"
          limits:
            memory: "512Mi"
            cpu: "200m"
        livenessProbe:
          httpGet:
            path: /health
            port: 3000
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /health
            port: 3000
          initialDelaySeconds: 10
          periodSeconds: 5
```


### Production Environment (Blue-Green)

**infrastructure/kubernetes/production/deployment-green.yaml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nestjs-api-green
  namespace: production
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
  selector:
    matchLabels:
      app: nestjs-api
      version: green
  template:
    metadata:
      labels:
        app: nestjs-api
        version: green
    spec:
      containers:
      - name: nestjs-api
        image: your-org/car-service-nestjs:latest
        env:
        - name: NODE_ENV
          value: "production"
        - name: DB_HOST
          valueFrom:
            secretKeyRef:
              name: db-credentials
              key: host
        resources:
          requests:
            memory: "1Gi"
            cpu: "500m"
          limits:
            memory: "2Gi"
            cpu: "1000m"
        livenessProbe:
          httpGet:
            path: /health
            port: 3000
          initialDelaySeconds: 60
          periodSeconds: 30
          failureThreshold: 3
        readinessProbe:
          httpGet:
            path: /ready
            port: 3000
          initialDelaySeconds: 30
          periodSeconds: 10
          failureThreshold: 3
```


## Database Migration Pipeline

**.github/workflows/database-migration.yml**

```yaml
name: Database Migration

on:
  push:
    branches:
      - develop
      - main
    paths:
      - 'database/migrations/**'

jobs:
  migrate-dev:
    if: github.ref == 'refs/heads/develop'
    runs-on: ubuntu-latest
    environment: development

    steps:
      - uses: actions/checkout@v4

      - name: Run migrations
        run: |
          docker run --rm \
            -e DATABASE_URL=${{ secrets.DEV_DATABASE_URL }} \
            -v $(pwd)/database/migrations:/migrations \
            migrate/migrate \
            -path=/migrations \
            -database ${{ secrets.DEV_DATABASE_URL }} \
            up

  migrate-staging:
    if: startsWith(github.ref, 'refs/heads/release/')
    runs-on: ubuntu-latest
    environment: staging

    steps:
      - uses: actions/checkout@v4

      - name: Backup database
        run: |
          ./scripts/backup-database.sh staging

      - name: Run migrations
        run: |
          docker run --rm \
            -e DATABASE_URL=${{ secrets.STAGING_DATABASE_URL }} \
            -v $(pwd)/database/migrations:/migrations \
            migrate/migrate \
            -path=/migrations \
            -database ${{ secrets.STAGING_DATABASE_URL }} \
            up

      - name: Verify migration
        run: |
          ./scripts/verify-migration.sh staging

  migrate-production:
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    environment: production

    steps:
      - uses: actions/checkout@v4

      - name: Create backup
        run: |
          ./scripts/backup-database.sh production

      - name: Dry-run migration
        run: |
          ./scripts/dry-run-migration.sh production

      - name: Wait for approval
        uses: trstringer/manual-approval@v1
        with:
          approvers: senior-dev-team
          minimum-approvals: 2

      - name: Run migrations
        run: |
          docker run --rm \
            -e DATABASE_URL=${{ secrets.PROD_DATABASE_URL }} \
            -v $(pwd)/database/migrations:/migrations \
            migrate/migrate \
            -path=/migrations \
            -database ${{ secrets.PROD_DATABASE_URL }} \
            up

      - name: Verify migration
        run: |
          ./scripts/verify-migration.sh production
```


## Monitoring and Alerts

**.github/workflows/monitoring.yml**

```yaml
name: Post-Deployment Monitoring

on:
  workflow_run:
    workflows: ["Car Service CI/CD Pipeline"]
    types:
      - completed

jobs:
  monitor:
    runs-on: ubuntu-latest
    if: ${{ github.event.workflow_run.conclusion == 'success' }}

    steps:
      - name: Check application health
        run: |
          for i in {1..10}; do
            if curl -f https://car-service.com/health; then
              echo "Health check passed"
              break
            fi
            echo "Retry $i/10"
            sleep 30
          done

      - name: Check error rates
        run: |
          # Query Prometheus/Grafana for error rates
          ERROR_RATE=$(curl -s "http://prometheus:9090/api/v1/query?query=rate(http_requests_total{status=~'5..'}[5m])")
          
          if [ "$ERROR_RATE" -gt "0.01" ]; then
            echo "High error rate detected: $ERROR_RATE"
            exit 1
          fi

      - name: Check response times
        run: |
          # Check average response time
          AVG_RESPONSE=$(curl -s "http://prometheus:9090/api/v1/query?query=http_request_duration_seconds")
          
          if [ "$AVG_RESPONSE" -gt "1.0" ]; then
            echo "High response time: $AVG_RESPONSE seconds"
            # Send alert but don't fail
          fi

      - name: Alert on failure
        if: failure()
        uses: 8398a7/action-slack@v3
        with:
          status: failure
          text: '🚨 Post-deployment monitoring failed!'
          webhook_url: ${{ secrets.SLACK_WEBHOOK }}
```


## CI/CD Workflow Summary

```
┌─────────────────────────────────────────────────────────────┐
│                    GIT PUSH EVENT                            │
└─────────────────┬───────────────────────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────────────────────┐
│  PHASE 1: CONTINUOUS INTEGRATION (CI)                        │
├─────────────────────────────────────────────────────────────┤
│  1. Detect Changes (paths-filter)                           │
│  2. Lint Code (ESLint, Prettier, Checkstyle)               │
│  3. Unit Tests (Jest, JUnit)                                │
│  4. Integration Tests (E2E, API Tests)                      │
│  5. Code Coverage (>80%)                                     │
│  6. Security Scan (Trivy, SonarQube)                        │
│  7. Build Validation                                         │
└─────────────────┬───────────────────────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────────────────────┐
│  PHASE 2: BUILD & PACKAGE                                    │
├─────────────────────────────────────────────────────────────┤
│  1. Build Docker Images                                      │
│  2. Tag with version (branch-sha, semver)                   │
│  3. Push to Docker Registry                                  │
│  4. Generate SBOM (Software Bill of Materials)              │
└─────────────────┬───────────────────────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────────────────────┐
│  PHASE 3: CONTINUOUS DEPLOYMENT (CD)                         │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌──────────┐  ┌──────────┐  ┌──────────────┐              │
│  │ Develop  │  │ Release  │  │    Main      │              │
│  │ Branch   │  │ Branch   │  │   Branch     │              │
│  └────┬─────┘  └────┬─────┘  └──────┬───────┘              │
│       │             │               │                       │
│       ▼             ▼               ▼                       │
│  ┌────────┐   ┌─────────┐   ┌──────────────┐              │
│  │  DEV   │   │ STAGING │   │ PRODUCTION   │              │
│  │  Auto  │   │  Auto   │   │ + Approval   │              │
│  └────────┘   └─────────┘   └──────────────┘              │
└─────────────────────────────────────────────────────────────┘
```


## Best Practices Implementation

### 1. Fast Feedback Loop

- Unit tests run first (fastest)
- Integration tests run after
- Build only if tests pass
- Parallel execution where possible
[^4]


### 2. Security First

- Scan dependencies for vulnerabilities
- Container image scanning
- Secret management with GitHub Secrets
- SBOM generation
[^4]


### 3. Progressive Deployment

- Development: Automatic
- Staging: Automatic with tests
- Production: Manual approval + Blue-Green
[^3][^2]


### 4. Rollback Strategy

- Keep previous version running (Blue-Green)
- Database backups before migrations
- One-click rollback capability
- Automated health checks
[^1]


### 5. Observability

- Health check endpoints
- Performance monitoring
- Error rate tracking
- Slack/Email notifications
[^4]

ระบบ CI/CD นี้รองรับ Git Flow อย่างสมบูรณ์ โดยแต่ละ branch มี pipeline ที่เหมาะสม มีการตรวจสอบคุณภาพ ความปลอดภัย และการ deploy แบบอัตโนมัติที่ปลอดภัย[^5][^1]
<span style="display:none">[^10][^7][^8][^9]</span>

<div align="center">⁂</div>

[^1]: https://hackernoon.com/from-git-flow-to-cicd-a-practical-guide-to-implement-git-workflow

[^2]: https://codefresh.io/learn/github-actions/deployment-with-github-actions-quick-tutorial-and-5-best-practices/

[^3]: https://docs.github.com/en/actions/concepts/workflows-and-actions/deployment-environments

[^4]: https://gitprotect.io/blog/exploring-best-practices-and-modern-trends-in-ci-cd/

[^5]: https://docs.bytebase.com/gitops/best-practices/git-and-cicd

[^6]: https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow

[^7]: https://about.gitlab.com/topics/version-control/what-are-gitlab-flow-best-practices/

[^8]: https://semaphore.io/blog/cicd-microservices-digitalocean-kubernetes

[^9]: https://www.scalefree.com/consulting/devops-solutions/behind-the-branches-navigating-git-workflows-in-modern-devops/

[^10]: https://dev.to/prodevopsguytech/devops-project-cicd-pipeline-for-a-microservices-based-application-on-kubernetes-1ba8


# DevOps Workflow และ Template สำหรับ Git Flow

## 1. การออกแบบ CI/CD Workflow สอดคล้องกับ Git Flow

### Git Flow Branch Structure
```
main (production)
├── release/*
│   ├── develop
│   │   ├── feature/*
│   │   └── hotfix/*
│   └── staging
```

### CI/CD Pipeline Design

**ขั้นตอนหลัก:**
1. **Feature Branch Workflow**
   - Trigger: เมื่อมี commit/push ไปที่ feature/*
   - Actions:
     - Code linting และ static analysis
     - Unit tests
     - Build package (Docker image, binary, etc.)
     - Deploy ไปยัง development environment

2. **Develop Branch Workflow**
   - Trigger: เมื่อมี merge ไปที่ develop
   - Actions:
     - Integration tests
     - Security scanning
     - Build และ deploy ไปยัง integration environment
     - Automated API testing

3. **Release Branch Workflow**
   - Trigger: เมื่อสร้าง release/* branch
   - Actions:
     - Performance testing
     - User acceptance testing (UAT)
     - Deploy ไปยัง staging environment
     - Manual approval สำหรับ production

4. **Hotfix Branch Workflow**
   - Trigger: เมื่อสร้าง hotfix/* branch จาก main
   - Actions:
     - Emergency security scans
     - Critical tests only
     - Fast-track deployment ไปยัง production

5. **Main Branch Workflow**
   - Trigger: เมื่อ merge ไปที่ main (จาก release หรือ hotfix)
   - Actions:
     - Tag version (semantic versioning)
     - Generate release notes
     - Deploy ไปยัง production
     - Backup และ monitoring verification

## 2. การออกแบบ Flow

### Development Flow Diagram
```
[Feature Request] → [Create Feature Branch] → [Development]
       ↓
[Code Review] → [Merge to Develop] → [Integration Testing]
       ↓
[Create Release Branch] → [Staging/UAT] → [Approval]
       ↓
[Merge to Main] → [Production Deployment] → [Monitoring]
```

### Deployment Strategy
1. **Blue-Green Deployment** สำหรับ production
2. **Canary Release** สำหรับ feature testing
3. **Rollback Mechanism** แบบอัตโนมัติ

### Environment Configuration
- **Development**: สำหรับ developer testing
- **Integration**: สำหรับ integration testing
- **Staging**: สำเนาของ production สำหรับ UAT
- **Production**: Live environment

## 3. TASK LIST Template

### Feature Development Template
```
## TASK: [Feature Name]
**ID:** PROJ-XXX
**Priority:** High/Medium/Low
**Estimated Time:** X days
```
### Description
[คำอธิบายโดยย่อของ feature]

### Acceptance Criteria
- [ ] Criteria 1
- [ ] Criteria 2
- [ ] Criteria 3

### Technical Requirements
- [ ] Database changes (ถ้ามี)
- [ ] API changes
- [ ] UI/UX requirements
- [ ] Performance requirements

### Development Tasks
- [ ] Create feature branch: `feature/PROJ-XXX-short-description`
- [ ] Implement core functionality
- [ ] Write unit tests (coverage > 80%)
- [ ] Update documentation
- [ ] Code review preparation

### Testing Tasks
- [ ] Unit tests passing
- [ ] Integration tests
- [ ] Manual testing checklist

### Deployment Tasks
- [ ] Merge to develop
- [ ] Deploy to integration environment
- [ ] Verify deployment

### Dependencies
- [Dependency 1]
- [Dependency 2]

### Notes
[Additional notes or comments]


### Release Preparation Template

## RELEASE: vX.Y.Z
**Release Date:** YYYY-MM-DD
**Release Manager:** [Name]

### Features Included
- [Feature 1] - PROJ-XXX
- [Feature 2] - PROJ-YYY
- [Bug Fixes] - PROJ-ZZZ

### Pre-release Checklist
- [ ] All features merged to develop
- [ ] Integration tests passing
- [ ] Performance testing completed
- [ ] Security scanning passed
- [ ] Documentation updated

### Release Process
- [ ] Create release branch: `release/vX.Y.Z`
- [ ] Update version numbers
- [ ] Deploy to staging
- [ ] UAT sign-off received
- [ ] Merge to main
- [ ] Create git tag

### Rollback Plan
[Step-by-step rollback procedure]


## 4. CHECKLIST Template

### Code Review Checklist

## CODE REVIEW CHECKLIST
**Reviewer:** [Name]
**PR Link:** [GitHub/GitLab Link]

### Code Quality
- [ ] Code follows style guide
- [ ] No dead code/commented code
- [ ] Error handling implemented
- [ ] Logging appropriate

### Testing
- [ ] Unit tests added/updated
- [ ] Test coverage adequate
- [ ] Edge cases considered

### Security
- [ ] No hard-coded secrets
- [ ] Input validation implemented
- [ ] SQL injection prevention
- [ ] XSS prevention

### Performance
- [ ] No memory leaks
- [ ] Database queries optimized
- [ ] API response time acceptable

### Documentation
- [ ] Code comments clear
- [ ] README updated (if needed)
- [ ] API documentation updated

### Approval
- [ ] Approve
- [ ] Request changes
- [ ] Comments: [Add comments here]


### Deployment Checklist

## DEPLOYMENT CHECKLIST
**Environment:** [Production/Staging]
**Version:** vX.Y.Z
**Deployment Time:** YYYY-MM-DD HH:MM

### Pre-deployment
- [ ] Backup completed
- [ ] Database migrations ready (if any)
- [ ] Configuration files updated
- [ ] Load balancer configured
- [ ] Monitoring alerts enabled

### During Deployment
- [ ] Deploy to 10% of instances (Canary)
- [ ] Health checks passing
- [ ] Monitor error rates
- [ ] Deploy to remaining instances
- [ ] Verify all services running

### Post-deployment
- [ ] Smoke tests passed
- [ ] API endpoints verified
- [ ] Database connections verified
- [ ] Performance monitoring stable
- [ ] Notify stakeholders

### Rollback Preparedness
- [ ] Rollback script ready
- [ ] Previous version artifacts available
- [ ] Team on standby for 1 hour post-deployment


### Production Release Checklist

## PRODUCTION RELEASE CHECKLIST
**Release:** vX.Y.Z
**Scheduled:** YYYY-MM-DD HH:MM
**Change Window:** [Time Window]

### Approval Required
- [ ] Product Owner approval
- [ ] Tech Lead approval
- [ ] DevOps approval
- [ ] Business approval (if major release)

### Communication
- [ ] Stakeholders notified
- [ ] Support team briefed
- [ ] Maintenance page ready (if needed)
- [ ] Rollback communication plan ready

### Verification
- [ ] All automated tests passing
- [ ] Performance tests passed
- [ ] Security scan clean
- [ ] Compliance requirements met

### Sign-off
**Release Manager:** ___________________
**Date/Time:** _________________________
**Status:** ☐ Success  ☐ Rolled Back
**Notes:** ____________________________


### Hotfix Checklist

## HOTFIX EMERGENCY CHECKLIST
**Issue:** [Brief description]
**Severity:** Critical/High
**Hotfix Branch:** hotfix/issue-description

### Assessment
- [ ] Business impact assessed
- [ ] Root cause identified
- [ ] Fix verified in dev environment

### Implementation
- [ ] Minimal changes only
- [ ] Tests updated
- [ ] Documentation updated

### Deployment
- [ ] Emergency approval received
- [ ] Deploy to production
- [ ] Immediate verification
- [ ] Monitor closely for 24 hours

### Post-mortem Required
- [ ] Schedule post-mortem meeting
- [ ] Document lessons learned
- [ ] Update runbooks


การใช้งาน template เหล่านี้จะช่วยให้ทีมพัฒนามีกระบวนการที่เป็นมาตรฐาน สม่ำเสมอ และสามารถติดตามได้อย่างมีประสิทธิภาพ



# DevOps Workflow และ Template สำหรับ Git Flow (ภาษาไทย)

## 1. การออกแบบ CI/CD Workflow สอดคล้องกับ Git Flow

### โครงสร้างสาขา Git Flow
```
main (production - สภาพแวดล้อมการผลิตจริง)
├── release/*
│   ├── develop (พัฒนารวม)
│   │   ├── feature/* (คุณสมบัติใหม่)
│   │   └── hotfix/* (แก้ไขด่วน)
│   └── staging (เตรียมขึ้นผลิต)
└── tags (เก็บเวอร์ชัน)
```

### การออกแบบ CI/CD Pipeline

**ขั้นตอนการทำงานหลัก:**

1. **Workflow สำหรับ Feature Branch**
   - ทริกเกอร์: เมื่อมีการ commit/push ไปที่ feature/*
   - การดำเนินการ:
     - ตรวจสอบรูปแบบโค้ด (linting) และวิเคราะห์โค้ด (static analysis)
     - ทดสอบหน่วย (unit tests)
     - สร้างแพ็คเกจ (Docker image, binary, ฯลฯ)
     - ดีพลอยไปยังสภาพแวดล้อม development

2. **Workflow สำหรับ Develop Branch**
   - ทริกเกอร์: เมื่อมีการ merge ไปที่ develop
   - การดำเนินการ:
     - ทดสอบการรวมระบบ (integration tests)
     - ตรวจสอบความปลอดภัย (security scanning)
     - สร้างและดีพลอยไปยังสภาพแวดล้อม integration
     - ทดสอบ API อัตโนมัติ

3. **Workflow สำหรับ Release Branch**
   - ทริกเกอร์: เมื่อสร้าง release/* branch
   - การดำเนินการ:
     - ทดสอบประสิทธิภาพ (performance testing)
     - ทดสอบยอมรับผู้ใช้ (UAT)
     - ดีพลอยไปยังสภาพแวดล้อม staging
     - รับอนุมัติด้วยมนุษย์ก่อนขึ้น production

4. **Workflow สำหรับ Hotfix Branch**
   - ทริกเกอร์: เมื่อสร้าง hotfix/* branch จาก main
   - การดำเนินการ:
     - ตรวจสอบความปลอดภัยเร่งด่วน
     - ทดสอบเฉพาะส่วนที่สำคัญเท่านั้น
     - ดีพลอยไปยัง production แบบเร่งด่วน

5. **Workflow สำหรับ Main Branch**
   - ทริกเกอร์: เมื่อ merge ไปที่ main (จาก release หรือ hotfix)
   - การดำเนินการ:
     - ติดแท็กเวอร์ชัน (semantic versioning)
     - สร้างบันทึกการปล่อยเวอร์ชัน (release notes)
     - ดีพลอยไปยัง production
     - ตรวจสอบการสำรองข้อมูลและการเฝ้าติดตาม

## 2. การออกแบบ Flow

### แผนภาพการพัฒนาซอฟต์แวร์
```
[คำขอคุณสมบัติใหม่] → [สร้างสาขา Feature] → [พัฒนา]
       ↓
[ตรวจสอบโค้ด] → [รวมกับ Develop] → [ทดสอบการรวมระบบ]
       ↓
[สร้างสาขา Release] → [Staging/UAT] → [อนุมัติ]
       ↓
[รวมกับ Main] → [ดีพลอย Production] → [เฝ้าติดตาม]
```

### กลยุทธ์การดีพลอย
1. **Blue-Green Deployment** สำหรับ production
2. **Canary Release** สำหรับการทดสอบคุณสมบัติ
3. **กลไกการย้อนกลับ** แบบอัตโนมัติ

### การกำหนดค่าสภาพแวดล้อม
- **Development**: สำหรับนักพัฒนาทดสอบ
- **Integration**: สำหรับทดสอบการรวมระบบ
- **Staging**: สำเนาของ production สำหรับ UAT
- **Production**: สภาพแวดล้อมจริง

## 3. TASK LIST Template (ภาษาไทย)

### Template สำหรับพัฒนาคุณสมบัติใหม่

## งาน: [ชื่อคุณสมบัติ]
**รหัส:** PROJ-XXX
**ลำดับความสำคัญ:** สูง/ปานกลาง/ต่ำ
**เวลาที่ประมาณการ:** X วัน

### คำอธิบาย
[คำอธิบายโดยย่อของคุณสมบัติ]

### เกณฑ์การยอมรับ
- [x] เกณฑ์ที่ 1
- [ ] เกณฑ์ที่ 2
- [x] เกณฑ์ที่ 3

### ข้อกำหนดทางเทคนิค
- [ ] การเปลี่ยนแปลงฐานข้อมูล (ถ้ามี)
- [ ] การเปลี่ยนแปลง API
- [ ] ข้อกำหนด UI/UX
- [ ] ข้อกำหนดประสิทธิภาพ

### งานการพัฒนา
- [ ] สร้างสาขา feature: `feature/PROJ-XXX-คำอธิบายสั้น`
- [ ] พัฒนาหน้าที่หลัก
- [ ] เขียนทดสอบหน่วย (ความครอบคลุม > 80%)
- [ ] อัปเดตเอกสาร
- [ ] เตรียมพร้อมสำหรับการตรวจสอบโค้ด

### งานการทดสอบ
- [ ] ทดสอบหน่วยผ่านทั้งหมด
- [ ] ทดสอบการรวมระบบ
- [ ] รายการตรวจสอบการทดสอบด้วยมนุษย์

### งานการดีพลอย
- [ ] รวมโค้ดกับ develop
- [ ] ดีพลอยไปยังสภาพแวดล้อม integration
- [ ] ยืนยันการดีพลอย

### การพึ่งพา
- [การพึ่งพาที่ 1]
- [การพึ่งพาที่ 2]

### หมายเหตุ
[หมายเหตุเพิ่มเติมหรือความคิดเห็น]


### Template สำหรับการเตรียมปล่อยเวอร์ชัน

## การปล่อยเวอร์ชัน: vX.Y.Z
**วันที่ปล่อย:** YYYY-MM-DD
**ผู้จัดการการปล่อย:** [ชื่อ]

### คุณสมบัติที่รวมอยู่
- [คุณสมบัติ 1] - PROJ-XXX
- [คุณสมบัติ 2] - PROJ-YYY
- [การแก้ไขข้อบกพร่อง] - PROJ-ZZZ

### รายการตรวจสอบก่อนปล่อย
- [ ] คุณสมบัติทั้งหมดรวมกับ develop แล้ว
- [ ] ทดสอบการรวมระบบผ่านแล้ว
- [ ] ทดสอบประสิทธิภาพเสร็จสิ้น
- [ ] ตรวจสอบความปลอดภัยผ่านแล้ว
- [ ] อัปเดตเอกสารแล้ว

### ขั้นตอนการปล่อย
- [ ] สร้างสาขา release: `release/vX.Y.Z`
- [ ] อัปเดตหมายเลขเวอร์ชัน
- [ ] ดีพลอยไปยัง staging
- [ ] ได้รับการลงนามจาก UAT
- [ ] รวมโค้ดกับ main
- [ ] สร้างแท็ก git

### แผนการย้อนกลับ
[ขั้นตอนการย้อนกลับทีละขั้น]


## 4. CHECKLIST Template (ภาษาไทย)

### รายการตรวจสอบการตรวจสอบโค้ด

## รายการตรวจสอบการตรวจสอบโค้ด
**ผู้ตรวจสอบ:** [ชื่อ]
**ลิงก์ PR:** [GitHub/GitLab Link]

### คุณภาพโค้ด
- [ ] โค้ดตามแนวทางสไตล์
- [ ] ไม่มีโค้ดที่ตาย/ถูกคอมเมนต์ออก
- [ ] มีการจัดการข้อผิดพลาด
- [ ] การบันทึกเหตุการณ์เหมาะสม

### การทดสอบ
- [ ] เพิ่ม/อัปเดตทดสอบหน่วย
- [ ] ความครอบคลุมการทดสอบเพียงพอ
- [ ] พิจารณากรณีขอบเขต

### ความปลอดภัย
- [ ] ไม่มีข้อมูลลับในโค้ด
- [ ] มีการตรวจสอบข้อมูลนำเข้า
- [ ] ป้องกัน SQL injection
- [ ] ป้องกัน XSS

### ประสิทธิภาพ
- [ ] ไม่มีหน่วยความจำรั่ว
- [ ] ประสิทธิภาพคำสั่งฐานข้อมูล
- [ ] เวลาตอบสนอง API ยอมรับได้

### เอกสาร
- [ ] คอมเมนต์ในโค้ดชัดเจน
- [ ] อัปเดต README (ถ้าจำเป็น)
- [ ] อัปเดตเอกสาร API

### การอนุมัติ
- [ ] อนุมัติ
- [ ] ขอให้เปลี่ยนแปลง
- [ ] ความคิดเห็น: [เพิ่มความคิดเห็นที่นี่]


### รายการตรวจสอบการดีพลอย

## รายการตรวจสอบการดีพลอย
**สภาพแวดล้อม:** [Production/Staging]
**เวอร์ชัน:** vX.Y.Z
**เวลาในการดีพลอย:** YYYY-MM-DD HH:MM

### ก่อนการดีพลอย
- [ ] สำรองข้อมูลเสร็จสิ้น
- [ ] การย้ายฐานข้อมูลพร้อม (ถ้ามี)
- [ ] อัปเดตไฟล์การกำหนดค่า
- [ ] กำหนดค่าตัวแบ่งเบาแรงงาน
- [ ] เปิดการแจ้งเตือนการเฝ้าติดตาม

### ระหว่างการดีพลอย
- [ ] ดีพลอยไปยัง 10% ของอินสแตนซ์ (Canary)
- [ ] ตรวจสอบสภาพการทำงานผ่าน
- [ ] เฝ้าติดตามอัตราข้อผิดพลาด
- [ ] ดีพลอยไปยังอินสแตนซ์ที่เหลือ
- [ ] ยืนยันการทำงานของบริการทั้งหมด

### หลังการดีพลอย
- [ ] ทดสอบพื้นฐานผ่าน
- [ ] ยืนยันจุดปลายทาง API
- [ ] ยืนยันการเชื่อมต่อฐานข้อมูล
- [ ] การเฝ้าติดตามประสิทธิภาพเสถียร
- [ ] แจ้งผู้มีส่วนได้ส่วนเสีย

### ความพร้อมสำหรับการย้อนกลับ
- [ ] สคริปต์ย้อนกลับพร้อม
- [ ] อาร์ติแฟกต์เวอร์ชันก่อนหน้าพร้อม
- [ ] ทีมพร้อม standby 1 ชั่วโมงหลังดีพลอย


### รายการตรวจสอบการปล่อยเวอร์ชันสู่ production

## รายการตรวจสอบการปล่อยเวอร์ชันสู่ Production
**การปล่อย:** vX.Y.Z
**กำหนดการ:** YYYY-MM-DD HH:MM
**ช่วงเวลาการเปลี่ยนแปลง:** [ช่วงเวลา]

### ต้องได้รับการอนุมัติ
- [ ] อนุมัติโดย Product Owner
- [ ] อนุมัติโดย Tech Lead
- [ ] อนุมัติโดย DevOps
- [ ] อนุมัติโดยธุรกิจ (ถ้าเป็นเวอร์ชันสำคัญ)

### การสื่อสาร
- [ ] แจ้งผู้มีส่วนได้ส่วนเสีย
- [ ] บรีฟทีมสนับสนุน
- [ ] หน้าแจ้งบำรุงรักษาพร้อม (ถ้าจำเป็น)
- [ ] แผนการสื่อสารการย้อนกลับพร้อม

### การยืนยัน
- [ ] ทดสอบอัตโนมัติทั้งหมดผ่าน
- [ ] ทดสอบประสิทธิภาพผ่าน
- [ ] ตรวจสอบความปลอดภัยผ่าน
- [ ] ตรงตามข้อกำหนดการปฏิบัติตาม

### การลงนาม
**ผู้จัดการการปล่อย:** ___________________
**วันที่/เวลา:** _________________________
**สถานะ:** ☐ สำเร็จ  ☐ ย้อนกลับแล้ว
**หมายเหตุ:** ____________________________


### รายการตรวจสอบการแก้ไขด่วน (Hotfix)

## รายการตรวจสอบการแก้ไขด่วนฉุกเฉิน
**ปัญหา:** [คำอธิบายสั้น]
**ความรุนแรง:** วิกฤต/สูง
**สาขา Hotfix:** hotfix/คำอธิบายปัญหา

### การประเมิน
- [ ] ประเมินผลกระทบทางธุรกิจ
- [ ] ระบุสาเหตุราก
- [ ] ยืนยันการแก้ไขในสภาพแวดล้อม dev

### การดำเนินการ
- [ ] เปลี่ยนแปลงเฉพาะส่วนที่จำเป็น
- [ ] อัปเดตการทดสอบ
- [ ] อัปเดตเอกสาร

### การดีพลอย
- [ ] ได้รับอนุมัติฉุกเฉิน
- [ ] ดีพลอยไปยัง production
- [ ] ยืนยันทันที
- [ ] เฝ้าติดตามอย่างใกล้ชิด 24 ชั่วโมง

### ต้องมีการทบทวนหลังเหตุการณ์
- [ ] จัดการประชุมทบทวนหลังเหตุการณ์
- [ ] บันทึกบทเรียนที่เรียนรู้
- [ ] อัปเดตเอกสารวิธีการทำงาน


**ประโยชน์ของการใช้ template เหล่านี้:**
1. **มาตรฐานการทำงาน** - ทีมพัฒนาทำงานในรูปแบบเดียวกัน
2. **ลดข้อผิดพลาด** - มีรายการตรวจสอบช่วยเตือน
3. **ติดตามได้** - รู้สถานะการทำงานทุกขั้นตอน
4. **สื่อสารชัดเจน** - ทุกคนเข้าใจกระบวนการเดียวกัน
5. **พัฒนาคุณภาพ** - มีการตรวจสอบหลายขั้นตอนก่อนปล่อย