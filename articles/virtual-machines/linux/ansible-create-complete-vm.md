---
title: Usare Ansible per creare una macchina virtuale Linux completa in Azure | Microsoft Docs
description: Informazioni su come usare Ansible per creare e gestire un ambiente completo per la macchina virtuale Linux in Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: iainfou
ms.openlocfilehash: 8f0e2fff8ea32874729cf9c4645d547df2449089
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2017
---
# <a name="create-a-complete-linux-virtual-machine-environment-in-azure-with-ansible"></a>Creare un ambiente completo per la macchina virtuale Linux in Azure con Ansible
Ansible consente di automatizzare la distribuzione e la configurazione delle risorse nell'ambiente in uso. È possibile usare Ansible per gestire le macchine virtuali in Azure, così come si farebbe con qualsiasi altra risorsa. In questo articolo viene illustrato come creare un ambiente Linux completo e le risorse di supporto con Ansible. È anche possibile apprendere come [creare una macchina virtuale di base con Ansible](ansible-create-vm.md).


## <a name="prerequisites"></a>Prerequisiti
Per gestire le risorse di Azure con Ansible, è necessario:

- avere Ansible e i moduli dell'SDK Python di Azure installati nel sistema host.
    - Installare Ansible su [Ubuntu 16.04 LTS](ansible-install-configure.md#ubuntu-1604-lts), [CentOS 7.3](ansible-install-configure.md#centos-73) e [SLES 12 SP2](ansible-install-configure.md#sles-12-sp2)
- configurare le credenziali di Azure e Ansible in modo che siano pronte all'uso.
    - [Creare le credenziali di Azure e configurare Ansible](ansible-install-configure.md#create-azure-credentials)
- Versione 2.0.4 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. 
    - Se è necessario eseguire l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). È anche possibile usare [Cloud Shell](/azure/cloud-shell/quickstart) dal browser.


## <a name="create-virtual-network"></a>Creare una rete virtuale
La sezione seguente in un playbook Ansible crea una rete virtuale denominata *myVnet* nello spazio degli indirizzi *10.0.0.0/16*:

```yaml
- name: Create virtual network
  azure_rm_virtualnetwork:
    resource_group: myResourceGroup
    name: myVnet
    address_prefixes: "10.10.0.0/16"
```

Per aggiungere una subnet, la sezione seguente crea una subnet denominata *mySubnet* nella rete virtuale *myVnet*:

```yaml
- name: Add subnet
  azure_rm_subnet:
    resource_group: myResourceGroup
    name: mySubnet
    address_prefix: "10.0.1.0/24"
    virtual_network: myVnet
```


## <a name="create-public-ip-address"></a>Creare un indirizzo IP pubblico
Per accedere alle risorse in Internet, creare e assegnare un indirizzo IP pubblico alla macchina virtuale. La sezione seguente in un playbook Ansible crea un indirizzo IP pubblico denominato *myPublicIP*:

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```


## <a name="create-network-security-group"></a>Creare un gruppo di sicurezza di rete
I gruppi di sicurezza di rete consentono di controllare il flusso del traffico di rete in ingresso e in uscita dalla macchina virtuale. La sezione seguente in un playbook Ansible crea un gruppo di sicurezza di rete denominato *myNetworkSecurityGroup* e definisce una regola per consentire il traffico SSH sulla porta TCP 22:

```yaml
- name: Create Network Security Group that allows SSH
  azure_rm_securitygroup:
    resource_group: myResourceGroup
    name: myNetworkSecurityGroup
    rules:
      - name: SSH
        protocol: TCP
        destination_port_range: 22
        access: Allow
        priority: 1001
        direction: Inbound
```


## <a name="create-virtual-network-interface-card"></a>Creare la scheda di interfaccia di rete virtuale
Una scheda di interfaccia di rete virtuale, NIC, connette la macchina virtuale a una rete virtuale specifica, a un indirizzo IP pubblico e a un gruppo di sicurezza di rete. La sezione seguente in un playbook Ansible crea una scheda di rete virtuale denominata *myNIC* connessa alle risorse di rete virtuale create:

```yaml
- name: Create virtual network inteface card
  azure_rm_networkinterface:
    resource_group: myResourceGroup
    name: myNIC
    virtual_network: myVnet
    subnet: mySubnet
    public_ip_name: myPublicIP
    security_group: myNetworkSecurityGroup
```


## <a name="create-virtual-machine"></a>Crea macchina virtuale
Il passaggio finale consiste nel creare una macchina virtuale e usare tutte le risorse create. La sezione seguente in un playbook Ansible crea una macchina virtuale denominata *myVM* e associa la scheda di rete virtuale denominata *myNIC*. Immettere i propri dati della chiave pubblica nella coppia *key_data* come indicato di seguito:

```yaml
- name: Create VM
  azure_rm_virtualmachine:
    resource_group: myResourceGroup
    name: myVM
    vm_size: Standard_DS1_v2
    admin_username: azureuser
    ssh_password_enabled: false
    ssh_public_keys: 
      - path: /home/azureuser/.ssh/authorized_keys
        key_data: "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
    network_interfaces: myNIC
    image:
      offer: CentOS
      publisher: OpenLogic
      sku: '7.3'
      version: latest
```

## <a name="complete-ansible-playbook"></a>Completare il playbook Ansible
Per unire tutte queste sezioni, creare un playbook Ansible denominato *azure_create_vm.yml* e incollarvi i contenuti seguenti:

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: myResourceGroup
      name: myVnet
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: myResourceGroup
      name: mySubnet
      address_prefix: "10.0.1.0/24"
      virtual_network: myVnet
  - name: Create public IP address
    azure_rm_publicipaddress:
      resource_group: myResourceGroup
      allocation_method: Static
      name: myPublicIP
  - name: Create Network Security Group that allows SSH
    azure_rm_securitygroup:
      resource_group: myResourceGroup
      name: myNetworkSecurityGroup
      rules:
        - name: SSH
          protocol: Tcp
          destination_port_range: 22
          access: Allow
          priority: 1001
          direction: Inbound
  - name: Create virtual network inteface card
    azure_rm_networkinterface:
      resource_group: myResourceGroup
      name: myNIC
      virtual_network: myVnet
      subnet: mySubnet
      public_ip_name: myPublicIP
      security_group: myNetworkSecurityGroup
  - name: Create VM
    azure_rm_virtualmachine:
      resource_group: myResourceGroup
      name: myVM
      vm_size: Standard_DS1_v2
      admin_username: azureuser
      ssh_password_enabled: false
      ssh_public_keys: 
        - path: /home/azureuser/.ssh/authorized_keys
          key_data: "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
      network_interfaces: myNIC
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.3'
        version: latest
```

Ansible deve distribuire tutte le risorse in un gruppo di risorse. Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/vm#create). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Per creare l'ambiente completo per la macchina virtuale con Ansible, eseguire il playbook come segue:

```bash
ansible-playbook azure_create_complete_vm.yml
```

L'output è simile all'esempio seguente che mostra che la macchina virtuale è stata creata correttamente:

```bash
PLAY [Create Azure VM] ****************************************************

TASK [Gathering Facts] ****************************************************
ok: [localhost]

TASK [Create virtual network] *********************************************
changed: [localhost]

TASK [Add subnet] *********************************************************
changed: [localhost]

TASK [Create public IP address] *******************************************
changed: [localhost]

TASK [Create Network Security Group that allows SSH] **********************
changed: [localhost]

TASK [Create virtual network inteface card] *******************************
changed: [localhost]

TASK [Create VM] **********************************************************
changed: [localhost]

PLAY RECAP ****************************************************************
localhost                  : ok=7    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>Passaggi successivi
In questo esempio viene creato un ambiente completo della macchina virtuale che include le risorse di rete virtuali necessarie. Per un esempio più diretto che illustri come creare una macchina virtuale nelle risorse di rete esistenti con le opzioni predefinite, vedere [Creare una macchina virtuale](ansible-create-vm.md).
