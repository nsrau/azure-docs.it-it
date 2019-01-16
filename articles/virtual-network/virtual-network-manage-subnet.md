---
title: Aggiungere, modificare o eliminare una subnet di rete virtuale di Azure
titlesuffix: Azure Virtual Network
description: Informazioni su come aggiungere, modificare o eliminare una subnet di rete virtuale in Azure.
services: virtual-network
documentationcenter: na
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: jdial
ms.openlocfilehash: f621bb562a963aa3c8b7296e4d75bbea5e9d47a2
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54052786"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Aggiungere, modificare o eliminare le subnet di rete virtuale

Informazioni su come aggiungere, modificare o eliminare una subnet di rete virtuale. Tutte le risorse di Azure distribuite in una rete virtuale vengono distribuite in una subnet entro una rete virtuale. Se non si ha familiarità con le reti virtuali, è possibile ottenere altre informazioni in [Panoramica della Rete virtuale](virtual-networks-overview.md) oppure completando un'[esercitazione](quick-create-portal.md). Per creare, modificare o eliminare una rete virtuale, vedere [Gestire una rete virtuale](manage-virtual-network.md).

## <a name="before-you-begin"></a>Prima di iniziare

Prima di completare i passaggi di qualsiasi sezione di questo articolo, eseguire le attività seguenti:

