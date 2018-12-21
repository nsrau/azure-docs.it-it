---
title: Crea set di scalabilità di macchine virtuali in Azure con Ansible
description: Informazioni su come usare Ansible per creare e configurare un set di scalabilità di macchine virtuali Linux in Azure
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, macchina virtuale, set di scalabilità di macchine virtuali, vmss
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 08/24/2018
ms.openlocfilehash: 8dacfb054a36801c076435037a530daa87c4a89c
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409403"
---
# <a name="create-virtual-machine-scale-sets-in-azure-using-ansible"></a>Crea set di scalabilità di macchine virtuali in Azure con Ansible
Ansible consente di automatizzare la distribuzione e la configurazione delle risorse nell'ambiente in uso. È possibile usare Ansible per gestire il set di scalabilità di macchine virtuali in Azure, così come si farebbe con qualsiasi altra risorsa di Azure. Questo articolo illustra come usare Ansible per creare e scalare orizzontalmente un set di scalabilità di macchine virtuali. 

## <a name="prerequisites"></a>Prerequisiti
- **Sottoscrizione di Azure** - Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> In questa esercitazione, per eseguire i playbook di esempio seguenti è necessario Ansible 2.6. 

## <a name="create-a-vmss"></a>Creare un set di scalabilità di macchine virtuali
In questa sezione viene presentato un playbook Ansible di esempio che definisce le seguenti risorse:
- **Gruppo di risorse** in cui verranno distribuite tutte le risorse
- **Rete virtuale** nello spazio indirizzi 10.0.0.0/16
- **Subnet** all'interno della rete virtuale
- **Indirizzo IP pubblico** che consente di accedere alle risorse in Internet
- **Gruppo di sicurezza di rete** che controlla il flusso del traffico di rete in ingresso e in uscita dal set di scalabilità di macchine virtuali
- **Bilanciamento del carico** che distribuisce il traffico in un set di macchine virtuali definite usando regole di bilanciamento del carico
- **Set di scalabilità di macchine virtuali** che usa tutte le risorse create

Immettere la propria password per il valore *admin_password*.

  ```yml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      vmss_name: myVMSS
      vmss_lb_name: myVMSSlb
      location: eastus
      admin_username: azureuser
      admin_password: "your_password"
    tasks:
      - name: Create a resource group
        azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
      - name: Create virtual network
        azure_rm_virtualnetwork:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          address_prefixes: "10.0.0.0/16"
      - name: Add subnet
        azure_rm_subnet:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          address_prefix: "10.0.1.0/24"
          virtual_network: "{{ vmss_name }}"
      - name: Create public IP address
        azure_rm_publicipaddress:
          resource_group: "{{ resource_group }}"
          allocation_method: Static
          name: "{{ vmss_name }}"
      - name: Create Network Security Group that allows SSH
        azure_rm_securitygroup:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          rules:
            - name: SSH
              protocol: Tcp
              destination_port_range: 22
              access: Allow
              priority: 1001
              direction: Inbound

      - name: Create a load balancer
        azure_rm_loadbalancer:
          name: "{{ vmss_lb_name }}"
          location: "{{ location }}"
          resource_group: "{{ resource_group }}"
          public_ip: "{{ vmss_name }}"
          probe_protocol: Tcp
          probe_port: 8080
          probe_interval: 10
          probe_fail_count: 3
          protocol: Tcp
          load_distribution: Default
          frontend_port: 80
          backend_port: 8080
          idle_timeout: 4
          natpool_frontend_port_start: 50000
          natpool_frontend_port_end: 50040
          natpool_backend_port: 22
          natpool_protocol: Tcp

      - name: Create VMSS
        azure_rm_virtualmachine_scaleset:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          vm_size: Standard_DS1_v2
          admin_username: "{{ admin_username }}"
          admin_password: "{{ admin_password }}"
          ssh_password_enabled: true
          capacity: 2
          virtual_network_name: "{{ vmss_name }}"
          subnet_name: "{{ vmss_name }}"
          upgrade_policy: Manual
          tier: Standard
          managed_disk_type: Standard_LRS
          os_disk_caching: ReadWrite
          image:
            offer: UbuntuServer
            publisher: Canonical
            sku: 16.04-LTS
            version: latest
          load_balancer: "{{ vmss_lb_name }}"
          data_disks:
            - lun: 0
              disk_size_gb: 20
              managed_disk_type: Standard_LRS
              caching: ReadOnly
            - lun: 1
              disk_size_gb: 30
              managed_disk_type: Standard_LRS
              caching: ReadOnly
  ```

Per creare l'ambiente del set di scalabilità di macchine virtuali con Ansible, salvare il playbook precedente come `vmss-create.yml` o [scaricare il playbook Ansible di esempio](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-create.yml).

