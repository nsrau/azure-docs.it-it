---
title: Creare, modificare o eliminare una rete virtuale di Azure | Microsoft Docs
description: Informazioni su come creare, modificare o eliminare una rete virtuale in Azure.
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
ms.openlocfilehash: 74aace2136136c25bc56327d38cfbab168265401
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="create-change-or-delete-a-virtual-network"></a>Creare, modificare o eliminare una rete virtuale

Informazioni su come creare ed eliminare una rete virtuale e su come modificare le impostazioni, ad esempio i server DNS e gli spazi indirizzi IP, di una rete virtuale esistente.

Una rete virtuale è una rappresentazione della rete dell'utente nel cloud. È una parte logicamente isolata del cloud di Azure dedicata alla sottoscrizione di Azure dell'utente. Per ogni rete virtuale creata, è possibile:
- Scegliere lo spazio indirizzi da assegnare. Uno spazio indirizzi è costituito da uno o più intervalli di indirizzi, definiti tramite la notazione CIDR (Classless Inter-Domain Routing), ad esempio 10.0.0.0/16.
- Scegliere se usare il server DNS fornito da Azure o il proprio server DNS. Per la risoluzione dei nomi all'interno della rete virtuale, a tutte le risorse connesse alla rete virtuale viene assegnato questo server DNS.
- Segmentare la rete virtuale in subnet, ognuna con il proprio intervallo di indirizzi, all'interno dello spazio indirizzi della rete virtuale. Per informazioni su come creare, modificare ed eliminare subnet, vedere [Creare, modificare o eliminare le subnet di rete virtuale](virtual-network-manage-subnet.md).

Questo articolo spiega come creare, modificare ed eliminare le reti virtuali tramite il modello di distribuzione Azure Resource Manager.

## <a name="before"></a>Prima di iniziare

Prima di iniziare le attività descritte in questo articolo, completare i prerequisiti seguenti:

