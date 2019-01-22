---
title: Creare, modificare o eliminare una tabella di route di Azure tramite Ansible
description: Informazioni su come usare Ansible per creare, modificare o eliminare una tabella di route
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, rete, route, tabella di route
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/17/2018
ms.openlocfilehash: 29672a75408e42fb9239e5d826784b46e7280805
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2019
ms.locfileid: "54332269"
---
# <a name="create-change-or-delete-an-azure-route-table-using-ansible"></a>Creare, modificare o eliminare una tabella di route di Azure tramite Ansible
Azure effettua il routing automatico del traffico tra subnet di Azure, reti virtuali e reti locali. Per modificare il routing predefinito di Azure è necessario creare una [tabella di route](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

Ansible consente di automatizzare la distribuzione e la configurazione delle risorse nell'ambiente in uso. Questo articolo illustra come creare, modificare o eliminare una tabella di route di Azure e collegare la tabella di route a una subnet. 

## <a name="prerequisites"></a>Prerequisiti
- **Sottoscrizione di Azure** - Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> In questa esercitazione per eseguire i playbook di esempio seguenti è necessario Ansible 2.7.

## <a name="create-a-route-table"></a>Creare una tabella di route
In questa sezione viene presentato un playbook Ansible di esempio che crea una tabella di route. È previsto un limite al numero di tabelle di route che è possibile creare per ogni sottoscrizione e località di Azure. Per informazioni dettagliate, vedere [Limiti di Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). 

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

Salvare il playbook come `route_table_create.yml`. Per eseguire il playbook, usare il comando **ansible-playbook** come segue:

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>Associare una route a una subnet
A una subnet può essere associata una o nessuna tabella di route. Una tabella di route può essere associata a nessuna o a più subnet. Poiché le tabelle di route non sono associate a reti virtuali, è necessario associare una tabella di route a ogni subnet a cui si vuole associare la tabella di route. Tutto il traffico in uscita dalla subnet viene instradato in base alle route create all'interno delle tabelle di route, alle [route predefinite](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#default) e alle route propagate da una rete locale, se la rete virtuale è connessa a un gateway di rete virtuale di Azure (ExpressRoute o VPN se si usa il BGP con un gateway VPN). È possibile associare solo una tabella di route alle subnet delle reti virtuali presenti nella stessa località e sottoscrizione di Azure della tabella di route.

In questa sezione viene presentato un playbook Ansible di esempio che crea una rete virtuale e una subnet e quindi associa una tabella di route alla subnet.

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

Salvare il playbook come `route_table_associate.yml`. Per eseguire il playbook Ansible, usare il comando **ansible-playbook** come segue:

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>Annullare l'associazione di una tabella di route da una subnet
Quando si annulla l'associazione di una tabella di route da una subnet, è sufficiente impostare `route_table` in una subnet su `None`. Di seguito è riportato un playbook Ansible di esempio. 

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

Salvare il playbook come `route_table_dissociate.yml`. Per eseguire il playbook Ansible, usare il comando **ansible-playbook** come segue:

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>Creare una route
In questa sezione viene presentato un playbook Ansible di esempio che crea una route nella tabella di route. Il playbook definisce `virtual_network_gateway` come `next_hop_type` e `10.1.0.0/16` come `address_prefix`. Il prefisso non può essere duplicato in più di una route all'interno della tabella di route, ma può essere contenuto in un altro prefisso. Per altre informazioni sul modo in cui Azure seleziona le route e per una descrizione dettagliata di tutti i tipi di hop successivo, vedere [Panoramica del routing](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

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
Salvare il playbook come `route_create.yml`. Per eseguire il playbook Ansible, usare il comando **ansible-playbook** come segue:

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>Eliminare una route
In questa sezione viene presentato un playbook Ansible di esempio che elimina una route da una tabella di route.

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

Salvare il playbook come `route_delete.yml`. Per eseguire il playbook Ansible, usare il comando **ansible-playbook** come segue:

```bash
ansible-playbook route_delete.yml
```

## <a name="get-information-of-a-route-table"></a>Ottenere informazioni su una tabella di route
È possibile visualizzare i dettagli di una tabella di route tramite il modulo Ansible denominato `azure_rm_routetable_facts`. Tale modulo restituisce le informazioni sulla tabella di route con tutte le route associate.
Di seguito è riportato un playbook Ansible di esempio. 

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

Salvare il playbook come `route_table_facts.yml`. Per eseguire il playbook Ansible, usare il comando **ansible-playbook** come segue:

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>Eliminare una tabella route
Se una tabella di route è associata a una subnet, non può essere eliminata. [Annullare l'associazione](#dissociate-a-route-table-from-a-subnet) di una tabella di route da tutte le subnet prima di tentare di eliminarla.

È possibile eliminare la tabella di route insieme a tutte le route. Di seguito è riportato un playbook Ansible di esempio. 

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

Salvare il playbook come `route_table_delete.yml`. Per eseguire il playbook Ansible, usare il comando **ansible-playbook** come segue:

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"] 
> [Ansible in Azure](https://docs.microsoft.com/azure/ansible/)
