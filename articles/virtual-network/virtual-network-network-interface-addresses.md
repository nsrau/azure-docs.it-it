---
title: Configurare gli indirizzi IP per un'interfacia di rete di Azure | Microsoft Docs
description: Informazioni su come aggiungere, modificare e rimuovere indirizzi IP pubblici e privati per un'interfaccia di rete.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: jdial
ms.openlocfilehash: 637b380dacc91e4ad55044c1d92936be2435138d
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2018
---
# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>Aggiungere, modificare o rimuovere indirizzi IP per un'interfaccia di rete di Azure

Informazioni su come aggiungere, modificare e rimuovere indirizzi IP pubblici e privati per un'interfaccia di rete. Gli indirizzi IP privati assegnati a un'interfaccia di rete consentono a una macchina virtuale di comunicare con altre risorse in una rete virtuale di Azure e in reti connesse. Un indirizzo IP privato abilita inoltre le comunicazioni in uscita a Internet usando un indirizzo IP non prevedibile. Un [indirizzo IP pubblico](virtual-network-public-ip-address.md) assegnato a un'interfaccia di rete abilita le comunicazioni in ingresso a una macchina virtuale da Internet. L'indirizzo abilita inoltre le comunicazioni in uscita dalla macchina virtuale a Internet usando un indirizzo IP prevedibile. Per maggiori informazioni, vedere [Informazioni sulle connessioni in uscita in Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

Se è necessario creare, modificare o eliminare un'interfaccia di rete, leggere l'articolo [Gestire un'interfaccia di rete](virtual-network-network-interface.md). Se è necessario aggiungere o rimuovere interfacce di rete in una macchina virtuale, leggere l'articolo [Aggiungere o rimuovere interfacce di rete](virtual-network-network-interface-vm.md). 


## <a name="before-you-begin"></a>Prima di iniziare

Prima di completare qualsiasi passaggio nelle altre sezioni di questo articolo, eseguire le operazioni seguenti:

- Per informazioni sui limiti per gli indirizzi IP pubblici e privati, leggere l'articolo relativo ai [limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Accedere al [portale](https://portal.azure.com) di Azure, all'interfaccia della riga di comando di Azure oppure ad Azure PowerShell con un account Azure. Se non si ha un account Azure, registrarsi per ottenere un [account per la versione di prova gratuita](https://azure.microsoft.com/free).
- Se si usano comandi di PowerShell per completare le attività di questo articolo, [installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Assicurarsi che sia installata la versione più recente dei cmdlet di Azure PowerShell. Per informazioni ed esempi relativi ai comandi di PowerShell, digitare `get-help <command> -full`.
- Se si usano comandi dell'interfaccia della riga di comando di Azure per completare le attività di questo articolo, [installare e configurare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Assicurarsi che sia installata la versione più recente dell'interfaccia della riga di comando di Azure. Per informazioni sui comandi dell'interfaccia della riga di comando, digitare `az <command> --help`. Invece di installare l'interfaccia della riga di comando e i rispettivi prerequisiti, è possibile usare Azure Cloud Shell. Azure Cloud Shell è una shell Bash gratuita che può essere eseguita direttamente nel portale di Azure. Include l'interfaccia della riga di comando di Azure preinstallata e configurata per l'uso con l'account. Per usare Cloud Shell, fare clic sul pulsante Cloud Shell **>_** nella parte superiore del [portale](https://portal.azure.com).

## <a name="add-ip-addresses"></a>Aggiungere indirizzi IP

È possibile aggiungere a un'interfaccia di rete il numero di indirizzi [IPv4](#ipv4) [privati](#private) e [pubblici](#public) necessari, entro i limiti elencati nell'articolo relativo ai [limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Non è possibile usare il portale per aggiungere un indirizzo IPv6 a un'interfaccia di rete esistente (sebbene sia possibile usare il portale per aggiungere un indirizzo IPv6 privato a un'interfaccia di rete quando la si crea). È possibile usare PowerShell o l'interfaccia della riga di comando per aggiungere un indirizzo IPv6 privato a una [configurazione IP secondaria](#secondary) (purché non siano presenti configurazioni IP secondarie) per un'interfaccia di rete esistente che non è collegata a una macchina virtuale. Non è possibile usare strumenti per aggiungere un indirizzo IPv6 pubblico a un'interfaccia di rete. Vedere [IPv6](#ipv6) per informazioni dettagliate sull'uso di indirizzi IPv6. 

1. Accedere al [portale di Azure](https://portal.azure.com) con un account avente almeno le autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Leggere l'articolo sui [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione dei ruoli e le autorizzazioni degli account.
2. Nella casella che contiene il testo *Cerca risorse* nella parte superiore del portale di Azure digitare *interfacce di rete*. Fare clic su **Interfacce di rete** quando viene visualizzato nei risultati della ricerca.
3. Nel pannello **Interfacce di rete** che viene visualizzato fare clic sull'interfaccia di rete per cui aggiungere l'indirizzo IPv4.
4. Fare clic su **Configurazioni IP** nella sezione **IMPOSTAZIONI** del pannello relativo all'interfaccia di rete selezionata.
5. Fare clic su **+ Aggiungi** nel pannello visualizzato per le configurazioni IP.
6. Specificare le impostazioni seguenti, quindi fare clic su **OK** per chiudere il pannello **Aggiungi configurazione IP**:

    |Impostazione|Obbligatorio?|Dettagli|
    |---|---|---|
    |Nome|Sì|Deve essere univoco per l'interfaccia di rete|
    |Tipo|Sì|Dato che si aggiunge una configurazione IP a un'interfaccia di rete esistente e ogni interfaccia di rete deve avere una configurazione IP [primaria](#primary), l'unica opzione possibile è **Secondaria**.|
    |Metodo di assegnazione di indirizzi IP privati|Sì|[**Dinamico**](#dynamic): Azure assegna l'indirizzo successivo disponibile per l'intervallo di indirizzi della subnet in cui viene distribuita l'interfaccia di rete. [**Statico**](#static): assegnare un indirizzo non usato per l'intervallo di indirizzi di subnet in cui viene distribuita l'interfaccia di rete.|
    |Indirizzo IP pubblico|No|**Disabilitato:** nessuna risorsa di indirizzo IP pubblico è attualmente associata alla configurazione IP. **Abilitato:** selezionare un indirizzo IPv4 pubblico esistente o crearne uno nuovo. Per informazioni su come creare un indirizzo IP pubblico, vedere l'articolo [Indirizzi IP](virtual-network-public-ip-address.md#create-a-public-ip-address).|
7. Aggiungere manualmente gli indirizzi IP privati secondari al sistema operativo della macchina virtuale seguendo le istruzioni disponibili nell'articolo [Assegnare più indirizzi IP ai sistemi operativi della macchina virtuale](virtual-network-multiple-ip-addresses-portal.md#os-config). Vedere indirizzi IP [privati](#private) per alcune considerazioni specifiche prima di aggiungere manualmente gli indirizzi IP a un sistema operativo della macchina virtuale. Non aggiungere indirizzi IP pubblici al sistema operativo della macchina virtuale.

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az network nic ip-config create](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[Add-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/add-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-ip-address-settings"></a>Modificare le impostazioni degli indirizzi IP

Può essere necessario modificare il metodo di assegnazione di un indirizzo IPv4, modificare l'indirizzo IPv4 statico o modificare l'indirizzo IP pubblico assegnato a un'interfaccia di rete. Se si modifica l'indirizzo IPv4 privato di una configurazione IP secondaria associata a un'interfaccia di rete secondaria in una macchina virtuale (ulteriori informazioni sulle [interfacce di rete principali e secondarie](virtual-network-network-interface-vm.md)), mettere la macchina virutale in stato di arresto (deallocato) prima di completare i passaggi seguenti: 

1. Accedere al [portale di Azure](https://portal.azure.com) con un account avente almeno le autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Leggere l'articolo sui [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione dei ruoli e le autorizzazioni degli account.
2. Nella casella che contiene il testo *Cerca risorse* nella parte superiore del portale di Azure digitare *interfacce di rete*. Fare clic su **Interfacce di rete** quando viene visualizzato nei risultati della ricerca.
3. Nel pannello **Interfacce di rete** visualizzato fare clic sull'interfaccia da visualizzare o per la quale modificare le impostazioni dell'indirizzo IP.
4. Fare clic su **Configurazioni IP** nella sezione **IMPOSTAZIONI** del pannello relativo all'interfaccia di rete selezionata.
5. Fare clic sulla configurazione IP da modificare nel pannello visualizzato per le configurazioni IP.
6. Modificare le impostazioni in base alle esigenze usando le informazioni sulle impostazioni descritte al passaggio 6 della sezione [Aggiungere una configurazione IP](#create-ip-config) di questo articolo. Fare clic su **Salva** per chiudere il pannello relativo alla configurazione IP modificata.

>[!NOTE]
>Se l'interfaccia di rete primaria ha più configurazioni IP e si modifica l'indirizzo IP privato della configurazione IP primaria, in Windows è necessario riassegnare manualmente gli indirizzi IP primari e secondari all'interfaccia di rete. Questa operazione non è necessaria in Linux. Per assegnare manualmente gli indirizzi IP a un'interfaccia di rete in un sistema operativo, vedere l'articolo [Assegnare più indirizzi IP alle macchine virtuali](virtual-network-multiple-ip-addresses-portal.md#os-config). Vedere indirizzi IP [privati](#private) per alcune considerazioni specifiche prima di aggiungere manualmente gli indirizzi IP a un sistema operativo della macchina virtuale. Non aggiungere indirizzi IP pubblici al sistema operativo della macchina virtuale.

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRMNetworkInterfaceIpConfig](/powershell/module/azurerm.network/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-ip-addresses"></a>Rimuovere indirizzi IP

È possibile rimuovere indirizzi IP [privati](#private) e [pubblici](#public) da un'interfaccia di rete, ma un'interfaccia di rete deve sempre avere almeno un indirizzo IPv4 privato assegnato.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account avente almeno le autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Leggere l'articolo sui [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione dei ruoli e le autorizzazioni degli account.
2. Nella casella che contiene il testo *Cerca risorse* nella parte superiore del portale di Azure digitare *interfacce di rete*. Fare clic su **Interfacce di rete** quando viene visualizzato nei risultati della ricerca.
3. Nel pannello **Interfacce di rete** che viene visualizzato fare clic sull'interfaccia di rete da cui rimuovere gli indirizzi IP.
4. Fare clic su **Configurazioni IP** nella sezione **IMPOSTAZIONI** del pannello relativo all'interfaccia di rete selezionata.
5. Fare clic con il pulsante destro del mouse su una configurazione IP [secondaria](#secondary) (non è possibile eliminare la configurazione [primaria](#primary)) da eliminare, fare clic su **Elimina** e quindi su **Sì** per confermare l'eliminazione. Se la configurazione aveva una risorsa di indirizzo IP pubblico associata, la risorsa verrà dissociata dalla configurazione IP, ma non eliminata.
6. Chiudere il pannello **Configurazioni IP**.

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az network nic ip-config delete](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/remove-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="ip-configurations"></a>Configurazioni IP

Gli indirizzi IP [privati](#private) e (facoltativamente) [pubblici](#public) vengono assegnati a uno o più configurazioni IP assegnate a un'interfaccia di rete. Esistono due tipi di configurazioni IP:

### <a name="primary"></a>Primaria

Ogni interfaccia di rete viene assegnata a una configurazione IP primaria. Una configurazione IP primaria:

- ha un indirizzo [IPv4](#ipv4) [privato](#private) assegnato. Non è possibile assegnare un indirizzo [IPv6](#ipv6) privato a una configurazione IP primaria.
- Può avere anche un indirizzo IPv4 [pubblico](#public) assegnato. Non è possibile assegnare un indirizzo IPv6 pubblico a una configurazione IP primaria o secondaria. È tuttavia possibile assegnare un indirizzo IPv6 pubblico a un servizio di bilanciamento del carico di Azure, che consente di caricare il traffico di bilanciamento all'indirizzo IPv6 privato di una macchina virtuale. Per altre informazioni, vedere [Dettagli e le limitazioni per IPv6](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations).

### <a name="secondary"></a>Secondario

Oltre a una configurazione IP primaria, un'interfaccia di rete può avere più configurazioni IP secondarie assegnate oppure nessuna. Una configurazione IP secondaria:

- Deve avere un indirizzo IPv4 o IPv6 privato assegnato. Se l'indirizzo è IPv6, l'interfaccia di rete può avere solo una configurazione IP secondaria. Se l'indirizzo è IPv4, l'interfaccia di rete può avere più configurazioni IP secondarie assegnate. Per altre informazioni su quanti gli indirizzi IPv4 pubblici e privati possono essere assegnati a un'interfaccia di rete, vedere l'articolo [limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).  
- Può anche avere un indirizzo IPv4 pubblico assegnato, se l'indirizzo IP privato è IPv4. Se l'indirizzo IP privato è IPv6, non è possibile assegnare un indirizzo IPv4 o IPv6 alla configurazione IP. L'assegnazione di più indirizzi IP a un'interfaccia di rete è utile per gli scenari seguenti:
    - Ospitare più siti Web o servizi con indirizzi IP e certificati SSL diversi in un singolo server.
    - Una macchina virtuale che funge da appliance virtuale di rete, ad esempio un firewall o un servizio di bilanciamento del carico.
    - La possibilità di aggiungere qualsiasi indirizzo IPv4 privato per qualsiasi interfaccia di rete a un pool di back-end di Azure Load Balancer. In passato, era possibile aggiungere a un pool di back-end solo l'indirizzo IPv4 primario per l'interfaccia di rete primaria. Per altre informazioni su come bilanciare il carico di più configurazioni IPv4, leggere l'articolo [Bilanciamento del carico in più configurazioni IP](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 
    - La possibilità di bilanciare il carico di un indirizzo IPv6 assegnato a un'interfaccia di rete. Per altre informazioni su come bilanciare il carico a un indirizzo IPv6 privato, vedere l'articolo [Panoramica di IPv6 per Azure Load Balancer](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).


## <a name="address-types"></a>Tipi di indirizzi

È possibile assegnare i tipi seguenti di indirizzi IP per una [configurazione IP](#ip-configurations):

### <a name="private"></a>Privato

Gli indirizzi [IPv4](#ipv4) privati consentono a una macchina virtuale di comunicare con altre risorse in una rete virtuale o in altre reti connesse. Una macchina virtuale non può comunicare in ingresso, né può comunicare in uscita con una indirizzo [IPv6](#ipv6) privato. Tuttavia, vi è un'eccezione. Una macchina virtuale può comunicare con Azure Load Balancer usando un indirizzo IPv6. Per altre informazioni, vedere [Dettagli e le limitazioni per IPv6](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations). 

Per impostazione predefinita, i server DHCP di Azure assegnano l'indirizzo IPv4 privato per la [configurazione IP primaria](#primary) dell'interfaccia di rete di Azure all'interfaccia di rete all'interno del sistema operativo della macchina virtuale. A meno che non sia necessario, non impostare mai manualmente l'indirizzo IP di un'interfaccia di rete all'interno del sistema operativo della macchina virtuale. 

> [!WARNING]
> Se l'indirizzo IPv4 impostato come indirizzo IP primario di un'interfaccia di rete all'interno del sistema operativo della macchina virtuale è sempre diverso dall'indirizzo IPv4 privato assegnato alla configurazione IP primaria dell'interfaccia di rete primaria associata a una macchina virtuale in Azure, si perde la connessione alla macchina virtuale.

In alcuni scenari è necessario impostare manualmente l'indirizzo IP di un'interfaccia di rete all'interno del sistema operativo della macchina virtuale. Ad esempio, è necessario impostare manualmente gli indirizzi IP primari e secondari di un sistema operativo Windows quando si aggiungono più indirizzi IP a una macchina virtuale di Azure. Per una macchina virtuale Linux, potrebbe essere necessario impostare manualmente solo gli indirizzi IP secondari. Vedere [Add IP addresses to a VM operating system](virtual-network-multiple-ip-addresses-portal.md#os-config) (Aggiungere indirizzi IP a un sistema operativo VM) per maggiori dettagli. Se è necessario modificare l'indirizzo assegnato a una configurazione IP, è consigliabile:

1. Assicurarsi che la macchina virtuale riceva un indirizzo dai server DHCP di Azure. Ripristinare quindi l'assegnazione dell'indirizzo IP a DHCP all'interno del sistema operativo e riavviare la macchina virtuale.
2. Arrestare (deallocare) la macchina virtuale.
3. Modificare l'indirizzo IP per la configurazione IP all'interno di Azure.
4. Avviare la macchina virtuale.
5. [Configurare manualmente](virtual-network-multiple-ip-addresses-portal.md#os-config) gli indirizzi IP secondari all'interno del sistema operativo (e anche l'indirizzo IP primario all'interno di Windows), in modo che corrisponda alle impostazioni di Azure.
 
Seguendo i passaggi precedenti, l'indirizzo IP privato assegnato all'interfaccia di rete all'interno di Azure corrisponderà a quello del sistema operativo della macchina virtuale. Per tenere traccia delle macchine virtuali nella sottoscrizione per cui sono stati impostati manualmente gli indirizzi IP all'interno di un sistema operativo, è possibile aggiungere una [tag](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags) di Azure alle macchine virtuali. È possibile usare ad esempio "Assegnazione indirizzi IP: statica". In questo modo, è possibile trovare facilmente le macchine virtuali nella sottoscrizione per cui impostare manualmente l'indirizzo IP all'interno del sistema operativo.

Oltre ad abilitare la comunicazione di una macchina virtuale con altre risorse all'interno della stessa rete virtuale o di reti virtuali connesse, un indirizzo IP privato consente inoltre a una macchina virtuale di comunicare in uscita a Internet. Le connessioni in uscita sono indirizzi di rete di origine traslati da Azure in un indirizzo IP pubblico non prevedibile. Per altre informazioni sulla connettività Internet in uscita di Azure, vedere l'articolo [Informazioni sulle connessioni in uscita in Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Non è possibile comunicare verso l'interno con l'indirizzo IP privato di una macchina virtuale da Internet. Se le connessioni in uscita richiedono un indirizzo IP pubblico prevedibile, associare una risorsa di indirizzo IP pubblico a un'interfaccia di rete.

### <a name="public"></a>Pubblico

Gli indirizzi IP pubblici assegnati tramite una risorsa di indirizzo IP pubblico consentono la connettività in ingresso verso una macchina virtuale da Internet. Le connessioni in uscita a Internet sfruttano un indirizzo IP prevedibile. Per maggiori informazioni, vedere [Informazioni sulle connessioni in uscita in Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). È possibile assegnare un indirizzo IP pubblico a una configurazione IP, ma non è necessario. Se non si assegna un indirizzo IP pubblico a una macchina virtuale associando una risorsa di indirizzo IP pubblico, la macchina virtuale può comunque comunicare in uscita a Internet. In questo caso, l'indirizzo IP privato è un indirizzo di rete di origine traslato da Azure in un indirizzo IP pubblico non prevedibile. Per altre informazioni sulle risorse di indirizzi IP pubblici, vedere [Risorsa indirizzo IP pubblico](virtual-network-public-ip-address.md).

È previsto un limite al numero di indirizzi IP pubblici e privati che possono essere assegnati a un'interfaccia di rete. Per informazioni dettagliate, vedere l'articolo relativo ai [limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

> [!NOTE]
> Azure trasla l'indirizzo IP privato di una macchina virtuale a un indirizzo IP pubblico. Di conseguenza, il sistema operativo della macchina virtuale non è a conoscenza di eventuali indirizzi IP pubblici assegnati, pertanto non è necessario assegnare manualmente un indirizzo IP pubblico all'interno del sistema operativo.

## <a name="assignment-methods"></a>Metodi di assegnazione

È possibile assegnare indirizzi IP pubblici e privati usando uno dei seguenti metodi di assegnazione:

### <a name="dynamic"></a>Dinamico

Gli indirizzi IPv4 e (facoltativamente) IPv6 privati dinamici vengono assegnati per impostazione predefinita. 

- **Solo pubblico**: Azure assegna gli indirizzi da un intervallo univoco a ogni area di Azure. Per informazioni sull'assegnazione degli intervalli a ogni area, vedere [Intervalli IP del data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). L'indirizzo può cambiare quando una macchina virtuale viene arrestata (deallocata), quindi avviata nuovamente. Nessuno dei due metodi di assegnazione consente di assegnare un indirizzo IPv6 pubblico a una configurazione IP.
- **Solo privato**: Azure riserva i primi quattro indirizzi dell'intervallo di indirizzi di ogni subnet e non li assegna. Azure assegna a una risorsa l'indirizzo disponibile successivo dell'intervallo di indirizzi della subnet. Ad esempio, se l'intervallo di indirizzi della subnet è 10.0.0.0/16 e gli indirizzi 10.0.0.0.4-10.0.0.14 sono già assegnati (quelli da .0 a .3 sono riservati), Azure assegna alla risorsa l'indirizzo 10.0.0.15. Il metodo di allocazione predefinito è quello dinamico. Dopo che sono stati assegnati, gli indirizzi IP dinamici vengono rilasciati solo se un'interfaccia di rete viene eliminata o assegnata a un'altra subnet della stessa rete virtuale oppure se il metodo di allocazione viene modificato in statico e viene specificato un diverso indirizzo IP. Quando si modifica il metodo di allocazione da dinamico a statico, per impostazione predefinita Azure assegna l'indirizzo assegnato dinamicamente precedente come indirizzo statico. È possibile assegnare solo un indirizzo IPv6 privato usando il metodo di assegnazione dinamica.

### <a name="static"></a>Statico

Facoltativamente, è possibile assegnare un indirizzo IPv4 statico pubblico o privato a una configurazione IP. Non è possibile assegnare un indirizzo IPv6 statico pubblico o privato a una configurazione IP. Per altre informazioni su come Azure assegna gli indirizzi IPv4 pubblici statici, vedere l'articolo [Indirizzo IP pubblico](virtual-network-public-ip-address.md).

- **Solo pubblico**: Azure assegna gli indirizzi da un intervallo univoco a ogni area di Azure. Per informazioni sull'assegnazione degli intervalli a ogni area, vedere [Intervalli IP del data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). L'indirizzo non cambia finché non viene eliminata la risorsa di indirizzo IP pubblico a cui è stato assegnato o il metodo di assegnazione non diventa dinamico. Se la risorsa di indirizzo IP pubblico è associata a una configurazione IP, prima di modificare il metodo di assegnazione è necessario annullare l'associazione della configurazione IP.
- **Solo privato**: si seleziona e si assegna un indirizzo dell'intervallo di indirizzi della subnet. È possibile assegnare qualsiasi indirizzo nell'intervallo di indirizzi della subnet che non sia uno dei primi quattro indirizzi di tale intervallo e non sia attualmente assegnato ad altre risorse nella subnet. Gli indirizzi statici vengono rilasciati solo in caso di eliminazione di un'interfaccia di rete. Se si modifica il metodo di allocazione in statico, Azure assegna dinamicamente l'indirizzo IP statico assegnato in precedenza come indirizzo dinamico, anche se non è l'indirizzo disponibile successivo nell'intervallo di indirizzi della subnet. L'indirizzo viene modificato anche in caso di assegnazione dell'interfaccia di rete a un'altra subnet nella stessa rete virtuale, ma per assegnare l'interfaccia di rete a un'altra subnet è prima necessario modificare il metodo di allocazione da statico a dinamico. Dopo aver assegnato l'interfaccia di rete a un'altra subnet, è possibile modificare il metodo di allocazione di nuovo in statico e assegnare un indirizzo IP dell'intervallo di indirizzi della nuova subnet.

## <a name="ip-address-versions"></a>Versioni di indirizzi IP

Durante l'assegnazione di indirizzi è possibile specificare le seguenti versioni:

### <a name="ipv4"></a>IPv4

Ogni interfaccia di rete deve avere una configurazione IP [primaria](#primary) a cui è assegnato un indirizzo [IPv4](#ipv4) [privato](#private). È possibile aggiungere una o più configurazioni IP [secondarie](#secondary) che dispongono di un indirizzo IPv4 privato e (facoltativamente) un indirizzo IPv4 [pubblico](#public).

### <a name="ipv6"></a>IPv6

È possibile assegnare uno o nessun indirizzo [IPv6](#ipv6) privato a una configurazione IP secondaria di un'interfaccia di rete. L'interfaccia di rete non può avere configurazioni IP secondarie esistenti. Non è possibile aggiungere una configurazione IP con un indirizzo IPv6 tramite il portale. Per aggiungere una configurazione IP con un indirizzo IPv6 privato a un'interfaccia di rete esistente, usare PowerShell o l'interfaccia della riga di comando. Non è possibile collegare l'interfaccia di rete a una macchina virtuale esistente.

> [!NOTE]
> Anche se è possibile creare un'interfaccia di rete con un indirizzo IPv6 tramite il portale, il portale non consente di aggiungere un'interfaccia di rete esistente a una macchina virtuale nuova o esistente. Usare PowerShell o l'interfaccia della riga di comando di Azure 2.0 per creare un'interfaccia di rete con un indirizzo IPv6 privato e quindi collegare l'interfaccia di rete durante la creazione di una macchina virtuale. Non è possibile collegare un'interfaccia di rete con un indirizzo IPv6 privato ad essa assegnato in una macchina virtuale esistente. Non è possibile aggiungere un indirizzo IPv6 privato a una configurazione IP per qualsiasi interfaccia di rete associata a una macchina virtuale usando gli strumenti (portale, interfaccia della riga di comando o PowerShell).

Non è possibile assegnare un indirizzo IPv6 pubblico a una configurazione IP primaria o secondaria.

## <a name="skus"></a>SKU

Viene creato un indirizzo IP pubblico con lo SKU di base o standard.  Per altre informazioni sulle differenze tra gli SKU, vedere [Gestire gli indirizzi IP pubblici](virtual-network-public-ip-address.md).

> [!NOTE]
> Quando si assegna un indirizzo IP pubblico con SKU Standard all'interfaccia di rete di una macchina virtuale, è necessario consentire in modo esplicito il traffico previsto con un [gruppo di sicurezza di rete](security-overview.md#network-security-groups). La comunicazione con la risorsa non riesce finché non si crea e si associa un gruppo di sicurezza di rete e si consente in modo esplicito il traffico desiderato.

## <a name="next-steps"></a>Passaggi successivi
Per creare una macchina virtuale con diverse configurazioni IP, vedere gli articoli seguenti:

|Attività|Strumento|
|---|---|
|Creare una macchina virtuale con più NIC|[Interfaccia della riga di comando](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Creare una macchina virtuale con una singola scheda di interfaccia di rete e più indirizzi IPv4|[Interfaccia della riga di comando](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Creare una macchina virtuale con una singola scheda di interfaccia di rete con un indirizzo IPv6 privato (dietro un Azure Load Balancer)|[Interfaccia della riga di comando](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Modello di Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