- Se non si ha familiarità con l'uso di reti virtuali, è consigliabile rivedere l'esercitazione [Creare la prima rete virtuale di Azure](virtual-network-get-started-vnet-subnet.md). Questa esercitazione consente di acquisire maggiore familiarità con le reti virtuali.
- Per altre informazioni sui limiti delle reti virtuali, rivedere i [limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Accedere al portale di Azure, allo strumento da riga di comando, ovvero all'interfaccia della riga di comando di Azure, oppure ad Azure PowerShell con il proprio account di Azure. Se non si ha un account di Azure, registrarsi per ottenere un [account per la versione di valutazione gratuita](https://azure.microsoft.com/free).
- Se per completare le attività descritte in questo articolo si prevede di usare i comandi di PowerShell, è prima necessario [installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Assicurarsi che sia installata la versione più recente dei cmdlet di Azure PowerShell. Per le informazioni della Guida sui comandi di PowerShell usati negli esempi, immettere `get-help <command> -full`.
- Se si prevede di usare i comandi dell'interfaccia della riga di comando di Azure per completare le attività descritte in questo articolo, è prima necessario [installare e configurare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Assicurarsi che sia installata la versione più recente dell'interfaccia della riga di comando di Azure. Per le informazioni della Guida sui comandi dell'interfaccia della riga di comando di Azure, immettere `az <command> --help`.


## <a name="create-vnet"></a>Creare una rete virtuale

Per creare una rete virtuale:

1. Accedere al [portale](https://portal.azure.com) con un account dotato almeno delle autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Per altre informazioni sull'assegnazione di ruoli e autorizzazioni agli account, vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Fare clic su **Nuovo** > **Rete** > **Rete virtuale**.
3. Nella casella **Selezionare un modello di distribuzione** del pannello **Rete virtuale** lasciare selezionato **Gestione risorse** e quindi fare clic su **Crea**.
4. Nel pannello **Crea rete virtuale** immettere o selezionare i valori necessari per le impostazioni seguenti e quindi fare clic su **Crea**:
    - **Nome**: il nome deve essere univoco nell'ambito del [gruppo di risorse](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) selezionato per la creazione della rete virtuale al suo interno. Dopo la creazione della rete virtuale non è possibile modificarne il nome. È possibile creare più reti virtuali in momenti diversi. Per alcuni suggerimenti sull'assegnazione del nome, vedere [Naming conventions](/azure/architecture/best-practices/naming-conventions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions) (Convenzioni di denominazione). Il rispetto di una convenzione di denominazione consente una gestione più semplice di più reti virtuali.
    - **Spazio indirizzi**: specificare lo spazio indirizzi nella notazione CIDR. Lo spazio di indirizzi che si definisce può essere pubblico o privato (RFC 1918). Indipendentemente dalla sua definizione come pubblico o privato, lo spazio indirizzi è raggiungibile solo all'interno della rete virtuale, da reti virtuali interconnesse e da eventuali reti locali connesse alla rete virtuale. Non è possibile aggiungere gli spazi di indirizzi seguenti:
        - 224.0.0.0/4 (multicast)
        - 255.255.255.255/32 (broadcast)
        - 127.0.0.0/8 (loopback)
        - 169.254.0.0/16 (locale rispetto al collegamento)
        - 168.63.129.16/32 (DNS interno)

      Al momento della creazione della rete virtuale è possibile definire un solo spazio indirizzi. Dopo la creazione della rete virtuale, tuttavia, è possibile aggiungerne altri. Per informazioni su come aggiungere uno spazio indirizzi a una rete virtuale esistente, vedere [Aggiungere o rimuovere uno spazio indirizzi](#add-address-spaces) in questo articolo.

      >[!WARNING]
      >Se gli spazi indirizzi di una rete virtuale si sovrappongono a quelli di un'altra rete virtuale o di una rete locale, non è possibile connettere le due reti. Prima di definire uno spazio indirizzi, tenere conto della possibilità che in futuro sia necessario connettere la rete virtuale ad altre reti virtuali o locali.
      >
      >

    - **Nome subnet**: il nome della subnet deve essere univoco all'interno della rete virtuale. Dopo la creazione della subnet non è possibile modificarne il nome. Al momento della creazione di una rete virtuale il portale richiede la definizione di una subnet, anche se una rete virtuale non deve necessariamente avere una o più subnet. Quando si crea una rete virtuale nel portale, è possibile definire una sola subnet. Dopo la creazione della rete virtuale è possibile aggiungerne altre. Per aggiungere una subnet a una rete virtuale, vedere [Creare una subnet](virtual-network-manage-subnet.md#create-subnet) in [Creare, modificare o eliminare le subnet](virtual-network-manage-subnet.md). Per creare una rete virtuale con più subnet, è possibile usare l'interfaccia della riga di comando di Azure o PowerShell.

      >[!TIP]
      >Talvolta gli amministratori creano subnet diverse per filtrare o controllare il routing del traffico tra queste. Prima di definire le subnet, tenere conto dell'eventuale necessità di filtrare e instradare il traffico tra le subnet stesse. Per altre informazioni sull'applicazione di filtri al traffico tra le subnet, vedere [Gruppi di sicurezza di rete](virtual-networks-nsg.md). Azure effettua il routing automatico del traffico tra le subnet. È tuttavia possibile eseguire l'override delle route predefinite di Azure. Per informazioni su come eseguire l'override del routing predefinito di Azure per il traffico tra le subnet, vedere [Route definite dall'utente](virtual-networks-udr-overview.md).
      >

    - **Intervallo di indirizzi subnet**: l'intervallo deve essere compreso nello spazio indirizzi immesso in precedenza per la rete virtuale. L'intervallo più piccolo che è possibile specificare è /29, che fornisce otto indirizzi IP per subnet. Azure riserva il primo e l'ultimo indirizzo in ogni subnet per conformità al protocollo. Altri tre indirizzi sono riservati per l'uso da parte del servizio di Azure. Di conseguenza, una rete virtuale con un intervallo di indirizzi di subnet /29 ha solo tre indirizzi IP utilizzabili. Se si prevede di connettere una rete virtuale a un gateway VPN, è necessario creare una subnet per il gateway. Altre informazioni su [considerazioni specifiche sugli intervalli di indirizzi per le subnet di gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Dopo aver creato la subnet, in determinate condizioni è possibile modificare l'intervallo di indirizzi. Per informazioni su come modificare un intervallo di indirizzi di subnet, vedere [Cambiare le impostazioni della subnet](#change-subnet) in [Creare, modificare o eliminare le subnet](virtual-network-manage-subnet.md).
    - **Sottoscrizione**: selezionare una [sottoscrizione](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Non è possibile usare la stessa rete virtuale in più sottoscrizioni di Azure. È tuttavia possibile connettere una rete virtuale in una sottoscrizione a reti virtuali in altre sottoscrizioni. Per connettere reti virtuali di sottoscrizioni diverse, usare il gateway VPN di Azure o il peering di reti virtuali. Qualsiasi risorsa di Azure da connettere alla rete virtuale deve essere compresa nella sottoscrizione della rete virtuale stessa.
    - **Gruppo di risorse**: selezionare un [gruppo di risorse](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) esistente o crearne uno nuovo. Una risorsa di Azure da connettere alla rete virtuale può trovarsi nello stesso gruppo di risorse della rete virtuale o in un gruppo di risorse diverso.
    - **Località**: selezionare una [località](https://azure.microsoft.com/regions/) di Azure. Le località sono dette anche aree. Una rete virtuale può trovarsi in una sola località di Azure. È tuttavia possibile connettere una rete virtuale in una località con una rete virtuale in un'altra località tramite un gateway VPN. Qualsiasi risorsa di Azure da connettere alla rete virtuale deve trovarsi nella località della rete virtuale stessa.

**Comandi**

|Strumento|Comando|
|---|---|
|Interfaccia della riga di comando di Azure|[az network vnet create](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name = "view-vnet"></a>Visualizzare le reti virtuali e le impostazioni

Per visualizzare le reti virtuali e le impostazioni:

1. Accedere al [portale](https://portal.azure.com) con un account dotato almeno delle autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Per altre informazioni sull'assegnazione di ruoli e autorizzazioni agli account, vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Nella casella di ricerca del portale immettere **reti virtuali**. Nei risultati della ricerca fare clic su **Reti virtuali**.
3. Nel pannello **Reti virtuali** fare clic sulla rete virtuale di cui si vogliono visualizzare le impostazioni.
4. Nel pannello visualizzato per la rete virtuale selezionata sono elencate le impostazioni seguenti:
    - **Panoramica**: offre informazioni sulla rete virtuale, ad esempio lo spazio indirizzi e i server DNS. Lo screenshot seguente illustra le impostazioni di panoramica per una rete virtuale con nome **MyVNet**:

        ![Panoramica dell'interfaccia di rete](./media/virtual-network-manage-network/vnet-overview.png)

      Nel pannello **Panoramica** è possibile spostare una rete virtuale in un'altra sottoscrizione o in un altro gruppo di risorse. Per informazioni su come spostare una rete virtuale, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json). L'articolo elenca i prerequisiti e descrive come spostare le risorse tramite il portale di Azure, PowerShell e l'interfaccia della riga di comando di Azure. Tutte le risorse connesse alla rete virtuale devono essere spostate con la rete virtuale stessa.
    - **Spazio indirizzi**: elenca gli spazi indirizzi assegnati alla rete virtuale. Per informazioni su come aggiungere e rimuovere spazi indirizzi, completare la procedura descritta in [Aggiungere o rimuovere uno spazio indirizzi](#address-spaces) in questo articolo.
    - **Dispositivi connessi**: elenca tutte le risorse connesse alla rete virtuale. Nello screenshot precedente, alla rete virtuale sono connessi tre interfacce di rete e un bilanciamento del carico. Nell'elenco sono presenti tutte le nuove risorse create e connesse alla rete virtuale. Se si elimina una risorsa connessa alla rete virtuale, la risorsa non compare più nell'elenco.
    - **Subnet**: presenta l'elenco delle subnet esistenti all'interno della rete virtuale. Per informazioni su come aggiungere e rimuovere una subnet, vedere [Creare una subnet](virtual-network-manage-subnet.md#create-subnet) ed [Eliminare una subnet](virtual-network-manage-subnet.md#delete-subnet) in [Creare, modificare o eliminare le subnet](virtual-network-manage-subnet.md).
    - **Server DNS**: consente di specificare se la risoluzione dei nomi per i dispositivi connessi alla rete virtuale deve essere eseguita dal server DNS interno di Azure o da un server DNS personalizzato. Per impostazione predefinita, quando si crea una rete virtuale tramite il Portale di Azure, per la risoluzione dei nomi all'interno di una rete virtuale vengono usati i server DNS di Azure. Per modificare i server DNS, completare la procedura in [Aggiungere, modificare o rimuovere un server DNS](#dns-servers) in questo articolo.
    - **Peering**: se presenti nella sottoscrizione, i peering sono elencati qui. È possibile visualizzare le impostazioni per i peering esistenti o creare, modificare ed eliminare peering. Per altre informazioni sui peering, vedere [Peering di rete virtuale](virtual-network-peering-overview.md).
    - **Proprietà**: visualizza le impostazioni della rete virtuale, inclusi l'ID risorsa della rete virtuale e la sottoscrizione di Azure in cui si trova.
    - **Diagramma**: il diagramma offre una rappresentazione visiva di tutti i dispositivi connessi alla rete virtuale con alcune informazioni chiave sui dispositivi stessi. Per gestire un dispositivo in questa vista, fare clic sul dispositivo all'interno del diagramma.
    - **Impostazioni comuni di Azure**: per altre informazioni sulle impostazioni comuni di Azure, vedere le informazioni seguenti:
        *   [Log attività](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
        *   [Controllo di accesso (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
        *   [Tag](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)
        *   [Blocchi](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        *   [Script di automazione](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Comandi**

|Strumento|Comando|
|---|---|
|Interfaccia della riga di comando di Azure|[az network vnet show](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork/?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="add-address-spaces"></a>Aggiungere o rimuovere uno spazio indirizzi

È possibile aggiungere e rimuovere spazi indirizzi per una rete virtuale. Gli spazi indirizzi devono essere specificati nella notazione CIDR e non possono sovrapporsi ad altri spazi indirizzi all'interno della stessa rete virtuale. Gli spazi di indirizzi che si definiscono possono essere pubblici o privati (RFC 1918). Indipendentemente dalla sua definizione come pubblico o privato, lo spazio indirizzi è raggiungibile solo all'interno della rete virtuale, da reti virtuali interconnesse e da eventuali reti locali connesse alla rete virtuale. Non è possibile aggiungere gli spazi di indirizzi seguenti:

- 224.0.0.0/4 (multicast)
- 255.255.255.255/32 (broadcast)
- 127.0.0.0/8 (loopback)
- 169.254.0.0/16 (locale rispetto al collegamento)
- 168.63.129.16/32 (DNS interno)

Per aggiungere o rimuovere uno spazio indirizzi

1. Accedere al [portale](https://portal.azure.com) con un account dotato almeno delle autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Per altre informazioni sull'assegnazione di ruoli e autorizzazioni agli account, vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Nella casella di ricerca del portale immettere **reti virtuali**. Nei risultati della ricerca selezionare **Reti virtuali**.
3. Nel pannello **Reti virtuali** fare clic sulla rete virtuale a cui aggiungere o da cui rimuovere uno spazio indirizzi.
4. Nel pannello della rete virtuale, in **IMPOSTAZIONI**, fare clic su **Spazio indirizzi**.
5. Nel pannello dello spazio indirizzi eseguire una delle operazioni seguenti:
    - **Aggiungere uno spazio indirizzi**: immettere il nuovo spazio indirizzi. Lo spazio indirizzi non può sovrapporsi a uno spazio indirizzi esistente definito per la rete virtuale.
    - **Rimuovere uno spazio indirizzi**: fare clic con il pulsante destro del mouse su uno spazio indirizzi e quindi fare clic su **Rimuovi**. Se è presente una subnet nello spazio di indirizzi, non è possibile rimuovere lo spazio di indirizzi. Prima di rimuovere uno spazio indirizzi è necessario eliminare tutte le subnet presenti nello spazio stesso e tutte le risorse connesse a queste subnet.
6. Fare clic su **Salva**.

**Comandi**

|Strumento|Comando|
|---|---|
|Interfaccia della riga di comando di Azure|Solo Resource Manager|[az network vnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="dns-servers"></a>Aggiungere, modificare o rimuovere un server DNS

Tutte le macchine virtuali connesse alla rete virtuale vengono registrate presso uno dei server DNS specificati per la rete virtuale. Il server DNS specificato viene usato da queste macchine virtuali anche per la risoluzione dei nomi. Ogni scheda di interfaccia di rete di una VM può avere le proprie impostazioni per i server DNS. Se per una scheda di interfaccia di rete sono definite impostazioni specifiche per i server DNS, queste sostituiscono le impostazioni relative ai server DNS per la rete virtuale. Per altre informazioni sulle impostazioni DNS delle schede di interfaccia di rete, vedere [attività e impostazioni relative all'interfaccia di rete](virtual-network-network-interface.md#change-dns-servers). Per altre informazioni sulla risoluzione dei nomi per le macchine virtuali e sulle istanze del ruolo in Servizi cloud di Microsoft Azure, vedere [Risoluzione dei nomi per le macchine virtuali e le istanze del ruolo](virtual-networks-name-resolution-for-vms-and-role-instances.md). Per aggiungere, modificare o rimuovere un server DNS:

1. Accedere al [portale](https://portal.azure.com) con un account dotato almeno delle autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Per altre informazioni sull'assegnazione di ruoli e autorizzazioni agli account, vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Nella casella di ricerca del portale digitare **reti virtuali**. Nei risultati della ricerca selezionare **Reti virtuali**.
3. Nel pannello **Reti virtuali** fare clic sulla rete virtuale di cui si vogliono modificare le impostazioni DNS.
4. Nel pannello della rete virtuale, in **IMPOSTAZIONI**, fare clic su **Server DNS**.
5. Nel pannello che elenca i server DNS selezionare una delle opzioni seguenti:
    - **Predefinito (fornito da Azure)**: tutti i nomi delle risorse e gli indirizzi IP privati vengono registrati automaticamente presso i server DNS di Azure. È possibile risolvere i nomi tra tutte le risorse connesse alla stessa rete virtuale. Non è possibile usare questa opzione per la risoluzione dei nomi tra reti virtuali diverse. Per risolvere i nomi tra reti virtuali diverse è necessario usare un server DNS personalizzato.
    - **Personalizzato**: è possibile aggiungere uno o più server, fino al limite di Azure per le reti virtuali. Per altre informazioni sui limiti dei server DNS, vedere i [limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic). L'utente ha a disposizione le seguenti opzioni:
        - **Aggiungere un indirizzo**: consente di aggiungere un server all'elenco dei server DNS della rete virtuale. Questa opzione consente anche di registrare il server DNS in Azure. Se un server DNS è già stato registrato in Azure, è possibile selezionarlo nell'elenco.
        - **Rimuovere un indirizzo**: fare clic sulla **X** accanto al server che si vuole rimuovere. Questa operazione rimuove il server solo dall'elenco della rete virtuale attiva. Il server DNS resta registrato in Azure in modo che venga usato dalle altre reti virtuali.
        - **Riordinare gli indirizzi dei server DNS**: è importante verificare che siano elencati nell'ordine corretto per l'ambiente. Gli elenchi di server DNS vengono usati nell'ordine in cui sono specificati e non rappresentano una configurazione di tipo round robin. Se il primo server DNS nell'elenco è raggiungibile, il client lo usa, indipendentemente dal fatto che funzioni correttamente o meno. Rimuovere tutti i server DNS elencati e quindi aggiungerli di nuovo nell'ordine desiderato.
        - **Modificare un indirizzo**: evidenziare il server DNS nell'elenco e quindi immettere il nuovo nome.
6. Fare clic su **Salva**.
7. Riavviare le macchine virtuali connesse alla rete virtuale in modo che vengano loro assegnate le nuove impostazioni del server DNS. Le VM continuano a usare le impostazioni DNS correnti finché non vengono riavviate.

**Comandi**

|Strumento|Comando|
|---|---|
|Interfaccia della riga di comando di Azure|[az network vnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="delete-vnet"></a>Eliminare una rete virtuale

È possibile eliminare una rete virtuale solo se a questa non sono connesse risorse. Se una o più risorse sono connesse a una qualsiasi subnet all'interno della rete virtuale, è prima necessario eliminare le risorse connesse a tutte le subnet della rete virtuale stessa. La procedura necessaria per l'eliminazione di una risorsa è diversa a seconda della risorsa. Per informazioni su come eliminare le risorse connesse a una subnet, leggere la documentazione relativa a ogni tipo di risorsa che si vuole eliminare. Per eliminare una rete virtuale:

1. Accedere al [portale](https://portal.azure.com) con un account che abbia almeno le autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Per altre informazioni sull'assegnazione di ruoli e autorizzazioni agli account, vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Nella casella di ricerca del portale immettere **reti virtuali**. Nei risultati della ricerca fare clic su **Reti virtuali**.
3. Nel pannello **Reti virtuali** selezionare la rete virtuale che si vuole eliminare.
4. Nel pannello della rete virtuale, confermare che non sono presenti dispositivi connessi a questa. A tale scopo, in **IMPOSTAZIONI** fare clic su **Dispositivi connessi**. Se sono presenti dispositivi connessi, è necessario eliminarli prima di eliminare la rete virtuale. Se non sono presenti dispositivi connessi, fare clic su **Panoramica**.
5. Nella parte superiore del pannello fare clic sull'icona **Elimina**.
6. Per confermare l'eliminazione della rete virtuale, fare clic su **Sì**.


**Comandi**

|Strumento|Comando|
|---|---|
|Interfaccia della riga di comando di Azure|[azure network vnet delete](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetwork](/powershell/module/azurerm.network/remove-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="next-steps"></a>Passaggi successivi

- Per creare una macchina virtuale e quindi connetterla a una rete virtuale, vedere [Creare una rete virtuale e connettere macchine virtuali](virtual-network-get-started-vnet-subnet.md#create-vms).
- Per filtrare il traffico di rete tra subnet all'interno di una rete virtuale, vedere [Creare gruppi di sicurezza di rete](virtual-networks-create-nsg-arm-pportal.md).
- Per effettuare il peering di una rete virtuale con un'altra, vedere [Create a virtual network peering](virtual-network-create-peering.md#portal) (Creare un peering di reti virtuali).
- Per informazioni sulle opzioni per la connessione di una rete virtuale a una rete locale, vedere [Informazioni sul gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#diagrams).
