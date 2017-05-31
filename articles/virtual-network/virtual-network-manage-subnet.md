---
title: Creare, modificare o eliminare le subnet di rete virtuale di Azure | Microsoft Docs
description: Informazioni su come creare, modificare o eliminare le subnet di rete virtuale.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: 38dcdf2f313c236a507e6a418c39812da16c6345
ms.contentlocale: it-it
ms.lasthandoff: 05/16/2017


---
# <a name="create-change-or-delete-virtual-network-subnets"></a>Creare, modificare o eliminare le subnet di rete virtuale

Informazioni su come creare, modificare o eliminare le subnet di rete virtuale. Se non si ha familiarità con le reti virtuali, è consigliabile leggere gli articoli [Panoramica delle reti virtuali](virtual-networks-overview.md) e [Creare, modificare o eliminare le reti virtuali](virtual-network-manage-network.md) prima di creare, modificare o eliminare le subnet. Le risorse di Azure che possono connettersi a una rete virtuale sono connesse a una subnet all'interno della rete virtuale. In genere vengono create più subnet all'interno di una rete virtuale per:
- **Filtrare il traffico tra le subnet**: è possibile applicare gruppi di sicurezza di rete alle subnet per filtrare il traffico di rete in ingresso e in uscita per tutte le risorse (ad esempio macchine virtuali) connesse alla rete virtuale. Per altre informazioni su come creare gruppi di sicurezza di rete, vedere l'articolo [Creare gruppi di sicurezza di rete](virtual-networks-create-nsg-arm-pportal.md).
- **Controllare il routing tra subnet**: Azure crea route predefinite in modo che il traffico venga indirizzato automaticamente tra le subnet. È possibile sostituire le route predefinite di Azure creando route definite dall'utente (UDR). Per altre informazioni sulle route definite dall'utente, vedere l'articolo [Creare route definite dall'utente](virtual-network-create-udr-arm-ps.md). 

Questo articolo spiega come creare, modificare ed eliminare le subnet per le reti virtuali create tramite il modello di distribuzione Azure Resource Manager.
 
## <a name="before"></a>Prima di iniziare

Prima di completare i passaggi di qualsiasi sezione di questo articolo, eseguire le attività seguenti:

