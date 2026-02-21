# Deploying-OpenCTI-Threat-Intelligence-with-Docker-Compose-on-AWS
On this repository I designed and deployed OpenCTI, a modern threat intelligence platform, on AWS EC2 using Docker Compose, enabling real-time ingestion of external threat feeds for cybersecurity analysis.

Perfect — you’ve already got a detailed write‑up of your deployment steps. To make this GitHub‑ready, you’ll want to structure it into a **README.md** file with clear sections, concise formatting, and code snippets. Here’s a polished version you can drop straight into your repository:

```markdown
# Deploying OpenCTI Threat Intelligence with Docker Compose on AWS

## 📌 Project Overview
**Objective:** Deploy [OpenCTI](https://www.opencti.io/), a threat intelligence platform, on AWS EC2 using Docker Compose.  
**Key Skills:** Cloud provisioning, Docker containerization, application deployment, networking, and security.  
**Outcome:** A fully functional OpenCTI instance hosted on AWS, accessible via web browser, integrated with AlienVault OTX for live threat intelligence feeds.

---

## ⚙️ AWS Setup

### Instance Creation
- Provisioned an **Ubuntu EC2 instance** using AWS AMI.
![Choose-instance](https://github.com/Div20231010/Deploying-OpenCTI-Threat-Intelligence-with-Docker-Compose-on-AWS/blob/main/1st_os_choose.png)

- Selected **t3.large** instance type for sufficient memory and CPU resources.
![Select T3.large-instance](https://github.com/Div20231010/Deploying-OpenCTI-Threat-Intelligence-with-Docker-Compose-on-AWS/blob/main/2nd_instance_selection.png)


### Networking
- Configured **VPC and security groups**.
- Allowed inbound rules:
  - **TCP 22** → SSH access (restricted to my IP).
  - **TCP 8080** → OpenCTI web UI.
  - **TCP 443** → HTTPS (future SSL/Nginx setup).

### Storage
- Attached an **Amazon EBS volume (50GB)** for persistent storage.
![Modify-the-volume](https://github.com/Div20231010/Deploying-OpenCTI-Threat-Intelligence-with-Docker-Compose-on-AWS/blob/main/selected_volume_adjust_80gb.png)

---

## 🖥️ System Preparation

Update packages:
```bash
sudo apt update && sudo apt upgrade -y
```

Install Docker & Docker Compose:
```bash
sudo apt install docker.io docker-compose git -y
sudo systemctl enable docker
sudo systemctl start docker
```

---

## 🚀 OpenCTI Deployment

Clone OpenCTI Docker repository:
```bash
git clone https://github.com/OpenCTI-Platform/docker.git
cd docker
cp .env.sample .env
nano .env
```

Edit `.env` file to configure **admin credentials and tokens**.

![.env-file](https://github.com/Div20231010/Deploying-OpenCTI-Threat-Intelligence-with-Docker-Compose-on-AWS/blob/main/modify_the_changeME.png)

Launch OpenCTI:
```bash
sudo docker-compose up -d
sudo docker ps
```

Access via browser:
```
http://<EC2-Public-IP>:8080
```

Login using admin email/password set in `.env`.

![Login-Using credentials-from .env files](https://github.com/Div20231010/Deploying-OpenCTI-Threat-Intelligence-with-Docker-Compose-on-AWS/blob/main/signed-in.png)

---
![openCTI-logged-in](https://github.com/Div20231010/Deploying-OpenCTI-Threat-Intelligence-with-Docker-Compose-on-AWS/blob/main/welcome_openCTI.png)

---
## 🔗 Connector Integration (AlienVault OTX)

Clone connectors repository:
```bash
git clone https://github.com/OpenCTI-Platform/connectors.git
cd connectors/alienvault
```

Edit `docker-compose.yml` and `config.yml`:
```yaml
opencti:
  url: http://<EC2-Public-IP>:8080
  token: <admin-token>

connector:
  id: alienvault-connector
  type: EXTERNAL_IMPORT
  name: AlienVault OTX

alienvault:
  api_key: <your-otx-api-key>
```
![config.yml-file](https://github.com/Div20231010/Deploying-OpenCTI-Threat-Intelligence-with-Docker-Compose-on-AWS/blob/main/yml_file_configured.png)
Run connector:
```bash
docker-compose down
docker-compose up -d
```

Verify integration in **OpenCTI Web UI → Data → Ingestion**.  
AlienVault OTX should be listed and running, with threat intel feeds populating automatically.
![AlienVault Connected](https://github.com/Div20231010/Deploying-OpenCTI-Threat-Intelligence-with-Docker-Compose-on-AWS/blob/main/alienvault_connected.png)

---

## 🛠️ Challenges & Solutions
- **Region mismatch in AWS console** → Switched back to original region. Always note your AWS region.  
- **Managing multiple dependencies (Elasticsearch, Redis, RabbitMQ)** → Used Docker Compose for orchestration.  
- **Invalid API key** → Retrieved correct key from OTX website and updated configs.
![Region Mismatched](https://github.com/Div20231010/Deploying-OpenCTI-Threat-Intelligence-with-Docker-Compose-on-AWS/blob/main/aws_region_notice.png)
![OTX API KEY](https://github.com/Div20231010/Deploying-OpenCTI-Threat-Intelligence-with-Docker-Compose-on-AWS/blob/main/OTX_API_KEY.png)

---

## ✅ Results
- Successfully deployed OpenCTI on AWS EC2 Ubuntu.  
- Verified web access via EC2 public IP.  
- Integrated AlienVault OTX connector for live threat intelligence feeds.  

---

## 📚 Key Takeaways
- Strengthened skills in **Docker Compose orchestration**.  
- Learned how to integrate **external threat intelligence feeds** into OpenCTI.  
- Gained practical experience in **troubleshooting AWS region settings**.  
- Built confidence in deploying **production-ready applications** on cloud infrastructure.  

---

## 🔒 Security Notes
- Restrict SSH access to trusted IPs.  
- Consider enabling HTTPS with Nginx + SSL certificates.  
- Regularly rotate API keys and tokens.  

---
