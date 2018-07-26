---
title: Creare e configurare i cluster di servizio Kubernetes di Azure con Ansible
description: Informazioni su come usare Ansible per creare e gestire un cluster di servizio Kubernetes di Azure in Azure
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, aks, contenitori, Kubernetes
author: tomarcher
manager: jpconnock
editor: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.date: 07/11/2018
ms.author: tarcher
ms.openlocfilehash: 6d7c5f961256e0ae1831bd76353cadd761f4b8ac
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011989"
---
# <a name="create-and-configure-azure-kubernetes-service-clusters-in-azure-using-ansible"></a>Creare e configurare i cluster di servizio Kubernetes di Azure con Ansible
Ansible consente di automatizzare la distribuzione e la configurazione delle risorse nell'ambiente in uso. È possibile usare Ansible per gestire servizio Kubernetes di Azure (AKS). Questo articolo illustra come usare Ansible per creare e configurare un cluster di servizio Kubernetes di Azure.

## <a name="prerequisites"></a>Prerequisiti
- **Sottoscrizione di Azure** - Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.
- **Configurare Ansible** - [Creare le credenziali di Azure e configurare Ansible](../virtual-machines/linux/ansible-install-configure.md#create-azure-credentials)
- **Ansible e i moduli dell'SDK Python di Azure** 
  - [CentOS 7.4](../virtual-machines/linux/ansible-install-configure.md#centos-74)
  - [Ubuntu 16.04 LTS](../virtual-machines/linux/ansible-install-configure.md#ubuntu-1604-lts)
  - [SLES 12 SP2](../virtual-machines/linux/ansible-install-configure.md#sles-12-sp2)
- **Entità servizio di Azure**: quando si [crea l'entità servizio](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal), prendere nota dei valori seguenti: **appId**, **displayName**, **password**  e **tenant**.

> [!Note]
> In questa esercitazione, per eseguire i playbook di esempio seguenti è necessario Ansible 2.6. 

## <a name="create-a-managed-aks-cluster"></a>Creare un cluster AKS gestito
L'esempio di playbook Ansible seguente crea un gruppo di risorse e un cluster AKS che risiede nel gruppo di risorse:

  ```yaml
  - name: Create Azure Kubernetes Service
    hosts: localhost
    connection: local
    vars:
      resource_group: myResourceGroup
      location: eastus
      aks_name: myAKSCluster
      username: azureuser
      ssh_key: "your_ssh_key"
      client_id: "your_client_id"
      client_secret: "your_client_secret"
    tasks:
    - name: Create resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    - name: Create a managed Azure Container Services (AKS) cluster
      azure_rm_aks:
        name: "{{ aks_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        dns_prefix: "{{ aks_name }}"
        linux_profile:
          admin_username: "{{ username }}"
          ssh_key: "{{ ssh_key }}"
        service_principal:
          client_id: "{{ client_id }}"
          client_secret: "{{ client_secret }}"
        agent_pool_profiles:
          - name: default
            count: 2
            vm_size: Standard_D2_v2
        tags:
          Environment: Production
  ```

L'elenco puntato riportato di seguito consente di spiegare il codice del playbook Ansible precedente:
- La prima sezione all'interno delle **attività** definisce un gruppo di risorse denominato **myResourceGroup** all'interno della posizione **eastus**. 
- La seconda sezione all'interno delle **attività** definisce un cluster AKS denominato **myAKSCluster** all'interno del gruppo di risorse **myResourceGroup**. 

Per creare il cluster AKS con Ansible, salvare il playbook di esempio precedente come `azure_create_aks.yml` ed eseguire il playbook con il comando seguente:

  ```bash
  ansible-playbook azure_create_aks.yml
  ```

L'output del comando **ansible-playbook* ha un aspetto simile a quello mostrato di seguito, che mostra che il cluster AKS è stato creato correttamente:

  ```bash
  PLAY [Create AKS] ****************************************************************************************

  TASK [Gathering Facts] ********************************************************************************************
  ok: [localhost]

  TASK [Create resource group] **************************************************************************************
  changed: [localhost]

  TASK [Create a Azure Container Services (AKS) cluster] ***************************************************
  changed: [localhost]

  PLAY RECAP *********************************************************************************************************
  localhost                  : ok=3    changed=2    unreachable=0    failed=0
  ```

## <a name="scale-aks-nodes"></a>Aumentare i nodi del servizio contenitore di Azure

Il playbook di esempio nella sezione precedente definisce due nodi. Se occorre un numero maggiore o minore di carichi di lavoro dei contenitori nel cluster, è possibile modificare facilmente il numero di nodi. Il playbook di esempio in questa sezione consente di aumentare il numero di nodi da due a tre. La modifica del numero di nodi viene eseguita cambiando il valore **count** nel blocco **agent_pool_profiles**. 

Immettere i propri `ssh_key`, `client_id` e `client_secret` nel blocco **service_principal**:

```yaml
- name: Scale AKS cluster
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    location: eastus
    aks_name: myAKSCluster
    username: azureuser
    ssh_key: "your_ssh_key"
    client_id: "your_client_id"
    client_secret: "your_client_secret"
  tasks:
  - name: Scaling an existed AKS cluster
    azure_rm_aks:
        name: "{{ aks_name }}"    
        location: "{{ location }}"
        resource_group: "{{ resource_group }}" 
        dns_prefix: "{{ aks_name }}" 
        linux_profile:
          admin_username: "{{ username }}"
          ssh_key: "{{ ssh_key }}"
        service_principal:
          client_id: "{{ client_id }}"
          client_secret: "{{ client_secret }}"
        agent_pool_profiles:
          - name: default
            count: 3
            vm_size: Standard_D2_v2
```

Per ridimensionare il cluster di servizio Kubernetes di Azure con Ansible, salvare il playbook precedente come *azure_configure_aks.yml* ed eseguire il playbook come segue:

  ```bash
  ansible-playbook azure_configure_aks.yml
  ```

L'output riportato di seguito mostra che il cluster AKS è stato creato correttamente:

  ```bash
  PLAY [Scale AKS cluster] ***************************************************************

  TASK [Gathering Facts] ******************************************************************
  ok: [localhost]

  TASK [Scaling an existed AKS cluster] **************************************************
  changed: [localhost]

  PLAY RECAP ******************************************************************************
  localhost                  : ok=2    changed=1    unreachable=0    failed=0
  ```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"] 
> [Esercitazione: ridimensionare un'applicazione in Azure Kubernetes Service (AKS)](https://docs.microsoft.com/en-us/azure/aks/tutorial-kubernetes-scale)