---
title: Creare, modificare o eliminare le reti virtuali di Azure | Microsoft Docs
description: Informazioni su come creare, modificare o eliminare le reti virtuali.
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
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: b577891afc52013b712634dd51e7e28efcfc4482
ms.contentlocale: it-it
ms.lasthandoff: 05/16/2017


---
# <a name="create-change-or-delete-virtual-networks"></a>Creare, modificare o eliminare le reti virtuali

Informazioni su come creare, eliminare le reti virtuali e modificare le loro impostazioni, come i server DNS e gli spazi di indirizzi IP per reti virtuali esistenti. Una rete virtuale è una rappresentazione della propria rete nel cloud. È un isolamento logico del cloud di Azure dedicato alla sottoscrizione. Per ogni rete virtuale è possibile:
- Scegliere quale spazio di indirizzi assegnare. Uno spazio di indirizzi è costituito da uno o più intervalli di indirizzi definiti usando la notazione CIDR, ad esempio 10.0.0.0/16.
- Scegliere di usare il server DNS fornito da Azure o il proprio server DNS, per ogni rete virtuale. Tutte le risorse connesse alla rete virtuale vengono assegnate a questo server DNS per risolvere i nomi all'interno della rete virtuale.
- Segmentare la rete virtuale in subnet, ciascuna con il proprio intervallo di indirizzi, nello spazio di indirizzi della rete virtuale. Per informazioni su come creare, modificare ed eliminare le subnet, leggere l'articolo [Creare, modificare o eliminare le subnet di rete virtuale](virtual-network-manage-subnet.md).

Questo articolo spiega come creare, modificare ed eliminare le reti virtuali create tramite il modello di distribuzione Azure Resource Manager.
 
## <a name="before"></a>Prima di iniziare

Prima di completare i passaggi di qualsiasi sezione di questo articolo, eseguire le attività seguenti:

