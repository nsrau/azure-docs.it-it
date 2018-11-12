---
title: Usare Ansible per creare una macchina virtuale Linux in Azure
description: Informazioni su come usare Ansible per creare una macchina virtuale Linux in Azure
ms.service: ansible
keywords: terraform, devops, macchina virtuale
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 08/22/2018
ms.openlocfilehash: 99822fc1d99d914327a64cfb9fe8e18189f30a4f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51251239"
---
# <a name="use-ansible-to-create-a-linux-virtual-machine-in-azure"></a>Usare Ansible per creare una macchina virtuale Linux in Azure
Attraverso un linguaggio dichiarativo, Ansible permette di automatizzare la creazione, la configurazione e la distribuzione di risorse di Azure tramite *playbook* Ansible. Ogni sezione di questo articolo mostra l'aspetto di ogni parte di un playbook Ansible per creare e configurare diversi elementi di una macchina virtuale Linux. Il [playbook Ansible completo](#complete-sample-ansible-playbook) è riportato alla fine di questo articolo.

## <a name="prerequisites"></a>Prerequisiti

- **Sottoscrizione di Azure**: se non si possiede una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)]

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Ansible richiede un gruppo di risorse, in cui devono essere distribuite le risorse. La sezione del playbook Ansible di esempio seguente crea un gruppo di risorse denominato `myResourceGroup` nella località `eastus`:

```yaml
- name: Create resource group
  azure_rm_resourcegroup:
    name: myResourceGroup
    location: eastus
```

## <a name="create-a-virtual-network"></a>Crea rete virtuale
Quando si crea una macchina virtuale di Azure, è necessario creare una [rete virtuale](/azure/virtual-network/virtual-networks-overview) o usarne una esistente. È anche necessario stabilire come si accederà alle macchine virtuali nella rete virtuale. La sezione del playbook Ansible di esempio seguente crea una rete virtuale denominata `myVnet` nello spazio degli indirizzi `10.0.0.0/16`:

```yaml
- name: Create virtual network
  azure_rm_virtualnetwork:
    resource_group: myResourceGroup
    name: myVnet
    address_prefixes: "10.0.0.0/16"
```

Tutte le risorse di Azure distribuite in una rete virtuale vengono distribuite in una [subnet](/azure/virtual-network/virtual-network-manage-subnet) all'interno di una macchina virtuale. 

La sezione del playbook Ansible di esempio seguente crea una subnet denominata `mySubnet` nella rete virtuale `myVnet`:

```yaml
- name: Add subnet
  azure_rm_subnet:
    resource_group: myResourceGroup
    name: mySubnet
    address_prefix: "10.0.1.0/24"
    virtual_network: myVnet
```

## <a name="create-a-public-ip-address"></a>Creare un indirizzo IP pubblico
Gli [indirizzi IP pubblici](/azure/virtual-network/virtual-network-ip-addresses-overview-arm) permettono la comunicazione in ingresso da risorse Internet a risorse di Azure, nonché la comunicazione in uscita dalle risorse di Azure a Internet e servizi pubblici di Azure con un indirizzo IP assegnato alla risorsa. L'indirizzo è dedicato alla risorsa finché non si annulla l'assegnazione. Se a una risorsa non è assegnato un indirizzo IP pubblico, la comunicazione in uscita dalla risorsa a Internet è comunque consentita, ma Azure assegna dinamicamente un indirizzo IP disponibile non dedicato alla risorsa. 