Per eseguire il playbook Ansible, usare il comando **ansible-playbook** come segue:

  ```bash
  ansible-playbook vmss-create.yml
  ```

Dopo aver eseguito il playbook, un output simile all'esempio seguente indica che il set di scalabilità di macchine virtuali è stato creato correttamente:

  ```Output
  PLAY [localhost] ***********************************************************

  TASK [Gathering Facts] *****************************************************
  ok: [localhost]

  TASK [Create a resource group] ****************************************************************************
  changed: [localhost]

  TASK [Create virtual network] ****************************************************************************
  changed: [localhost]

  TASK [Add subnet] **********************************************************
  changed: [localhost]

  TASK [Create public IP address] ****************************************************************************
  changed: [localhost]

  TASK [Create Network Security Group that allows SSH] ****************************************************************************
  changed: [localhost]

  TASK [Create a load balancer] ****************************************************************************
  changed: [localhost]

  TASK [Create VMSS] *********************************************************
  changed: [localhost]

  PLAY RECAP *****************************************************************
  localhost                  : ok=8    changed=7    unreachable=0    failed=0

  ```

## <a name="scale-out-a-vmss"></a>Scalabilità orizzontale di un set di scalabilità di macchine virtuali
Il set di scalabilità di macchine virtuali creato ha due istanze. Se si passa al set di scalabilità di macchine virtuali nel portale di Azure, viene visualizzato **Standard_DS1_v2 (2 instances)**. È anche possibile usare [Azure Cloud Shell](https://shell.azure.com/) eseguendo il comando seguente in Cloud Shell:

  ```azurecli-interactive
  az vmss show -n myVMSS -g myResourceGroup --query '{"capacity":sku.capacity}' 
  ```

Verrà visualizzato un output simile al seguente:

  ```bash
  {
    "capacity": 2,
  }
  ```

A questo punto, è possibile passare da due a tre istanze. Il seguente codice del playbook Ansible recupera le informazioni sulla scalabilità delle macchine virtuali e ne modifica la capacità da due a tre. 

  ```yml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      vmss_name: myVMSS
    tasks: 
      - name: Get scaleset info
        azure_rm_virtualmachine_scaleset_facts:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          format: curated
        register: output_scaleset

      - name: Dump scaleset info
        debug:
          var: output_scaleset

      - name: Modify scaleset (change the capacity to 3)
        set_fact:
          body: "{{ output_scaleset.ansible_facts.azure_vmss[0] | combine({'capacity': 3}, recursive=True) }}"

      - name: Update something in that VMSS
        azure_rm_virtualmachine_scaleset: "{{ body }}"
  ```

Per scalare orizzontalmente il set di scalabilità di macchine virtuali creato, salvare il playbook precedente come `vmss-scale-out.yml` o [scaricare il playbook Ansible di esempio](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-scale-out.yml). 

Il playbook verrà eseguito dal comando seguente:

  ```bash
  ansible-playbook vmss-scale-out.yml
  ```

L'output generato dall'esecuzione del playbook Ansible indica che il set di scalabilità di macchine virtuali è stato aumentato correttamente:

  ```Output
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Get scaleset info] ***************************************************************************
  ok: [localhost]

  TASK [Dump scaleset info] ***************************************************************************
  ok: [localhost] => {
      "output_scaleset": {
          "ansible_facts": {
              "azure_vmss": [
                  {
                      ......
                  }
              ]
          },
          "changed": false,
          "failed": false
      }
  }

  TASK [Modify scaleset (set upgradePolicy to Automatic and capacity to 3)] ***************************************************************************
  ok: [localhost]

  TASK [Update something in that VMSS] ***************************************************************************
  changed: [localhost]

  PLAY RECAP ****************************************************************
  localhost                  : ok=5    changed=1    unreachable=0    failed=0
  ```

Se si passa al set di scalabilità di macchine virtuali configurato nel portale di Azure, viene visualizzato **Standard_DS1_v2 (3 instances)**. È anche possibile verificare la modifica con [Azure Cloud Shell](https://shell.azure.com/) eseguendo questo comando:

  ```azurecli-interactive
  az vmss show -n myVMSS -g myResourceGroup --query '{"capacity":sku.capacity}' 
  ```

I risultati dell'esecuzione del comando in Cloud Shell indicano che ora sono disponibili tre istanze. 

  ```bash
  {
    "capacity": 3,
  }
  ```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"] 
> [Distribuire applicazioni nei set di scalabilità di macchine virtuali tramite Ansible](https://docs.microsoft.com/azure/ansible/ansible-deploy-app-vmss)

> [Ridimensionare automaticamente un set di scalabilità di macchine virtuali con Ansible](https://docs.microsoft.com/azure/ansible/ansible-auto-scale-vmss)