- Se non si ha familiarità con le reti virtuali, è consigliabile svolgere l'esercizio disponibile in [Creare la prima rete virtuale di Azure](virtual-network-get-started-vnet-subnet.md) prima di leggere questo articolo. L'esercizio consente di acquisire familiarità con le reti virtuali.
- Per informazioni sui limiti delle reti virtuali, vedere l'articolo sui [limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Accedere al portale di Azure, all'interfaccia della riga di comando di Azure oppure ad Azure PowerShell con un account Azure. Se non si ha un account Azure, registrarsi per ottenere un [account per la versione di prova gratuita](https://azure.microsoft.com/free).
- Se si usano comandi Azure PowerShell per completare le attività di questo articolo, è necessario innanzitutto [installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Assicurarsi che sia installata la versione più recente dei cmdlet di Azure PowerShell. Per informazioni ed esempi relativi ai comandi di PowerShell, digitare `get-help <command> -full`.
- Se si usano comandi dell'interfaccia della riga di comando di Azure per completare le attività di questo articolo, è necessario innanzitutto [installare e configurare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Assicurarsi che sia installata la versione più recente dell'interfaccia della riga di comando di Azure. Per informazioni sui comandi dell'interfaccia della riga di comando, digitare `az <command> --help`.


## <a name="create-vnet"></a>Creare una rete virtuale

1. Accedere al [portale](https://portal.azure.com) con un account che abbia almeno le autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione di ruoli e autorizzazioni agli account.
2. Nel portale di Azure fare clic su **+ Nuovo**. Nel pannello **Nuovo** che viene visualizzato fare clic su **Rete**. Nel pannello **Rete**  che viene visualizzato fare clic su **Rete virtuale**.
3. Nel pannello **Rete virtuale** che viene visualizzato lasciare selezionato *Resource Manager* nella casella **Selezionare un modello di distribuzione** e fare clic su **Crea**.
4. Immettere o selezionare i valori per le impostazioni seguenti nel pannello **Crea rete virtuale** che viene visualizzato, quindi fare clic su **Crea**:
    - **Nome**: il nome deve essere univoco all'interno del [gruppo di risorse](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) selezionato in cui creare la rete virtuale. Il nome non può essere modificato dopo la creazione della rete virtuale. È possibile creare più reti virtuali nel tempo. Per suggerimenti sulla denominazione in modo da facilitare la gestione di più reti virtuali, leggere l'articolo [Convenzioni di denominazione](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions).
    - **Spazio di indirizzi**: specificare nella notazione CIDR. Lo spazio di indirizzi che si definisce può essere pubblico o privato (RFC 1918). Che si definisca uno spazio di indirizzi pubblico o privato, lo spazio di indirizzi è raggiungibile solo dall'interno della rete virtuale, da reti virtuali interconnesse e da qualsiasi rete locale connessa alla rete virtuale. Non è possibile aggiungere gli spazi di indirizzi seguenti:
        - 224.0.0.0/4 (multicast)
        - 255.255.255.255/32 (broadcast)
        - 127.0.0.0/8 (loopback)
        - 169.254.0.0/16 (locale rispetto al collegamento)
        - 168.63.129.16/32 (DNS interno)
    
      Durante la creazione della rete virtuale è possibile definire un solo spazio di indirizzi, ma è possibile aggiungerne altri dopo averla creata. Per informazioni su come fare, completare la procedura descritta nella sezione [Aggiungere e rimuovere spazi di indirizzi](#add-address-spaces) di questo articolo.

      >[!WARNING]
      >Se le reti virtuali hanno spazi di indirizzi sovrapposti con altre reti virtuali o reti locali, non possono essere connesse tra loro. Prima di definire uno spazio di indirizzi, considerare le altre reti locali o reti virtuali che si potrebbero connettere alla rete virtuale in futuro.
      >
      >
    
    - **Nome della subnet**: il nome deve essere univoco all'interno della rete virtuale. Il nome non può essere modificato dopo la creazione della subnet. Nel portale viene richiesto di definire una subnet durante la creazione di una rete virtuale, anche se una rete virtuale non deve necessariamente avere subnet. Il portale consente di definire una subnet solo durante la creazione della rete virtuale, sebbene sia possibile aggiungere altre subnet alla rete virtuale dopo averla creata. Per informazioni su come aggiungere una subnet a una rete virtuale, leggere la sezione [Creare una subnet](virtual-network-manage-subnet.md#create-subnet) dell'articolo [Creare, modificare o eliminare le subnet](virtual-network-manage-subnet.md). È possibile creare una rete virtuale con più subnet mediante l'interfaccia della riga di comando o PowerShell.

      >[!TIP]
      >Subnet diverse vengono spesso create per filtrare o controllare il routing del traffico tra esse. Prima di definire le subnet, considerare come si vuole filtrare e instradare il traffico tra le subnet stesse. Per altre informazioni sul filtraggio del traffico fra le subnet, vedere l'articolo [Gruppi di sicurezza di rete](virtual-networks-nsg.md). Azure esegue il routing tra le subnet automaticamente, ma è possibile sostituire le route predefinite di Azure. Per informazioni su come procedere, vedere l'articolo [Route definite dall'utente](virtual-networks-udr-overview.md).
      >

    - **Intervallo di indirizzi di subnet**: deve essere compreso nello **spazio di indirizzi** immesso per la rete virtuale. L'intervallo più piccolo che è possibile specificare è /29, che fornisce otto indirizzi IP per subnet. Azure riserva il primo e l'ultimo indirizzo in ogni subnet per conformità al protocollo. Altri tre indirizzi sono riservati per l'uso da parte del servizio di Azure. Di conseguenza una rete virtuale con l'intervallo di indirizzi di subnet /29 ha solo tre indirizzi IP utilizzabili. Se si intende connettere una rete virtuale a un Gateway VPN, è necessario creare una subnet del gateway. Altre informazioni su [considerazioni specifiche sugli intervalli di indirizzi per le subnet di gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#a-namegwsubagateway-subnet). Dopo aver creato la subnet, in determinate condizioni è possibile modificare l'intervallo di indirizzi. Per informazioni su come modificare un intervallo di indirizzi di subnet, leggere la sezione [Modificare una subnet](#change-subnet) dell'articolo [Creare, modificare o eliminare le subnet di rete virtuale](virtual-network-manage-subnet.md).
    - **Sottoscrizione**: selezionare una [sottoscrizione](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Una rete virtuale non può estendersi su più sottoscrizioni anche se può essere connessa a reti virtuali di altre sottoscrizioni mediante Gateway VPN di Azure o il peering di rete virtuale. Le risorse di Azure che si connettono alla rete virtuale devono trovarsi nella stessa sottoscrizione.
    - **Gruppo di risorse**: selezionare un [gruppo di risorse](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) esistente o crearne uno nuovo. Le risorse di Azure che si connettono alla rete virtuale possono trovarsi nello stesso o in diversi gruppi di risorse.
    - **Posizione**: selezionare una [posizione](https://azure.microsoft.com/regions/) di Azure, anche chiamata area. Una rete virtuale non può estendersi su più posizioni di Azure anche se una rete virtuale in una posizione può essere connessa a una rete virtuale in un'altra posizione mediante un Gateway VPN di Azure. Le risorse di Azure che si connettono alla rete virtuale devono trovarsi nella stessa posizione.

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az network vnet create](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name = "view-vnet"></a>Visualizzare le reti virtuali e le impostazioni

1. Accedere al [portale](https://portal.azure.com) con un account che abbia almeno le autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione di ruoli e autorizzazioni agli account.
2. Nella finestra che contiene il testo *Cerca risorse*, nella parte superiore del portale, digitare *reti virtuali*. Fare clic su **Reti virtuali** quando viene visualizzato nei risultati della ricerca.
3. Nel pannello **Reti virtuali** che viene visualizzato fare clic sulla rete virtuale di cui visualizzare le impostazioni.
4. Nel pannello che viene visualizzato per la rete virtuale selezionata sono elencate le impostazioni seguenti:
    - **Panoramica**: fornisce informazioni sulla rete virtuale, ad esempio il relativo spazio di indirizzi e i server DNS. L'immagine seguente mostra le impostazioni di panoramica per una rete virtuale denominata **MyVNet**:
    
        ![Panoramica dell'interfaccia di rete](./media/virtual-network-manage-network/vnet-overview.png)

      Da questo pannello è possibile spostare una rete virtuale in un'altra sottoscrizione o gruppo di risorse. Per informazioni su come spostare una rete virtuale, leggere l'articolo [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json). L'articolo elenca i prerequisiti e descrive come spostare le risorse tramite il portale di Azure, PowerShell e l'interfaccia della riga di comando di Azure. Insieme alla rete virtuale devono essere spostate anche tutte le risorse connesse a essa. 
    - **Spazio di indirizzi**: sono elencati gli spazi di indirizzi assegnati alla rete virtuale. Per informazioni su come aggiungere e rimuovere gli spazi di indirizzi, completare la procedura descritta nella sezione [Aggiungere e rimuovere spazi di indirizzi](#address-spaces) di questo articolo.
    - **Dispositivi connessi**: sono visualizzate tutte le risorse connesse alla rete virtuale. Nell'esempio illustrato nella figura precedente tre interfacce di rete e un bilanciamento del carico sono connessi alla rete virtuale. Sono elencate le nuove risorse creare e connesse alla rete virtuale. Se si elimina una risorsa connessa alla rete virtuale, non è più visualizzata nell'elenco.
    - **Subnet**: un elenco delle subnet che si trovano all'interno della rete virtuale. Per informazioni su come aggiungere e rimuovere subnet, leggere le sezioni [Aggiungere subnet](virtual-network-manage-subnet.md#create-subnet) ed [Eliminare subnet](virtual-network-manage-subnet.md#delete-subnet) dell'articolo [Creare, modificare o eliminare le subnet di rete virtuale](virtual-network-manage-subnet.md).
    - **Server DNS**: è possibile specificare se il server DNS interno di Azure o il server DNS personalizzato fornisce la risoluzione dei nomi per i dispositivi connessi alla rete virtuale. Quando si crea una rete virtuale tramite il portale di Azure, per la risoluzione dei nomi all'interno di una rete virtuale vengono usati i server DNS di Azure per impostazione predefinita. Per modificare i server DNS, completare i passaggi della sezione [Aggiungere, modificare o eliminare server DNS](#dns-servers) di questo articolo. 
    - **Peering**: se sono presenti peering nella sottoscrizione, sono elencati qui. È possibile visualizzare le impostazioni per i peering esistenti o creare, modificare ed eliminare peering. Per altre informazioni sui peering, vedere l'articolo [Panoramica del peering](virtual-network-peering-overview.md).
    - **Proprietà**: visualizza le impostazioni della rete virtuale, inclusi l'ID risorsa della rete virtuale e la sottoscrizione in cui si trova.
    - **Diagramma**: il diagramma fornisce una rappresentazione visiva di tutti i dispositivi connessi alla rete virtuale con alcune informazioni chiave sui dispositivi stessi. È possibile fare clic su uno dei dispositivi per gestirlo direttamente tramite questa vista.
    - **Impostazioni comuni di Azure**: per altre informazioni sulle impostazioni comuni di Azure, vedere gli articoli [Log attività](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs), [Controllo di accesso (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control), [Tag](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags), [Blocchi](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Script di automazione](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group).

**Comandi**

|**Strumento**|**Comando**|
|---|---|
|CLI|[az network vnet show](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVirtualNetwork](/powershell/resourcemanager/azurerm.network/v3.8.0/get-azurermvirtualnetwork/?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="address-spaces"></a>Aggiungere e rimuovere spazi di indirizzi

È possibile aggiungere e rimuovere spazi di indirizzi da una rete virtuale. Gli spazi di indirizzi devono essere specificati nella notazione CIDR e non possono sovrapporsi all'interno della stessa rete virtuale. Gli spazi di indirizzi che si definiscono possono essere pubblici o privati (RFC 1918). Che si definiscano spazi di indirizzi pubblici o privati, gli spazi di indirizzi sono raggiungibili solo dall'interno della rete virtuale, da reti virtuali interconnesse e da qualsiasi rete locale connessa alla rete virtuale. Non è possibile aggiungere gli spazi di indirizzi seguenti:
    - 224.0.0.0/4 (multicast)
    - 255.255.255.255/32 (broadcast)
    - 127.0.0.0/8 (loopback)
    - 169.254.0.0/16 (locale rispetto al collegamento)
    - 168.63.129.16/32 (DNS interno)

1. Accedere al [portale](https://portal.azure.com) con un account che abbia almeno le autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione di ruoli e autorizzazioni agli account.
2. Nella finestra che contiene il testo *Cerca risorse*, nella parte superiore del portale di Azure, digitare *reti virtuali*. Fare clic su **Reti virtuali** quando viene visualizzato nei risultati della ricerca.
3. Nel pannello **Reti virtuali** che viene visualizzato fare clic sulla rete virtuale a cui aggiungere o da cui rimuovere uno spazio di indirizzi.
4. Nel pannello che viene visualizzato per la rete virtuale selezionata fare clic su **Spazio di indirizzi** nella sezione **IMPOSTAZIONI**.
5. Completare una delle seguenti opzioni nel pannello che viene visualizzato con gli spazi di indirizzi:
    - **Aggiungere uno spazio di indirizzi**: immettere il nuovo spazio di indirizzi nella casella. Lo spazio di indirizzi non può sovrapporsi con uno spazio di indirizzi esistente definito per la rete virtuale.
    - **Rimuovere uno spazio di indirizzi**: fare clic con il pulsante destro del mouse su uno spazio di indirizzi e quindi fare clic su **Rimuovi**. Se è presente una subnet nello spazio di indirizzi, non è possibile rimuovere lo spazio di indirizzi. Prima di rimuovere uno spazio di indirizzi, devono essere eliminate tutte le subnet presenti nello spazio stesso (e tutte le risorse connesse alle subnet).
6. Fare clic su **Salva**.

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|Solo Resource Manager|[az network vnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="dns-servers"></a>Aggiungere, modificare o eliminare server DNS

Tutte le VM connesse alla rete virtuale sono registrate con i server DNS specificati per la rete virtuale e usano il server DNS per la risoluzione dei nomi. Ogni scheda di interfaccia di rete di una VM può avere le proprie impostazioni per i server DNS. Se una scheda di interfaccia di rete ha le proprie impostazioni per i server DNS, queste sostituiscono le impostazioni di server DNS per la rete virtuale. Per altre informazioni sulle impostazioni DNS della scheda di interfaccia di rete, vedere l'articolo sulle [attività e le impostazioni dell'interfaccia di rete](virtual-network-network-interface.md#dns). Per altre informazioni sulla risoluzione dei nomi per le VM e le istanze del ruolo di Servizi cloud, vedere l'articolo [Risoluzione dei nomi per le macchine virtuali e le istanze del ruolo](virtual-networks-name-resolution-for-vms-and-role-instances.md).

1. Accedere al [portale](https://portal.azure.com) con un account che abbia almeno le autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione di ruoli e autorizzazioni agli account.
2. Nella finestra che contiene il testo *Cerca risorse*, nella parte superiore del portale di Azure, digitare *reti virtuali*. Fare clic su **Reti virtuali** quando viene visualizzato nei risultati della ricerca. 
3. Nel pannello **Reti virtuali** che viene visualizzato fare clic sulla rete virtuale di cui modificare le impostazioni DNS.
4. Nel pannello che viene visualizzato per la rete virtuale selezionata fare clic su **Server DNS** nella sezione **IMPOSTAZIONI**.
5. Selezionare una delle seguenti opzioni nel pannello che viene visualizzato con i server DNS:
    - **Valore predefinito (fornito da Azure)**: tutti i nomi delle risorse e gli indirizzi IP privati sono registrati automaticamente sui server DNS di Azure. È possibile risolvere i nomi tra le risorse connesse alla stessa rete virtuale. Non è possibile usare questa opzione per la risoluzione dei nomi tra le reti virtuali. Per risolvere i nomi tra le reti virtuali è necessario usare un server DNS personalizzato.
    - **Personalizzato**: è possibile aggiungere uno o più server, fino al limite di Azure per le reti virtuali. Per altre informazioni sui limiti dei server DNS, vedere l'articolo relativo ai [limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic). L'utente ha a disposizione le seguenti opzioni:
        - **Aggiungere un indirizzo**: viene aggiunto un server DNS all'elenco dei server DNS della rete virtuale e tale server viene registrato in Azure. Se un server DNS è stato registrato in precedenza con Azure, è possibile selezionarlo nell'elenco che viene visualizzato. 
        - **Rimuovere un indirizzo**: fare clic sulla **X** accanto al server che si desidera rimuovere. Eliminando il server lo si rimuove semplicemente da questo elenco di reti virtuali. Il server DNS rimane registrato in Azure e utilizzabile dalle altre reti virtuali. 
        - **Riordinare gli indirizzi dei server DNS**: è importante verificare che siano elencati nell'ordine corretto per l'ambiente. Gli elenchi dei server DNS non supportano il round robin. Vengono usati nell'ordine in cui sono specificati. Se il primo server DNS nell'elenco è raggiungibile, il client usa tale server DNS indipendentemente dal fatto che funzioni correttamente o meno. Rimuovere tutti i server DNS elencati e quindi aggiungerli di nuovo nell'ordine desiderato.
        - **Cambiare un indirizzo**: evidenziare il server DNS nell'elenco e quindi digitare il nuovo nome.
6. Fare clic su **Salva**.
7. Riavviare le VM connesse alla rete virtuale in modo che vengano loro assegnate le nuove impostazioni di server DNS. Le VM continuano a usare le impostazioni DNS correnti finché non vengono riavviate.

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az network vnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="delete-vnet"></a>Eliminare una rete virtuale

È possibile eliminare una rete virtuale solo se non sono presenti risorse connesse a essa. Se sono presenti risorse connesse a una qualsiasi subnet all'interno della rete virtuale, è necessario innanzitutto eliminare le risorse connesse a tutte le subnet interne alla rete virtuale. Le istruzioni per eliminare una risorsa variano in base alla risorsa. Per informazioni su come eliminare le risorse connesse a una subnet, leggere la documentazione per ogni tipo di risorsa che si vuole eliminare. Per eliminare una rete virtuale, seguire questa procedura:

1. Accedere al [portale](https://portal.azure.com) con un account che abbia almeno le autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione di ruoli e autorizzazioni agli account.
2. Nella finestra che contiene il testo *Cerca risorse*, nella parte superiore del portale di Azure, digitare *reti virtuali*. Fare clic su **Reti virtuali** quando viene visualizzato nei risultati della ricerca.
3. Nel pannello **Reti virtuali** che viene visualizzato fare clic sulla rete virtuale che si vuole eliminare.
4. Verificare che non ci siano dispositivi connessi alla rete virtuale facendo clic su **Dispositivi connessi** nella sezione **IMPOSTAZIONI** del pannello che viene visualizzato per la rete virtuale selezionata. Se sono presenti dispositivi connessi, è necessario innanzitutto eliminarli per poter eliminare la rete virtuale.  Se non sono presenti dispositivi connessi, fare clic su **Panoramica** nel pannello.
5. Fare clic sull'icona **Elimina** nella parte superiore del pannello. 
6. Fare clic su **Sì** per confermare l'eliminazione della rete virtuale.


**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[azure network vnet delete](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetwork](/powershell/module/azurerm.network/remove-azurermvirtualnetwork?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="next-steps"></a>Passaggi successivi

- Per creare una VM e connetterla a una rete virtuale, leggere l'articolo su come [creare una rete virtuale e connettere le VM](virtual-network-get-started-vnet-subnet.md#a-namecreate-vmsacreate-virtual-machines).
- Per filtrare il traffico di rete tra subnet all'interno di una rete virtuale, leggere l'articolo [Creare gruppi di sicurezza di rete](virtual-networks-create-nsg-arm-pportal.md).
- Per eseguire il peering di una rete virtuale con un'altra rete virtuale, leggere l'articolo [Creare un peering reti virtuali](virtual-networks-create-vnetpeering-arm-portal.md#peering-vnets-in-the-same-subscription).
- Per altre informazioni sulle opzioni per la connessione di una rete virtuale a una rete locale, leggere l'articolo [About network gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#a-namediagramsaconnection-topology-diagrams) (Informazioni sul gateway di rete).

