---
title: Esercitazione - Configurare il peering di reti virtuali di Azure con Ansible
description: Informazioni su come usare Ansible per connettere reti virtuali con il peering di rete virtuale.
keywords: ansible, azure, devops, bash, playbook, rete, peering
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 78699a005d721b46a88a26452f5db68438793d34
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155722"
---
# <a name="tutorial-configure-azure-virtual-network-peering-using-ansible"></a>Esercitazione: Configurare il peering di rete virtuale di Azure tramite Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

Il [peering di rete virtuale (VNet)](/azure/virtual-network/virtual-network-peering-overview) consente di connettere facilmente due reti virtuali di Azure. Dopo avere eseguito il peering, le due reti virtuali vengono visualizzate come una sola dal punto di vista della connettività. 

Il traffico viene instradato tra le macchine virtuali nella stessa rete virtuale tramite indirizzi IP privati. Analogamente, il traffico tra macchine virtuali in una rete virtuale con peering viene instradato attraverso l'infrastruttura backbone Microsoft. Di conseguenza, le macchine virtuali in reti virtuali diverse possono comunicare tra loro.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Creare due reti virtuali
> * Eseguire il peering delle due reti virtuali
> * Eliminare il peering tra le due reti

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-two-resource-groups"></a>Creare due gruppi di risorse

Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Il codice del playbook di esempio in questa sezione viene usato per:

- Creare due gruppi di risorse 

```yml
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create secondary resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group_secondary }}"
      location: "{{ location }}"
```

## <a name="create-the-first-virtual-network"></a>Creare la prima rete virtuale

Il codice del playbook di esempio in questa sezione viene usato per:

- Crea rete virtuale
- Creare una subnet all'interno della rete virtuale

```yml
  - name: Create first virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefix: "10.0.0.0/24"
      virtual_network: "{{ vnet_name1 }}"
```

## <a name="create-the-second-virtual-network"></a>Creare la seconda macchina virtuale

Il codice del playbook di esempio in questa sezione viene usato per:

- Crea rete virtuale
- Creare una subnet all'interno della rete virtuale

```yml
  - name: Ceate second virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ vnet_name2 }}"
      address_prefixes: "10.1.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name2 }}"
      address_prefix: "10.1.0.0/24"
      virtual_network: "{{ vnet_name2 }}"
```

## <a name="peer-the-two-virtual-networks"></a>Eseguire il peering delle due reti virtuali

Il codice del playbook di esempio in questa sezione viene usato per:

- Inizializzare il peering di rete virtuale
- Eseguire il peering delle due reti virtuali create in precedenza

```yml
  - name: Initial vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group_secondary }}"
        name: "{{ vnet_name2 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Connect vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name2 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group }}"
        name: "{{ vnet_name1 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true
```

## <a name="delete-the-virtual-network-peering"></a>Eliminare il peering di rete virtuale

Il codice del playbook di esempio in questa sezione viene usato per:

- Eliminare il peering tra le due reti virtuali create in precedenza

```yml
  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## <a name="get-the-sample-playbook"></a>Ottenere il playbook di esempio

È possibile ottenere il playbook di esempio completo in due modi:

- [Scaricare il playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vnet_peering.yml) e salvarlo in `vnet_peering.yml`.
- Creare un nuovo file denominato `vnet_peering.yml` e copiarvi il contenuto seguente:

```yml
- hosts: localhost
  tasks:
    - name: Prepare random postfix
      set_fact:
        rpfx: "{{ 1000 | random }}"
      run_once: yes

- name: Connect virtual networks with virtual network peering
  hosts: localhost
  connection: local
  vars:
    resource_group: "{{ resource_group_name }}"
    resource_group_secondary: "{{ resource_group_name }}2"
    vnet_name1: "myVnet{{ rpfx }}"
    vnet_name2: "myVnet{{ rpfx }}2"
    peering_name: peer1
    location: eastus2
  tasks:
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create secondary resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group_secondary }}"
      location: "{{ location }}"
  - name: Create first virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefix: "10.0.0.0/24"
      virtual_network: "{{ vnet_name1 }}"
  - name: Ceate second virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ vnet_name2 }}"
      address_prefixes: "10.1.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name2 }}"
      address_prefix: "10.1.0.0/24"
      virtual_network: "{{ vnet_name2 }}"
  - name: Initial vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group_secondary }}"
        name: "{{ vnet_name2 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Connect vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name2 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group }}"
        name: "{{ vnet_name1 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## <a name="run-the-sample-playbook"></a>Eseguire il playbook di esempio

Il codice del playbook di esempio in questa sezione viene usato per testare varie funzionalità illustrate in questa esercitazione.

Quando si usa il playbook di esempio è necessario tenere conto di alcuni aspetti fondamentali:

- Nella sezione `vars` sostituire il segnaposto `{{ resource_group_name }}` con il nome del proprio gruppo di risorse.

Eseguire il playbook usando il comando ansible-playbook:

```bash
ansible-playbook vnet_peering.yml
```

Dopo avere eseguito il playbook, viene visualizzato un output simile ai risultati seguenti:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Prepare random postfix] 
ok: [localhost]

PLAY [Connect virtual networks with virtual network peering] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create secondary resource group] 
changed: [localhost]

TASK [Create first virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Ceate second virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Initial vnet peering] 
changed: [localhost]

TASK [Connect vnet peering] 
changed: [localhost]

TASK [Delete vnet peering] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=12   changed=9    unreachable=0    failed=0    skipped=0   rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eliminare le risorse create in questo articolo. 

Il codice del playbook di esempio in questa sezione viene usato per:

- Eliminare i due gruppi di risorse creati in precedenza

Salvare il playbook seguente come `cleanup.yml`:

```bash
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name-1 }}"
    resource_group_secondary: "{{ resource_group_name-2 }}"
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent

    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group_secondary }}"
        force_delete_nonempty: yes
        state: absent
```

Quando si usa il playbook di esempio è necessario tenere conto di alcuni aspetti fondamentali:

- Sostituire il segnaposto `{{ resource_group_name-1 }}` con il nome del primo gruppo di risorse creato.
- Sostituire il segnaposto `{{ resource_group_name-2 }}` con il nome del secondo gruppo di risorse creato.
- Tutte le risorse nei due gruppi di risorse specificati verranno eliminate.

Eseguire il playbook usando il comando ansible-playbook:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"] 
> [Ansible in Azure](/azure/ansible/)