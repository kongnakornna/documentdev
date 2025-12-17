#  Docker 
### **Docker คืออะไร?**
Docker คือ **Platform สำหรับพัฒนา, จัดส่ง, และรันแอปพลิเคชันด้วย Container**
## 📌 **ส่วนที่ 1: แนะนำ Docker - แนวคิดพื้นฐาน**
### **คำศัพท์สำคัญ**
```
1. Container      - แอปพลิเคชันที่รันแยกกัน (เหมือนห้องพักในอาคาร)
2. Image          - เทมเพลตสำหรับสร้าง Container (เหมือนพิมพ์เขียว)
3. Dockerfile     - สคริปต์สำหรับสร้าง Image
4. Docker Hub     - รีจิสทรีเก็บ Images (เหมือน GitHub สำหรับ Docker)
5. Docker Engine  - โปรแกรมหลักที่รัน Container
```

### **ประโยชน์ของ Docker**
✅ **ความสอดคล้อง** - รันเหมือนกันทุก environment  
✅ **การแยกส่วน** - แอปแต่ละตัวรันแยกกัน  
✅ **ประสิทธิภาพ** - ใช้ทรัพยากรน้อยกว่า VM  
✅ **รวดเร็ว** - เปิดปิด Container ในไม่กี่วินาที  

### **Container vs Virtual Machine**
```
Virtual Machine:  Host OS → Hypervisor → Guest OS → App
Container:        Host OS → Docker Engine → App
```

---

## 📌 **ส่วนที่ 2: การติดตั้ง**