La sezione del playbook Ansible di esempio seguente crea un indirizzo IP pubblico denominato `myPublicIP`:

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```

## <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete
Un [gruppo di sicurezza di rete](/azure/virtual-network/security-overview) permette di filtrare il traffico di rete da e verso risorse di Azure in una rete virtuale di Azure. Un gruppo di sicurezza di rete contiene regole di sicurezza che consentono o negano il traffico di rete in ingresso o il traffico di rete in uscita rispettivamente verso o da diversi tipi di risorse di Azure. 

La sezione del playbook Ansible di esempio seguente crea un gruppo di sicurezza di rete denominato `myNetworkSecurityGroup` e definisce una regola per consentire il traffico SSH sulla porta TCP 22:

```yaml
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
```


## <a name="create-a-virtual-network-interface-card"></a>Creare una scheda di interfaccia di rete virtuale
Una scheda di interfaccia di rete virtuale connette la macchina virtuale a una rete virtuale, un indirizzo IP pubblico e un gruppo di sicurezza di rete specifici. 

La sezione del playbook Ansible di esempio seguente crea una scheda di interfaccia di rete virtuale denominata `myNIC` connessa alle risorse di rete virtuale create:

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

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale
Il passaggio finale consiste nella creazione di una macchina virtuale che usa tutte le risorse create nelle sezioni precedenti di questo articolo. 

La sezione del playbook Ansible di esempio presentata in questa sezione crea una macchina virtuale denominata `myVM` e collega la scheda di interfaccia di rete virtuale denominata `myNIC`. Sostituire il segnaposto &lt;your-key-data> con i dati completi della chiave pubblica.

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
        key_data: <your-key-data>
    network_interfaces: myNIC
    image:
      offer: CentOS
      publisher: OpenLogic
      sku: '7.5'
      version: latest
```

## <a name="complete-sample-ansible-playbook"></a>Playbook Ansible di esempio completo

In questa sezione è riportato l'intero playbook Ansible che è stato creato durante l'esercitazione presentata in questo articolo. 

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
      name: myResourceGroup
      location: eastus
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
    register: output_ip_address
  - name: Dump public IP for VM which will be created
    debug:
      msg: "The public IP is {{ output_ip_address.state.ip_address }}."
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
          key_data: <your-key-data>
      network_interfaces: myNIC
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.5'
        version: latest
```

## <a name="run-the-sample-ansible-playbook"></a>Eseguire il playbook Ansible di esempio

Questa sezione descrive in modo dettagliato l'esecuzione del playbook Ansible di esempio presentato in questo articolo.

1. Accedere al [portale di Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Aprire [Cloud Shell](/azure/cloud-shell/overview).

1. Creare un file (che conterrà il playbook) denominato `azure_create_complete_vm.yml` e aprirlo nell'editor VI, in questo modo:

  ```azurecli-interactive
  vi azure_create_complete_vm.yml
  ```

1. Attivare la modalità di inserimento con il tasto **I**.

1. Incollare il [playbook Ansible di esempio completo](#complete-sample-ansible-playbook) nell'editor.

1. Premere **ESC** per disattivare la modalità di inserimento.

1. Salvare il file e chiudere l'editor vi immettendo il comando seguente:

    ```bash
    :wq
    ```

1. Eseguire il playbook Ansible di esempio.

  ```bash
  ansible-playbook azure_create_complete_vm.yml
  ```

1. L'output è simile all'esempio seguente, che mostra che la macchina virtuale è stata creata correttamente:

  ```bash
  PLAY [Create Azure VM] ****************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Create resource group] *********************************************
  changed: [localhost]

  TASK [Create virtual network] *********************************************
  changed: [localhost]

  TASK [Add subnet] *********************************************************
  changed: [localhost]

  TASK [Create public IP address] *******************************************
  changed: [localhost]

  TASK [Dump public IP for VM which will be created] ********************************************************************
  ok: [localhost] => {
      "msg": "The public IP is <ip-address>."
  }

  TASK [Create Network Security Group that allows SSH] **********************
  changed: [localhost]

  TASK [Create virtual network inteface card] *******************************
  changed: [localhost]

  TASK [Create VM] **********************************************************
  changed: [localhost]

  PLAY RECAP ****************************************************************
  localhost                  : ok=8    changed=7    unreachable=0    failed=0
  ```

1. Il comando SSH viene usato per accedere alla macchina virtuale Linux. Sostituire il segnaposto &lt;ip-address> con l'indirizzo IP del passaggio precedente.

  ```bash
  ssh azureuser@<ip-address>
  ```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"] 
> [Usare Ansible per gestire una macchina virtuale Linux in Azure](./ansible-manage-linux-vm.md)
