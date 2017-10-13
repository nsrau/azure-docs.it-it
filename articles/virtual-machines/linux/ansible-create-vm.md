---
title: Usare Ansible per creare una macchina virtuale Linux completa in Azure | Microsoft Docs
description: Informazioni su come usare Ansible per creare e gestire una macchina virtuale Linux di base in Azure
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
ms.openlocfilehash: 07cb786e3053cb933fb87b2991cebb979a91ace0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-basic-virtual-machine-in-azure-with-ansible"></a>Creare una macchina virtuale di base in Azure con Ansible
Ansible consente di automatizzare la distribuzione e la configurazione delle risorse nell'ambiente in uso. È possibile usare Ansible per gestire le macchine virtuali in Azure, così come si farebbe con qualsiasi altra risorsa. Questo articolo mostra come creare una macchina virtuale di base con Ansible. È anche possibile capire come [Creare un ambiente completo della macchina virtuale con Ansible](ansible-create-complete-vm.md).


## <a name="prerequisites"></a>Prerequisiti
Per gestire le risorse di Azure con Ansible, è necessario:

- avere Ansible e i moduli dell'SDK Python di Azure installati nel sistema host.
    - Installare Ansible su [Ubuntu 16.04 LTS](ansible-install-configure.md#ubuntu-1604-lts), [CentOS 7.3](ansible-install-configure.md#centos-73) e [SLES 12.2 SP2](ansible-install-configure.md#sles-122-sp2)
- configurare le credenziali di Azure e Ansible in modo che siano pronte all'uso.
    - [Creare le credenziali di Azure e configurare Ansible](ansible-install-configure.md#create-azure-credentials)
- Versione 2.0.4 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. 
    - Se è necessario eseguire l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). È anche possibile usare [Cloud Shell](/azure/cloud-shell/quickstart) dal browser.


## <a name="create-supporting-azure-resources"></a>Creare risorse di supporto di Azure
In questo esempio, viene creato un runbook che consente di distribuire una macchina virtuale in un'infrastruttura esistente. Creare prima un gruppo di risorse con [az group create](/cli/azure/vm#create). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Creare una rete virtuale per la macchina virtuale con [az network vnet create](/cli/azure/network/vnet#create). L'esempio seguente crea una rete virtuale denominata *myVnet* e una subnet denominata *mySubnet*:

```azurecli
az network vnet create \
  --resource-group myResourceGroup \
  --name myVnet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name mySubnet \
  --subnet-prefix 10.0.1.0/24
```


## <a name="create-and-run-ansible-playbook"></a>Creare ed eseguire il playbook Ansible
Creare un playbook Ansible denominato **azure_create_vm.yml** e incollarvi i contenuti seguenti. In questo esempio viene creata una singola macchina virtuale e vengono configurate le credenziali SSH. Immettere i propri dati della chiave pubblica nella coppia *key_data* come indicato di seguito:

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
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
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.3'
        version: latest
```

Per creare la macchina virtuale con Ansible, eseguire il playbook come segue:

```bash
ansible-playbook azure_create_vm.yml
```

L'output è simile all'esempio seguente che mostra che la macchina virtuale è stata creata correttamente:

```bash
PLAY [Create Azure VM] ****************************************************

TASK [Gathering Facts] ****************************************************
ok: [localhost]

TASK [Create VM] **********************************************************
changed: [localhost]

PLAY RECAP ****************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```


## <a name="next-steps"></a>Passaggi successivi
Questo esempio crea una macchina virtuale in un gruppo di risorse esistente e con una rete virtuale già distribuita. Per un esempio più dettagliato su come usare Ansible per creare le risorse di supporto, ad esempio le regole per una rete virtuale e per il gruppo di sicurezza di rete, vedere [Creare un ambiente completo per la macchina virtuale con Ansible](ansible-create-complete-vm.md).