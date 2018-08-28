---
title: Creare e configurare i cluster di servizio Kubernetes di Azure con Ansible
description: Informazioni su come usare Ansible per creare e gestire un cluster di servizio Kubernetes di Azure in Azure
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, aks, contenitori, Kubernetes
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 08/21/2018
ms.openlocfilehash: de692b29902145e44a055680d662c16ed90c56c2
ms.sourcegitcommit: a62cbb539c056fe9fcd5108d0b63487bd149d5c3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2018
ms.locfileid: "42617176"
---
# <a name="create-and-configure-azure-kubernetes-service-clusters-in-azure-using-ansible"></a>Creare e configurare i cluster di servizio Kubernetes di Azure con Ansible
Ansible consente di automatizzare la distribuzione e la configurazione delle risorse nell'ambiente in uso. È possibile usare Ansible per gestire servizio Kubernetes di Azure (AKS). Questo articolo illustra come usare Ansible per creare e configurare un cluster di servizio Kubernetes di Azure.

## <a name="prerequisites"></a>Prerequisiti
- **Sottoscrizione di Azure** - Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.
- **Entità servizio di Azure**: quando si [crea l'entità servizio](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal), prendere nota dei valori seguenti: **appId**, **displayName**, **password**  e **tenant**.

- **Configurare Azure Cloud Shell** o **installare e configurare Ansible in una macchina virtuale Linux**

  **Configurare Azure Cloud Shell**

  1. **Configurare Azure Cloud Shell**: se non si ha familiarità con Azure Cloud Shell, l'articolo [Guida introduttiva a Bash in Azure Cloud Shell](/azure/cloud-shell/quickstart) descrive come avviare e configurare Cloud Shell. 

  **-- OPPURE --**

  **Installare e configurare Ansible in una macchina virtuale Linux**

  1. **Installare Ansible**: installare Ansible in una [piattaforma Linux supportata](/azure/virtual-machines/linux/ansible-install-configure#install-ansible-on-an-azure-linux-virtual-machine).

  1. **Configurare Ansible** - [Creare le credenziali di Azure e configurare Ansible](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

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
## <a name="delete-a-managed-aks-cluster"></a>Eliminare un cluster AKS gestito

La sezione del playbook Ansible di esempio seguente illustra come eliminare un cluster AKS:

  ```yaml
  - name: Delete a managed Azure Container Services (AKS) cluster
    hosts: localhost
    connection: local
    vars:
      resource_group: myResourceGroup
      aks_name: myAKSCluster
    tasks:
    - name: 
      azure_rm_aks:
        name: "{{ aks_name }}"
        resource_group: "{{ resource_group }}"
        state: absent
   ```

Per eliminare il cluster di servizio Kubernetes di Azure con Ansible, salvare il playbook precedente come *azure_delete_aks.yml* ed eseguire il playbook come indicato di seguito:

  ```bash
  ansible-playbook azure_delete_aks.yml
  ```

L'output riportato di seguito mostra che il cluster AKS è stato eliminato correttamente:
  ```bash
PLAY [Delete a managed Azure Container Services (AKS) cluster] ****************************

TASK [Gathering Facts] ********************************************************************
ok: [localhost]

TASK [azure_rm_aks] *********************************************************************

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0
  ```
  
## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"] 
> [Esercitazione: ridimensionare un'applicazione in Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/tutorial-kubernetes-scale)
