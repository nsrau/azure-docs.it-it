---
title: Esercitazione - Configurare tabelle di route di Azure con Ansible
description: Informazioni su come creare, modificare ed eliminare tabelle di route di Azure con Ansible
keywords: ansible, azure, devops, bash, playbook, rete, route, tabella di route
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 14753af58a179ddf4011cb29c7ed08faab62875c
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241773"
---
# <a name="tutorial-configure-azure-route-tables-using-ansible"></a>Esercitazione: Configurare tabelle di route di Azure tramite Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

Azure effettua il routing automatico del traffico tra subnet di Azure, reti virtuali e reti locali. Se è necessario un maggiore controllo sul routing dell'ambiente in uso, è possibile creare un [tabella di route](/azure/virtual-network/virtual-networks-udr-overview). 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> Crea una tabella di route Create una rete virtuale e una subnet Associare una tabella di route a una subnet Annullare l'associazione di una tabella di route da una subnet Creare ed eliminare route Eseguire query in una tabella di route Eliminare una tabella di route

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-route-table"></a>Creare una tabella di route

Il codice del playbook in questa sezione crea una tabella di route. Per informazioni sui limiti delle tabelle di route, vedere i [limiti di Azure](/azure/azure-subscription-service-limits#azure-resource-manager-virtual-networking-limits). 

Salvare il playbook seguente come `route_table_create.yml`:

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
```

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>Associare una route a una subnet

Il codice del playbook in questa sezione:

* Crea una rete virtuale
* Crea una subnet all'interno della rete virtuale
* Associa una tabella di route alla subnet

Le tabelle di route non vengono associate alle reti virtuali, bensì alla subnet di una rete virtuale.

La rete virtuale e la tabella di route devono coesistere nella stessa località e sottoscrizione di Azure.

Le subnet e le tabelle di route hanno una relazione uno-a-molti. Una subnet può essere definita senza alcuna tabella di route associata o con una sola tabella di route associata. Le tabelle di route possono essere associate a nessuna, a una o a più subnet. 

Il traffico dalla subnet viene instradato in base a:

- route definite all'interno delle tabelle di route
- [route predefinite](/azure/virtual-network/virtual-networks-udr-overview#default)
- route propagate da una rete locale

La rete virtuale deve essere connessa a un gateway di rete virtuale di Azure. Il gateway può essere ExpressRoute o VPN se si usa BGP con un gateway VPN.

Salvare il playbook seguente come `route_table_associate.yml`:

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create virtual network
      azure_rm_virtualnetwork:
        name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
        - 10.1.0.0/16
        - 172.100.0.0/16
        dns_servers:
        - 127.0.0.1
        - 127.0.0.3
    - name: Create a subnet with route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
        route_table: "{ route_table_name }"
```

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>Annullare l'associazione di una tabella di route da una subnet

Il codice del playbook in questa sezione annulla l'associazione di una tabella di route da una subnet.

Quando si annulla l'associazione di una tabella di route da una subnet, impostare `route_table` per la subnet su `None`. 

Salvare il playbook seguente come `route_table_dissociate.yml`:

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    resource_group: myResourceGroup
  tasks:
    - name: Dissociate a route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
```

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>Creare una route

Il codice del playbook in questa sezione crea una route all'interno di una tabella di route. 

Salvare il playbook seguente come `route_create.yml`:

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        next_hop_type: virtual_network_gateway
        address_prefix: "10.1.0.0/16"
        route_table_name: "{{ route_table_name }}"
```

Prima di eseguire il playbook, vedere le note seguenti:

* `virtual_network_gateway` viene definito come `next_hop_type`. Per altre informazioni su come vengono selezionate le route in Azure, vedere [Panoramica del routing](/azure/virtual-network/virtual-networks-udr-overview).
* `address_prefix` viene definito come `10.1.0.0/16`. Il prefisso non può essere duplicato all'interno della tabella di route.

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>Eliminare una route

Il codice del playbook in questa sezione consente di eliminare una route da una tabella di route.

Salvare il playbook seguente come `route_delete.yml`:

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Remove route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        route_table_name: "{{ route_table_name }}"
        state: absent
```

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook route_delete.yml
```

## <a name="get-route-table-information"></a>Ottenere informazioni sulla tabella di route

Il codice del playbook in questa sezione usa il modulo Ansible `azure_rm_routetable_facts` per recuperare informazioni sulla tabella di route.

Salvare il playbook seguente come `route_table_facts.yml`:

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks: 
    - name: Get route table information
      azure_rm_routetable_facts:
         resource_group: "{{ resource_group }}"
         name: "{{ route_table_name }}"
      register: query
    
    - debug:
         var: query.route_tables[0]
```

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>Eliminare una tabella route

Il codice del playbook in questa sezione elimina una tabella di route.

Quando viene eliminata una tabella di route, vengono eliminate anche tutte le route corrispondenti.

Una tabella di route non può essere eliminata se è associata a una subnet. [Annullare l'associazione della tabella di route da tutte le subnet](#dissociate-a-route-table-from-a-subnet) prima di tentare di eliminarla. 

Salvare il playbook seguente come `route_table_delete.yml`:

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
        state: absent
```

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"] 
> [Ansible in Azure](/azure/ansible/)