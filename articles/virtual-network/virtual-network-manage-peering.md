---
title: Creare, modificare o eliminare un peering reti virtuali di Azure | Microsoft Docs
description: Informazioni su come creare, modificare o eliminare un peering reti virtuali.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: jdial;anavin
ms.openlocfilehash: 8fa7496c3489a0a3e5ee6d829bbeef0f0ccdf315
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>Creare, modificare o eliminare un peering reti virtuali

Informazioni su come creare, modificare o eliminare un peering reti virtuali. Il peering di rete virtuale consente di connettere le reti virtuali tramite la rete backbone di Azure. Dopo aver eseguito il peering, le reti virtuali vengono ancora gestite come risorse separate. Se non si ha familiarità con il peering reti virtuali, è consigliabile leggere [Panoramica del peering reti virtuali](virtual-network-peering-overview.md) e completare l'esercitazione [Create a virtual network peering](tutorial-connect-virtual-networks-portal.md) (Creare un peering reti virtuali), prima di completare le attività di questo articolo.

Il peering reti virtuali nella stessa area è disponibile a livello generale. Il peering di reti virtuali in aree diverse è attualmente disponibile in anteprima. Vedere gli [aggiornamenti delle reti virtuali](https://azure.microsoft.com/updates/?product=virtual-network) per le aree disponibili. È necessario [registrare la sottoscrizione per l'anteprima](tutorial-connect-virtual-networks-powershell.md#register).

> [!WARNING]
> I peering reti virtuali creati in questo scenario potrebbero non avere lo stesso livello di disponibilità e affidabilità di quelli creati in scenari di rilascio con disponibilità generale. I peering reti virtuali possono presentare funzionalità limitate e potrebbero non essere disponibili in tutte le aree di Azure. Per ricevere le notifiche più aggiornate su disponibilità e stato della funzionalità, vedere la pagina [Aggiornamenti della rete virtuale di Azure](https://azure.microsoft.com/updates/?product=virtual-network) .
>

## <a name="before-you-begin"></a>Prima di iniziare

Prima di completare i passaggi di qualsiasi sezione di questo articolo, eseguire le attività seguenti:

- Se non si ha un account Azure, registrarsi per ottenere un [account per la versione di prova gratuita](https://azure.microsoft.com/free).
- Se si usa il portale, aprire https://portal.azure.com e accedere con l'account Azure.
- Se si usano i comandi di PowerShell per completare le attività in questo articolo, eseguire i comandi in [Azure Cloud Shell](https://shell.azure.com/powershell) o tramite PowerShell dal computer in uso. Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. Questa esercitazione richiede il modulo di Azure PowerShell 5.2.0 o versioni successive. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzureRmAccount` per creare una connessione con Azure.
- Se si usano i comandi dell'interfaccia della riga di comando di Azure per completare le attività in questo articolo, eseguire i comandi in [Azure Cloud Shell](https://shell.azure.com/bash) o tramite l'interfaccia della riga di comando dal computer in uso. Questa esercitazione richiede l'interfaccia della riga di comando di Azure versione 2.0.26 o successive. Eseguire `az --version` per trovare la versione installata. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli). Se si esegue l'interfaccia della riga di comando di Azure in locale, è anche necessario eseguire `az login` per creare una connessione con Azure.

## <a name="create-a-peering"></a>Creare un peering

>[!NOTE]
>Prima di creare un peering, assicurarsi di avere acquisito familiarità con i [requisiti e i vincoli](#requirements-and-constraints) e con le [autorizzazioni necessarie](#permissions).
>

1. Nella casella di ricerca nella parte superiore del portale immettere *reti virtuali*. Selezionare **Reti virtuali** quando viene visualizzato nei risultati della ricerca. Non selezionare **Reti virtuali (versione classica)** se è visualizzato nell'elenco, perché non è possibile creare un peering da una rete virtuale distribuita tramite il modello di distribuzione classica.
2. Nell'elenco selezionare la rete virtuale per cui si vuole creare un peering.
3. Nell'elenco delle reti virtuali selezionare la rete virtuale per cui si vuole creare un peering.
4. In **Impostazioni** selezionare **Peer**.
5. Selezionare **+ Aggiungi**. 
6. <a name="add-peering"></a>Immettere o selezionare i valori per le impostazioni seguenti:
    - **Nome**: il nome per il peering deve essere univoco all'interno della rete virtuale.
    - **Modello di distribuzione della rete virtuale:** selezionare il modello di distribuzione tramite il quale è stata distribuita la rete virtuale con cui si vuole eseguire il peering.
    - **Conosco l'ID della risorsa:** se si ha accesso in lettura alla rete virtuale con la quale si vuole eseguire il peering, lasciare deselezionate questa casella di controllo. Se non si ha accesso in lettura alla rete virtuale o alla sottoscrizione con la quale si vuole eseguire il peering, selezionare questa casella. Immettere l'ID risorsa completo della rete virtuale con la quale si vuole eseguire il peering nella casella **ID risorsa** visualizzata quando è stata selezionata la casella. L'ID risorsa immesso deve essere quello di una rete virtuale che esiste nella stessa [regione](https://azure.microsoft.com/regions) di Azure della rete virtuale. Se si desidera selezionare una rete virtuale in un'area diversa, [registrare la sottoscrizione nell'anteprima.](tutorial-connect-virtual-networks-portal.md) L'ID risorsa completo è simile a /subscriptions/<Id>/resourceGroups/<nome-gruppo-di-risorse>/providers/Microsoft.Network/virtualNetworks/<nome-rete-virtuale>. È possibile ottenere l'ID risorsa per una rete virtuale visualizzandone le proprietà. Per informazioni su come visualizzare le proprietà per una rete virtuale, vedere [Gestire le reti virtuali](manage-virtual-network.md#view-virtual-networks-and-settings).
    - **Sottoscrizione:** selezionare la [sottoscrizione](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) della rete virtuale con la quale si vuole eseguire il peering. Il numero di sottoscrizioni che vengono elencate dipende dal numero di sottoscrizioni a cui l'account ha accesso in lettura. Se si è selezionata la casella di controllo **ID risorsa**, questa impostazione non è disponibile.
    - **Rete virtuale:** selezionare la rete virtuale con cui si vuole eseguire il peering. È possibile selezionare una rete virtuale creata tramite un modello di distribuzione di Azure. Se si desidera selezionare una rete virtuale in un'area diversa, [registrare la sottoscrizione nell'anteprima.](tutorial-connect-virtual-networks-portal.md) È necessario avere accesso in lettura alla rete virtuale perché venga visualizzata nell'elenco. Se una rete virtuale è elencata, ma disattivata, la causa potrebbe essere la sovrapposizione dello spazio indirizzi per la rete virtuale con lo spazio indirizzi per questa rete virtuale. Se gli spazi indirizzi delle reti virtuali si sovrappongono, non è possibile eseguire il peering. Se si è selezionata la casella di controllo **ID risorsa**, questa impostazione non è disponibile.
    - **Consenti accesso alla rete virtuale:** selezionare **Abilitata** (impostazione predefinita) per abilitare la comunicazione tra le due reti virtuali. L'abilitazione della comunicazione tra reti virtuali consente alle risorse connesse a una o all'altra delle reti virtuali di comunicare tra loro con la stessa larghezza di banda e latenza che userebbero se fossero connesse alla stessa rete virtuale. Tutte le comunicazioni tra le risorse nelle due reti virtuali avvengono tramite la rete privata di Azure. Il tag predefinito **VirtualNetwork** per i gruppi di sicurezza di rete comprende la rete virtuale e la rete virtuale con peering. Per altre informazioni sui tag predefiniti dei gruppi di sicurezza di rete, vedere l'articolo [Panoramica dei gruppi di sicurezza di rete](virtual-networks-nsg.md#default-tags).  Selezionare **Disabilitata** se non si vuole inviare il flusso del traffico alla rete virtuale con peering. È possibile selezionare **Disabilitata** se si è eseguito il peering di una rete virtuale con un'altra rete virtuale, ma occasionalmente si vuole disabilitare il flusso del traffico tra le due reti virtuali. L'abilitazione/disabilitazione può risultare più pratica che eliminare e ricreare i peering. Quando questa impostazione è disabilitata, il traffico non viene trasmesso tra le due reti virtuali con peering.
    - **Consenti traffico inoltrato:** selezionare questa casella per consentire al traffico *inoltrato* da un'appliance virtuale di rete a una rete virtuale (ma che non ha avuto origine dalla rete virtuale) di raggiungere la rete virtuale tramite un peering. Si considerino ad esempio tre reti virtuali denominate Spoke1, Spoke2 e Hub. Tra ognuna delle reti virtuali Spoke e la rete virtuale Hub esiste un peering, che però non esiste tra le reti virtuali Spoke. Un'appliance virtuale di rete viene distribuita nella rete virtuale Hub e route definite dall'utente vengono applicate a ogni rete virtuale Spoke che instrada il traffico tra le subnet attraverso l'appliance virtuale di rete. Se questa casella di controllo non è selezionata per il peering tra ognuna delle reti virtuali spoke e la rete virtuale hub, il traffico non transita tra le reti virtuali spoke, perché l'hub inoltra il traffico tra le reti virtuali. L'abilitazione di questa funzionalità consente il traffico inoltrato attraverso il peering, tuttavia non crea route definite dall'utente né appliance virtuali di rete. Le route definite dall'utente e le appliance virtuali di rete vengono create separatamente. Informazioni sulle [route definite dall'utente](virtual-networks-udr-overview.md#user-defined). Non è necessario selezionare questa impostazione se il traffico viene inoltrato tra le reti virtuali tramite un Gateway VPN di Azure.
    - **Consenti transito gateway:** selezionare questa casella se un gateway di rete virtuale è collegato a questa rete virtuale e si vuole consentire il flusso attraverso il gateway del traffico dalla rete virtuale con peering. Questa rete virtuale, ad esempio, potrebbe essere collegata a una rete locale tramite un gateway di rete virtuale. Il gateway può essere un gateway ExpressRoute o VPN. Selezionando questa casella, si consente il flusso del traffico verso la rete locale dalla rete virtuale con peering attraverso il gateway collegato alla rete virtuale stessa. Se si seleziona questa casella, la rete virtuale con peering non può avere un gateway configurato. Quando si configura il peering dall'altra rete virtuale a questa, per la rete virtuale con peering la casella di controllo **Usa gateway remoti** deve essere selezionata. Se si lascia questa casella deselezionata (impostazione predefinita), il traffico dalla rete virtuale con peering viene ancora trasmesso a questa rete virtuale, ma non può essere trasmesso attraverso un gateway di rete virtuale collegato a questa rete virtuale. 
    
    Oltre a inoltrare il traffico a una rete locale, un gateway VPN può inoltrare il traffico di rete tra reti virtuali con peering con la rete virtuale in cui si trova il gateway, senza che sia necessario eseguire il peering delle reti virtuali tra loro. Ciò è utile quando si vuole usare un gateway VPN in una rete virtuale hub (vedere l'esempio relativo a reti hub e spoke descritto per **Consenti traffico inoltrato**) per instradare il traffico tra reti virtuali spoke senza peering tra loro. Altre informazioni sui [gateway di rete virtuale](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti). Questo scenario richiede l'implementazione di route definite dall'utente che specificano il gateway di rete virtuale come tipo di hop successivo. Informazioni sulle [route definite dall'utente](virtual-networks-udr-overview.md#user-defined). In una route definita dall'utente è possibile specificare solo un gateway VPN come tipo di hop successivo. Non è possibile specificare un gateway ExpressRoute come tipo di hop successivo in una route definita dall'utente.

        You cannot enable this option if you're peering a virtual network (Resource Manager) with a virtual network (classic). Though the traffic flows between the two virtual networks, the virtual network (classic) traffic cannot flow through a network gateway attached to the virtual network (Resource Manager). 

    - **Usa gateway remoti:** selezionare questa casella per consentire il flusso del traffico da questa rete virtuale attraverso un gateway di rete virtuale collegato alla rete virtuale con cui si sta eseguendo il peering. Ad esempio, la rete virtuale con cui si sta eseguendo il peering ha un gateway VPN collegato che consente la comunicazione con una rete locale.  Selezionando questa casella, si consente il flusso del traffico da questa rete virtuale attraverso il gateway VPN collegato alla rete virtuale con peering. Se si seleziona questa casella, alla rete virtuale con peering deve essere collegato un gateway di rete virtuale e la casella di controllo **Consenti transito gateway** deve essere selezionata. Se si lascia questa casella deselezionata (impostazione predefinita), il traffico dalla rete virtuale con peering può ancora essere trasmesso a questa rete virtuale, ma non può essere trasmesso attraverso un gateway di rete virtuale collegato a questa rete virtuale. 
Per questa rete virtuale questa impostazione può essere abilitata per un solo peering.
Non è possibile usare questa impostazione se nella rete virtuale è già configurato un gateway.
        Non è possibile abilitare questa opzione se si esegue il peering di una rete virtuale (Resource Manager) con una rete virtuale (versione classica). Anche se il traffico viene trasmesso tra le due reti virtuali, il traffico della rete virtuale (Resource Manager) non può essere trasmesso attraverso un gateway di rete collegato alla rete virtuale (versione classica).

7. Fare clic su **OK** per aggiungere la subnet alla rete virtuale selezionata.

### <a name="commands"></a>Comandi:

- Interfaccia della riga di comando di Azure: [az network vnet peering create](/cli/azure/network/vnet/peering#create)
- PowerShell: [Add-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering)

### <a name="scenarios"></a>Scenari

Un peering di reti virtuali viene generato tra reti virtuali create tramite modelli di distribuzione uguali o diversi esistenti in sottoscrizioni uguali o diverse. Completare un'esercitazione dettagliata per uno degli scenari seguenti:
 
|Modello di distribuzione di Azure  | Sottoscrizione  |
|---------|---------|
|Entrambi Resource Manager |[Uguale](tutorial-connect-virtual-networks-portal.md)|
| |[Diversa](create-peering-different-subscriptions.md)|
|Uno di Resource Manager, uno della versione classica     |[Uguale](create-peering-different-deployment-models.md)|
| |[Diversa](create-peering-different-deployment-models-subscriptions.md)|

## <a name="view-or-change-peering-settings"></a>Visualizzare o modificare le impostazioni dei peering

1. Nella casella di ricerca nella parte superiore del portale immettere *reti virtuali*. Selezionare **Reti virtuali** quando viene visualizzato nei risultati della ricerca. Non selezionare **Reti virtuali (versione classica)** se è visualizzato nell'elenco, perché non è possibile creare un peering da una rete virtuale distribuita tramite il modello di distribuzione classica.
2. Nell'elenco selezionare la rete virtuale per cui si vogliono modificare le impostazioni di peering.
3. Nell'elenco delle reti virtuali selezionare la rete virtuale per cui si vogliono modificare le impostazioni di peering.
4. In **Impostazioni** selezionare **Peer**.
5. Fare clic sul peering di cui si vogliono visualizzare o modificare le impostazioni.
6. Modificare l'impostazione appropriata. Per informazioni sulle opzioni per ogni impostazione, vedere il [passaggio 6](#add-peering) in Creare un peering. 

    >[!NOTE]
    >Prima di creare un peering, assicurarsi di avere acquisito familiarità con i [requisiti e i vincoli](#requirements-and-constraints) e con le [autorizzazioni necessarie](#permissions).
    >

7. Fare clic su **Save**.

**Comandi**

Interfaccia della riga di comando di Azure: [az network vnet peering list](/cli/azure/network/vnet/peering#az_network_vnet_peering_list) per elencare i peering di una rete virtuale, [az network vnet peering show](/cli/azure/network/vnet/peering#az_network_vnet_peering_show) per visualizzare le impostazioni di uno specifico peering e [az network vnet peering update](/cli/azure/network/vnet/peering#az_network_vnet_peering_update) per modificare le impostazioni di un peering.
- PowerShell: [Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering) per recuperare le impostazioni di peering e [Set-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/set-azurermvirtualnetworkpeering) per modificare le impostazioni.

## <a name="delete-a-peering"></a>Eliminare un peering

Quando un peering viene eliminato, il traffico da una rete virtuale non viene più trasmesso alla rete virtuale con peering. Quando viene eseguito il peering di reti virtuali distribuite tramite Resource Manager, ogni rete virtuale ha un peering all'altra rete virtuale. L'eliminazione del peering da una rete virtuale, anche se disabilita la comunicazione tra le reti virtuali, non elimina il peering dall'altra rete virtuale. Lo stato del peering che esiste nell'altra rete virtuale è **Disconnesso**. È possibile ricreare il peering solo dopo aver ricreato il peering nella prima rete virtuale e dopo che lo stato del peering per entrambe le reti virtuali viene impostato su *Connesso*. 

Per far comunicare le reti virtuali non sempre, ma solo in alcuni casi, invece di eliminare un peering, è possibile configurare l'impostazione **Consenti accesso alla rete virtuale** su **Disabilitato**. Per la procedura, vedere il passaggio 6 della sezione [Creare un peering](#create-peering) di questo articolo. Disabilitare e abilitare l'accesso alla rete può risultare più facile che eliminare e ricreare i peering.

1. Nella casella di ricerca nella parte superiore del portale immettere *reti virtuali*. Selezionare **Reti virtuali** quando viene visualizzato nei risultati della ricerca. Non selezionare **Reti virtuali (versione classica)** se è visualizzato nell'elenco, perché non è possibile creare un peering da una rete virtuale distribuita tramite il modello di distribuzione classica.
2. Nell'elenco selezionare la rete virtuale per cui si vuole eliminare un peering.
3. Nell'elenco delle reti virtuali selezionare la rete virtuale per cui si vuole eliminare un peering.
4. In **Impostazioni** selezionare **Peer**.
5. Sul lato destro del peering da eliminare, selezionare **...**, **Elimina** e quindi **Sì** per eliminare il peering dalla prima rete virtuale.
6. Completare la procedura precedente per eliminare il peering dall'altra rete virtuale nel peering.

**Comandi**

- Interfaccia della riga di comando di Azure: [az network vnet peering delete](/cli/azure/network/vnet/peering#az_network_vnet_peering_delete)
- PowerShell: [Remove-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/remove-azurermvirtualnetworkpeering)

## <a name="requirements-and-constraints"></a>Requisiti e vincoli 

- Le reti virtuali di cui si esegue il peering non devono avere spazi di indirizzi IP sovrapposti.
- Non è possibile aggiungere o eliminare intervalli di indirizzi nello spazio indirizzi di una rete virtuale dopo che ne è stato eseguito il peering con un'altra rete virtuale. Per aggiungere o rimuovere intervalli di indirizzi, eliminare il peering, aggiungere o rimuovere gli intervalli di indirizzi e quindi ricreare il peering. Per aggiungere o rimuovere intervalli di indirizzi in reti virtuali, vedere [Creare, modificare o eliminare una rete virtuale](manage-virtual-network.md).
- È possibile eseguire il peering di due reti virtuali distribuite tramite Resource Manager o di una rete virtuale distribuita tramite Resource Manager con una rete virtuale distribuita tramite il modello di distribuzione classica. Non è possibile eseguire il peering di due reti virtuali create tramite il modello di distribuzione classica. Se non si ha familiarità con i modelli di distribuzione di Azure, vedere l'articolo [Informazioni sui modelli di distribuzione di Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). È possibile usare un [gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) per collegare due reti virtuali create tramite il modello di distribuzione classica.
- Quando si esegue il peering di due reti virtuali create tramite Resource Manager, deve essere configurato un peering per ogni rete virtuale nel peering. 
    - *Avviato:* quando si crea il peering alla seconda rete virtuale dalla prima rete virtuale, lo stato del peering è *Avviato*. 
    - *Connesso:* quando si crea il peering dalla seconda rete virtuale alla prima rete virtuale, lo stato del peering è *Connesso*. Se si visualizza lo stato del peering per la prima rete virtuale, si osserva che lo stato è cambiato da *Avviato* a *Connesso*. Il peering è stabilito correttamente solo dopo che lo stato del peering per entrambe le reti virtuali è *Connesso*.
- Quando si esegue il peering di una rete virtuale creata tramite Resource Manager con una rete virtuale creata tramite il modello di distribuzione classica, si configura solo un peering per la rete virtuale distribuita tramite Resource Manager. Non è possibile configurare il peering per una rete virtuale (versione classica) o tra due reti virtuali distribuite tramite il modello di distribuzione classica. Quando si crea il peering dalla rete virtuale (Resource Manager) alla rete virtuale (versione classica), lo stato del peering è *Aggiornamento* e dopo poco diventa *Connesso*.
- Un peering viene stabilito tra due reti virtuali. I peering non sono transitivi. Se si creano peering tra:
    - VirtualNetwork1 e VirtualNetwork2
    - VirtualNetwork2 e VirtualNetwork3

  Non vengono stabiliti peering tra la VirtualNetwork1 e la VirtualNetwork3 tramite la VirtualNetwork2. Se si desidera creare un peering delle reti virtuali tra VirtualNetwork1 e VirtualNetwork3, è necessario creare un peering tra VirtualNetwork1 e VirtualNetwork3.
- Non è possibile risolvere i nomi nelle reti virtuali con peering usando la risoluzione dei nomi di Azure predefinita. Per risolvere i nomi in altre reti virtuali, è necessario usare un server DNS personalizzato. Per informazioni su come configurare il server DNS, vedere l'articolo [Risoluzione dei nomi usando il server DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).
- Le risorse in entrambe le reti virtuali del peering possono comunicare tra loro con la stessa larghezza di banda e latenza che userebbero se fossero nella stessa rete virtuale. Ogni dimensione di macchina virtuale presenta tuttavia una specifica larghezza di banda di rete massima. Per altre informazioni sulla larghezza di banda di rete massima per le diverse dimensioni delle macchine virtuali, vedere gli articoli sulle dimensioni delle macchine virtuali [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- È possibile eseguire il peering delle reti virtuali distribuite tramite Resource Manager, che sono nella stessa sottoscrizione o in sottoscrizioni diverse.
- È possibile eseguire il peering di reti virtuali distribuite tramite modelli di distribuzione diversi, che si trovano nella stessa sottoscrizione o in sottoscrizioni differenti. 
- Le sottoscrizione in cui si trovano entrambe le reti virtuali devono essere associate allo stesso tenant di Azure Active Directory. Se non si ha già un tenant di AD, è possibile [crearne uno](../active-directory/develop/active-directory-howto-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#start-from-scratch) rapidamente. È possibile usare un [Gateway VPN ](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) per connettere due reti virtuali esistenti in diverse sottoscrizioni associate a tenant differenti di Active Directory.
- È possibile eseguire il peering di una rete virtuale a un'altra rete virtuale e anche connettere una rete virtuale a un'altra con un gateway di rete virtuale di Azure. Quando le reti virtuali vengono connesse sia tramite il peering che tramite un gateway, il traffico tra le reti virtuali segue la configurazione del peering invece che il gateway.
- È previsto un importo minimo per il traffico in ingresso e in uscita che usa un peering di rete virtuale. Per altre informazioni vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="permissions"></a>Autorizzazioni

Gli account usati per creare un peering di rete virtuale devono disporre del ruolo o delle autorizzazioni necessarie. Ad esempio, per eseguire il peering di due reti virtuali denominate myVNetA e myVNetB, è necessario assegnare all'account il ruolo minimo o le autorizzazioni minime seguenti per ogni rete virtuale:
    
|Rete virtuale|Modello di distribuzione|Ruolo|Autorizzazioni|
|---|---|---|---|
|myVnetA|Gestione risorse|[Collaboratore di rete](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Classico|[Collaboratore reti virtuali classiche](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/D|
|myVnetB|Gestione risorse|[Collaboratore di rete](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Classico|[Collaboratore reti virtuali classiche](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Altre informazioni sui [ruoli predefiniti](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) e sull'assegnazione di autorizzazioni specifiche ai [ruoli personalizzati](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (solo Resource Manager).

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come creare una [topologia di rete hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering)