### **Windows 10/11**
1. ดาวน์โหลด **Docker Desktop** จาก [docker.com](https://www.docker.com/products/docker-desktop)
2. ติดตั้งและรีสตาร์ทคอมพิวเตอร์
3. เปิด PowerShell/CMD ตรวจสอบ:
```powershell
docker --version
docker run hello-world
```

### **macOS**
```bash
# วิธีที่ 1: ใช้ Homebrew
brew install --cask docker

# วิธีที่ 2: ดาวน์โหลด .dmg
# จากเว็บไซต์ Docker

# เปิด Terminal ตรวจสอบ
docker --version
```

### **Linux (Ubuntu/Debian)**
```bash
# 1. อัพเดทแพ็กเกจ
sudo apt-get update

# 2. ติดตั้ง dependencies
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

# 3. เพิ่ม Docker GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# 4. เพิ่ม repository
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# 5. ติดตั้ง Docker Engine
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io

# 6. เพิ่ม user เข้า docker group (ไม่ต้องใช้ sudo)
sudo usermod -aG docker $USER

# 7. ล็อกอินใหม่ หรือรีสตาร์ท
```

### **ตรวจสอบการติดตั้ง**
```bash
# ทดสอบ
docker --version
docker-compose --version

# รัน Container ทดสอบ
docker run hello-world
```

---

## 📌 **ส่วนที่ 3: คำสั่งพื้นฐาน**

### **โครงสร้างคำสั่ง Docker**
```
docker [OPTIONS] COMMAND [ARG...]
```

### **1. docker run - รัน Container**
```bash
# รัน Container จาก Image
docker run nginx

# รันในโหมดเบื้องหลัง (-d = detach)
docker run -d nginx

# ตั้งชื่อ Container
docker run --name my-nginx nginx

# Map port (host:container)
docker run -p 8080:80 nginx

# รันแล้วลบเมื่อหยุด (--rm)
docker run --rm -it ubuntu bash

# ตัวอย่าง: รันและเข้า shell
docker run -it ubuntu /bin/bash
```

### **2. docker build - สร้าง Image**
```bash
# สร้าง Image จาก Dockerfile
docker build -t my-image:1.0 .

# สร้างจาก context อื่น
docker build -t my-app /path/to/Dockerfile

# ไม่ใช้ cache
docker build --no-cache -t my-image .
```

### **3. docker push/pull - ดาวน์โหลด/อัปโหลด Image**
```bash
# ดาวน์โหลด Image จาก Docker Hub
docker pull nginx:latest
docker pull ubuntu:20.04

# ดู Images ที่มี
docker images

# ติดแท็ก Image สำหรับ push
docker tag my-image:1.0 username/my-image:1.0

# อัปโหลดไป Docker Hub
docker push username/my-image:1.0

# ดาวน์โหลดจาก Private Registry
docker pull registry.example.com/my-image:1.0
```

### **4. คำสั่งจัดการ Container**
```bash
# ดู Container ที่กำลังรัน
docker ps

# ดูทั้งหมด (รวมที่หยุดแล้ว)
docker ps -a

# หยุด Container
docker stop container_name

# เริ่ม Container ที่หยุด
docker start container_name

# ลบ Container
docker rm container_name

# ลบทั้งหมดที่หยุดแล้ว
docker container prune

# ดู logs
docker logs container_name

# ติดตาม logs ใหม่ (-f = follow)
docker logs -f container_name
```

### **5. คำสั่งจัดการ Images**
```bash
# ลิสต์ Images
docker images
docker image ls

# ลบ Image
docker rmi image_name

# ลบ Images ที่ไม่ได้ใช้
docker image prune -a

# ดูประวัติการสร้าง Image
docker history image_name
```

### **เช็ตคำสั่งสำหรับมือใหม่**
```bash
# ชุดคำสั่งเริ่มต้น
docker --version                    # ตรวจสอบเวอร์ชัน
docker info                         # ข้อมูล Docker
docker run hello-world              # ทดสอบการทำงาน
docker run -d -p 80:80 nginx        # รัน Nginx
docker ps                           # ดู Container
docker images                       # ดู Images
docker stop [ชื่อ]                  # หยุด Container
docker rm [ชื่อ]                    # ลบ Container
```

---

## 📌 **ส่วนที่ 4: Docker Images - สร้างและจัดการ**

### **เข้าใจ Docker Image**
```
Image = ไฟล์ระบบ + แอปพลิเคชัน + dependencies
```

### **แหล่ง Images**
1. **Docker Hub** (hub.docker.com) - แหล่งหลัก
2. **Private Registry** - สำหรับองค์กร
3. **สร้างเอง** - จาก Dockerfile

### **จัดการ Images**
```bash
# ดึง Images ยอดนิยม
docker pull nginx:alpine
docker pull mysql:8.0
docker pull node:14
docker pull python:3.9-slim

# ตรวจสอบ Image
docker inspect nginx:alpine

# Save Image เป็นไฟล์ .tar
docker save -o nginx.tar nginx:alpine

# Load Image จากไฟล์
docker load -i nginx.tar

# Tag Image
docker tag nginx:alpine my-nginx:v1

# Export/Import Container
docker export container_id > container.tar
docker import container.tar my-image:new
```

### **Image Layers**
```
Image ประกอบด้วยหลาย Layer
Layer แต่ละอันคือการเปลี่ยนแปลงจาก Layer ก่อนหน้า
ทำให้ประหยัดพื้นที่และดาวน์โหลดเร็ว
```

---

## 🎯 **แบบฝึกหัดพื้นฐาน**

### **แบบฝึกหัด 1: รัน Container แรก**
```bash
# 1. ดึง Image
docker pull ubuntu:latest

# 2. รันและเข้าไปข้างใน
docker run -it ubuntu bash

# 3. ใน Container: สร้างไฟล์
touch /test.txt
echo "Hello Docker" > /test.txt
exit

# 4. ตรวจสอบ
docker ps -a
```

### **แบบฝึกหัด 2: Web Server**
```bash
# 1. รัน Nginx
docker run -d -p 8080:80 --name my-web nginx

# 2. เปิดเบราว์เซอร์ไปที่ http://localhost:8080

# 3. เข้าไปดู logs
docker logs my-web

# 4. เข้าไปใน Container
docker exec -it my-web bash

# 5. ดูไฟล์ config
cat /etc/nginx/nginx.conf
```

### **แบบฝึกหัด 3: สร้าง Image ง่ายๆ**
```bash
# 1. สร้างไฟล์ Dockerfile
echo "FROM alpine:latest
RUN apk add --no-cache curl
CMD [\"curl\", \"--version\"]" > Dockerfile

# 2. สร้าง Image
docker build -t my-curl .

# 3. รัน
docker run my-curl
```

---

## ❓ **ปัญหาที่พบบ่อยสำหรับมือใหม่**

### **1. "Cannot connect to the Docker daemon"**
```bash
# แก้ไข:
sudo systemctl start docker
sudo systemctl enable docker

# หรือสำหรับ Docker Desktop:
# เปิดโปรแกรม Docker Desktop ก่อน
```

### **2. Permission Denied**
```bash
# เพิ่ม user เข้า docker group
sudo usermod -aG docker $USER

# ล็อกอินใหม่ หรือใช้คำสั่ง:
newgrp docker
```

### **3. Port ถูกใช้งานแล้ว**
```bash
# ตรวจสอบ
sudo netstat -tulpn | grep :8080

# เปลี่ยน port
docker run -p 8081:80 nginx
```

### **4. Disk เต็ม**
```bash
# ล้างทรัพยากรที่ไม่ได้ใช้
docker system prune

# ล้างทั้งหมด (ระวัง!)
docker system prune -a
```

---

## 📚 **ขั้นตอนต่อไป**

### **สิ่งที่ควรเรียนต่อ**
1. **Dockerfile** - สร้าง Images เอง
2. **Docker Compose** - จัดการหลาย Container
3. **Docker Networking** - เชื่อมต่อ Container
4. **Docker Volumes** - จัดเก็บข้อมูลถาวร

### **คำแนะนำสำหรับฝึกฝน**
```bash
# ลองทำทุกวัน
1. docker pull [imageใหม่]
2. docker run [ด้วย options ต่างๆ]
3. docker exec [เข้าไปดูข้างใน]
4. docker build [สร้าง image เอง]
```

---

## 🆘 **ช่วยเหลือเพิ่มเติม**
```bash
# ค้นหาคำสั่ง
docker --help
docker run --help

# ดู documentation
docker [command] --help
```

ต้องการข้อมูลเพิ่มเติมเกี่ยวกับหัวข้อใดเป็นพิเศษ หรือพร้อมจะเรียน **Dockerfile** ต่อไหมครับ? 🚀

---------------
# 🚀 **คู่มือ Kubernetes Architecture - ภาษาไทย**

## 📖 **อ้างอิงจาก:** [Kubernetes Architecture Explained - DevOpsCube](https://devopscube.com/kubernetes-architecture-explained/)

---




## 🏗️ **ภาพรวมสถาปัตยกรรม Kubernetes**

### **Kubernetes คืออะไร?**
Kubernetes (K8s) คือ **ระบบจัดการ container orchestration** ที่ช่วยในการ deploy, scale และจัดการแอปพลิเคชันที่อยู่ใน container

### **สถาปัตยกรรมพื้นฐาน**
```
┌─────────────────────────────────────────────────────────────┐
│                     Kubernetes Cluster                       │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────────┐      ┌─────────────────┐             │
│  │   Control Plane │      │   Worker Nodes  │             │
│  │   (Master)      │      │   (Minions)     │             │
│  │                 │      │                 │             │
│  │  • API Server   │      │  • kubelet      │             │
│  │  • etcd         │      │  • kube-proxy   │             │
│  │  • Scheduler    │      │  • Container    │             │
│  │  • Controller   │      │    Runtime      │             │
│  │    Manager      │      │                 │             │
│  └─────────────────┘      └─────────────────┘             │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## 🎯 **ส่วนประกอบ Control Plane (Master)**

### **1. API Server (kube-apiserver)**
- **หน้าที่**: จุดติดต่อหลักสำหรับการสื่อสารทั้งหมด
- **คุณสมบัติ**:
  - ตรวจสอบและประมวลผลคำขอ REST
  - อัพเดทสถานะใน etcd
  - รับคำสั่งจาก kubectl, dashboard, และ clients อื่นๆ
- **Port**: 443 (HTTPS), 8080 (HTTP - สำหรับ dev)

### **2. etcd**
- **หน้าที่**: ฐานข้อมูล Key-Value แบบกระจาย
- **ข้อมูลที่เก็บ**:
  - สถานะของ cluster
  - Configurations
  - Metadata
  - Secrets
- **ลักษณะ**: Highly available, consistent, distributed

### **3. Scheduler (kube-scheduler)**
- **หน้าที่**: กำหนดว่า Pod ควรรันที่ Node ไหน
- **ขั้นตอนการทำงาน**:
  1. **Filtering**: กรอง Node ที่ไม่เหมาะ
  2. **Scoring**: ให้คะแนน Node ที่เหมาะสม
  3. **Binding**: ผูก Pod กับ Node ที่เลือก
- **ปัจจัยที่พิจารณา**:
  - Resource requirements
  - Affinity/Anti-affinity rules
  - Taints and tolerations

### **4. Controller Manager (kube-controller-manager)**
ประกอบด้วยหลาย Controller:
- **Node Controller**: ตรวจสอบสถานะ Node
- **Replication Controller**: รักษาจำนวน Pod ตาม ReplicaSet
- **Endpoints Controller**: จัดการ Endpoint objects
- **Service Account & Token Controllers**: จัดการ service accounts

### **5. Cloud Controller Manager (cloud-controller-manager)**
- **หน้าที่**: เชื่อมต่อกับ cloud provider
- **Controllers**:
  - Node Controller
  - Route Controller
  - Service Controller
  - Volume Controller

---

## ⚙️ **ส่วนประกอบ Worker Nodes**

### **1. kubelet**
- **หน้าที่**: Agent ที่รันบนแต่ละ Node
- **ความรับผิดชอบ**:
  - รับ PodSpecs จาก API Server
  - ตรวจสอบว่า containers ใน Pod รันตามที่กำหนด
  - รายงานสถานะกลับไปยัง Control Plane
  - จัดการ Pod lifecycle

### **2. kube-proxy**
- **หน้าที่**: จัดการ network connectivity
- **โหมดการทำงาน**:
  - **Userspace** (legacy)
  - **IPTables** (default)
  - **IPVS** (high performance)
- **หน้าที่หลัก**:
  - สร้าง network rules สำหรับ Service
  - Load balancing สำหรับ traffic
  - Service discovery

### **3. Container Runtime**
- **หน้าที่**: รัน containers
- **ตัวเลือกที่รองรับ**:
  - **Docker** (legacy, กำลังถูกเลิกใช้)
  - **containerd** (recommended)
  - **CRI-O** (lightweight)
  - **Mirantis Container Runtime**

---

## 🔄 **การทำงานของ Kubernetes Cluster**

### **ลำดับการสร้าง Pod**
```
1. User → kubectl apply -f pod.yaml
2. kubectl → API Server (REST API call)
3. API Server → etcd (บันทึกข้อมูล)
4. API Server → Scheduler (แจ้งว่ามี Pod ใหม่)
5. Scheduler → Filtering + Scoring → เลือก Node
6. Scheduler → API Server (แจ้ง Node ที่เลือก)
7. API Server → etcd (บันทึก Node assignment)
8. API Server → kubelet (บน Node ที่เลือก)
9. kubelet → Container Runtime (สร้าง container)
10. kubelet → API Server (อัพเดทสถานะ)
11. API Server → etcd (บันทึกสถานะใหม่)
```

---

## 🌐 **Network Architecture**

### **Pod Network**
- **Pod-to-Pod Communication**: ทุก Pod สามารถติดต่อกันได้
- **CNI (Container Network Interface)**: Plugin สำหรับจัดการ network
- **Pod IP**: IP ที่ unique ใน cluster

### **Service Network**
- **ClusterIP**: Virtual IP ภายใน cluster
- **NodePort**: เปิด port บนทุก Node
- **LoadBalancer**: สร้าง external load balancer
- **ExternalName**: Map ไปยัง DNS name ภายนอก

### **DNS ใน Kubernetes**
- **CoreDNS**: DNS server ภายใน cluster
- **Service Discovery**: `service-name.namespace.svc.cluster.local`
- **Pod DNS**: `pod-ip-address.namespace.pod.cluster.local`

---

## 📦 **Objects และ Resources**

### **Basic Objects**
1. **Pod**: หน่วยที่เล็กที่สุด
2. **Service**: Network endpoint
3. **Volume**: Persistent storage
4. **Namespace**: Logical partition

### **Controller Objects**
1. **ReplicaSet**: รักษาจำนวน Pod replicas
2. **Deployment**: จัดการ ReplicaSet + rolling updates
3. **StatefulSet**: สำหรับ stateful applications
4. **DaemonSet**: รัน Pod บนทุก Node
5. **Job/CronJob**: สำหรับ batch processing

### **Configuration Objects**
1. **ConfigMap**: เก็บ configuration data
2. **Secret**: เก็บ sensitive data
3. **ServiceAccount**: สำหรับ authentication

---

## 🛡️ **Security Architecture**

### **Authentication**
- **Static Token File**
- **Bootstrap Tokens**
- **Service Account Tokens**
- **OpenID Connect (OIDC)**
- **Webhook Token Authentication**
- **X.509 Client Certificates**

### **Authorization**
- **Node** - สำหรับ kubelet
- **ABAC** - Attribute-Based Access Control
- **RBAC** - Role-Based Access Control (recommended)
- **Webhook** - External authorization

### **Admission Control**
- **Validating Admission**: ตรวจสอบความถูกต้อง
- **Mutating Admission**: แก้ไข/เพิ่มข้อมูล
- **ตัวอย่าง plugins**:
  - NamespaceLifecycle
  - LimitRanger
  - ServiceAccount
  - ResourceQuota

---

## 📊 **Storage Architecture**

### **Volume Types**
1. **emptyDir**: ชั่วคราว
2. **hostPath**: ใช้ไฟล์จาก host
3. **PersistentVolume (PV)**: Persistent storage
4. **PersistentVolumeClaim (PVC)**: Request สำหรับ storage

### **Storage Classes**
- **Dynamic Provisioning**: สร้าง storage อัตโนมัติ
- **ตัวอย่าง providers**:
  - AWS EBS
  - GCE Persistent Disk
  - Azure Disk
  - NFS
  - Ceph

---

## 🔧 **Add-ons และ Extensions**

### **Cluster Add-ons**
- **DNS**: CoreDNS/kube-dns
- **Dashboard**: Web UI
- **Monitoring**: Prometheus, Grafana
- **Logging**: EFK Stack (Elasticsearch, Fluentd, Kibana)
- **Ingress Controller**: Nginx, Traefik, HAProxy
- **Network Policy**: Calico, Weave Net

### **Custom Resource Definitions (CRD)**
- ขยาย Kubernetes API
- กำหนด resource types ใหม่
- ใช้กับ Operators

---

## 🚀 **รูปแบบการติดตั้ง**

### **Single-node Cluster**
```
ใช้สำหรับ development/testing
Minikube, Docker Desktop, MicroK8s
```

### **Multi-master High Availability**
```
┌─────────────────────────────────────┐
│         Load Balancer (VIP)         │
├─────────────────────────────────────┤
│      ┌─────────┬─────────┬─────────┐│
│      │ Master1 │ Master2 │ Master3 ││
│      └─────────┴─────────┴─────────┘│
├─────────────────────────────────────┤
│      ┌─────────┬─────────┬─────────┐│
│      │ Node1   │ Node2   │ Node3   ││
│      └─────────┴─────────┴─────────┘│
└─────────────────────────────────────┘
```

### **Cloud Managed**
- **EKS**: Amazon Elastic Kubernetes Service
- **GKE**: Google Kubernetes Engine
- **AKS**: Azure Kubernetes Service

---

## ⚡ **Best Practices**

### **1. Resource Management**
```yaml
resources:
  requests:
    memory: "64Mi"
    cpu: "250m"
  limits:
    memory: "128Mi"
    cpu: "500m"
```

### **2. High Availability**
- รันหลาย replicas
- กระจาย Pod ไปหลาย Node (Pod Anti-affinity)
- ใช้ PodDisruptionBudget

### **3. Security**
- ใช้ RBAC
- จำกัดใช้ privileged containers
- ใช้ Network Policies
- Update images และ security patches

### **4. Monitoring**
- Monitor resource usage
- ตั้ง alerts สำหรับ critical events
- เก็บ logs อย่างเหมาะสม

---

## 🧪 **ตัวอย่างการทำงาน**

### **Deployment Workflow**
```bash
# 1. สร้าง Deployment
kubectl create deployment nginx --image=nginx:latest

# 2. Scale out
kubectl scale deployment nginx --replicas=3

# 3. Expose Service
kubectl expose deployment nginx --port=80 --type=LoadBalancer

# 4. Rolling Update
kubectl set image deployment/nginx nginx=nginx:1.19
```

---

## 🔍 **Troubleshooting Tips**

### **คำสั่งตรวจสอบ**
```bash
# ตรวจสอบ cluster
kubectl cluster-info
kubectl get nodes
kubectl get componentstatuses

# ตรวจสอบ Pods
kubectl get pods --all-namespaces
kubectl describe pod <pod-name>

# ดู logs
kubectl logs <pod-name>
kubectl logs -f <pod-name>  # follow mode

# ดู events
kubectl get events --sort-by='.lastTimestamp'
```

### **ปัญหาที่พบบ่อย**
1. **ImagePullBackOff**: Image ดาวน์โหลดไม่ได้
2. **CrashLoopBackOff**: Container รันแล้วตายซ้ำๆ
3. **Pending**: ไม่มีทรัพยากรพอ
4. **NodeNotReady**: Node มีปัญหา

---

## 📚 **แหล่งเรียนรู้เพิ่มเติม**

### **Official Resources**
- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [Kubernetes API Reference](https://kubernetes.io/docs/reference/)
- [Kubernetes GitHub](https://github.com/kubernetes/kubernetes)

### **Interactive Learning**
- [Kubernetes Playground](https://www.katacoda.com/courses/kubernetes)
- [Play with Kubernetes](https://labs.play-with-k8s.com/)

### **Certification**
- **CKAD**: Certified Kubernetes Application Developer
- **CKA**: Certified Kubernetes Administrator
- **CKS**: Certified Kubernetes Security Specialist

---

## 🎯 **สรุปสถาปัตยกรรมสำคัญ**
1. **Control Plane** = สมองของระบบ
2. **Worker Nodes** = แรงงานที่ทำงานจริง
3. **etcd** = ความจำของระบบ
4. **kubelet** = ตัวแทนบนแต่ละ Node
5. **kube-proxy** = จราจรผู้จัดการเครือข่าย

---
# 🚀 **คู่มือ Jenkins ฉบับสมบูรณ์ - ภาษาไทย**

## 📌 **บทที่ 1: Jenkins คืออะไร?**

### **Jenkins คืออะไร?**
Jenkins เป็น **Open Source Automation Server** สำหรับสร้าง **Continuous Integration (CI)** และ **Continuous Delivery (CD)** pipelines

### **เหตุผลที่ควรใช้ Jenkins**
✅ **Open Source** - ฟรี!  
✅ **Plugin Ecosystem** - รองรับมากกว่า 1,800 plugins  
✅ **Scalable** - กระจายงานไปหลาย agent ได้  
✅ **Community** - ชุมชนใหญ่ มี support ดี  

### **CI/CD คืออะไร?**
```
CI (Continuous Integration): รวมโค้ดจากหลายคน → ทดสอบ → แจ้งผล
CD (Continuous Delivery): Deploy ไปยัง environment ต่างๆ อัตโนมัติ
```

---

## 📌 **บทที่ 2: การติดตั้ง Jenkins**

### **วิธีติดตั้ง Jenkins**
เลือกวิธีที่เหมาะกับคุณ:

### **1. Docker (แนะนำสำหรับทดสอบ)**
```bash
# Pull Jenkins image
docker pull jenkins/jenkins:lts

# Run Jenkins container
docker run -d \
  --name jenkins \
  -p 8080:8080 \
  -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  jenkins/jenkins:lts

# Get initial admin password
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

### **2. Ubuntu/Debian**
```bash
# 1. อัพเดทระบบ
sudo apt update

# 2. ติดตั้ง Java (requirement)
sudo apt install openjdk-11-jdk -y

# 3. เพิ่ม Jenkins repository
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null

echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null

# 4. ติดตั้ง Jenkins
sudo apt update
sudo apt install jenkins -y

# 5. เริ่มบริการ
sudo systemctl start jenkins
sudo systemctl enable jenkins

# 6. ตรวจสอบสถานะ
sudo systemctl status jenkins
```

### **3. Windows**
1. ดาวน์โหลด Jenkins Windows installer จาก [jenkins.io](https://www.jenkins.io/download/)
2. รัน installer
3. เปิด http://localhost:8080
4. ใช้ password จาก: `C:\Program Files\Jenkins\secrets\initialAdminPassword`

### **4. macOS**
```bash
# ใช้ Homebrew
brew install jenkins-lts

# Start Jenkins
brew services start jenkins-lts

# หรือรันแบบ manual
jenkins-lts
```

---

## 📌 **บทที่ 3: การตั้งค่าเริ่มต้น**

### **First-time Setup**
1. เปิดเบราว์เซอร์ไปที่ **http://localhost:8080**
2. ใส่ **initial admin password** (หาได้จากคำสั่งด้านบน)
3. **Install suggested plugins** หรือเลือกเอง
4. สร้าง **admin user**
5. **Instance configuration** - ตั้งค่า URL

### **Recommended Plugins สำหรับเริ่มต้น**
```
1. Git
2. GitHub
3. Pipeline
4. Docker
5. Blue Ocean (UI ใหม่)
6. Credentials Binding
7. Email Extension
8. Workspace Cleanup
```

---

## 📌 **บทที่ 4: สร้าง Job แรกของคุณ**

### **ประเภทของ Job ใน Jenkins**
1. **Freestyle project** - ง่ายสุด สำหรับเริ่มต้น
2. **Pipeline** - ใช้ Jenkinsfile (แนะนำ)
3. **Multi-configuration project** - รันหลาย configuration
4. **Folder** - จัดกลุ่ม Jobs
5. **Multibranch Pipeline** - สำหรับ Git branches

### **สร้าง Freestyle Job**
1. คลิก **New Item**
2. ตั้งชื่อ **My-First-Job**
3. เลือก **Freestyle project**
4. คลิก **OK**

### **กำหนดค่า Job**
```bash
# ในส่วน Build
Add build step → Execute shell

# คำสั่งตัวอย่าง
echo "Hello, Jenkins!"
whoami
pwd
ls -la
```

### **ตัวอย่าง Job สำหรับ Build Maven Project**
```bash
# Clean and package
mvn clean package

# Run tests
mvn test

# Generate reports
mvn site
```

---

## 📌 **บทที่ 5: Jenkins Pipeline**

### **Pipeline คืออะไร?**
Pipeline คือ **การกำหนด workflow ของ CI/CD เป็นโค้ด** (Infrastructure as Code)

### **Pipeline Syntax**
#### **1. Declarative Pipeline (แนะนำ)**
```groovy
pipeline {
    agent any
    
    stages {
        stage('Build') {
            steps {
                echo 'Building...'
                sh 'mvn clean compile'
            }
        }
        
        stage('Test') {
            steps {
                echo 'Testing...'
                sh 'mvn test'
            }
        }
        
        stage('Deploy') {
            steps {
                echo 'Deploying...'
                sh 'mvn deploy'
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline completed'
        }
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
```

#### **2. Scripted Pipeline**
```groovy
node {
    stage('Build') {
        echo 'Building...'
    }
    stage('Test') {
        echo 'Testing...'
    }
    stage('Deploy') {
        echo 'Deploying...'
    }
}
```

### **สร้าง Pipeline Job**
1. New Item → **Pipeline**
2. ใน Pipeline section → เลือก **Pipeline script**
3. วางโค้ด Pipeline ลงไป
4. Save → Build Now

---

## 📌 **บทที่ 6: Jenkinsfile**

### **Jenkinsfile คืออะไร?**
ไฟล์ที่เก็บ Pipeline configuration ใน repository ของคุณ

### **ตัวอย่าง Jenkinsfile เต็มรูปแบบ**
```groovy
pipeline {
    agent {
        docker {
            image 'maven:3.8.4-openjdk-11'
            args '-v $HOME/.m2:/root/.m2'
        }
    }
    
    triggers {
        pollSCM('H/5 * * * *')  // Poll SCM ทุก 5 นาที
    }
    
    options {
        timeout(time: 1, unit: 'HOURS')
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }
    
    environment {
        APP_NAME = 'my-application'
        VERSION = '1.0.0'
        DOCKER_REGISTRY = 'registry.example.com'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                sh 'mvn clean compile'
            }
        }
        
        stage('Test') {
            steps {
                sh 'mvn test'
                junit 'target/surefire-reports/*.xml'
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh 'mvn sonar:sonar'
                }
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_REGISTRY}/${APP_NAME}:${VERSION}")
                }
            }
        }
        
        stage('Push to Registry') {
            steps {
                script {
                    docker.withRegistry("https://${DOCKER_REGISTRY}", 'docker-credentials') {
                        docker.image("${DOCKER_REGISTRY}/${APP_NAME}:${VERSION}").push()
                        docker.image("${DOCKER_REGISTRY}/${APP_NAME}:${VERSION}").push('latest')
                    }
                }
            }
        }
        
        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                    kubectl set image deployment/${APP_NAME} \
                    ${APP_NAME}=${DOCKER_REGISTRY}/${APP_NAME}:${VERSION} \
                    --namespace=production
                '''
            }
        }
    }
    
    post {
        success {
            emailext (
                subject: "SUCCESS: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                body: "Pipeline succeeded!\n\nCheck console output: ${env.BUILD_URL}",
                to: 'team@example.com'
            )
        }
        failure {
            emailext (
                subject: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                body: "Pipeline failed!\n\nCheck console output: ${env.BUILD_URL}",
                to: 'team@example.com'
            )
            slackSend(
                channel: '#alerts',
                color: 'danger',
                message: "Build ${env.JOB_NAME} #${env.BUILD_NUMBER} FAILED!"
            )
        }
        always {
            cleanWs()  // Clean workspace
        }
    }
}
```

---

## 📌 **บทที่ 7: Jenkins Agents (Nodes)**

### **Master vs Agent**
```
Master: จัดการ Jobs, UI, Configuration
Agent: รัน Jobs จริง (Build, Test, Deploy)
```

### **เพิ่ม Agent**
1. **Manage Jenkins** → **Manage Nodes and Clouds**
2. **New Node**
3. กำหนดชื่อและเลือก **Permanent Agent**
4. กำหนดค่า:
   - Remote root directory
   - Launch method (SSH, Java Web Start, etc.)
   - Labels (สำหรับกำหนดงานเฉพาะ)

### **Docker Agent**
```groovy
pipeline {
    agent {
        docker {
            image 'node:14'
            label 'docker-agent'
            args '-v /tmp:/tmp'
        }
    }
    stages {
        // Your stages here
    }
}
```

### **Kubernetes Agent**
```groovy
pipeline {
    agent {
        kubernetes {
            label 'k8s-agent'
            yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: maven
    image: maven:3.8.4-openjdk-11
    command: ['cat']
    tty: true
    volumeMounts:
    - name: maven-cache
      mountPath: /root/.m2
"""
        }
    }
}
```

---

## 📌 **บทที่ 8: Credentials Management**

### **ประเภท Credentials**
1. **Username with password**
2. **SSH Username with private key**
3. **Secret file**
4. **Secret text**
5. **Certificate**

### **การใช้งาน Credentials ใน Pipeline**
```groovy
pipeline {
    agent any
    
    environment {
        // ใช้ credentials ใน environment
        GITHUB_TOKEN = credentials('github-token')
        DOCKER_PASSWORD = credentials('docker-hub-password')
    }
    
    stages {
        stage('Use Credentials') {
            steps {
                // ใช้กับคำสั่ง sh
                sh '''
                    echo $GITHUB_TOKEN | docker login \
                    -u myuser --password-stdin registry.example.com
                '''
                
                // หรือใช้ withCredentials block
                withCredentials([
                    usernamePassword(
                        credentialsId: 'aws-credentials',
                        usernameVariable: 'AWS_ACCESS_KEY',
                        passwordVariable: 'AWS_SECRET_KEY'
                    )
                ]) {
                    sh 'aws s3 ls'
                }
            }
        }
    }
}
```

---

## 📌 **บทที่ 9: Integration กับ Tools อื่นๆ**

### **1. GitHub/GitLab Integration**
```groovy
pipeline {
    triggers {
        // Webhook trigger
        githubPush()
        
        // หรือ Poll SCM
        pollSCM('*/5 * * * *')
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],
                    extensions: [
                        [$class: 'CleanBeforeCheckout'],
                        [$class: 'CloneOption', depth: 1, shallow: true]
                    ],
                    userRemoteConfigs: [[
                        url: 'https://github.com/username/repo.git',
                        credentialsId: 'github-credentials'
                    ]]
                ])
            }
        }
    }
}
```

### **2. Docker Integration**
```groovy
stage('Build and Push Docker Image') {
    steps {
        script {
            // Build image
            docker.build("myapp:${env.BUILD_ID}")
            
            // Push to registry
            docker.withRegistry('https://registry.example.com', 'docker-credentials') {
                docker.image("myapp:${env.BUILD_ID}").push()
                docker.image("myapp:${env.BUILD_ID}").push('latest')
            }
        }
    }
}
```

### **3. Kubernetes Integration**
```groovy
stage('Deploy to K8s') {
    steps {
        // Apply Kubernetes manifests
        sh '''
            kubectl apply -f k8s/deployment.yaml
            kubectl apply -f k8s/service.yaml
            kubectl apply -f k8s/ingress.yaml
        '''
        
        // หรือใช้ Kubernetes Continuous Deploy plugin
        kubernetesDeploy(
            configs: 'k8s/**',
            kubeconfigId: 'k8s-credentials'
        )
    }
}
```

### **4. SonarQube Integration**
```groovy
stage('Code Quality') {
    steps {
        withSonarQubeEnv('sonar-server') {
            sh 'mvn sonar:sonar'
        }
    }
    
    post {
        success {
            // Wait for quality gate
            timeout(time: 1, unit: 'HOURS') {
                waitForQualityGate abortPipeline: true
            }
        }
    }
}
```

---

## 📌 **บทที่ 10: Best Practices**

### **1. Security**
```groovy
// ใช้ credentials ไม่เก็บ plain text
// จำกัด permissions ด้วย Role-Based Strategy plugin
// Update Jenkins และ plugins เป็นประจำ
```

### **2. Performance**
```groovy
// ใช้ parallel stages
stage('Parallel Tests') {
    parallel {
        stage('Unit Tests') {
            steps { sh 'mvn test' }
        }
        stage('Integration Tests') {
            steps { sh 'mvn integration-test' }
        }
    }
}

// Clean workspace
post {
    always {
        cleanWs()
    }
}
```

### **3. Maintenance**
```
1. Backup Jenkins Home (/var/jenkins_home)
2. Monitor disk space
3. Rotate logs
4. Review plugin usage
```

### **4. Pipeline Templates**
```groovy
// Shared Library สำหรับ reuse code
@Library('shared-library@master') _

pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                buildUtils.mavenBuild()
            }
        }
    }
}
```

---

## 📌 **บทที่ 11: Monitoring และ Troubleshooting**

### **คำสั่งตรวจสอบ Jenkins**
```bash
# ตรวจสอบ logs
sudo tail -f /var/log/jenkins/jenkins.log

# ตรวจสอบ disk space
df -h /var/lib/jenkins

# ตรวจสอบ memory
free -h

# ตรวจสอบ Jenkins process
ps aux | grep jenkins
```

### **Jenkins CLI**
```bash
# Download CLI
wget http://localhost:8080/jnlpJars/jenkins-cli.jar

# ตัวอย่างคำสั่ง
java -jar jenkins-cli.jar -s http://localhost:8080/ who-am-i
java -jar jenkins-cli.jar -s http://localhost:8080/ list-jobs
java -jar jenkins-cli.jar -s http://localhost:8080/ build "Job-Name"
```

### **ปัญหาที่พบบ่อย**
1. **Out of memory**: เพิ่ม heap size ใน `/etc/default/jenkins`
2. **Build stuck**: Restart agent หรือ master
3. **Plugin errors**: Update หรือ reinstall plugin
4. **Permission denied**: ตรวจสอบ file permissions

---

## 📌 **บทที่ 12: Blue Ocean UI**

### **Blue Ocean คืออะไร?**
UI ใหม่ของ Jenkins ที่ใช้ง่ายและสวยงามกว่า

### **การใช้งาน**
```bash
# ติดตั้ง Blue Ocean
Manage Jenkins → Manage Plugins → Available
ค้นหา "Blue Ocean" → Install

# หรือใช้ Docker
docker run -d \
  -p 8080:8080 \
  -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  jenkinsci/blueocean
```

### **คุณสมบัติเด่น**
- **Pipeline Editor** - สร้าง Pipeline ด้วย GUI
- **Branch and Pull Request** - จัดการ branches ง่าย
- **Visualization** - ดู pipeline แบบ graphical
- **Personalization** - Customize dashboard

---

## 📌 **บทที่ 13: ตัวอย่าง Pipeline จริง**

### **ตัวอย่างที่ 1: Node.js Application**
```groovy
pipeline {
    agent {
        docker {
            image 'node:14-alpine'
            args '-p 3000:3000'
        }
    }
    
    stages {
        stage('Setup') {
            steps {
                sh 'npm ci'
            }
        }
        
        stage('Lint') {
            steps {
                sh 'npm run lint'
            }
        }
        
        stage('Test') {
            steps {
                sh 'npm test'
            }
            post {
                always {
                    junit 'reports/**/*.xml'
                }
            }
        }
        
        stage('Build') {
            steps {
                sh 'npm run build'
                archiveArtifacts 'dist/**/*'
            }
        }
        
        stage('Deploy to Staging') {
            when {
                branch 'develop'
            }
            steps {
                sh 'npm run deploy:staging'
            }
        }
        
        stage('Deploy to Production') {
            when {
                branch 'main'
            }
            steps {
                input 'Deploy to production?'
                sh 'npm run deploy:production'
            }
        }
    }
}
```

### **ตัวอย่างที่ 2: Python Django Application**
```groovy
pipeline {
    agent any
    
    environment {
        PYTHON_VERSION = '3.9'
        VENV_PATH = 'venv'
    }
    
    stages {
        stage('Setup Virtual Environment') {
            steps {
                sh """
                    python${PYTHON_VERSION} -m venv ${VENV_PATH}
                    . ${VENV_PATH}/bin/activate
                    pip install -r requirements.txt
                """
            }
        }
        
        stage('Run Tests') {
            steps {
                sh """
                    . ${VENV_PATH}/bin/activate
                    python manage.py test
                    pytest --cov=myapp tests/
                """
            }
        }
        
        stage('Security Scan') {
            steps {
                sh """
                    . ${VENV_PATH}/bin/activate
                    bandit -r myapp/
                    safety check
                '''
            }
        }
    }
}
```

---

## 📌 **บทที่ 14: คำสั่ง Jenkins ที่ควรรู้**

### **Jenkinsfile Functions**
```groovy
// Email notification
emailext(
    subject: "Build Status: ${currentBuild.currentResult}",
    body: "Check details at: ${env.BUILD_URL}",
    to: 'team@example.com'
)

// Slack notification
slackSend(
    channel: '#builds',
    color: currentBuild.currentResult == 'SUCCESS' ? 'good' : 'danger',
    message: "Build ${env.JOB_NAME} #${env.BUILD_NUMBER} ${currentBuild.currentResult}"
)

// Archive artifacts
archiveArtifacts artifacts: 'target/*.jar', fingerprint: true

// Record test results
junit '**/target/surefire-reports/*.xml'

// Timeout
timeout(time: 30, unit: 'MINUTES') {
    // Your steps here
}

// Retry
retry(3) {
    // Steps that might fail
}

// Sleep
sleep time: 10, unit: 'SECONDS'
```

---

## 🎯 **แบบฝึกหัดสำหรับฝึกฝน**

### **ระดับเริ่มต้น**
1. ติดตั้ง Jenkins บน Docker
2. สร้าง Freestyle job ที่พิมพ์ "Hello World"
3. สร้าง Pipeline ที่มี 3 stages (Build, Test, Deploy)
4. Integrate กับ GitHub repository

### **ระดับกลาง**
1. สร้าง Pipeline ที่ใช้ Docker agent
2. เพิ่ม parallel execution สำหรับ tests
3. Integrate กับ SonarQube
4. สร้าง Shared Library

### **ระดับสูง**
1. สร้าง Kubernetes cluster สำหรับ Jenkins agents
2. Implement Canary Deployment
3. สร้าง Multi-branch Pipeline
4. Implement Blue-Green Deployment

---

## 📚 **แหล่งเรียนรู้เพิ่มเติม**

### **Official Resources**
- [Jenkins Documentation](https://www.jenkins.io/doc/)
- [Jenkins Plugins](https://plugins.jenkins.io/)
- [Jenkins GitHub](https://github.com/jenkinsci)

### **คอร์สเรียน**
- [Jenkins Bootcamp - Udemy](https://www.udemy.com/course/jenkins-from-zero-to-hero/)
- [Jenkins, From Zero To Hero](https://www.youtube.com/playlist?list=PLhW3qG5bs-L_ZCOA4zNPSoGbnVQ-rp_dG)

### **Community**
- [Jenkins Forum](https://community.jenkins.io/)
- [Stack Overflow - Jenkins tag](https://stackoverflow.com/questions/tagged/jenkins)

---

## 🆘 **Troubleshooting Guide**

### **Quick Checks**
1. Jenkins service กำลังรันไหม? `sudo systemctl status jenkins`
2. มี disk space พอไหม? `df -h`
3. Jenkins ใช้ memory เยอะไปไหม? `top -p $(pgrep jenkins)`
4. Check logs: `/var/log/jenkins/jenkins.log`

### **Common Errors & Solutions**
```
1. "No space left on device" → Clean workspace, archive old builds
2. "Connection refused" → Check Jenkins service, firewall
3. "Permission denied" → Check file permissions, SELinux
4. "Plugin incompatible" → Update Jenkins หรือ plugin
```
---------------
# โครงสร้างเอกสารโครงการ (Project Documentation Structure)

## 📚 1. สารบัญ (Table of Contents)
- 1.1 บทนำ (Introduction)
- 1.2 วัตถุประสงค์ (Objectives)
- 1.3 ขอบเขตโครงการ (Project Scope)
- 1.4 โครงสร้างทีม (Team Structure)
- 1.5 การสื่อสาร (Communication)
- 1.6 เอกสารอ้างอิง (References)

## 📋 2. แม่แบบ (Templates)
### 2.1 แม่แบบเอกสาร (Document Templates)
- เอกสารข้อกำหนด (Specification Document)
- รายงานความคืบหน้า (Progress Report)
- บันทึกการประชุม (Meeting Minutes)

### 2.2 แม่แบบโค้ด (Code Templates)
- โครงสร้างโปรเจค (Project Structure)
- แม่แบบคลาส (Class Template)
- แม่แบบฟังก์ชัน (Function Template)

## 📝 3. Product Backlog
### 3.1 โครงสร้าง (Structure)
```
ID | ชื่อฟีเจอร์ (Feature Name) | คำอธิบาย (Description) | ความสำคัญ (Priority) | ระดับความซับซ้อน (Complexity) | สถานะ (Status)
```

### 3.2 การจัดลำดับความสำคัญ (Prioritization)
- P0: สำคัญเร่งด่วน (Critical)
- P1: สำคัญสูง (High)
- P2: สำคัญปานกลาง (Medium)
- P3: สำคัญน้อย (Low)

## 🔄 4. Gitflow Workflow
### 4.1 Branch Structure
```
main (production)
├── develop (staging)
│   ├── feature/feature-name
│   ├── bugfix/bug-name
│   └── hotfix/hotfix-name
└── release/vX.X.X
```

### 4.2 Workflow Rules
1. **Feature Development**
   ```
   git checkout develop
   git checkout -b feature/feature-name
   git commit -m "feat: description"
   git push origin feature/feature-name
   ```

2. **Code Review Process**
   - สร้าง Pull Request
   - Review โดยอย่างน้อย 1 คน
   - Pass CI/CD pipeline
   - Approve และ merge

## ⚙️ 5. Workflow
### 5.1 Development Workflow
```
Requirement → Design → Development → Testing → Deployment → Monitoring
```

### 5.2 Review Process
```
Code Review → Unit Testing → Integration Testing → QA Testing → UAT
```

## 🐛 6. Bug Detection Techniques Design
### 6.1 Static Analysis
- Code linting (ESLint, Pylint)
- Static type checking (TypeScript, MyPy)
- Security scanning (SonarQube)

### 6.2 Dynamic Analysis
- Unit testing (Jest, Pytest)
- Integration testing
- E2E testing (Cypress, Selenium)

### 6.3 Automated Testing Strategy
```
Unit Tests (80% coverage) → Integration Tests → E2E Tests → Performance Tests
```

## 🚀 7. Code Enhancement Design
### 7.1 Code Quality Metrics
- Cyclomatic complexity < 10
- Maintainability index > 80
- Code duplication < 5%

### 7.2 Refactoring Guidelines
1. **เมื่อควร Refactor**
   - Code smell detection
   - Performance bottlenecks
   - Changing requirements

2. **Refactoring Techniques**
   - Extract method/class
   - Rename for clarity
   - Simplify conditionals

### 7.3 Performance Optimization
- Database query optimization
- Caching strategy
- Load balancing

## 🌍 8. Global Settings
### 8.1 Configuration Management
```yaml
# config.yaml
environment: production
api:
  base_url: "https://api.example.com"
  timeout: 30
database:
  host: ${DB_HOST}
  port: ${DB_PORT}
logging:
  level: "INFO"
  format: "json"
```

### 8.2 Environment Variables
```
# .env.example
APP_ENV=production
DB_HOST=localhost
DB_PORT=5432
API_KEY=your_api_key
```

## 🛡️ 9. Project Risk Management
### 9.1 Risk Identification
| Risk Category | Description | Probability | Impact | Mitigation |
|--------------|-------------|-------------|---------|------------|
| Technical | Technology stack changes | Medium | High | Regular technology assessment |
| Schedule | Deadline delays | High | High | Agile methodology, buffer time |
| Resource | Team member turnover | Medium | Medium | Knowledge sharing, documentation |

### 9.2 Risk Monitoring
- Weekly risk review meetings
- Risk dashboard
- Early warning indicators

## 💼 10. Business Model Canvas
### 10.1 Canvas Components
1. **Customer Segments** (กลุ่มลูกค้า)
2. **Value Propositions** (คุณค่าที่เสนอ)
3. **Channels** (ช่องทางการสื่อสาร)
4. **Customer Relationships** (ความสัมพันธ์กับลูกค้า)
5. **Revenue Streams** (แหล่งรายได้)
6. **Key Resources** (ทรัพยากรสำคัญ)
7. **Key Activities** (กิจกรรมสำคัญ)
8. **Key Partnerships** (พันธมิตรหลัก)
9. **Cost Structure** (โครงสร้างค่าใช้จ่าย)

---

# 🔧 แม่แบบที่ออกแบบ (Designed Templates)

## 1. TASK LIST Template

### English Version
```markdown
```
# Task: [Task Name]
**ID:** TASK-001
**Created:** [Date]
**Due:** [Date]
**Priority:** [P0/P1/P2/P3]
**Status:** [Not Started/In Progress/Review/Done]

## 📋 Description
[Detailed description of the task]

## 🎯 Objectives
- [ ] Objective 1
- [ ] Objective 2
- [ ] Objective 3

## 🔧 Technical Requirements
- [ ] Requirement 1
- [ ] Requirement 2

## 📁 Dependencies
- [Dependent task 1]
- [Dependent task 2]

## 👥 Assignees
- Primary: [Name]
- Reviewer: [Name]

## 📊 Progress Tracking
| Date | Status Update | Hours Spent | Next Steps |
|------|---------------|-------------|------------|
| | | | |

## ✅ Acceptance Criteria
- [ ] Criterion 1
- [ ] Criterion 2

## 📝 Notes
[Additional notes or comments]


### ภาษาไทย Version
```markdown
```
# งาน: [ชื่องาน]
**รหัส:** TASK-001
**วันที่สร้าง:** [วันที่]
**กำหนดเสร็จ:** [วันที่]
**ความสำคัญ:** [P0/P1/P2/P3]
**สถานะ:** [ยังไม่เริ่ม/กำลังทำ/รอรีวิว/เสร็จแล้ว]

## 📋 คำอธิบาย
[คำอธิบายรายละเอียดของงาน]

## 🎯 วัตถุประสงค์
- [ ] วัตถุประสงค์ 1
- [ ] วัตถุประสงค์ 2
- [ ] วัตถุประสงค์ 3

## 🔧 ข้อกำหนดทางเทคนิค
- [ ] ข้อกำหนด 1
- [ ] ข้อกำหนด 2

## 📁 งานที่เกี่ยวข้อง
- [งานที่ต้องทำก่อน 1]
- [งานที่ต้องทำก่อน 2]

## 👥 ผู้รับผิดชอบ
- ผู้รับผิดชอบหลัก: [ชื่อ]
- ผู้รีวิว: [ชื่อ]

## 📊 การติดตามความคืบหน้า
| วันที่ | อัพเดทสถานะ | เวลาที่ใช้ | ขั้นตอนต่อไป |
|-------|-------------|------------|-------------|
| | | | |

## ✅ เกณฑ์การยอมรับ
- [ ] เกณฑ์ 1
- [ ] เกณฑ์ 2

## 📝 หมายเหตุ
[หมายเหตุหรือความคิดเห็นเพิ่มเติม]


## 2. CHECKLIST Template

### English Version
```markdown
```
# Checklist: [Checklist Name]
**Category:** [Development/Testing/Deployment/Documentation]
**Version:** 1.0
**Last Updated:** [Date]

## 🔍 Pre-Development Checklist
- [ ] Requirements clarified
- [ ] Design reviewed
- [ ] Dependencies identified
- [ ] Environment setup completed

## 💻 Development Checklist
- [ ] Code follows style guide
- [ ] Unit tests written
- [ ] Error handling implemented
- [ ] Performance considered
- [ ] Security considerations addressed

## 🧪 Testing Checklist
- [ ] Unit tests pass (100%)
- [ ] Integration tests pass
- [ ] Edge cases tested
- [ ] Cross-browser compatibility verified
- [ ] Mobile responsiveness tested

## 📝 Code Review Checklist
- [ ] Code is readable and maintainable
- [ ] No dead code or commented-out code
- [ ] Proper logging implemented
- [ ] Configuration managed properly
- [ ] Documentation updated

## 🚀 Deployment Checklist
- [ ] All tests pass
- [ ] Database migrations ready
- [ ] Environment variables configured
- [ ] Backup procedures in place
- [ ] Rollback plan prepared

## 📚 Documentation Checklist
- [ ] API documentation updated
- [ ] User manual updated
- [ ] Change log updated
- [ ] Deployment instructions documented

## ✅ Sign-off
**Developer:** _________________  Date: _________
**Reviewer:** _________________  Date: _________
**QA:** _________________  Date: _________


### ภาษาไทย Version

# ตรวจสอบรายการ: [ชื่อรายการตรวจสอบ]
**หมวดหมู่:** [พัฒนาระบบ/ทดสอบ/ติดตั้ง/เอกสาร]
**เวอร์ชัน:** 1.0
**อัพเดตล่าสุด:** [วันที่]

## 🔍 รายการตรวจสอบก่อนพัฒนา
- [ ] ข้อกำหนดชัดเจน
- [ ] ออกแบบได้รับการรีวิว
- [ ] ระบุงานที่เกี่ยวข้อง
- [ ] ตั้งค่าenvironment เสร็จแล้ว

## 💻 รายการตรวจสอบการพัฒนา
- [ ] โค้ดตาม style guide
- [ ] เขียน unit test แล้ว
- [ ] จัดการ error handling
- [ ] พิจารณาประสิทธิภาพ
- [ ] พิจารณาด้านความปลอดภัย

## 🧪 รายการตรวจสอบการทดสอบ
- [ ] Unit test ผ่าน (100%)
- [ ] Integration test ผ่าน
- [ ] ทดสอบ edge cases
- [ ] ตรวจสอบความเข้ากันได้กับเบราว์เซอร์
- [ ] ทดสอบการแสดงผลบนมือถือ

## 📝 รายการตรวจสอบการรีวิวโค้ด
- [ ] โค้ดอ่านง่ายและบำรุงรักษาได้
- [ ] ไม่มีโค้ดที่ไม่ได้ใช้หรือถูก comment ไว้
- [ ] มีระบบ logging ที่เหมาะสม
- [ ] จัดการ configuration อย่างเหมาะสม
- [ ] อัพเดทเอกสารแล้ว

## 🚀 รายการตรวจสอบการติดตั้ง
- [ ] ทุกการทดสอบผ่าน
- [ ] พร้อมสำหรับ database migration
- [ ] ตั้งค่า environment variables แล้ว
- [ ] มีขั้นตอนการ backup
- [ ] เตรียมแผน rollback แล้ว

## 📚 รายการตรวจสอบเอกสาร
- [ ] อัพเดทเอกสาร API
- [ ] อัพเดทคู่มือผู้ใช้
- [ ] อัพเดท change log
- [ ] มีคำแนะนำการติดตั้ง

## ✅ การลงนามรับรอง
**ผู้พัฒนา:** _________________  วันที่: _________
**ผู้รีวิว:** _________________  วันที่: _________
**QA:** _________________  วันที่: _________
```markdown
```

---

## 📊 Additional Management Templates

### Meeting Minutes Template (สองภาษา)
```markdown
# Meeting Minutes
**วันที่/Date:** [Date]
**เวลา/Time:** [Start Time] - [End Time]
**สถานที่/Location:** [Physical/Virtual - Platform]
**หัวข้อ/Title:** [Meeting Title]

## 👥 ผู้เข้าร่วม/Attendees
**ผู้主持/Chair:** [Name]
**ผู้จดบันทึก/Secretary:** [Name]
**ผู้เข้าร่วม/Participants:** 
- [Name 1]
- [Name 2]

## 📋 วาระการประชุม/Agenda
1. [Agenda Item 1]
2. [Agenda Item 2]
3. [Agenda Item 3]

## 💬 การอภิปราย/Discussion
**หัวข้อ/Topic 1:** [Topic Name]
- [Discussion point 1]
- [Decision made]

**หัวข้อ/Topic 2:** [Topic Name]
- [Discussion point 1]
- [Decision made]

## ✅ มติที่ประชุม/Decisions
1. [Decision 1] - Owner: [Name], Deadline: [Date]
2. [Decision 2] - Owner: [Name], Deadline: [Date]

## 📝 งานที่ต้องทำ/Action Items
| รหัส/ID | รายการ/Description | ผู้รับผิดชอบ/Responsible | กำหนดเสร็จ/Due Date | สถานะ/Status |
|---------|-------------------|-------------------------|-------------------|-------------|
| AI-001 | [Action Item 1] | [Name] | [Date] | [Pending] |
| AI-002 | [Action Item 2] | [Name] | [Date] | [Pending] |

## 📅 การประชุมครั้งต่อไป/Next Meeting
**วันที่/Date:** [Date]
**เวลา/Time:** [Time]
**หัวข้อ/Topics:** [Topics to discuss]

## ✍️ ลายเซ็น/Signatures
**ผู้主持/Chair:** _________________
**ผู้จดบันทึก/Secretary:** _________________
```

### Project Status Report Template
```markdown
# Project Status Report
**Project:** [Project Name]
**Period:** [Start Date] to [End Date]
**Report Date:** [Date]

## 🎯 Executive Summary
**สถานะโดยรวม/Overall Status:** [Green/Yellow/Red]
**ความคืบหน้า/Progress:** [X]%

### จุดเด่น/Highlights:
- [Highlight 1]
- [Highlight 2]

### จุดที่ต้องดูแล/Concerns:
- [Concern 1]
- [Concern 2]

## 📊 Metrics Dashboard
| Metric | Target | Actual | Status |
|--------|--------|--------|--------|
| Completion % | 100% | [X]% | ⚠️ |
| Budget Used | [Y]% | [Z]% | ✅ |
| Issues Resolved | [A] | [B] | ⚠️ |

## 📈 Progress by Phase
| Phase | Planned | Actual | Variance |
|-------|---------|--------|----------|
| Phase 1 | [Date] | [Date] | [Days] |
| Phase 2 | [Date] | [Date] | [Days] |

## ⚠️ Risks and Issues
| Risk/Issue | Impact | Probability | Mitigation | Owner |
|------------|--------|-------------|------------|-------|
| [Risk 1] | High | Medium | [Action] | [Name] |

## 📅 Next Period Plan
**เป้าหมายหลัก/Key Objectives:**
1. [Objective 1]
2. [Objective 2]

**แผนการ/Plan:**
- Week 1: [Tasks]
- Week 2: [Tasks]

## 🤝 Resource Summary
**ทีม/Team:** [Number] members
**อุปกรณ์/Equipment:** [Status]
**งบประมาณ/Budget:** [Status]
```

---

## 🎯 Implementation Guidelines

### 1. การใช้งานในทีม (Team Usage)
- ใช้ TASK LIST สำหรับงานรายบุคคล
- ใช้ CHECKLIST สำหรับกระบวนการมาตรฐาน
- ทบทวนและอัพเดทแม่แบบทุกไตรมาส

### 2. การติดตาม (Tracking)
- เก็บประวัติการเปลี่ยนแปลงของแม่แบบ
- วัดประสิทธิภาพการใช้แม่แบบ
- ปรับปรุงตาม feedback จากทีม

### 3. การฝึกอบรม (Training)
- จัด workshop การใช้แม่แบบ
- มีตัวอย่างการใช้งานจริง
- สร้าง cheat sheet สรุป

### 4. การประเมินผล (Evaluation)
**KPIs สำหรับแม่แบบ:**
- อัตราการใช้งาน (Usage rate)
- เวลาที่ลดลง (Time reduction)
- ความผิดพลาดที่ลดลง (Error reduction)
- ความพึงพอใจของผู้ใช้ (User satisfaction)

---

- เอกสารนี้ถูกออกแบบให้ใช้งานได้จริง พร้อมรองรับสองภาษา และสามารถปรับปรุงได้ตามความต้องการของโครงการ สามารถนำไปใช้งานได้ทันทีหรือปรับแต่งให้เหมาะกับวัฒนธรรมองค์กรและลักษณะของโครงการเฉพาะ

 
---------------



---------------
