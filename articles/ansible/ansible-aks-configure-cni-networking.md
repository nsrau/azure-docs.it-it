---
title: Esercitazione - Configurare funzionalità di rete di Azure CNI nel servizio Azure Kubernetes con Ansible
description: Informazioni su come usare Ansible per configurare funzionalità di rete kubenet nel cluster del servizio Azure Kubernetes
keywords: ansible, azure, devops, bash, cloudshell, playbook, servizio Azure Kubernetes, contenitore, servizio Azure Container, kubernetes
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 04da0e8fb06d0a32c8e8bdc39d7722fc1c3fcdba
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242042"
---
# <a name="tutorial-configure-azure-cni-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Esercitazione: Configurare funzionalità di rete di Azure CNI nel servizio Azure Kubernetes usando Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Usando il servizio Azure Kubernetes è possibile distribuire un cluster che usa uno dei modelli di rete seguenti:

- [Funzionalità di rete kubenet](/azure/aks/configure-kubenet): le risorse di rete vengono in genere create e configurate quando viene distribuito il cluster del servizio Azure Kubernetes.
- [Funzionalità di rete di Azure CNI](/azure/aks/configure-azure-cni): il cluster servizio Azure Kubernetes viene connesso alle risorse di rete virtuale (VNET) e alle configurazioni esistenti.

Per altre informazioni sulle funzionalità di rete per l'applicazione nel servizio Azure Kubernetes, vedere [Concetti relativi alla rete per le applicazioni nel servizio Azure Kubernetes](/azure/aks/concepts-network).

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Creare un cluster del servizio Azure Container
> * Configurare le funzionalità di rete dell'interfaccia di rete dei contenitori di Azure

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 

## <a name="create-a-virtual-network-and-subnet"></a>Creare una rete virtuale e una subnet

Il codice di playbook di esempio in questa sezione viene usato per:

- Crea rete virtuale
- Creare una subnet all'interno della rete virtuale

Salvare il playbook seguente come `vnet.yml`:

```yml
- name: Create vnet
  azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefixes_cidr:
          - 10.0.0.0/8

- name: Create subnet
  azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefix_cidr: 10.240.0.0/16
      virtual_network_name: "{{ name }}"
  register: subnet
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Creare un cluster del servizio Azure Kubernetes nella rete virtuale

Il codice di playbook di esempio in questa sezione viene usato per:

- Creare un cluster del servizio Azure Kubernetes in una rete virtuale.

Salvare il playbook seguente come `aks.yml`:

```yml
- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster within a VNet
  azure_rm_aks:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      dns_prefix: "{{ name }}"
      kubernetes_version: "{{ versions.azure_aks_versions[-1] }}"
      agent_pool_profiles:
        - count: 3
          name: nodepool1
          vm_size: Standard_D2_v2
          vnet_subnet_id: "{{ vnet_subnet_id }}"
      linux_profile:
          admin_username: azureuser
          ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
      service_principal:
          client_id: "{{ lookup('ini', 'client_id section=default file=~/.azure/credentials') }}"
          client_secret: "{{ lookup('ini', 'secret section=default file=~/.azure/credentials') }}"
      network_profile:
          network_plugin: azure
          docker_bridge_cidr: 172.17.0.1/16
          dns_service_ip: 10.2.0.10
          service_cidr: 10.2.0.0/24
  register: aks
```

Quando si usa il playbook di esempio è necessario tenere conto di alcuni concetti fondamentali:

- Usare il modulo `azure_rm_aks_version` per trovare la versione supportata.
- `vnet_subnet_id` è la subnet creata nella sezione precedente.
- Il playbook carica `ssh_key` da `~/.ssh/id_rsa.pub`. Per modificare, usare il formato a riga singola che inizia con "ssh-rsa" (senza virgolette).
- I valori `client_id` e `client_secret` vengono caricati da `~/.azure/credentials`, cioè il file credenziali predefinito. È possibile impostare questi valori sull'entità servizio o caricarli dalle variabili di ambiente:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="run-the-sample-playbook"></a>Eseguire il playbook di esempio

Il codice del playbook di esempio in questa sezione viene usato per testare varie funzionalità illustrate in questa esercitazione.

Salvare il playbook seguente come `aks-azure-cni.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exists
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create vnet
       include_tasks: vnet.yml

     - name: Create AKS
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
       include_tasks: aks.yml

     - name: Show AKS cluster detail
       debug:
           var: aks
