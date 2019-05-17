---
title: Esercitazione - Configurare funzionalità di rete di kubenet nel servizio Azure Kubernetes con Ansible | Microsoft Docs
description: Informazioni su come usare Ansible per configurare funzionalità di rete kubenet nel cluster del servizio Azure Kubernetes
keywords: ansible, azure, devops, bash, cloudshell, playbook, servizio Azure Kubernetes, contenitore, aks, kubernetes
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: cd32347f9de87ea6272be922d0359f1cc7f6f758
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231309"
---
# <a name="tutorial-configure-kubenet-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Esercitazione: Configurare funzionalità di rete di kubenet nel servizio Azure Kubernetes con Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Usando il servizio Azure Kubernetes è possibile distribuire un cluster che usa uno dei modelli di rete seguenti:

- [Funzionalità di rete kubenet](/azure/aks/configure-kubenet): le risorse di rete vengono in genere create e configurate quando viene distribuito il cluster del servizio Azure Kubernetes.
- [Funzionalità di rete Azure Container Networking Interface (CNI)](/azure/aks/configure-azure-cni): il cluster del servizio Azure Kubernetes viene connesso alle configurazioni e alle risorse di rete virtuale esistenti.

Per altre informazioni sulle funzionalità di rete per le applicazioni nel servizio Azure Kubernetes, vedere [Concetti relativi alla rete per le applicazioni nel servizio Azure Kubernetes](/azure/aks/concepts-network).

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Creare un cluster del servizio Azure Kubernetes
> * Configurare le funzionalità di rete kubenet di Azure

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-virtual-network-and-subnet"></a>Creare una rete virtuale e una subnet

Il codice del playbook in questa sezione consente di creare le risorse di Azure seguenti:

- Rete virtuale
- Subnet all'interno della rete virtuale

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

Il codice del playbook in questa sezione consente di creare un cluster del servizio Azure Kubernetes in una rete virtuale. 

Salvare il playbook seguente come `aks.yml`:

```yml
- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster with vnet
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
          network_plugin: kubenet
          pod_cidr: 192.168.0.0/16
          docker_bridge_cidr: 172.17.0.1/16
          dns_service_ip: 10.0.0.10
          service_cidr: 10.0.0.0/16
  register: aks
```

Quando si usa il playbook di esempio, è necessario tenere conto di alcuni concetti fondamentali:

- Usare il modulo `azure_rm_aks_version` per trovare la versione supportata.
- `vnet_subnet_id` è la subnet creata nella sezione precedente.
- `network_profile` definisce le proprietà del plug-in di rete kubenet.
- `service_cidr` consente di assegnare i servizi interni nel cluster del servizio Azure Kubernetes a un indirizzo IP. Questo intervallo di indirizzi IP deve essere uno spazio indirizzi non in uso in qualsiasi altra posizione nella rete. 
- L'indirizzo `dns_service_ip` deve essere l'indirizzo ".10" dell'intervallo di indirizzi IP del servizio.
- `pod_cidr` deve essere uno spazio indirizzi ampio non in uso in qualsiasi altra posizione nell'ambiente di rete. L'intervallo di indirizzi deve essere abbastanza ampio da contenere il numero di nodi in base a cui si prevede di aumentare le dimensioni. Non è possibile cambiare questo intervallo di indirizzi dopo la distribuzione del cluster.
- L'intervallo di indirizzi IP dei pod viene usato per assegnare uno spazio indirizzi /24 a ogni nodo del cluster. Nell'esempio seguente l'intervallo `pod_cidr` 192.168.0.0/16 assegna al primo nodo 192.168.0.0/24, al secondo nodo 192.168.1.0/24 e al terzo nodo 192.168.2.0/24.
- Quando il cluster viene ridimensionato o aggiornato, Azure continua ad assegnare un intervallo di indirizzi IP dei pod a ogni nuovo nodo.
- Il playbook carica `ssh_key` da `~/.ssh/id_rsa.pub`. Per modificare, usare il formato a riga singola che inizia con "ssh-rsa" (senza virgolette).
- I valori `client_id` e `client_secret` vengono caricati da `~/.azure/credentials`, cioè il file credenziali predefinito. È possibile impostare questi valori sull'entità servizio o caricarli dalle variabili di ambiente:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="associate-the-network-resources"></a>Associare le risorse di rete

Quando si crea un cluster del servizio Azure Kubernetes, vengono creati un gruppo di sicurezza e una tabella di route. Queste risorse vengono gestite dal servizio Azure Kubernetes e vengono aggiornate quando si creano e si espongono i servizi. Associare il gruppo di sicurezza di rete e la tabella di route alla subnet di rete virtuale nel modo seguente. 

Salvare il playbook seguente come `associate.yml`.

```yml
- name: Get route table
  azure_rm_routetable_facts:
      resource_group: "{{ node_resource_group }}"
  register: routetable

- name: Get network security group
  azure_rm_securitygroup_facts:
      resource_group: "{{ node_resource_group }}"
  register: nsg

- name: Parse subnet id
  set_fact:
      subnet_name: "{{ vnet_subnet_id | regex_search(subnet_regex, '\\1') }}"
      subnet_rg: "{{ vnet_subnet_id | regex_search(rg_regex, '\\1') }}"
      subnet_vn: "{{ vnet_subnet_id | regex_search(vn_regex, '\\1') }}"
  vars:
      subnet_regex: '/subnets/(.+)'
      rg_regex: '/resourceGroups/(.+?)/'
      vn_regex: '/virtualNetworks/(.+?)/'

- name: Associate network resources with the node subnet
  azure_rm_subnet:
      name: "{{ subnet_name[0] }}"
      resource_group: "{{  subnet_rg[0] }}"
      virtual_network_name: "{{ subnet_vn[0] }}"
      security_group: "{{ nsg.ansible_facts.azure_securitygroups[0].id }}"
      route_table: "{{ routetable.route_tables[0].id }}"
```

