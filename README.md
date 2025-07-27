# azure-secure-vm-deployment
Securely deploy a Windows or Linux VM in Azure using best practices: custom RBAC roles, Network Security Groups (NSGs), and Just-in-Time (JIT) VM access. Perfect for AZ-104/AZ-500 prep or anyone wanting hands-on Azure VM security experience. Step-by-step with screenshots included.

# Secure Virtual Machine Deployment on Azure

---

## 🚀 Project Overview

This project walks through deploying a **secure Windows or Linux Virtual Machine (VM)** in Microsoft Azure. It highlights key security best practices like:

- Deploying the VM with strict network controls  
- Creating and assigning a **custom RBAC role** to limit VM management permissions  
- Securing network traffic using **Network Security Groups (NSGs)**  
- Implementing **Just-in-Time (JIT) VM Access** (partial, due to license restrictions)  
- Optional automation via PowerShell or Azure CLI (not included here)

This is a great demo project for **Azure Administrator (AZ-104)** and **Azure Security Engineer (AZ-500)** certification preparation or professional portfolios.

---

## 📋 Prerequisites

- Azure subscription with **Contributor** or higher permissions  
- Public IP address for secure VM access  
- Optional: Microsoft Defender for Cloud plan for JIT (not available in all subscriptions)  
- Azure Portal access and/or Azure CLI/PowerShell installed

---

## 🔥 Step 1: Deploy the Virtual Machine

1. Login to the [Azure Portal](https://portal.azure.com)  
2. Navigate to **Virtual Machines** > **+ Create** > **Azure virtual machine**  
3. Fill out the **Basics** tab:  

| Field           | Value                                |
|-----------------|------------------------------------|
| Subscription    | Your Azure subscription             |
| Resource group  | `MyResourceGroup` (create new)      |
| VM Name         | `SecureVM01`                        |
| Region          | East US (or your preferred region) |
| Image           | Windows Server 2022 Datacenter / Ubuntu 20.04 LTS |
| Size            | Standard B2s                       |
| Authentication  | Password                           |
| Username        | `azureuser`                       |
| Password        | `AzuRe!Vm_2025#` (replace with your own secure password) |
| Public inbound ports | Allow selected ports (3389 for Windows, 22 for Linux) |
| Source IP       | Your public IP address (lock down for security) |

4. Configure **Disks**, **Networking**, **Management**, **Advanced**, and **Tags** with defaults or as needed (example below)  
5. Click **Review + create** then **Create**

---

### Example: Networking Tab Setup  

| Setting                    | Value                              |
|----------------------------|----------------------------------|
| Virtual network            | `myVnet` (auto-created or existing) |
| Subnet                    | `default`                        |
| Public IP                  | `securevm01-pip` (dynamic or static) |
| NIC network security group | Create new NSG (`securevm01-nsg`) |
| Public inbound ports       | Selected: RDP (3389) or SSH (22)  |

---

### Screenshot: VM Creation Basics Tab  
![Basics Tab Screenshot](./screenshots/vm-basics.png)

---

## 🔥 Step 2: Create and Assign a Custom RBAC Role

1. Go to **Subscriptions** in Azure Portal  
2. Select your subscription  
3. Click **Access control (IAM)** > **Roles** tab > **+ Add > Add custom role**  
4. In the **Basics** tab, fill in:  
   - Role name: `VM Reader with Start/Stop`  
   - Description: `Can read VM details and start/stop it`  
   - Start from scratch  
5. In **Permissions**, add these actions:  
   - `Microsoft.Compute/virtualMachines/read`  
   - `Microsoft.Compute/virtualMachines/start/action`  
   - `Microsoft.Compute/virtualMachines/deallocate/action`  
6. Assign **Assignable scope** to your resource group or subscription  
7. Click **Review + create** > **Create**  
8. Assign the role to a user or group on the VM or resource group via **Access Control (IAM) > Add role assignment**

---

### Screenshot: Custom Role Permissions  
![Custom Role Permissions](./screenshots/custom-role-permissions.png)

---

## 🔥 Step 3: Secure the VM with Network Security Groups (NSGs)

1. In the Azure Portal, go to your VM > **Networking** tab  
2. Click on the associated **NSG** (e.g., `securevm01-nsg`)  
3. Under **Inbound security rules**, click **+ Add**  
4. Create a rule to allow RDP or SSH only from your public IP:  

| Field                   | Value                               |
|-------------------------|-----------------------------------|
| Source                  | IP Addresses                      |
| Source IP ranges        | Your public IP (e.g., `203.0.113.25`) |
| Destination             | Any                               |
| Destination port ranges | `3389` (RDP) or `22` (SSH)        |
| Protocol                | TCP                               |
| Action                  | Allow                             |
| Priority                | 100                               |
| Name                    | Allow-RDP-MyIP or Allow-SSH-MyIP  |


### Screenshot: NSG Inbound Rule  
![NSG Inbound Rule](./screenshots/nsg-inbound-rule.png)

---

## 🔥 Step 4: Enable Just-in-Time (JIT) VM Access (Partial)

> **Note:** JIT could not be enabled due to subscription/license limitations. Relying on NSG for now.

1. Open **Microsoft Defender for Cloud** from the Azure Portal  
2. Navigate to **Workload protections > Just-in-time VM access**  
3. Find your VM (`SecureVM01`) and attempt to enable JIT  
4. If unavailable, ensure NSG is restricting inbound access as above  

---

### Screenshot: Defender for Cloud JIT VM Access  
![JIT VM Access](./screenshots/defender-jit.png)

---

## 🎯 How to Connect to the VM

- Use **Remote Desktop (mstsc.exe)** for Windows VMs  
- Use **SSH** for Linux VMs:  
  ```bash
  ssh azureuser@<public-ip-address>
