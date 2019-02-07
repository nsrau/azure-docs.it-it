---
title: Creare e configurare i cluster del servizio Azure Kubernetes con Ansible
description: Informazioni su come usare Ansible per creare e gestire un cluster del servizio Azure Kubernetes in Azure
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, servizio Azure Kubernetes, contenitori, Kubernetes
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 08/23/2018
ms.openlocfilehash: df1efc1506fbbe51ba5afb03f147c51a57d9bbdb
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2019
ms.locfileid: "55727059"
---
# <a name="create-and-configure-azure-kubernetes-service-clusters-in-azure-using-ansible"></a>Creare e configurare i cluster del servizio Azure Kubernetes con Ansible
Ansible consente di automatizzare la distribuzione e la configurazione delle risorse nell'ambiente in uso. È possibile usare Ansible per gestire il servizio Azure Kubernetes. Questo articolo illustra come usare Ansible per creare e configurare un cluster del servizio Azure Kubernetes.

## <a name="prerequisites"></a>Prerequisiti
- **Sottoscrizione di Azure** - Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.
- **Entità servizio di Azure**: quando si [crea l'entità servizio](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal), prendere nota dei valori seguenti: **appId**, **displayName**, **password**  e **tenant**.

- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> In questa esercitazione, per eseguire i playbook di esempio seguenti è necessario Ansible 2.6.

## <a name="create-a-managed-aks-cluster"></a>Creare un cluster servizio Azure Kubernetes gestito
Il codice in questa sezione mostra un esempio di playbook Ansible per creare un gruppo di risorse e un cluster del servizio Azure Kubernetes che risiede nel gruppo di risorse.

> [!Tip]
> Per il segnaposto `your_ssh_key` immettere una chiave pubblica RSA nel formato a riga singola che inizi con "ssh-rsa" (senza virgolette).

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
- La seconda sezione all'interno delle **attività** definisce un cluster servizio Azure Kubernetes denominato **myservizio Azure KubernetesCluster** all'interno del gruppo di risorse **myResourceGroup**.

Per creare il cluster servizio Azure Kubernetes con Ansible, salvare il playbook di esempio precedente come `azure_create_aks.yml` ed eseguire il playbook con il comando seguente:

  ```bash
  ansible-playbook azure_create_aks.yml
  ```

L'output del comando **ansible-playbook* ha un aspetto simile a quello mostrato di seguito, che mostra che il cluster servizio Azure Kubernetes è stato creato correttamente:

  ```Output
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

## <a name="scale-aks-nodes"></a>Aumentare i nodi del servizio Azure Container

Il playbook di esempio nella sezione precedente definisce due nodi. Se occorre un numero maggiore o minore di carichi di lavoro dei contenitori nel cluster, è possibile modificare facilmente il numero di nodi. Il playbook di esempio in questa sezione consente di aumentare il numero di nodi da due a tre. La modifica del numero di nodi viene eseguita cambiando il valore **count** nel blocco **agent_pool_profiles**.

> [!Tip]
> Per il segnaposto `your_ssh_key` immettere una chiave pubblica RSA nel formato a riga singola che inizi con "ssh-rsa" (senza virgolette).

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

Per ridimensionare il cluster del servizio Azure Kubernetes con Ansible, salvare il playbook precedente come *azure_configure_servizio Azure Kubernetes.yml* ed eseguire il playbook come segue:

  ```bash
  ansible-playbook azure_configure_aks.yml
  ```

L'output riportato di seguito mostra che il cluster servizio Azure Kubernetes è stato creato correttamente:

  ```Output
  PLAY [Scale AKS cluster] ***************************************************************

  TASK [Gathering Facts] ******************************************************************
  ok: [localhost]

  TASK [Scaling an existed AKS cluster] **************************************************
  changed: [localhost]

  PLAY RECAP ******************************************************************************
  localhost                  : ok=2    changed=1    unreachable=0    failed=0
  ```
## <a name="delete-a-managed-aks-cluster"></a>Eliminare un cluster servizio Azure Kubernetes gestito

La sezione del playbook Ansible di esempio seguente illustra come eliminare un cluster servizio Azure Kubernetes:

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

Per eliminare il cluster del servizio Azure Kubernetes con Ansible, salvare il playbook precedente come *azure_delete_servizio Azure Kubernetes.yml* ed eseguire il playbook come indicato di seguito:

  ```bash
  ansible-playbook azure_delete_aks.yml
  ```

L'output riportato di seguito mostra che il cluster servizio Azure Kubernetes è stato eliminato correttamente:
  ```Output
PLAY [Delete a managed Azure Container Services (AKS) cluster] ****************************

TASK [Gathering Facts] ********************************************************************
ok: [localhost]

TASK [azure_rm_aks] *********************************************************************

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0
  ```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Esercitazione: Ridimensionare un'applicazione nel servizio Azure Kubernetes (AKS)](https://docs.microsoft.com/azure/aks/tutorial-kubernetes-scale)