Quando si usa il playbook di esempio, è necessario tenere conto di alcuni concetti fondamentali:

- `node_resource_group` è il nome del gruppo di risorse in cui vengono creati i nodi del servizio Azure Kubernetes.
- `vnet_subnet_id` è la subnet creata nella sezione precedente.


## <a name="run-the-sample-playbook"></a>Eseguire il playbook di esempio

Questa sezione riporta il playbook di esempio completo che chiama le attività create in questo articolo. 

Salvare il playbook seguente come `aks-kubenet.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exist
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create vnet
       include_tasks: vnet.yml

     - name: Create AKS
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
       include_tasks: aks.yml

     - name: Associate network resources with the node subnet
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
           node_resource_group: "{{ aks.node_resource_group }}"
       include_tasks: associate.yml

     - name: Get details of the AKS
       azure_rm_aks_facts:
           name: "{{ name }}"
           resource_group: "{{ resource_group }}"
           show_kubeconfig: user
       register: output

     - name: Show AKS cluster detail
       debug:
           var: output.aks[0]
```

Nella sezione `vars` apportare le modifiche seguenti:

- Per la chiave `resource_group` impostare il valore `aksansibletest` sul nome del gruppo di risorse.
- Per la chiave `name` impostare il valore `aksansibletest` sul nome del servizio Azure Kubernetes.
- Per la chiave `Location` impostare il valore `eastus` sulla località del gruppo di risorse.

Per eseguire il playbook completo, usare il comando `ansible-playbook`:

```bash
ansible-playbook aks-kubenet.yml
```

Quando si esegue il playbook, i risultati visualizzati sono simili all'output seguente:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Ensure resource group exist] 
ok: [localhost]

TASK [Create vnet] 
included: /home/devops/aks-kubenet/vnet.yml for localhost

TASK [Create vnet] 
ok: [localhost]

TASK [Create subnet] 
ok: [localhost]

TASK [Create AKS] 
included: /home/devops/aks-kubenet/aks.yml for localhost

TASK [List supported kubernetes version from Azure] 
 [WARNING]: Azure API profile latest does not define an entry for
ContainerServiceClient

ok: [localhost]

TASK [Create AKS cluster with vnet] 
changed: [localhost]

TASK [Associate network resources with the node subnet] 
included: /home/devops/aks-kubenet/associate.yml for localhost

TASK [Get route table] 
ok: [localhost]

TASK [Get network security group] 
ok: [localhost]

TASK [Parse subnet id] 
ok: [localhost]

TASK [Associate network resources with the node subnet] 
changed: [localhost]

TASK [Get details of the AKS] 
ok: [localhost]

TASK [Show AKS cluster detail] 
ok: [localhost] => {
    "output.aks[0]": {
        "id": /subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourcegroups/aksansibletest/providers/Microsoft.ContainerService/managedClusters/aksansibletest",
        "kube_config": "apiVersion: ...",
        "location": "eastus",
        "name": "aksansibletest",
        "properties": {
            "agentPoolProfiles": [
                {
                    "count": 3,
                    "maxPods": 110,
                    "name": "nodepool1",
                    "osDiskSizeGB": 100,
                    "osType": "Linux",
                    "storageProfile": "ManagedDisks",
                    "vmSize": "Standard_D2_v2",
                    "vnetSubnetID": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourceGroups/aksansibletest/providers/Microsoft.Network/virtualNetworks/aksansibletest/subnets/aksansibletest"
                }
            ],
            "dnsPrefix": "aksansibletest",
            "enableRBAC": false,
            "fqdn": "aksansibletest-cda2b56c.hcp.eastus.azmk8s.io",
            "kubernetesVersion": "1.12.6",
            "linuxProfile": {
                "adminUsername": "azureuser",
                "ssh": {
                    "publicKeys": [
                        {
                            "keyData": "ssh-rsa ..."
                        }
                    ]
                }
            },
            "networkProfile": {
                "dnsServiceIP": "10.0.0.10",
                "dockerBridgeCidr": "172.17.0.1/16",
                "networkPlugin": "kubenet",
                "podCidr": "192.168.0.0/16",
                "serviceCidr": "10.0.0.0/16"
            },
            "nodeResourceGroup": "MC_aksansibletest_pcaksansibletest_eastus",
            "provisioningState": "Succeeded",
            "servicePrincipalProfile": {
                "clientId": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA"
            }
        },
        "type": "Microsoft.ContainerService/ManagedClusters"
    }
}

PLAY RECAP 
localhost                  : ok=15   changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eliminare le risorse create in questo articolo. 

Salvare il codice seguente come `cleanup.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
```

Nella sezione `vars` sostituire il segnaposto `{{ resource_group_name }}` con il nome del proprio gruppo di risorse.

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione - Configurare funzionalità di rete Azure Container Networking Interface (CNI) nel servizio Azure Kubernetes con Ansible](./ansible-aks-configure-cni-networking.md)