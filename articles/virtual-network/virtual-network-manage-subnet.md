---
title: Aggiungere, modificare o eliminare una subnet di rete virtuale di Azure
titlesuffix: Azure Virtual Network
description: Informazioni su come aggiungere, modificare o eliminare una subnet di rete virtuale in Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2020
ms.author: kumud
ms.openlocfilehash: b43fb027116d746a60c9cd4e690e63181fff4ade
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84711018"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Aggiungere, modificare o eliminare le subnet di rete virtuale

Informazioni su come aggiungere, modificare o eliminare una subnet di rete virtuale. Tutte le risorse di Azure distribuite in una rete virtuale vengono distribuite in una subnet entro una rete virtuale. Se non si ha familiarità con le reti virtuali, è possibile saperne di più su di essi nella [Panoramica della rete virtuale](virtual-networks-overview.md) o completando una [Guida introduttiva](quick-create-portal.md). Per altre informazioni sulla gestione di una rete virtuale, vedere [creare, modificare o eliminare una rete virtuale](manage-virtual-network.md).

## <a name="before-you-begin"></a>Prima di iniziare

Se non si ha una sottoscrizione, configurare un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Quindi completare una di queste attività prima di iniziare i passaggi in una sezione di questo articolo: 

- **Utenti del portale**: accedere al [portale di Azure](https://portal.azure.com) con l'account Azure.

- **Utenti di PowerShell**: è possibile eseguire i comandi nel [Azure cloud Shell](https://shell.azure.com/powershell)o eseguire PowerShell dal computer. Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. Nella scheda Azure Cloud Shell browser trovare l'elenco a discesa **Seleziona ambiente** , quindi scegliere **PowerShell** se non è già selezionato.

    Se si esegue PowerShell localmente, usare Azure PowerShell modulo 1.0.0 o versione successiva. Eseguire `Get-Module -ListAvailable Az.Network` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Eseguire anche `Connect-AzAccount` per creare una connessione con Azure.

- **Utenti dell'interfaccia della riga di comando di Azure**: è possibile eseguire i comandi nel [Azure cloud Shell](https://shell.azure.com/bash)o eseguire l'interfaccia della riga di comando dal computer. Usare l'interfaccia della riga di comando di Azure versione 2.0.31 o successiva se si esegue l'interfaccia della riga di comando di Azure Eseguire `az --version` per trovare la versione installata. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). Eseguire anche `az login` per creare una connessione con Azure.

L'account a cui si accede o che si connette ad Azure deve essere assegnato al ruolo ruolo [collaboratore rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o a un [ruolo personalizzato](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a cui sono assegnate le azioni appropriate elencate in [autorizzazioni](#permissions).

## <a name="add-a-subnet"></a>Aggiungere una subnet

1. Passare alla [portale di Azure](https://portal.azure.com) per visualizzare le reti virtuali. Cercare e selezionare **Reti virtuali**.

2. Selezionare il nome della rete virtuale a cui si vuole aggiungere una subnet.

3. In **Impostazioni** **selezionare Subnet Subnet**  >  **Subnet**.

4. Nella finestra di dialogo **Aggiungi subnet** immettere i valori per le impostazioni seguenti:

    | Impostazione | Descrizione |
    | --- | --- |
    | **Nome** | Il nome deve essere univoco all'interno della rete virtuale. Per la massima compatibilità con altri servizi di Azure, è consigliabile usare una lettera come primo carattere del nome. Il gateway applicazione di Azure, ad esempio, non verrà distribuito in una subnet con un nome che inizia con un numero. |
    | **Intervallo di indirizzi** | <p>L'intervallo deve essere univoco all'interno dello spazio di indirizzi della rete virtuale. L'intervallo non può sovrapporsi ad altri intervalli di indirizzi della subnet all'interno della rete virtuale. Lo spazio degli indirizzi deve essere specificato usando la notazione Classless Interdomain Routing (CIDR).</p><p>Ad esempio, in una rete virtuale con spazio di indirizzi *10.0.0.0/16*, è possibile definire uno spazio di indirizzi della subnet di *10.0.0.0/22*. L'intervallo più piccolo che è possibile specificare è */29*, che fornisce otto indirizzi IP per la subnet. Azure riserva il primo e l'ultimo indirizzo in ogni subnet per conformità al protocollo. Altri tre indirizzi sono riservati per l'uso da parte del servizio di Azure. Di conseguenza, la definizione di una subnet con un intervallo di indirizzi */29* comporta tre indirizzi IP utilizzabili nella subnet.</p><p>Se si prevede di connettere una rete virtuale a un gateway VPN, è necessario creare una subnet per il gateway. Altre informazioni su [considerazioni specifiche sugli intervalli di indirizzi per le subnet di gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Dopo aver aggiunto la subnet, in determinate condizioni è possibile modificare l'intervallo di indirizzi. Per informazioni su come modificare l'intervallo di indirizzi di una subnet, vedere [Cambiare le impostazioni della subnet](#change-subnet-settings).</p> |
    | **Gruppo di sicurezza di rete** | Per filtrare il traffico di rete in ingresso e in uscita per la subnet, è possibile associare un gruppo di sicurezza di rete esistente a una subnet. Il gruppo di sicurezza di rete deve esistere nello stesso percorso e nella stessa sottoscrizione della rete virtuale. Per altre informazioni, vedere [Gruppi di sicurezza di rete](security-overview.md) e [Creare gruppi di sicurezza di rete mediante il portale di Azure](tutorial-filter-network-traffic.md). |
    | **Tabella di route** | Per controllare il routing del traffico di rete ad altre reti, è possibile associare facoltativamente una tabella di route esistente a una subnet. La tabella di route deve esistere nello stesso percorso e nella stessa sottoscrizione della rete virtuale. Altre informazioni sul [routing di Azure](virtual-networks-udr-overview.md) e su [come creare una tabella di route](tutorial-create-route-table-portal.md). |
    | **Endpoint servizio** | <p>Una subnet può facoltativamente avere uno o più endpoint servizio abilitati. Per abilitare un endpoint di servizio per un servizio, selezionare i servizi per i quali si vogliono abilitare gli endpoint di servizio nell'elenco **Servizi**. Azure configura automaticamente il percorso per un endpoint. Per impostazione predefinita, Azure configura gli endpoint di servizio per l'area della rete virtuale. Per supportare gli scenari di failover a livello di area, Azure configura automaticamente gli endpoint in [aree abbinate di Azure](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions) per archiviazione di Azure.</p><p>Per rimuovere un endpoint di servizio, deselezionare il servizio per cui si vuole rimuovere l'endpoint di servizio. Per ulteriori informazioni sugli endpoint di servizio e sui servizi per i quali è possibile abilitare, vedere [endpoint del servizio di rete virtuale](virtual-network-service-endpoints-overview.md). Dopo aver abilitato un endpoint di servizio per un servizio, è necessario abilitare anche l'accesso alla rete per la subnet per una risorsa creata con il servizio. Ad esempio, se si abilita l'endpoint di servizio per **Microsoft.Storage**, è necessario abilitare anche l'accesso alla rete per tutti gli account di Archiviazione di Azure a cui si vuole concedere l'accesso alla rete. Per abilitare l'accesso di rete alle subnet per cui è abilitato un endpoint di servizio, vedere la documentazione per il singolo servizio per cui è stato abilitato l'endpoint del servizio.</p><p>Per controllare che un endpoint di servizio sia attivato per una subnet, visualizzare le [route valide](diagnose-network-routing-problem.md) per qualsiasi interfaccia di rete nella subnet. Quando si configura un endpoint, viene visualizzata una route *predefinita* con i prefissi degli indirizzi del servizio e un tipo di hop successivo **VirtualNetworkServiceEndpoint**. Per altre informazioni sul routing, vedere [routing del traffico di rete virtuale](virtual-networks-udr-overview.md).</p> |
    | **Delega subnet** | Una subnet può facoltativamente disporre di una o più delega abilitata. La delega della subnet fornisce le autorizzazioni esplicite al servizio per creare risorse specifiche del servizio nella subnet usando un identificatore univoco durante la distribuzione del servizio. Per la delega per un servizio, selezionare il servizio che si desidera delegare dall'elenco **Servizi**. |

5. Per aggiungere la subnet alla rete virtuale selezionata, selezionare **OK**.

### <a name="commands"></a>Comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) |
| PowerShell | [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) |

## <a name="change-subnet-settings"></a>Cambiare le impostazioni della subnet

1. Passare alla [portale di Azure](https://portal.azure.com) per visualizzare le reti virtuali. Cercare e selezionare **Reti virtuali**.

2. Selezionare il nome della rete virtuale che contiene la subnet che si desidera modificare.

3. In **Impostazioni**selezionare **Subnet**.

4. Nell'elenco di subnet selezionare la subnet per cui si vogliono modificare le impostazioni.

5. Nella pagina subnet modificare le impostazioni seguenti:

    | Impostazione | Descrizione |
    | --- | --- |
    | **Intervallo di indirizzi** | se nessuna risorsa viene distribuita all'interno della subnet, è possibile modificare l'intervallo di indirizzi. Se esistono risorse nella subnet, è necessario spostare le risorse in un'altra subnet o eliminarle prima di tutto dalla subnet. La procedura per spostare o eliminare una risorsa è diversa a seconda della risorsa. Per informazioni su come spostare o eliminare le risorse presenti nelle subnet, leggere la documentazione relativa a ognuno di questi tipi di risorse. Vedere i vincoli per l' **intervallo di indirizzi** nel passaggio 4 di [aggiungere una subnet](#add-a-subnet). |
    | **Utenti** | è possibile controllare l'accesso alla subnet tramite i ruoli predefiniti o i ruoli personalizzati. Per ulteriori informazioni sull'assegnazione di ruoli e utenti per l'accesso alla subnet, vedere l'articolo relativo all' [aggiunta di un'assegnazione di ruolo](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-role-assignment). |
    | **Gruppo di sicurezza di rete** e **Tabella di route** | Vedere il passaggio 4 di [aggiungere una subnet](#add-a-subnet). |
    | **Endpoint servizio** | <p>Vedere endpoint di servizio nel passaggio 4 di [aggiungere una subnet](#add-a-subnet). Quando si abilita un endpoint di servizio per una subnet esistente, assicurarsi che non vi siano attività critiche in esecuzione in nessuna risorsa nella subnet. Gli endpoint di servizio scambiano le route in ogni interfaccia di rete nella subnet. Gli endpoint del servizio passano dall'uso della route predefinita con il prefisso dell'indirizzo *0.0.0.0/0* e il tipo di hop successivo di *Internet*, all'uso di una nuova route con i prefissi degli indirizzi del servizio e di un tipo di hop successivo *VirtualNetworkServiceEndpoint*.</p><p>Durante il cambio, tutte le connessioni TCP aperte possono essere terminate. L'endpoint servizio non è abilitato fino a quando il traffico verso il servizio per tutte le interfacce di rete viene aggiornato con la nuova route. Per altre informazioni sul routing, vedere [routing del traffico di rete virtuale](virtual-networks-udr-overview.md).</p> |
    | **Delega subnet** | Vedere endpoint di servizio nel passaggio 4 di [aggiungere una subnet](#add-a-subnet). Una delega di subnet può essere modificata per avere da zero a più deleghe abilitate. Se una risorsa per un servizio è già distribuita nella subnet, la delega della subnet non può essere aggiunta o rimossa fino a quando non vengono rimosse tutte le risorse per il servizio. Per la delega per un altro servizio, selezionare il servizio che si desidera delegare dall'elenco **Servizi**. |

6. Selezionare **Salva**.

### <a name="commands"></a>Comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-subnet"></a>Eliminare una subnet

È possibile eliminare una subnet solo se non include risorse. Se le risorse si trovano nella subnet, è necessario eliminare tali risorse prima di poter eliminare la subnet. La procedura necessaria per l'eliminazione di una risorsa è diversa a seconda della risorsa. Per informazioni su come eliminare le risorse presenti nelle subnet, leggere la documentazione relativa a ognuno di questi tipi di risorse.

1. Passare alla [portale di Azure](https://portal.azure.com) per visualizzare le reti virtuali. Cercare e selezionare **Reti virtuali**.

2. Selezionare il nome della rete virtuale che contiene la subnet che si vuole eliminare.

3. In **Impostazioni**selezionare **Subnet**.

4. Nell'elenco di subnet selezionare la subnet che si desidera eliminare.

5. Selezionare **Elimina**e quindi fare clic su **Sì** nella finestra di dialogo di conferma.

### <a name="commands"></a>Comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [AZ Network VNET subnet Delete](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete) |
| PowerShell | [Remove-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json) |

## <a name="permissions"></a>Autorizzazioni

Per eseguire attività nelle subnet, l'account deve essere assegnato al [ruolo Collaboratore rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o a un [ruolo personalizzato](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a cui sono assegnate le azioni appropriate nella tabella seguente:

|Action                                                                   |   Nome                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Leggere una subnet della rete virtuale              |
|Microsoft.Network/virtualNetworks/subnets/write                          |   Creare o aggiornare una subnet della rete virtuale  |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   Eliminare una subnet della rete virtuale            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Aggiungere una rete virtuale                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   Abilitare un endpoint di servizio per una subnet     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   Ottenere le macchine virtuali in una subnet       |

## <a name="next-steps"></a>Passaggi successivi

- Creare una rete virtuale e le subnet usando gli script di esempio di [PowerShell](powershell-samples.md) o dell'[interfaccia della riga di comando di Azure](cli-samples.md) oppure i [modelli di Resource Manager](template-samples.md)
- Creare e assegnare [definizioni di criteri di Azure](policy-samples.md) per le reti virtuali