- Se non si ha un account Azure, registrarsi per ottenere un [account per la versione di prova gratuita](https://azure.microsoft.com/free).
- Se si usa il portale, aprire https://portal.azure.com e accedere con l'account Azure.
- Se si usano i comandi di PowerShell per completare le attività in questo articolo, eseguire i comandi in [Azure Cloud Shell](https://shell.azure.com/powershell) o tramite PowerShell dal computer in uso. Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. Questa esercitazione richiede il modulo Azure PowerShell 5.7.0 o versioni successive. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzureRmAccount` per creare una connessione con Azure.
- Se si usano i comandi dell'interfaccia della riga di comando di Azure per completare le attività in questo articolo, eseguire i comandi in [Azure Cloud Shell](https://shell.azure.com/bash) o tramite l'interfaccia della riga di comando dal computer in uso. Questa esercitazione richiede l'interfaccia della riga di comando di Azure 2.0.31 o versioni successive. Eseguire `az --version` per trovare la versione installata. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). Se si esegue l'interfaccia della riga di comando di Azure in locale, è anche necessario eseguire `az login` per creare una connessione con Azure.

L'account con cui si accede o con cui ci si collega ad Azure deve essere assegnato al ruolo [collaboratore di rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o a un [ruolo personalizzato](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a cui sono assegnate le operazioni appropriate elencate nelle [Autorizzazioni](#permissions).

## <a name="add-a-subnet"></a>Aggiungere una subnet

1. Nella casella di ricerca nella parte superiore del portale immettere *reti virtuali*. Selezionare **Reti virtuali** quando viene visualizzato nei risultati della ricerca.
2. Nell'elenco delle reti virtuali selezionare la rete virtuale a cui si vuole aggiungere una subnet.
3. In **Impostazioni** selezionare **Subnet**.
4. Selezionare **+Subnet**.
5. Immettere i valori per i parametri seguenti:
    - **Nome**: Il nome deve essere univoco all'interno della rete virtuale. Per la massima compatibilità con altri servizi di Azure, è consigliabile usare una lettera come primo carattere del nome. Il gateway applicazione di Azure, ad esempio, non verrà distribuito in una subnet con un nome che inizia con un numero.
    - **Intervallo di indirizzi**: L'intervallo deve essere univoco all'interno dello spazio di indirizzi della rete virtuale. L'intervallo non può sovrapporsi ad altri intervalli di indirizzi di subnet all'interno della rete virtuale. Lo spazio degli indirizzi deve essere specificato usando la notazione Classless Interdomain Routing (CIDR). Ad esempio, in una rete virtuale con lo spazio di indirizzi 10.0.0.0/16 è possibile definire lo spazio di indirizzi di subnet 10.0.0.0/24. L'intervallo più piccolo che è possibile specificare è /29, che fornisce otto indirizzi IP per subnet. Azure riserva il primo e l'ultimo indirizzo in ogni subnet per conformità al protocollo. Altri tre indirizzi sono riservati per l'uso da parte del servizio di Azure. Di conseguenza la definizione di una subnet con l'intervallo di indirizzi /29 genera tre indirizzi IP utilizzabili nella subnet. Se si prevede di connettere una rete virtuale a un gateway VPN, è necessario creare una subnet per il gateway. Altre informazioni su [considerazioni specifiche sugli intervalli di indirizzi per le subnet di gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Dopo aver aggiunto la subnet, in determinate condizioni è possibile modificare l'intervallo di indirizzi. Per informazioni su come modificare l'intervallo di indirizzi di una subnet, vedere [Cambiare le impostazioni della subnet](#change-subnet-settings).
    - **Network security group** (Gruppo di sicurezza di rete): è possibile associare zero o un gruppo di sicurezza di rete esistente alla subnet per filtrare il traffico di rete in ingresso e in uscita per la subnet. Il gruppo di sicurezza di rete deve esistere nello stesso percorso e nella stessa sottoscrizione della rete virtuale. Per altre informazioni, vedere [Gruppi di sicurezza di rete](security-overview.md) e [Creare gruppi di sicurezza di rete mediante il portale di Azure](tutorial-filter-network-traffic.md).
    - **Tabella di route**: facoltativamente, è possibile associare una tabella di route esistente a una subnet per controllare il routing del traffico di rete ad altre reti. La tabella di route deve esistere nello stesso percorso e nella stessa sottoscrizione della rete virtuale. Per altre informazioni, vedere [Routing del traffico di rete virtuale](virtual-networks-udr-overview.md) e [Come creare una tabella di route](tutorial-create-route-table-portal.md).
    - **Endpoint di servizio:** una subnet può avere zero o più endpoint di servizio abilitati. Per abilitare un endpoint di servizio per un servizio, selezionare i servizi per i quali si vogliono abilitare gli endpoint di servizio nell'elenco **Servizi**. La posizione viene configurata automaticamente per un endpoint. Per impostazione predefinita, gli endpoint di servizio vengono configurati per la regione della rete virtuale. Per Archiviazione di Azure, per supportare scenari di failover a livello di regione, gli endpoint vengono configurati automaticamente per [aree abbinate di Azure](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
    - **Delega di subnet**: una subnet può avere da zero a più deleghe abilitate. La delega di subnet offre esplicite autorizzazioni per creare le risorse specifiche del servizio nella subnet, tramite un identificatore univoco durante la distribuzione del servizio. Per la delega per un servizio, selezionare il servizio che si desidera delegare dall'elenco **Servizi**. 

    Per rimuovere un endpoint di servizio, deselezionare il servizio per cui si vuole rimuovere l'endpoint di servizio. Per altre informazioni sugli endpoint di servizio e sui servizi per cui possono essere abilitati, vedere [Panoramica sugli endpoint di servizio della rete virtuale](virtual-network-service-endpoints-overview.md). Dopo aver abilitato un endpoint di servizio per un servizio, è necessario abilitare anche l'accesso alla rete per la subnet per una risorsa creata con il servizio. Ad esempio, se si abilita l'endpoint di servizio per *Microsoft.Storage*, è necessario abilitare anche l'accesso alla rete per tutti gli account di Archiviazione di Azure a cui si vuole concedere l'accesso alla rete. Per informazioni dettagliate su come abilitare l'accesso alla rete a subnet con un endpoint di servizio abilitato, vedere la documentazione per il singolo servizio per cui è stato abilitato l'endpoint di servizio.

    Per controllare che un endpoint di servizio sia attivato per una subnet, visualizzare le [route valide](diagnose-network-routing-problem.md) per qualsiasi interfaccia di rete nella subnet. Quando un endpoint è configurato, verrà visualizzata una route *predefinita* con i prefissi degli indirizzi del servizio e un nextHopType di **VirtualNetworkServiceEndpoint**. Per altre informazioni sul routing, vedere [Panoramica sul routing](virtual-networks-udr-overview.md).
6. Per aggiungere la subnet alla rete virtuale selezionata, selezionare **OK**.

**Comandi**

- Interfaccia della riga di comando di Azure: [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)
- PowerShell: [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig)

## <a name="change-subnet-settings"></a>Cambiare le impostazioni della subnet

1. Nella casella di ricerca nella parte superiore del portale immettere *reti virtuali*. Selezionare **Reti virtuali** quando viene visualizzato nei risultati della ricerca.
2. Nell'elenco di reti virtuali selezionare la rete virtuale che contiene la subnet per cui si vogliono modificare le impostazioni.
3. In **Impostazioni** selezionare **Subnet**.
4. Nell'elenco di subnet selezionare la subnet per cui si vogliono modificare le impostazioni. È possibile modificare le impostazioni seguenti:

    - **Intervallo di indirizzi:** se nessuna risorsa viene distribuita all'interno della subnet, è possibile modificare l'intervallo di indirizzi. Se esistono risorse nella subnet, è necessario spostare le risorse in un'altra subnet o eliminarle prima di tutto dalla subnet. La procedura per spostare o eliminare una risorsa è diversa a seconda della risorsa. Per informazioni su come spostare o eliminare le risorse disponibili nelle subnet, leggere la documentazione relativa a ogni tipo di risorsa che si vuole spostare o eliminare. Vedere i vincoli per **Intervallo di indirizzi** nel passaggio 5 di [Aggiungere una subnet](#add-a-subnet).
    - **Utenti**: è possibile controllare l'accesso alla subnet tramite i ruoli predefiniti o i ruoli personalizzati. Per altre informazioni sull'assegnazione di ruoli e utenti per l'accesso alla subnet, leggere l'articolo [Usare l'assegnazione del ruolo per gestire l'accesso alle risorse di Azure](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-role-assignment).
    - **Gruppo di sicurezza di rete** e **Tabella di route**: Vedere il passaggio 5 di [Aggiungere una subnet](#add-a-subnet).
    - **Endpoint di servizio**: vedere gli endpoint di servizio nel passaggio 5 di [Aggiungere una subnet](#add-a-subnet). Quando si abilita un endpoint di servizio per una subnet esistente, assicurarsi che non vi siano attività critiche in esecuzione in nessuna risorsa nella subnet. Gli endpoint del servizio cambiano le route in ogni interfaccia di rete nella subnet dall'uso della route predefinita con il prefisso dell'indirizzo *0.0.0.0/0* e il tipo dell'hop successivo di *Internet*, all'uso di una nuova route con i prefissi dell'indirizzo del servizio e un tipo di hop successivo di *VirtualNetworkServiceEndpoint*. Durante il cambio, tutte le connessioni TCP aperte possono essere terminate. L'endpoint di servizio non è attivato fino a quando i flussi di traffico al servizio per tutte le interfacce di rete non vengono aggiornati con la nuova route. Per altre informazioni sul routing, vedere [Panoramica sul routing](virtual-networks-udr-overview.md).
    - **Delega di subnet**: vedere gli endpoint di servizio nel passaggio 5 di [Aggiungere una subnet](#add-a-subnet). Una delega di subnet può essere modificata per avere da zero a più deleghe abilitate. Se una risorsa per un servizio è già distribuita nella subnet, la delega di subnet non può essere rimossa fino a quando non vengono rimosse tutte le risorse per il servizio. Per la delega per un altro servizio, selezionare il servizio che si desidera delegare dall'elenco **Servizi**. 
5. Selezionare **Salva**.

**Comandi**

- Interfaccia della riga di comando di Azure: [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)
- PowerShell: [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)

## <a name="delete-a-subnet"></a>Eliminare una subnet

È possibile eliminare una subnet solo se non include risorse. Se sono presenti risorse nella subnet, è necessario eliminare le risorse disponibili nella subnet prima di poter eliminare la subnet. La procedura necessaria per l'eliminazione di una risorsa è diversa a seconda della risorsa. Per informazioni su come eliminare le risorse disponibili nelle subnet, leggere la documentazione relativa a ogni tipo di risorsa che si vuole eliminare.

1. Nella casella di ricerca nella parte superiore del portale immettere *reti virtuali*. Selezionare **Reti virtuali** quando viene visualizzato nei risultati della ricerca.
2. Nell'elenco di reti virtuali selezionare la rete virtuale che contiene la subnet che si vuole eliminare.
3. In **Impostazioni** selezionare **Subnet**.
4. Nell'elenco di subnet selezionare **...** a destra per la subnet da eliminare.
5. Selezionare **Elimina** e quindi scegliere **Sì**.

**Comandi**

- Interfaccia della riga di comando di Azure: [az network vnet delete](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete)
- PowerShell: [Remove-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/remove-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Autorizzazioni

Per eseguire attività nelle subnet, l'account deve essere assegnato al ruolo [collaboratore rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o a un ruolo [personalizzato](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a cui sono assegnate le azioni appropriate elencate nella tabella seguente:

|Azione                                                                   |   NOME                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Leggere una subnet della rete virtuale              |
|Microsoft.Network/virtualNetworks/subnets/write                          |   Creare o aggiornare una subnet della rete virtuale  |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   Eliminare una subnet della rete virtuale            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Aggiungere una rete virtuale                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   Abilitare un endpoint di servizio per una subnet     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   Ottenere le macchine virtuali in una subnet       |

## <a name="next-steps"></a>Passaggi successivi

- Creare una rete virtuale e le subnet usando gli script di esempio di [PowerShell](powershell-samples.md) o dell'[interfaccia della riga di comando di Azure](cli-samples.md) oppure i [modelli di Resource Manager](template-samples.md)
- Creare e applicare i [criteri di Azure](policy-samples.md) per le reti virtuali