- Se non si ha familiarità con le reti virtuali e le subnet di Azure, è consigliabile svolgere l'esercizio disponibile in [Creare la prima rete virtuale di Azure](virtual-network-get-started-vnet-subnet.md) prima di leggere questo articolo. L'esercizio consente di acquisire familiarità con le reti virtuali e le subnet.
- Per informazioni sui limiti delle subnet e le reti virtuali, vedere l'articolo sui [limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Accedere al portale di Azure, all'interfaccia della riga di comando di Azure oppure ad Azure PowerShell con un account Azure. Se non si ha un account Azure, registrarsi per ottenere un [account per la versione di prova gratuita](https://azure.microsoft.com/free).
- Se si usano comandi Azure PowerShell per completare le attività di questo articolo, è necessario innanzitutto [installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Assicurarsi che sia installata la versione più recente dei cmdlet di Azure PowerShell. Per informazioni ed esempi relativi ai comandi di PowerShell, digitare `get-help <command> -full`.
- Se si usano comandi dell'interfaccia della riga di comando di Azure per completare le attività di questo articolo, è necessario innanzitutto [installare e configurare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Assicurarsi che sia installata la versione più recente dell'interfaccia della riga di comando di Azure. Per informazioni sui comandi dell'interfaccia della riga di comando, digitare `az <command> --help`.


## <a name="create-subnet"></a>Creare una subnet

1. Accedere al [portale](https://portal.azure.com) con un account che abbia almeno le autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione di ruoli e autorizzazioni agli account.
2. Nella finestra che contiene il testo *Cerca risorse*, nella parte superiore del portale di Azure, digitare *reti virtuali*. Fare clic su **Reti virtuali** quando viene visualizzato nei risultati della ricerca.
3. Nel pannello **Reti virtuali** che viene visualizzato fare clic sulla rete virtuale a cui si vuole aggiungere una subnet.
4. Nel riquadro che viene visualizzato per la rete virtuale selezionata fare clic su **Subnet**.
5. Fare clic su **+ Subnet**.
6. Nel pannello **Aggiungi subnet** immettere i valori per i parametri seguenti:
    - **Nome**: il nome deve essere univoco all'interno della rete virtuale.
    - **Intervallo di indirizzi**: l'intervallo deve essere univoco all'interno dello spazio di indirizzi per la rete virtuale e non può sovrapporsi ad altri intervalli di indirizzi della subnet all'interno della rete virtuale. Lo spazio di indirizzi deve essere specificato mediante la notazione CIDR. Ad esempio, in una rete virtuale con lo spazio di indirizzi 10.0.0.0/16 è possibile definire lo spazio di indirizzi di subnet 10.0.0.0/24. L'intervallo più piccolo che è possibile specificare è /29, che fornisce otto indirizzi IP per subnet. Azure riserva il primo e l'ultimo indirizzo in ogni subnet per conformità al protocollo. Altri tre indirizzi sono riservati per l'uso da parte del servizio di Azure. Di conseguenza la definizione di una subnet con l'intervallo di indirizzi /29 genera tre indirizzi IP utilizzabili nella subnet. Se si intende connettere una rete virtuale a un Gateway VPN, è necessario creare una subnet del gateway. Altre informazioni su [considerazioni specifiche sugli intervalli di indirizzi per le subnet di gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#a-namegwsubagateway-subnet). Dopo aver creato la subnet, in determinate condizioni è possibile modificare l'intervallo di indirizzi. Per informazioni su come modificare un intervallo di indirizzi di subnet, leggere la sezione [Modificare la subnet](#change-subnet) di questo articolo.
    - **Gruppo di sicurezza di rete**: è possibile associare un gruppo di sicurezza di rete esistente alla subnet per controllare il filtraggio del traffico di rete in ingresso e in uscita per la subnet. Il gruppo di sicurezza di rete deve esistere nella stessa sottoscrizione e posizione della rete virtuale ed essere creato tramite il modello di distribuzione Resource Manager. Per altre informazioni su come creare gruppi di sicurezza di rete, vedere l'articolo [Gruppi di sicurezza di rete](virtual-networks-create-nsg-arm-pportal.md).
    - **Tabella di route**: è possibile associare una tabella di route alla subnet per controllare il routing del traffico di rete ad altre reti. La tabella di route deve esistere nella stessa sottoscrizione e posizione della rete virtuale ed essere creata tramite il modello di distribuzione Resource Manager. Per altre informazioni su come creare tabelle di route, vedere l'articolo [Route definite dall'utente](virtual-network-create-udr-arm-ps.md).
    - **Utenti**: è possibile controllare l'accesso alla subnet tramite ruoli predefiniti o ruoli personalizzati. Per altre informazioni sull'assegnazione di ruoli e utenti per l'accesso alla subnet, leggere l'articolo [Usare il controllo degli accessi in base al ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](../active-directory/role-based-access-control-configure.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-access).
7. Fare clic sul pulsante **OK** per aggiungere la subnet alla rete virtuale selezionata.

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az network vnet subnet create](/cli/azure/network/vnet/subnet?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json), [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-subnet"></a>Cambiare le impostazioni della subnet

È possibile modificare il gruppo di sicurezza di rete, le tabelle di route e l'accesso utente alla subnet con risorse connesse a una subnet. Per informazioni su queste impostazioni, vedere il passaggio 6 della sezione [Creare una subnet](#create-subnet) di questo articolo. Per modificare lo spazio degli indirizzi di una subnet, nessuna risorsa può essere connessa alla subnet. Se sono presenti risorse connesse alla subnet, è necessario innanzitutto eliminare le risorse connesse alla subnet prima di poter modificare l'intervallo di indirizzi. Le istruzioni per eliminare una risorsa variano in base alla risorsa. Per informazioni su come eliminare le risorse connesse a una subnet, leggere la documentazione per ogni tipo di risorsa che si vuole eliminare. Per modificare l'intervallo di indirizzi per una subnet completare i passaggi seguenti:

1. Accedere al [portale](https://portal.azure.com) con un account che abbia almeno le autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione di ruoli e autorizzazioni agli account.
2. Nella finestra che contiene il testo *Cerca risorse*, nella parte superiore del portale, digitare *reti virtuali*. Fare clic su **Reti virtuali** quando viene visualizzato nei risultati della ricerca.
3. Nel pannello **Reti virtuali** che viene visualizzato fare clic sulla rete virtuale relativa alla subnet di cui si vuole cambiare l'intervallo di indirizzi.
4. Fare clic sulla subnet per cui si vuole modificare l'intervallo di indirizzi.
5. Nel pannello che viene visualizzato per la subnet selezionata immettere il nuovo intervallo di indirizzi nella casella **Intervallo di indirizzi**. L'intervallo deve essere univoco all'interno dello spazio di indirizzi per la rete virtuale e non può sovrapporsi ad altri intervalli di indirizzi della subnet all'interno della rete virtuale. Lo spazio di indirizzi deve essere specificato mediante la notazione CIDR. Ad esempio, in una rete virtuale con lo spazio di indirizzi 10.0.0.0/16 è possibile definire lo spazio di indirizzi di subnet 10.0.0.0/24. L'intervallo più piccolo che è possibile specificare è /29, che fornisce otto indirizzi IP per subnet. Azure riserva il primo e l'ultimo indirizzo in ogni subnet per conformità al protocollo. Altri tre indirizzi sono riservati per l'uso da parte del servizio di Azure. Di conseguenza una subnet con l'intervallo di indirizzi /29 ha tre indirizzi IP utilizzabili. Se si intende connettere una rete virtuale a un Gateway VPN, è necessario creare una subnet del gateway. Altre informazioni su [considerazioni specifiche sugli intervalli di indirizzi per le subnet di gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#a-namegwsubagateway-subnet). Dopo aver creato la subnet, in determinate condizioni è possibile modificare l'intervallo di indirizzi. Per informazioni su come modificare un intervallo di indirizzi di subnet, leggere la sezione [Modificare la subnet](#change-subnet) di questo articolo.
6. Fare clic su **Salva**.

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az network vnet subnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="delete-subnet"></a>Eliminare una subnet

È possibile eliminare una subnet solo se non sono presenti risorse connesse a essa. Se sono presenti risorse connesse alla subnet, è necessario innanzitutto eliminare le risorse connesse alla subnet. Le istruzioni per eliminare una risorsa variano in base alla risorsa. Per informazioni su come eliminare le risorse connesse a una subnet, leggere la documentazione per ogni tipo di risorsa che si vuole eliminare.

1. Accedere al [portale](https://portal.azure.com) con un account che abbia almeno le autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione di ruoli e autorizzazioni agli account.
2. Nella finestra che contiene il testo *Cerca risorse*, nella parte superiore del portale di Azure, digitare *reti virtuali*. Fare clic su **Reti virtuali** quando viene visualizzato nei risultati della ricerca.
3. Nel pannello **Reti virtuali** che viene visualizzato fare clic sulla rete virtuale da cui si vuole eliminare la subnet.
4. Nel pannello che viene visualizzato per la rete virtuale selezionata fare clic su **Subnet** sotto **Impostazioni**.
5. Nell'elenco di subnet che viene visualizzato nel pannello delle subnet fare clic con il pulsante destro del mouse sulla subnet che si vuole eliminare, fare clic su **Elimina** e quindi su **Sì** per eliminare la subnet.

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az network vnet delete](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/remove-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="next-steps"></a>Passaggi successivi

Per creare una VM e connetterla a una subnet, leggere l'articolo su come [creare una rete virtuale e connettere le VM](virtual-network-get-started-vnet-subnet.md#a-namecreate-vmsacreate-virtual-machines).
