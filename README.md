# Automated Docker Swarm Cluster with Vagrant

This project automates the creation of a local Docker Swarm cluster using Infrastructure as Code (IaC) principles. It provisions four Ubuntu virtual machines (one Manager and three Workers) using Vagrant and VirtualBox, ensuring a consistent and reproducible environment.

<br>

## 🛠️ Technologies

* **Vagrant**: Virtual machine orchestration and automation.
* **VirtualBox**: Hypervisor for running the virtualized environment.
* **Docker**: Container engine and Swarm orchestration.
* **Shell Scripting**: Automated installation and node configuration.

<br>

## 🏗️ Cluster Architecture

The environment consists of 4 nodes with static IPs in a private network:

| Hostname | Role | Static IP | RAM |
| :--- | :--- | :--- | :--- |
| **master** | Swarm Manager | 192.168.56.10 | 512MB |
| **node01** | Swarm Worker | 192.168.56.11 | 512MB |
| **node02** | Swarm Worker | 192.168.56.12 | 512MB |
| **node03** | Swarm Worker | 192.168.56.13 | 512MB |

<br>

## 🚀 How to Run

### Prerequisites
* [Vagrant](https://www.vagrantup.com/downloads) installed.
* [VirtualBox](https://www.virtualbox.org/wiki/Downloads) installed.

### Setup Instructions

1. **Clone the repository:**
   ```bash
   git clone [https://github.com/PabloQuadros/automated-docker-swarm-vagrant.git](https://github.com/PabloQuadros/automated-docker-swarm-vagrant.git)
   cd automated-docker-swarm-vagrant
   ```

   
2. **Spin up the cluster:**
   
    Run the following command in your terminal (as Administrator):
    ```bash
    vagrant up
    ```
    This will download the Ubuntu box, create 4 VMs, install Docker, and configure the Swarm automatically.


4. **Verify the Cluster:**
   
    Access the master node to check the status of all nodes:

    ```bash
    vagrant ssh master
    docker node ls
    ```
    Your terminal output should look like the following image.
   
    <img width="913" height="135" alt="image" src="https://github.com/user-attachments/assets/30bfe225-142d-47c1-9e18-587b52ca77b4" />

<br>

## 🧪 Testing Orchestration

To verify if the cluster is distributing tasks correctly, run the following command inside the master node:
```bash
# Create a service with 4 replicas
docker service create --name cluster-test --replicas 4 alpine sleep 1d

# Check service distribution
docker service ps cluster-test
```
Your terminal output should look like the following image.

<img width="1080" height="311" alt="image" src="https://github.com/user-attachments/assets/b9261b44-b290-461b-b6e3-e029dbe07c38" />

<br>

## 🧹 Cleanup
To stop the machines without deleting them:
```bash
  vagrant halt
```

To delete everything and free up your disk space:
```bash
vagrant destroy -f
```