```

Quando si usa il playbook di esempio è necessario tenere conto di alcuni concetti fondamentali:

- Modificare il valore `aksansibletest` nel nome del gruppo di risorse.
- Modificare il valore `aksansibletest` nel nome del servizio Azure Kubernetes.
- Modificare il valore `eastus` nella località del gruppo di risorse.

Eseguire il playbook usando il comando ansible-playbook:

```bash
ansible-playbook aks-azure-cni.yml
```

Dopo avere eseguito il playbook, viene visualizzato un output simile ai risultati seguenti:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Ensure resource group exists] 
changed: [localhost]

TASK [Create vnet] 
included: /home/devops/aks-cni/vnet.yml for localhost

TASK [Create vnet] 
changed: [localhost]

TASK [Create subnet] 
changed: [localhost]

TASK [Create AKS] 
included: /home/devops/aks-cni/aks.yml for localhost

TASK [List supported kubernetes version from Azure] 
 [WARNING]: Azure API profile latest does not define an entry for
ContainerServiceClient

ok: [localhost]

TASK [Create AKS cluster with vnet] 
changed: [localhost]

TASK [Show AKS cluster detail] 
ok: [localhost] => {
    "aks": {
        "aad_profile": {},
        "addon": {},
        "agent_pool_profiles": [
            {
                "count": 3,
                "name": "nodepool1",
                "os_disk_size_gb": 100,
                "os_type": "Linux",
                "storage_profile": "ManagedDisks",
                "vm_size": "Standard_D2_v2",
                "vnet_subnet_id": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourceGroups/aksansibletest/providers/Microsoft.Network/virtualNetworks/aksansibletest/subnets/aksansibletest"
            }
        ],
        "changed": true,
        "dns_prefix": "aksansibletest",
        "enable_rbac": false,
        "failed": false,
        "fqdn": "aksansibletest-0272707d.hcp.eastus.azmk8s.io",
        "id": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourcegroups/aksansibletest/providers/Microsoft.ContainerService/managedClusters/aksansibletest",
        "kube_config": "..."
        },
        "location": "eastus",
        "name": "aksansibletest",
        "network_profile": {
            "dns_service_ip": "10.2.0.10",
            "docker_bridge_cidr": "172.17.0.1/16",
            "network_plugin": "azure",
            "network_policy": null,
            "pod_cidr": null,
            "service_cidr": "10.2.0.0/24"
        },
        "node_resource_group": "MC_aksansibletest_aksansibletest_eastus",
        "provisioning_state": "Succeeded",
        "service_principal_profile": {
            "client_id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA"
        },
        "tags": null,
        "type": "Microsoft.ContainerService/ManagedClusters",
        "warnings": [
            "Azure API profile latest does not define an entry for ContainerServiceClient",
            "Azure API profile latest does not define an entry for ContainerServiceClient"
        ]
    }
}

PLAY RECAP 
localhost                  : ok=9    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eliminare le risorse create in questo articolo. 

Il codice di playbook di esempio in questa sezione viene usato per:

- Eliminare un gruppo di risorse a cui si è fatto riferimento nella sezione `vars`.

Salvare il playbook seguente come `cleanup.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: {{ resource_group_name }}
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
```

Quando si usa il playbook di esempio è necessario tenere conto di alcuni concetti fondamentali:

- Sostituire il segnaposto `{{ resource_group_name }}` con il nome del proprio gruppo di risorse.
- Tutte le risorse nel gruppo di risorse specificato verranno eliminate.

Eseguire il playbook usando il comando ansible-playbook:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: Configurare Azure Active Directory nel servizio Azure Kubernetes usando Ansible](./ansible-aks-configure-rbac.md)