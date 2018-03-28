---
title: Creare, modificare o eliminare una tabella di route di Azure | Microsoft Docs
description: Informazioni su come creare, modificare o eliminare una tabella di route.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: jdial
ms.openlocfilehash: a7e45d6bccfd8113157eba63d311b6609bf35aaa
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="create-change-or-delete-a-route-table"></a>Creare, modificare o eliminare una tabella di route

Azure effettua il routing automatico del traffico tra subnet di Azure, reti virtuali e reti locali. Per modificare il routing predefinito di Azure è necessario creare una tabella di route. Se non si ha familiarità con il routing di Azure, è consigliabile leggere l'articolo [Panoramica del routing](virtual-networks-udr-overview.md) e completare l'esercitazione [Instradare il traffico di rete con una tabella di route](tutorial-create-route-table-portal.md) prima di completare le attività presenti in questo articolo.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di completare i passaggi di qualsiasi sezione di questo articolo, eseguire le attività seguenti:

- Se non si ha un account Azure, registrarsi per ottenere un [account per la versione di prova gratuita](https://azure.microsoft.com/free).
- Se si usa il portale, aprire https://portal.azure.com e accedere con l'account Azure.
- Se si usano i comandi di PowerShell per completare le attività in questo articolo, eseguire i comandi in [Azure Cloud Shell](https://shell.azure.com/powershell) o tramite PowerShell dal computer in uso. Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. Questa esercitazione richiede il modulo di Azure PowerShell 5.2.0 o versioni successive. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzureRmAccount` per creare una connessione con Azure.
- Se si usano i comandi dell'interfaccia della riga di comando di Azure per completare le attività in questo articolo, eseguire i comandi in [Azure Cloud Shell](https://shell.azure.com/bash) o tramite l'interfaccia della riga di comando dal computer in uso. Questa esercitazione richiede l'interfaccia della riga di comando di Azure versione 2.0.26 o successive. Eseguire `az --version` per trovare la versione installata. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli). Se si esegue l'interfaccia della riga di comando di Azure in locale, è anche necessario eseguire `az login` per creare una connessione con Azure.

## <a name="create-a-route-table"></a>Creare una tabella di route

È previsto un limite al numero di tabelle di route che è possibile creare per ogni sottoscrizione e località di Azure. Per informazioni dettagliate, vedere [Limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Nell'angolo superiore sinistro del portale selezionare **+ Crea una risorsa**.
2. Selezionare **Rete** e quindi **Tabella di route**.
3. Immettere un **Nome** per la tabella di route, selezionare **Sottoscrizione**, creare un nuovo **Gruppo di risorse** o selezionare un gruppo di risorse esistente, selezionare una **Località** e quindi scegliere **Crea**. L'opzione **Disabilita propagazione route BGP** impedisce la propagazione di route locali tramite BGP alle interfacce di rete nelle subnet a cui la tabella di route è associata. Se la rete virtuale non è connessa a un gateway di rete di Azure (VPN o ExpressRoute), lasciare l'opzione impostata su *Disabilitato*.

**Comandi**

- Interfaccia della riga di comando di Azure: [az network route-table create](/cli/azure/network/route-table/route#az_network_route_table_create)
- PowerShell: [New-AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable)

## <a name="view-route-tables"></a>Visualizzare tabelle di route

Nella casella di ricerca nella parte superiore del portale immettere *tabelle di route*. Selezionare **Tabelle di route** quando viene visualizzato nei risultati della ricerca. Vengono elencate le tabelle di route presenti nella sottoscrizione.

**Comandi**

- Interfaccia della riga di comando di Azure: [az network route-table list](/cli/azure/network/route-table/route#az_network_route_table_list)
- PowerShell: [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable)

## <a name="view-details-of-a-route-table"></a>Visualizzare i dettagli di una tabella di route

1. Nella casella di ricerca nella parte superiore del portale immettere *tabelle di route*. Selezionare **Tabelle di route** quando viene visualizzato nei risultati della ricerca.
2. Selezionare nell'elenco la tabella di route di cui si vuole visualizzare i dettagli. In **IMPOSTAZIONI** è possibile visualizzare le **Route** presenti nella tabella di route e le **Subnet** a cui è associata la tabella di route.
3. Per altre informazioni sulle impostazioni comuni di Azure, vedere le informazioni seguenti:
    *   [Log attività](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
    *   [Controllo di accesso (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
    *   [Tag](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)
    *   [Blocchi](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Script di automazione](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Comandi**

- Interfaccia della riga di comando di Azure: [az network route-table show](/cli/azure/network/route-table/route#az_network_route_table_show)
- PowerShell: [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable)

## <a name="change-a-route-table"></a>Modificare una tabella di route

1. Nella casella di ricerca nella parte superiore del portale immettere *tabelle di route*. Selezionare **Tabelle di route** quando viene visualizzato nei risultati della ricerca.
2. Selezionare la tabella di route che si vuole modificare. Le modifiche più comuni sono l'[aggiunta](#create-a-route) o la [rimozione](#delete-a-route) di route e l'[associazione](#associate-a-route-table-to-a-subnet) e la [dissociazione](#dissociate-a-route-table-from-a-subnet) di tabelle di route a e da subnet.

**Comandi**

- Interfaccia della riga di comando di Azure: [az network route-table update](/cli/azure/network/route-table/route#az_network_route_table_update)
- PowerShell: [Set-AzureRmRouteTable](/powershell/module/azurerm.network/set-azurermroutetable)

## <a name="associate-a-route-table-to-a-subnet"></a>Associare una route a una subnet

A una subnet può essere associata una o nessuna tabella di route. Una tabella di route può essere associata a nessuna o a più subnet. Poiché le tabelle di route non sono associate a reti virtuali, è necessario associare una tabella di route a ogni subnet a cui si vuole associare la tabella di route. Tutto il traffico in uscita dalla subnet viene instradato in base alle route create all'interno delle tabelle di route, alle [route predefinite](virtual-networks-udr-overview.md#default) e alle route propagate da una rete locale, se la rete virtuale è connessa a un gateway di rete virtuale di Azure (ExpressRoute o VPN se si usa il BGP con un gateway VPN). È possibile associare solo una tabella di route alle subnet delle reti virtuali presenti nella stessa località e sottoscrizione di Azure della tabella di route.

1. Nella casella di ricerca nella parte superiore del portale immettere *reti virtuali*. Selezionare **Reti virtuali** quando viene visualizzato nei risultati della ricerca.
2. Selezionare nell'elenco la rete virtuale contenente la subnet a cui si vuole associare una tabella di route.
3. Selezionare **Subnet** in **IMPOSTAZIONI**.
4. Selezionare la subnet a cui si vuole associare la tabella di route.
5. Selezionare **Tabella di route**, scegliere la tabella di route che si vuole associare alla subnet e quindi selezionare **Salva**.

**Comandi**

- Interfaccia della riga di comando di Azure: [az network vnet subnet update](/cli/azure/network/vnet/subnet?view=azure-cli-latest#az_network_vnet_subnet_update)
- PowerShell: [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)

## <a name="dissociate-a-route-table-from-a-subnet"></a>Annullare l'associazione di una tabella di route da una subnet

Quando si annulla l'associazione di una tabella di route da una subnet, Azure instrada il traffico in base alle relative [route predefinite](virtual-networks-udr-overview.md#default).

1. Nella casella di ricerca nella parte superiore del portale immettere *reti virtuali*. Selezionare **Reti virtuali** quando viene visualizzato nei risultati della ricerca.
2. Selezionare la rete virtuale contenente la subnet da cui si vuole disassociare una tabella di route.
3. Selezionare **Subnet** in **IMPOSTAZIONI**.
4. Selezionare la subnet da cui si vuole annullare l'associazione della tabella di route.
5. Selezionare **Tabella di route**, **Nessuno** e quindi **Salva**.

**Comandi**

- Interfaccia della riga di comando di Azure: [az network vnet subnet update](/cli/azure/network/vnet/subnet?view=azure-cli-latest#az_network_vnet_subnet_update)
- PowerShell: [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) 

## <a name="delete-a-route-table"></a>Eliminare una tabella route

Se una tabella di route è associata a una subnet, non può essere eliminata. [Annullare l'associazione](#dissociate-a-route-table-from-a-subnet) di una tabella di route da tutte le subnet prima di tentare di eliminarla.

1. Nella casella di ricerca nella parte superiore del portale immettere *tabelle di route*. Selezionare **Tabelle di route** quando viene visualizzato nei risultati della ricerca.
2. Selezionare **...**  sul lato destro della tabella di route che si vuole eliminare.
3. Selezionare **Elimina** e quindi scegliere **Sì**.

**Comandi**

- Interfaccia della riga di comando di Azure: [az network route-table delete](/cli/azure/network/route-table/route#az_network_route_table_delete)
- PowerShell: [Delete-AzureRmRouteTable](/powershell/module/azurerm.network/delete-azurermroutetable) 

## <a name="create-a-route"></a>Creare una route

È previsto un limite al numero di route per tabella di route che è possibile creare per ogni sottoscrizione e località di Azure. Per informazioni dettagliate, vedere [Limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Nella casella di ricerca nella parte superiore del portale immettere *tabelle di route*. Selezionare **Tabelle di route** quando viene visualizzato nei risultati della ricerca.
2. Selezionare nell'elenco la tabella di route a cui si vuole aggiungere una route.
3. Selezionare **Route** in **IMPOSTAZIONI**.
4. Selezionare **+ Aggiungi**.
5. Immettere un **Nome** univoco per la route all'interno della tabella di route.
6. Immettere il valore **Prefisso indirizzo**, sotto forma di notazione CIDR, a cui si vuole indirizzare il traffico. Il prefisso non può essere duplicato in più di una route all'interno della tabella di route, ma può essere contenuto in un altro prefisso. Se, ad esempio, è stato definito 10.0.0.0/16 come prefisso di una route, è possibile definire un'altra route con il prefisso di indirizzo 10.0.0.0/24. Azure seleziona una route per il traffico in base all'algoritmo LPM (Longest Prefix Match). Per altre informazioni su come vengono selezionate le route in Azure, vedere [Panoramica del routing](virtual-networks-udr-overview.md#how-azure-selects-a-route).
7. Selezionare una voce per **Tipo hop successivo**. Per una descrizione dettagliata di tutti i tipi di hop successivi, vedere [Panoramica del routing](virtual-networks-udr-overview.md).
8. Immettere un indirizzo IP per **Indirizzo hop successivo**. Se per *Indirizzo hop successivo* è stata selezionata la voce **Appliance virtuale**, è possibile immettere solo un indirizzo.
9. Selezionare **OK**. 

**Comandi**

- Interfaccia della riga di comando di Azure: [az network route-table route create](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_create)
- PowerShell: [New-AzureRmRouteConfig](/powershell/module/azurerm.network/new-azurermrouteconfig)

## <a name="view-routes"></a>Visualizzare le route

Una tabella di route può contenere nessuna o più route. Per saperne di più sulle informazioni elencate quando vengono visualizzate le route, vedere [Panoramica del routing](virtual-networks-udr-overview.md).

1. Nella casella di ricerca nella parte superiore del portale immettere *tabelle di route*. Selezionare **Tabelle di route** quando viene visualizzato nei risultati della ricerca.
2. Selezionare nell'elenco la tabella di route di cui si vogliono visualizzare le route.
3. Selezionare **Route** in **IMPOSTAZIONI**.

**Comandi**

- Interfaccia della riga di comando di Azure: [az network route-table route list](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_list)
- PowerShell: [Get-AzureRmRouteConfig](/powershell/module/azurerm.network/get-azurermrouteconfig)

## <a name="view-details-of-a-route"></a>Visualizzare i dettagli di una route

1. Nella casella di ricerca nella parte superiore del portale immettere *tabelle di route*. Selezionare **Tabelle di route** quando viene visualizzato nei risultati della ricerca.
2. Selezionare la tabella di route in cui è contenuta la route di cui si vogliono visualizzare i dettagli.
3. Selezionare **Route**.
4. Selezionare le route di cui si vogliono visualizzare i dettagli.

**Comandi**

- Interfaccia della riga di comando di Azure: [az network route-table route show](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_show)
- PowerShell: [Get-AzureRmRouteConfig](/powershell/module/azurerm.network/get-azurermrouteconfig)

## <a name="change-a-route"></a>Modificare una route

1. Nella casella di ricerca nella parte superiore del portale immettere *tabelle di route*. Selezionare **Tabelle di route** quando viene visualizzato nei risultati della ricerca.
2. Selezionare la tabella di route per cui si vuole modificare una route.
3. Selezionare **Route**.
4. Selezionare la route che si vuole modificare.
5. Modificare le impostazioni esistenti con le nuove impostazioni e quindi selezionare **Salva**.

**Comandi**

- Interfaccia della riga di comando di Azure: [az network route-table route update](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_update)
- PowerShell: [Set-AzureRmRouteConfig](/powershell/module/azurerm.network/set-azurermrouteconfig)

## <a name="delete-a-route"></a>Eliminare una route

1. Nella casella di ricerca nella parte superiore del portale immettere *tabelle di route*. Selezionare **Tabelle di route** quando viene visualizzato nei risultati della ricerca.
2. Selezionare la tabella di route di cui si vuole eliminare una route.
3. Selezionare **Route**.
4. Nell'elenco di route selezionare **...**  sul lato destro della route che si vuole eliminare.
5. Selezionare **Elimina** e quindi scegliere **Sì**.

**Comandi**

- Interfaccia della riga di comando di Azure: [az network route-table route delete](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_delete)
- PowerShell: [Remove-AzureRmRouteConfig](/powershell/module/azurerm.network/remove-azurermrouteconfig)

## <a name="view-effective-routes"></a>Visualizzare le route valide

Le route valide per ogni interfaccia di rete associata a una macchina virtuale sono costituite da una combinazione delle tabelle di route create, delle route predefinite di Azure e di eventuali route propagate da reti locali tramite BGP attraverso un gateway di rete virtuale di Azure. Conoscere le route valide per un'interfaccia di rete può essere utile durante la risoluzione dei problemi di routing. È possibile visualizzare le route valide per qualsiasi interfaccia di rete associata a una macchina virtuale in esecuzione.

1. Nella casella di ricerca nella parte superiore del portale immettere il nome di una macchina virtuale per la quale si vogliono visualizzare le route valide. Se non si conosce il nome della macchina virtuale, immettere *macchine virtuali* nella casella di ricerca. Quando **Macchine virtuali** viene visualizzato nei risultati della ricerca, selezionarlo e scegliere una macchina virtuale dall'elenco.
2. Selezionare **Rete** in **IMPOSTAZIONI**.
3. Selezionare il nome di un'interfaccia di rete.
4. Selezionare **Route valide** in **SUPPORTO + RISOLUZIONE DEI PROBLEMI**.
5. Esaminare l'elenco delle route valide per determinare se è presente la route corretta per la destinazione a cui si vuole indirizzare il traffico. Per altre informazioni sui tipi di hop successivi visualizzati nell'elenco, vedere [Panoramica del routing](virtual-networks-udr-overview.md).

**Comandi**

- Interfaccia della riga di comando di Azure: [az network nic show-effective-route-table](/cli/azure/network/nic?view=azure-cli-latest#az_network_nic_show_effective_route_table)
- PowerShell: [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/remove-azurermrouteconfig) 

## <a name="validate-routing-between-two-endpoints"></a>Convalidare il routing tra due endpoint

È possibile determinare il tipo di hop successivo tra una macchina virtuale e l'indirizzo IP di un'altra risorsa di Azure, una risorsa locale o una risorsa in Internet. Determinare il routing di Azure può essere utile durante la risoluzione dei problemi di routing. Per completare questa attività, è necessario disporre di un Network Watcher esistente. Se non è disponibile, crearne uno eseguendo la procedura descritta in [Creare un'istanza di Network Watcher](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

1. Nella casella di ricerca nella parte superiore del portale immettere *network watcher*. Selezionare **Network Watcher** quando viene visualizzato tra i risultati della ricerca.
2. Selezionare **Hop successivo** in **Strumenti di diagnostica di rete**.
3. Selezionare la **Sottoscrizione** e il **Gruppo di risorse** della macchina virtuale di origine di cui si vuole convalidare il routing.
4. Selezionare la **Macchina virtuale**, l'**Interfaccia di rete** associata alla macchina virtuale e l'**Indirizzo IP di origine** assegnato all'interfaccia di rete di cui si vuole convalidare il routing.
5. Immettere l'**Indirizzo IP di destinazione** di cui si vuole convalidare il routing.
6. Selezionare **Hop successivo**.
7. Dopo un breve periodo di attesa, vengono restituite alcune informazioni che indicano il tipo di hop successivo e l'ID della route che ha indirizzato il traffico. Per altre informazioni sui tipi di hop successivi restituiti, vedere [Panoramica del routing](virtual-networks-udr-overview.md).

**Comandi**

- Interfaccia della riga di comando di Azure: [az network watcher show-next-hop](/cli/azure/network/watcher?view=azure-cli-latest#az_network_watcher_show_next_hop)
- PowerShell: [Get-AzureRmNetworkWatcherNextHop](/powershell/module/azurerm.network/get-azurermnetworkwatchernexthop) 
 
## <a name="permissions"></a>Autorizzazioni

Per eseguire attività nelle route e nelle tabelle di route, l'account deve essere assegnato al ruolo [Collaboratore Rete](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o a un ruolo [personalizzato](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a cui sono assegnate le autorizzazioni appropriate elencate nella tabella seguente:

|Operazione                                                       |   Nome operazione                               |
|--------------------------------------------------------------  |   -------------------------------------------  |
|Microsoft.Network/routeTables/read                              |   Ottenere una tabella di route                              |
|Microsoft.Network/routeTables/write                             |   Creare o aggiornare una tabella di route                 |
|Microsoft.Network/routeTables/delete                            |   Eliminare una tabella di route                           |
|Microsoft.Network/routeTables/join/action                       |   Aggiungere una tabella di route                             |
|Microsoft.Network/routeTables/routes/read                       |   Ottenere una route                                    |
|Microsoft.Network/routeTables/routes/write                      |   Creare o aggiornare una route                       |
|Microsoft.Network/routeTables/routes/delete                     |   Eliminare una route                                 |
|Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   Ottenere una tabella di route valida per l'interfaccia di rete  | 
|Microsoft.Network/networkWatchers/nextHop/action                |   Ottenere l'hop successivo da una macchina virtuale                  |

Per associare una tabella di route a una subnet è necessario *aggiungere una tabella di route*.
