---
title: Creare, modificare o eliminare una subnet di rete virtuale di Azure | Microsoft Docs
description: Informazioni su come creare, modificare o eliminare una subnet di rete virtuale in Azure.
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
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: f3b8a5cc0c85e56e535871f1f7fcfd72bde65a03
ms.contentlocale: it-it
ms.lasthandoff: 05/31/2017


---
# <a name="create-change-or-delete-a-virtual-network-subnet"></a>Creare, modificare o eliminare una subnet di rete virtuale

Informazioni su come creare, modificare o eliminare una subnet di rete virtuale. 

Se non avete familiarità con le reti virtuali, prima di creare, modificare o eliminare una subnet, è consigliabile leggere [Panoramica della rete virtuale di Azure](virtual-networks-overview.md) e [Creare, modificare o eliminare le reti virtuali](virtual-network-manage-network.md). Tutte le risorse di Azure in grado di connettersi a una rete virtuale sono connesse a una subnet in una rete virtuale. In genere, vengono create più subnet all'interno di una rete virtuale per:
- **Filtrare il traffico tra subnet**. È possibile applicare gruppi di sicurezza di rete alle subnet per filtrare il traffico di rete in ingresso e in uscita per tutte le risorse, come per le macchine virtuali, che sono connesse alla rete virtuale. Per altre informazioni su come creare un gruppo di sicurezza di rete, vedere l'articolo [Creare gruppi di sicurezza di rete](virtual-networks-create-nsg-arm-pportal.md).
- **Controllare il routing tra subnet**. Azure crea route predefinite in modo che il traffico venga indirizzato automaticamente tra le subnet. È possibile sostituire le route predefinite di Azure creando route definite dall'utente. Per altre informazioni sulle route definite dall'utente, vedere l'articolo [Creare route definite dall'utente](virtual-network-create-udr-arm-ps.md). 

Questo articolo spiega come creare, modificare ed eliminare le subnet per le reti virtuali tramite il modello di distribuzione Azure Resource Manager.
 
## <a name="before"></a>Prima di iniziare

Prima di iniziare le attività descritte in questo articolo, completare i prerequisiti seguenti:

