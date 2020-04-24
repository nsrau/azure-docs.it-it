---
title: Creare, modificare o eliminare una tabella di route di Azure
titlesuffix: Azure Virtual Network
description: Informazioni su come creare, modificare o eliminare una tabella di route.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: kumud
ms.openlocfilehash: 79310ddf121d6ada10755b198b515fdc9c1114d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247062"
---
# <a name="create-change-or-delete-a-route-table"></a>Creare, modificare o eliminare una tabella di route

Azure effettua il routing automatico del traffico tra subnet di Azure, reti virtuali e reti locali. Per modificare il routing predefinito di Azure è necessario creare una tabella di route. Se non si ha familiarità con il routing nelle reti virtuali, è possibile ottenere altre informazioni in [routing del traffico di rete virtuale](virtual-networks-udr-overview.md) o completando un' [esercitazione](tutorial-create-route-table-portal.md).

## <a name="before-you-begin"></a>Prima di iniziare

Se non si ha una sottoscrizione, configurare un account Azure con una sottoscrizione attiva. [Creare un account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Quindi completare una di queste attività prima di iniziare i passaggi in una sezione di questo articolo:

- **Utenti del portale**: accedere al [portale di Azure](https://portal.azure.com) con l'account Azure.

- **Utenti di PowerShell**: è possibile eseguire i comandi nel [Azure cloud Shell](https://shell.azure.com/powershell)o eseguire PowerShell dal computer. Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. Nella scheda Azure Cloud Shell browser trovare l'elenco a discesa **Seleziona ambiente** , quindi scegliere **PowerShell** se non è già selezionato.

    Se si esegue PowerShell localmente, usare Azure PowerShell modulo 1.0.0 o versione successiva. Eseguire `Get-Module -ListAvailable Az.Network` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Eseguire `Connect-AzAccount` anche per creare una connessione con Azure.

- **Utenti dell'interfaccia della riga di comando di Azure**: è possibile eseguire i comandi nel [Azure cloud Shell](https://shell.azure.com/bash)o eseguire l'interfaccia della riga di comando dal computer. Usare l'interfaccia della riga di comando di Azure versione 2.0.31 o successiva se si esegue l'interfaccia della riga di comando di Azure Eseguire `az --version` per trovare la versione installata. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). Eseguire `az login` anche per creare una connessione con Azure.

L'account a cui si accede o che si connette ad Azure deve essere assegnato al [ruolo Collaboratore rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o a un [ruolo personalizzato](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a cui sono assegnate le azioni appropriate elencate in [autorizzazioni](#permissions).

## <a name="create-a-route-table"></a>Creare una tabella di route

Esiste un limite al numero di tabelle di route che è possibile creare per ogni sottoscrizione e località di Azure. Per informazioni dettagliate, vedere [limiti di rete-Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Nel menu del [portale di Azure](https://portal.azure.com) o dalla pagina **Home** selezionare **Crea una risorsa**.

1. Nella casella di ricerca immettere *Tabella di route*. Selezionare **Tabella di route** quando viene visualizzata nei risultati della ricerca.

1. Nella pagina **Tabella di route** selezionare **Crea**.

1. Nella finestra di dialogo **Crea tabella di route** :

    1. Immettere un **nome** per la tabella di route.
    1. Scegliere la **sottoscrizione**.
    1. Scegliere un **gruppo di risorse** esistente oppure selezionare **Crea nuovo** per creare un nuovo gruppo di risorse.
    1. Scegliere una **località**.
    1. Se si prevede di associare la tabella di route a una subnet in una rete virtuale connessa alla rete locale tramite un gateway VPN e non si vuole propagare le route locali alle interfacce di rete nella subnet, impostare la **propagazione delle route del gateway di rete virtuale** su **disabilitato**.

1. Selezionare **Crea** per creare la nuova tabella di route.

### <a name="create-route-table---commands"></a>Creare una tabella di route-comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create) |
| PowerShell | [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) |

## <a name="view-route-tables"></a>Visualizzare tabelle di route

Passare al [portale di Azure](https://portal.azure.com) per gestire la rete virtuale. Cercare e selezionare **Tabelle di route**. Vengono elencate le tabelle di route presenti nella sottoscrizione.

### <a name="view-route-table---commands"></a>Visualizza tabella di route-comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [AZ Network Route-Table List](/cli/azure/network/route-table#az-network-route-table-list) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="view-details-of-a-route-table"></a>Visualizzare i dettagli di una tabella di route

1. Passare al [portale di Azure](https://portal.azure.com) per gestire la rete virtuale. Cercare e selezionare **Tabelle di route**.

1. Nell'elenco tabella di route scegliere la tabella di route per la quale si desidera visualizzare i dettagli.

1. Nella pagina della tabella di route, in **Impostazioni**, visualizzare le **Route** nella tabella di route o le **subnet** a cui è associata la tabella di route.

Per altre informazioni sulle impostazioni comuni di Azure, vedere le informazioni seguenti:

- [Log attività](../azure-monitor/platform/platform-logs-overview.md)
- [Controllo di accesso (IAM)](../role-based-access-control/overview.md)
- [Tag](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Locks](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Script di automazione](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>Visualizzare i dettagli dei comandi della tabella di route

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [AZ Network Route-Table Show](/cli/azure/network/route-table#az-network-route-table-show) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="change-a-route-table"></a>Modificare una tabella di route

1. Passare al [portale di Azure](https://portal.azure.com) per gestire la rete virtuale. Cercare e selezionare **Tabelle di route**.

1. Nell'elenco tabella di route scegliere la tabella di route che si desidera modificare.

Le modifiche più comuni sono l' [aggiunta](#create-a-route) di route, la [rimozione](#delete-a-route) di route, l'associazione delle tabelle di [Route alle subnet o l'](#dissociate-a-route-table-from-a-subnet) [associazione](#associate-a-route-table-to-a-subnet) delle tabelle di route alle subnet.

### <a name="change-a-route-table---commands"></a>Modificare una tabella di route-comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [AZ Network Route-Table Update](/cli/azure/network/route-table#az-network-route-table-update) |
| PowerShell | [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) |

## <a name="associate-a-route-table-to-a-subnet"></a>Associare una route a una subnet

Facoltativamente, è possibile associare una tabella di route a una subnet. Una tabella di route può essere associata a zero o più subnet. Poiché le tabelle di route non sono associate a reti virtuali, è necessario associare una tabella di route a ogni subnet a cui si vuole associare la tabella di route. Azure instrada tutto il traffico in uscita dalla subnet in base alle route create all'interno delle tabelle di route, delle [route predefinite](virtual-networks-udr-overview.md#default)e delle route propagate da una rete locale, se la rete virtuale è connessa a un gateway di rete virtuale di Azure (EXPRESSROUTE o VPN). È possibile associare solo una tabella di route alle subnet delle reti virtuali presenti nella stessa località e sottoscrizione di Azure della tabella di route.

1. Passare al [portale di Azure](https://portal.azure.com) per gestire la rete virtuale. Cercare e selezionare **Reti virtuali**.

1. Nell'elenco rete virtuale scegliere la rete virtuale che contiene la subnet a cui si vuole associare una tabella di route.

1. Nella barra dei menu della rete virtuale scegliere **subnet**.

1. Selezionare la subnet a cui si vuole associare la tabella di route.

1. In **tabella di route**scegliere la tabella di route che si desidera associare alla subnet.

1. Selezionare **Salva**.

Se la rete virtuale è connessa a un gateway VPN di Azure, non associare una tabella di route alla [subnet del gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) che include una route con una destinazione di *0.0.0.0/0*. Ciò potrebbe impedire il corretto funzionamento del gateway. Per ulteriori informazioni sull'utilizzo di *0.0.0.0/0* in una route, vedere [routing del traffico di rete virtuale](virtual-networks-udr-overview.md#default-route).

### <a name="associate-a-route-table---commands"></a>Associare una tabella di route-comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="dissociate-a-route-table-from-a-subnet"></a>Annullare l'associazione di una tabella di route da una subnet

Quando si annulla l'associazione di una tabella di route da una subnet, Azure instrada il traffico in base alle relative [route predefinite](virtual-networks-udr-overview.md#default).

1. Passare al [portale di Azure](https://portal.azure.com) per gestire la rete virtuale. Cercare e selezionare **Reti virtuali**.

1. Nell'elenco rete virtuale scegliere la rete virtuale che contiene la subnet da cui si vuole annullare l'associazione di una tabella di route.

1. Nella barra dei menu della rete virtuale scegliere **subnet**.

1. Selezionare la subnet da cui si vuole annullare l'associazione della tabella di route.

1. In **tabella di route**scegliere **nessuno**.

1. Selezionare **Salva**.

### <a name="dissociate-a-route-table---commands"></a>Annullare l'associazione di una tabella di route-comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-route-table"></a>Eliminare una tabella route

Non è possibile eliminare una tabella di route associata a una subnet. [Annullare l'associazione](#dissociate-a-route-table-from-a-subnet) di una tabella di route da tutte le subnet prima di tentare di eliminarla.

1. Passare alla [portale di Azure](https://portal.azure.com) per gestire le tabelle di route. Cercare e selezionare **Tabelle di route**.

1. Nell'elenco tabella di route scegliere la tabella di route che si desidera eliminare.

1. Selezionare **Elimina**e quindi fare clic su **Sì** nella finestra di dialogo di conferma.

### <a name="delete-a-route-table---commands"></a>Eliminare una tabella di route-comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [AZ Network Route-Table Delete](/cli/azure/network/route-table#az-network-route-table-delete) |
| PowerShell | [Remove-AzRouteTable](/powershell/module/az.network/remove-azroutetable) |

## <a name="create-a-route"></a>Creare una route

Esiste un limite al numero di route per tabella di route che è possibile creare per ogni sottoscrizione e località di Azure. Per informazioni dettagliate, vedere [limiti di rete-Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Passare alla [portale di Azure](https://portal.azure.com) per gestire le tabelle di route. Cercare e selezionare **Tabelle di route**.

1. Nell'elenco tabella di route scegliere la tabella di route a cui si vuole aggiungere una route.

1. Dalla barra dei menu della tabella di route scegliere **Route** > **Aggiungi**.

1. Immettere un **nome di route** univoco per la route all'interno della tabella di route.

1. Immettere il **prefisso dell'indirizzo**, nella notazione CIDR (Inter-Domain Routing) a cui si vuole indirizzare il traffico. Il prefisso non può essere duplicato in più di una route all'interno della tabella di route, sebbene il prefisso possa trovarsi all'interno di un altro prefisso. Se ad esempio è stato definito *10.0.0.0/16* come prefisso in una route, è comunque possibile definire un'altra route con il prefisso dell'indirizzo *10.0.0.0/22* . Azure seleziona una route per il traffico in base all'algoritmo LPM (Longest Prefix Match). Per altre informazioni, vedere [come Azure seleziona una route](virtual-networks-udr-overview.md#how-azure-selects-a-route).

1. Scegliere un **tipo di hop successivo**. Per altre informazioni sui tipi di hop successivi, vedere [routing del traffico di rete virtuale](virtual-networks-udr-overview.md).

1. Se si sceglie un **tipo di hop successivo** di **appliance virtuale**, immettere un indirizzo IP per l' **indirizzo hop successivo**.

1. Fare clic su **OK**.

### <a name="create-a-route---commands"></a>Creare una route-comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create) |
| PowerShell | [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig) |

## <a name="view-routes"></a>Visualizzare le route

Una tabella di route può contenere zero o più route. Per ulteriori informazioni sulle informazioni elencate quando si visualizzano le route, vedere [routing del traffico di rete virtuale](virtual-networks-udr-overview.md).

1. Passare alla [portale di Azure](https://portal.azure.com) per gestire le tabelle di route. Cercare e selezionare **Tabelle di route**.

1. Nell'elenco tabella di route scegliere la tabella di route per la quale si desidera visualizzare le route.

1. Nella barra dei menu della tabella di route scegliere **Route** per visualizzare l'elenco delle route.

### <a name="view-routes---commands"></a>Visualizza Route-comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [AZ Network Route-Table Route List](/cli/azure/network/route-table/route#az-network-route-table-route-list) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="view-details-of-a-route"></a>Visualizzare i dettagli di una route

1. Passare alla [portale di Azure](https://portal.azure.com) per gestire le tabelle di route. Cercare e selezionare **Tabelle di route**.

1. Nell'elenco tabella di route scegliere la tabella di route contenente la route per cui si desidera visualizzare i dettagli.

1. Nella barra dei menu della tabella di route scegliere **Route** per visualizzare l'elenco delle route.

1. Selezionare le route di cui si vogliono visualizzare i dettagli.

### <a name="view-details-of-a-route---commands"></a>Visualizzare i dettagli di una route-comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [AZ Network Route-Table route show](/cli/azure/network/route-table/route#az-network-route-table-route-show) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="change-a-route"></a>Modificare una route

1. Passare alla [portale di Azure](https://portal.azure.com) per gestire le tabelle di route. Cercare e selezionare **Tabelle di route**.

1. Nell'elenco tabella di route scegliere la tabella di route che contiene la route che si vuole modificare.

1. Nella barra dei menu della tabella di route scegliere **Route** per visualizzare l'elenco delle route.

1. Scegliere la route che si vuole modificare.

1. Modificare le impostazioni esistenti con le nuove impostazioni e quindi selezionare **Salva**.

### <a name="change-a-route---commands"></a>Modificare i comandi di una route

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [AZ Network Route-Table Route Update](/cli/azure/network/route-table/route#az-network-route-table-route-update) |
| PowerShell | [Set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig) |

## <a name="delete-a-route"></a>Eliminare una route

1. Passare alla [portale di Azure](https://portal.azure.com) per gestire le tabelle di route. Cercare e selezionare **Tabelle di route**.

1. Nell'elenco tabella di route scegliere la tabella di route che contiene la route che si vuole eliminare.

1. Nella barra dei menu della tabella di route scegliere **Route** per visualizzare l'elenco delle route.

1. Scegliere la route che si vuole eliminare.

1. Selezionare **Elimina**, quindi scegliere **Sì** nella finestra di dialogo di conferma.

### <a name="delete-a-route---commands"></a>Eliminare una route-comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [AZ Network Route-Table route delete](/cli/azure/network/route-table/route#az-network-route-table-route-delete) |
| PowerShell | [Remove-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig) |

## <a name="view-effective-routes"></a>Visualizzare le route valide

Le route valide per ogni interfaccia di rete collegata alla macchina virtuale sono costituite da una combinazione di tabelle di route create, route predefinite di Azure ed eventuali Route propagate da reti locali tramite il Border Gateway Protocol (BGP) tramite un gateway di rete virtuale di Azure. Conoscere le route valide per un'interfaccia di rete può essere utile durante la risoluzione dei problemi di routing. È possibile visualizzare le route valide per qualsiasi interfaccia di rete collegata a una macchina virtuale in esecuzione.

1. Passare alla [portale di Azure](https://portal.azure.com) per gestire le macchine virtuali. Cercare e selezionare **Macchine virtuali**.

1. Nell'elenco macchina virtuale scegliere la VM per la quale si vogliono visualizzare le route valide.

1. Nella barra dei menu della macchina virtuale scegliere **rete**.

1. Selezionare il nome di un'interfaccia di rete.

1. Nella barra dei menu dell'interfaccia di rete selezionare **Route effettive**.

1. Esaminare l'elenco delle route valide per verificare se esiste la route corretta per la posizione in cui si vuole instradare il traffico. Altre informazioni sui tipi di hop successivi visualizzati nell'elenco in routing del [traffico di rete virtuale](virtual-networks-udr-overview.md).

### <a name="view-effective-routes---commands"></a>Visualizza route valide-comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [AZ Network NIC Show-effective-Route-Table](/cli/azure/network/nic#az-network-nic-show-effective-route-table) |
| PowerShell | [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) |

## <a name="validate-routing-between-two-endpoints"></a>Convalidare il routing tra due endpoint

È possibile determinare il tipo di hop successivo tra una macchina virtuale e l'indirizzo IP di un'altra risorsa di Azure, una risorsa locale o una risorsa in Internet. Determinare il routing di Azure può essere utile durante la risoluzione dei problemi di routing. Per completare questa attività, è necessario disporre di un Network Watcher esistente. Se non si dispone di un Network Watcher esistente, crearne uno completando i passaggi descritti in [creare un'istanza di Network Watcher](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

1. Passare alla [portale di Azure](https://portal.azure.com) per gestire i controlli di rete. Cercare e selezionare **Network Watcher**.

1. Nella barra dei menu di Network Watcher scegliere **hop successivo**.

1. Nel **Network Watcher | Pagina hop successivo** :

    1. Scegliere la **sottoscrizione** e il **gruppo di risorse** della VM di origine da cui si vuole convalidare il routing.

    1. Scegliere la **macchina virtuale** e l' **interfaccia di rete** collegata alla VM.
    
    1. Immettere un **indirizzo IP di origine** assegnato all'interfaccia di rete da cui si vuole convalidare il routing.

    1. Immettere un **indirizzo IP di destinazione** a cui si desidera convalidare il routing.

1. Selezionare **Hop successivo**.

Dopo una breve attesa, Azure indica il tipo di hop successivo e l'ID della route che ha indirizzato il traffico. Altre informazioni sui tipi di hop successivi restituiti nel routing del [traffico della rete virtuale](virtual-networks-udr-overview.md).

### <a name="validate-routing-between-two-endpoints---commands"></a>Convalidare il routing tra due endpoint-comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [AZ Network Watcher Show-next-hop](/cli/azure/network/watcher#az-network-watcher-show-next-hop) |
| PowerShell | [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) |

## <a name="permissions"></a>Autorizzazioni

Per eseguire attività nelle tabelle e nelle route di route, l'account deve essere assegnato al [ruolo Collaboratore rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o a un [ruolo personalizzato](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a cui sono assegnate le azioni appropriate elencate nella tabella seguente:

| Azione                                                          |   Nome                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft.Network/routeTables/read                              |   Leggere una tabella di route                                    |
| Microsoft.Network/routeTables/write                             |   Creare o aggiornare una tabella di route                        |
| Microsoft.Network/routeTables/delete                            |   Eliminare una tabella route                                  |
| Microsoft.Network/routeTables/join/action                       |   Associare una route a una subnet                   |
| Microsoft.Network/routeTables/routes/read                       |   Leggere una route                                          |
| Microsoft.Network/routeTables/routes/write                      |   Creare o aggiornare una route                              |
| Microsoft.Network/routeTables/routes/delete                     |   Eliminare una route                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   Ottenere la tabella di route valida per un'interfaccia di rete |
| Microsoft.Network/networkWatchers/nextHop/action                |   Ottenere l'hop successivo da una macchina virtuale                           |

## <a name="next-steps"></a>Passaggi successivi

- Creare una tabella di route usando gli script di esempio di [PowerShell](powershell-samples.md) o dell'interfaccia della riga di comando di [azure](cli-samples.md) o Azure [Gestione risorse](template-samples.md)
- Creare e applicare i [criteri di Azure](policy-samples.md) per le reti virtuali
