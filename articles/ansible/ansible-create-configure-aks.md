---
title: Esercitazione - Configurare cluster del servizio Azure Kubernetes in Azure con Ansible
description: Informazioni su come usare Ansible per creare e gestire un cluster del servizio Azure Kubernetes in Azure
keywords: ansible, azure, devops, bash, cloudshell, playbook, servizio Azure Kubernetes, contenitore, servizio Azure Kubernetes, kubernetes
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 11/04/2019
ms.openlocfilehash: b0839cf418cd30f62623e046960c32d41537609a
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614387"
---
# <a name="tutorial-configure-azure-kubernetes-service-aks-clusters-in-azure-using-ansible"></a>Esercitazione: Configurare cluster del servizio Azure Kubernetes Service in Azure con Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

È possibile configurare il servizio Azure Kubernetes in modo da usare [Azure Active Directory (AD)](/azure/active-directory/) per l'autenticazione utente. Dopo la configurazione si usa il token di autenticazione di Azure AD per accedere al cluster del servizio Azure Kubernetes. Il ruolo Controllo degli accessi in base al ruolo può essere basato sull'identità di un utente o sull'appartenenza a gruppi di Active Directory.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Creare un cluster del servizio Azure Container
> * Configurare un cluster del servizio Azure Kubernetes

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-managed-aks-cluster"></a>Creare un cluster servizio Azure Kubernetes gestito

Il playbook di esempio consente di creare un gruppo di risorse e un cluster del servizio Azure Kubernetes nel gruppo di risorse.

Salvare il playbook seguente come `azure_create_aks.yml`:

```yml
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
    aks_version: aks_version
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
      kubernetes_version: "{{aks_version}}"
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

Prima di eseguire il playbook, vedere le note seguenti:

- La prima sezione in `tasks` consente di definire un gruppo di risorse denominato `myResourceGroup` nella località `eastus`.
- La seconda sezione in `tasks` consente di definire un cluster del servizio Azure Kubernetes risorse denominato `myAKSCluster` nel gruppo di risorse `myResourceGroup`.
- Per il segnaposto `your_ssh_key` immettere una chiave pubblica RSA nel formato a riga singola che inizi con "ssh-rsa" (senza virgolette).
- Per il segnaposto `aks_version`, usare il comando [az aks get-versions](/cli/azure/aks?view=azure-cli-latest#az-aks-get-versions).

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook azure_create_aks.yml
```

Quando si esegue il playbook, i risultati visualizzati sono simili all'output seguente:

```Output
PLAY [Create AKS] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create resource group] 
changed: [localhost]

TASK [Create an Azure Container Services (AKS) cluster] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="scale-aks-nodes"></a>Aumentare i nodi del servizio Azure Container

Il playbook di esempio nella sezione precedente definisce due nodi. Per adattare il numero di nodi, modificare il valore `count` nel blocco `agent_pool_profiles`.

Salvare il playbook seguente come `azure_configure_aks.yml`:

```yml
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

Prima di eseguire il playbook, vedere le note seguenti:

- Per il segnaposto `your_ssh_key` immettere una chiave pubblica RSA nel formato a riga singola che inizi con "ssh-rsa" (senza virgolette).

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook azure_configure_aks.yml
```

Quando si esegue il playbook, i risultati visualizzati sono simili all'output seguente:

```Output
PLAY [Scale AKS cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Scaling an existed AKS cluster] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="delete-a-managed-aks-cluster"></a>Eliminare un cluster servizio Azure Kubernetes gestito

Il playbook di esempio consente di eliminare un cluster del servizio Azure Kubernetes.

Salvare il playbook seguente come `azure_delete_aks.yml`:


```yml
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

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook azure_delete_aks.yml
```

Quando si esegue il playbook, i risultati visualizzati sono simili all'output seguente:

```Output
PLAY [Delete a managed Azure Container Services (AKS) cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [azure_rm_aks] 

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: Ridimensionare un'applicazione nel servizio Azure Kubernetes (AKS)](/azure/aks/tutorial-kubernetes-scale)