- Se non si ha familiarità con l'uso di reti virtuali, è consigliabile rivedere l'esercitazione [Creare la prima rete virtuale di Azure](virtual-network-get-started-vnet-subnet.md). Questa esercitazione consente di acquisire maggiore familiarità con le reti virtuali.
- Per altre informazioni sui limiti delle reti virtuali, rivedere i [limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Accedere al portale di Azure, allo strumento da riga di comando, ovvero all'interfaccia della riga di comando di Azure, oppure ad Azure PowerShell con il proprio account di Azure. Se non si ha un account di Azure, registrarsi per ottenere un [account per la versione di valutazione gratuita](https://azure.microsoft.com/free).
- Se per completare le attività descritte in questo articolo si prevede di usare i comandi di PowerShell, è prima necessario [installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Assicurarsi che sia installata la versione più recente dei cmdlet di Azure PowerShell. Per le informazioni della Guida sui comandi di PowerShell usati negli esempi, immettere `get-help <command> -full`.
- Se si prevede di usare i comandi dell'interfaccia della riga di comando di Azure per completare le attività descritte in questo articolo, è prima necessario [installare e configurare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Assicurarsi che sia installata la versione più recente dell'interfaccia della riga di comando di Azure. Per le informazioni della Guida sui comandi dell'interfaccia della riga di comando di Azure, immettere `az <command> --help`.

## <a name="create-subnet"></a>Creare una subnet

Per creare una subnet:

1. Accedere al [portale](https://portal.azure.com) con un account dotato almeno delle autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Per altre informazioni sull'assegnazione di ruoli e autorizzazioni agli account, vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Nella casella di ricerca del portale immettere **reti virtuali**. Nei risultati della ricerca fare clic su **Reti virtuali**.
3. Nel pannello **Reti virtuali** fare clic sulla rete virtuale a cui si vuole aggiungere una subnet.
4. Nel pannello della rete virtuale fare clic su **Subnet**.
5. Fare clic su **+Subnet**.
6. Nel pannello **Aggiungi subnet** immettere i valori per i parametri seguenti:
    - **Nome**: il nome deve essere univoco all'interno della rete virtuale.
    - **Intervallo indirizzi**: l'intervallo deve essere univoco e compreso nello spazio indirizzi per la rete virtuale. L'intervallo non può sovrapporsi ad altri intervalli di indirizzi di subnet all'interno della rete virtuale. Lo spazio degli indirizzi deve essere specificato usando la notazione Classless Interdomain Routing (CIDR). Ad esempio, in una rete virtuale con lo spazio di indirizzi 10.0.0.0/16 è possibile definire lo spazio di indirizzi di subnet 10.0.0.0/24. L'intervallo più piccolo che è possibile specificare è /29, che fornisce otto indirizzi IP per subnet. Azure riserva il primo e l'ultimo indirizzo in ogni subnet per conformità al protocollo. Altri tre indirizzi sono riservati per l'uso da parte del servizio di Azure. Di conseguenza la definizione di una subnet con l'intervallo di indirizzi /29 genera tre indirizzi IP utilizzabili nella subnet. Se si prevede di connettere una rete virtuale a un gateway VPN, è necessario creare una subnet per il gateway. Altre informazioni su [considerazioni specifiche sugli intervalli di indirizzi per le subnet di gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#a-namegwsubagateway-subnet). Dopo aver creato la subnet, in determinate condizioni è possibile modificare l'intervallo di indirizzi. Per informazioni su come modificare un intervallo di indirizzi di subnet, leggere la sezione [Cambiare le impostazioni della subnet](#change-subnet) di questo articolo.
    - **Gruppo di sicurezza di rete**: è possibile associare facoltativamente un gruppo di sicurezza di rete esistente alla subnet per controllare il filtraggio del traffico di rete in ingresso e in uscita per la subnet. Il gruppo di sicurezza di rete deve esistere nello stesso percorso e nella stessa sottoscrizione della rete virtuale. Deve anche essere creato con il modello di distribuzione di Resource Manager. Per altre informazioni su come creare un gruppo di sicurezza di rete, vedere l'articolo [Gruppi di sicurezza di rete](virtual-networks-create-nsg-arm-pportal.md).
    - **Tabella di route**: è possibile associare facoltativamente una tabella di route alla subnet per controllare il routing del traffico di rete ad altre reti. La tabella di route deve esistere nello stesso percorso e nella stessa sottoscrizione della rete virtuale. Deve anche essere creato con il modello di distribuzione di Resource Manager. Per altre informazioni su come creare tabelle di route, vedere l'articolo [Route definite dall'utente](virtual-network-create-udr-arm-ps.md).
    - **Utenti**: è possibile controllare l'accesso alla subnet tramite i ruoli predefiniti o i ruoli personalizzati. Per altre informazioni sull'assegnazione di ruoli e utenti per l'accesso alla subnet, leggere l'articolo [Usare l'assegnazione del ruolo per gestire l'accesso alle risorse di Azure](../active-directory/role-based-access-control-configure.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-access).
7. Per aggiungere la subnet alla rete virtuale selezionata fare clic su **OK**.

**Comandi**

|Strumento|Comando|
|---|---|
|Interfaccia della riga di comando di Azure|[az network vnet subnet create](/cli/azure/network/vnet/subnet?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json), [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-subnet"></a>Cambiare le impostazioni della subnet

È possibile modificare i gruppi di sicurezza di rete, le tabelle di routing e l'accesso dell'utente a una subnet per la gestione delle risorse connesse a una subnet. Per informazioni su queste impostazioni, vedere il passaggio 6 in [Creare una subnet](#create-subnet). Se si desidera modificare lo spazio degli indirizzi di una subnet, è innanzitutto necessario eliminare tutte le risorse connesse alla subnet. La procedura necessaria per l'eliminazione di una risorsa è diversa a seconda della risorsa. Per informazioni su come eliminare le risorse connesse alle subnet, leggere la documentazione relativa a ogni tipo di risorsa che si vuole eliminare. Per modificare l'intervallo di indirizzi per una subnet:

1. Accedere al [portale](https://portal.azure.com) con un account dotato almeno delle autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Per altre informazioni sull'assegnazione di ruoli e autorizzazioni agli account, vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Nella casella di ricerca del portale immettere **reti virtuali**. Nei risultati della ricerca fare clic su **Reti virtuali**.
3. Nel pannello **Reti virtuali** fare clic sulla rete virtuale per cui si desidera modificare l'intervallo di indirizzi di una subnet.
4. Fare clic sulla subnet per cui si vuole modificare l'intervallo di indirizzi.
5. Nel pannello della subnet, nella casella **Intervallo indirizzi** immettere il nuovo intervallo di indirizzi. L'intervallo deve essere univoco all'interno dello spazio di indirizzi della rete virtuale. L'intervallo non può sovrapporsi ad altri intervalli di indirizzi di subnet all'interno della rete virtuale. Lo spazio di indirizzi deve essere specificato mediante la notazione CIDR. Ad esempio, in una rete virtuale con lo spazio di indirizzi 10.0.0.0/16 è possibile definire lo spazio di indirizzi di subnet 10.0.0.0/24. L'intervallo più piccolo che è possibile specificare è /29, che fornisce otto indirizzi IP per subnet. Azure riserva il primo e l'ultimo indirizzo in ogni subnet per conformità al protocollo. Altri tre indirizzi sono riservati per l'uso da parte del servizio di Azure. Di conseguenza una subnet con l'intervallo di indirizzi /29 ha tre indirizzi IP utilizzabili. Se si prevede di connettere una rete virtuale a un gateway VPN, è necessario creare una subnet per il gateway. Altre informazioni su [considerazioni specifiche sugli intervalli di indirizzi per le subnet di gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#a-namegwsubagateway-subnet). Dopo aver creato la subnet, in determinate condizioni è possibile modificare l'intervallo di indirizzi. Per informazioni su come modificare un intervallo di indirizzi di subnet, leggere la sezione [Cambiare le impostazioni della subnet](#change-subnet) di questo articolo.
6. Fare clic su **Salva**.

**Comandi**

|Strumento|Comando|
|---|---|
|Interfaccia della riga di comando di Azure|[az network vnet subnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="delete-subnet"></a>Eliminare una subnet

È possibile eliminare una subnet solo se non sono presenti risorse connesse a essa. Se sono presenti risorse connesse alla subnet, è necessario eliminare le risorse connesse alla subnet prima di poter eliminare la subnet. La procedura necessaria per l'eliminazione di una risorsa è diversa a seconda della risorsa. Per informazioni su come eliminare le risorse connesse alle subnet, leggere la documentazione relativa a ogni tipo di risorsa che si vuole eliminare. Per eliminare una subnet:

1. Accedere al [portale](https://portal.azure.com) con un account dotato almeno delle autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Per altre informazioni sull'assegnazione di ruoli e autorizzazioni agli account, vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Nella casella di ricerca del portale immettere **reti virtuali**. Nei risultati della ricerca fare clic su **Reti virtuali**.
3. Nel pannello **Reti virtuali** fare clic sulla rete virtuale che si desidera eliminare dalla subnet.
4. Nel pannello della rete virtuale, in **IMPOSTAZIONI** fare clic su **Subnet**.
5. Nell'elenco di subnet che viene visualizzato nel pannello delle subnet fare clic con il pulsante destro del mouse sulla subnet che si vuole eliminare, fare clic su **Elimina** e quindi su **Sì** per eliminare la subnet.

**Comandi**

|Strumento|Comando|
|---|---|
|Interfaccia della riga di comando di Azure|[az network vnet delete](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/remove-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="next-steps"></a>Passaggi successivi

Per creare una macchina virtuale e connetterla a una subnet, vedere [Creare una rete virtuale e connettere macchine virtuali](virtual-network-get-started-vnet-subnet.md#a-namecreate-vmsacreate-virtual-machines).
