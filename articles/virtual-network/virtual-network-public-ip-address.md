---
title: Creare, modificare o eliminare un indirizzo IP pubblico di Azure | Microsoft Docs
description: Informazioni su come creare, modificare o eliminare un indirizzo IP pubblico.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: jdial
ms.translationtype: HT
ms.sourcegitcommit: 7bf5d568e59ead343ff2c976b310de79a998673b
ms.openlocfilehash: 41e4fe685f38a6a19a2024a12198cb8a8566182e
ms.contentlocale: it-it
ms.lasthandoff: 08/01/2017

---

# <a name="create-change-or-delete-a-public-ip-address"></a>Creare, modificare o eliminare un indirizzo IP pubblico

Informazioni su un indirizzo IP pubblico e su come crearlo, modificarlo ed eliminarlo. Un indirizzo IP pubblico è una risorsa con impostazioni proprie configurabili. L'assegnazione di un indirizzo IP pubblico ad altre risorse di Azure abilita le funzionalità seguenti:
- Connettività Internet in ingresso a risorse quali macchine virtuali di Azure, set di scalabilità di macchine virtuali di Azure, gateway VPN di Azure, gateway applicazione e servizi di bilanciamento del carico di Azure con connessione Internet. Le risorse di Azure non possono ricevere comunicazioni in ingresso da Internet senza un indirizzo IP pubblico assegnato. Mentre alcune risorse di Azure sono intrinsecamente accessibili tramite indirizzi IP pubblici, altre devono avere indirizzi IP pubblici assegnati perché siano accessibili da Internet.
- Connettività in uscita verso Internet tramite un indirizzo IP prevedibile. Una macchina virtuale può ad esempio comunicare in uscita con Internet senza avere un indirizzo IP pubblico assegnato: il relativo indirizzo sarà l'indirizzo di rete convertito da Azure in un indirizzo pubblico non prevedibile. L'assegnazione di un indirizzo IP pubblico a una risorsa consente di conoscere l'indirizzo IP usato per la connessione in uscita. Anche se prevedibile, l'indirizzo può essere modificato, a seconda del metodo di assegnazione scelto. Per altre informazioni, vedere [Creare un indirizzo IP pubblico](#create-a-public-ip-address). Per altre informazioni sulle connessioni in uscita dalle risorse di Azure, leggere l'articolo [Informazioni sulle connessioni in uscita](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="before-you-begin"></a>Prima di iniziare

Prima di completare qualsiasi altro passaggio nelle altre sezioni di questo articolo, eseguire le operazioni seguenti:

- Leggere l'articolo [Limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) per informazioni sui limiti per gli indirizzi IP pubblici.
- Accedere al [portale](https://portal.azure.com) di Azure, all'interfaccia della riga di comando di Azure oppure ad Azure PowerShell con un account Azure. Se non si ha un account Azure, registrarsi per ottenere un [account per la versione di prova gratuita](https://azure.microsoft.com/free).
- Se si usano comandi di PowerShell per completare le attività di questo articolo, [installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Assicurarsi che sia installata la versione più recente dei cmdlet di Azure PowerShell. Per informazioni ed esempi relativi ai comandi di PowerShell, digitare `get-help <command> -full`.
- Se si usano comandi dell'interfaccia della riga di comando di Azure per completare le attività di questo articolo, [installare e configurare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Assicurarsi che sia installata la versione più recente dell'interfaccia della riga di comando di Azure. Per informazioni sui comandi dell'interfaccia della riga di comando, digitare `az <command> --help`. Invece di installare l'interfaccia della riga di comando e i rispettivi prerequisiti, è possibile usare Azure Cloud Shell. Azure Cloud Shell è una shell Bash gratuita che può essere eseguita direttamente nel portale di Azure. Include l'interfaccia della riga di comando di Azure preinstallata e configurata per l'uso con l'account. Per usare Cloud Shell, fare clic sul pulsante Cloud Shell **>_** nella parte superiore del [portale](https://portal.azure.com).

Per gli indirizzi IP pubblici è previsto un addebito nominale. Per visualizzare i prezzi, consultare la pagina [Prezzi per gli indirizzi IP](https://azure.microsoft.com/pricing/details/ip-addresses). 

## <a name="create-a-public-ip-address"></a>Creare un indirizzo IP pubblico

1. Accedere al [portale di Azure](https://portal.azure.com) con un account avente almeno le autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Leggere l'articolo sui [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione dei ruoli e le autorizzazioni degli account.
2. Nella finestra che contiene il testo *Cerca risorse*, nella parte superiore del portale di Azure, digitare *indirizzo ip pubblico*. Selezionare la voce **Indirizzi IP pubblici** visualizzata nei risultati della ricerca.
3. Fare clic su **+ Aggiungi** nel pannello **Indirizzi IP pubblici** visualizzato.
4. Immettere o selezionare i valori delle impostazioni seguenti nel pannello **Crea indirizzo IP pubblico**, quindi fare clic su **Crea**:

    |Impostazione|Obbligatorio?|Dettagli|
    |---|---|---|
    |Nome|Sì|Il nome deve essere univoco all'interno del gruppo di risorse selezionato.|
    |Versione indirizzo IP|Sì| Selezionare IPv4 o IPv6. Mentre gli indirizzi IPv4 pubblici possono essere assegnati a diverse risorse di Azure, un indirizzo IP pubblico IPv6 può essere assegnato solo a un servizio di bilanciamento del carico con connessione Internet. Il servizio di bilanciamento del carico può bilanciare il carico del traffico IPv6 verso le macchine virtuali di Azure. Altre informazioni sul [bilanciamento del carico del traffico IPv6 verso le macchine virtuali](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    |Assegnazione indirizzi IP|Sì|**Dinamico**: gli indirizzi dinamici vengono assegnati solo dopo che l'indirizzo IP pubblico è stato associato a una scheda di interfaccia di rete collegata a una macchina virtuale e che tale macchina virtuale è stata avviata per la prima volta. Gli indirizzi dinamici possono cambiare se la scheda di interfaccia di rete alla quale è collegata la macchina virtuale viene arrestata (deallocata). L'indirizzo rimane invariato se la macchina virtuale viene riavviata o arrestata, ma non deallocata. **Statico**: gli indirizzi statici vengono assegnati al momento della creazione dell'indirizzo IP pubblico. Non cambiano anche se la macchina virtuale viene arrestata (deallocata). L'indirizzo viene rilasciato solo quando viene eliminata la scheda di interfaccia di rete. È possibile modificare il metodo di assegnazione dopo aver creato la l'interfaccia di rete. Se si seleziona IPv6 in **Versione indirizzo IP**, il solo metodo di assegnazione disponibile è **Dinamico**.|
    |Timeout di inattività (minuti)|No|Il numero di minuti in cui la connessione TCP o HTTP resta aperta senza affidarsi ai client per l'invio di messaggi Keep-Alive. Se si seleziona IPv6 in **Versione indirizzo IP**, questo valore non può essere modificato. |
    |Etichetta del nome DNS|No|Deve essere univoca all'interno della località di Azure nella quale viene creato il nome (incluse tutte le sottoscrizioni e tutti i clienti). Il servizio DNS pubblico di Azure registra automaticamente il nome e l'indirizzo IP, consentendo la connessione a una risorsa con tale nome. Per creare il nome DNS completo, Azure aggiunge *location.cloudapp.azure.com* al nome specificato, dove location è la località selezionata. Se si sceglie di creare entrambe le versioni dell'indirizzo, lo stesso nome DNS viene assegnato sia all'indirizzo IPv4 che all'indirizzo IPv6. Il servizio DNS di Azure contiene i record dei nomi AAAA sia per IPv4 che per IPv6 e risponde con entrambi i record quando viene eseguita una ricerca per il nome DNS. Il client sceglie con quale indirizzo comunicare, IPv4 o IPv6.|
    |Crea un indirizzo IPv6 (o IPv4)|No| A seconda del valore selezionato in **Versione indirizzo IP**, viene visualizzato l'indirizzo IPv6 o IPv4. Se ad esempio si seleziona **IPv4** per **Versione indirizzo IP**, qui viene visualizzato **IPv6**.
    |Nome (visibile solo se è stata selezionata la casella di controllo **Crea un indirizzo IPv6 (o IPv4)**)|Sì, se si seleziona la casella di controllo **Crea un indirizzo IPv6** (o IPv4).|Il nome deve essere diverso da quello immesso come primo **nome** in questo elenco. Se si sceglie di creare sia un indirizzo IPv4 che un indirizzo IPv6, il portale crea due risorse indirizzo IP pubblico separate, a ognuna delle quali è assegnata una versione dell'indirizzo IP.|
    |Assegnazione indirizzi IP (visibile solo se è stata selezionata la casella di controllo **Crea un indirizzo IPv6 (o IPv4)**)|Sì, se si seleziona la casella di controllo **Crea un indirizzo IPv6** (o IPv4).|Se la casella di controllo visualizzata è **Crea un indirizzo IPv4**, è possibile selezionare un metodo di assegnazione. Se la casella di controllo visualizzata è **Crea un indirizzo IPv6**, non è possibile selezionare un metodo di assegnazione, perché deve essere **Dinamico**.|
    |Subscription|Sì|Deve essere inclusa nella stessa [sottoscrizione](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) della risorsa cui si vuole associare l'indirizzo IP pubblico.|
    |Gruppo di risorse|Sì|Può trovarsi nello stesso [gruppo di risorse](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) della risorsa cui si vuole associare l'indirizzo IP pubblico oppure in un gruppo di risorse diverso.|
    |Località|Sì|Deve trovarsi nella stessa [località](https://azure.microsoft.com/regions), chiamata anche area, di quella della risorsa cui si vuole associare l'indirizzo IP pubblico.|

**Comandi**

Anche se il portale consente di creare due risorse indirizzo IP pubblico (IPv4 e IPv6), i comandi dell'interfaccia della riga di comando e di PowerShell seguenti creano una sola risorsa con un indirizzo per una versione IP o per l'altra. Per creare due risorse indirizzo IP pubblico, una per ogni versione IP, è necessario eseguire il comando due volte, specificando nomi e versioni diverse per le risorse indirizzo IP pubblico. 

|Strumento|Comando|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>Visualizzare un indirizzo IP pubblico, modificarne le impostazioni o eliminarlo

1. Accedere al [portale di Azure](https://portal.azure.com) con un account avente almeno le autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Leggere l'articolo sui [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione dei ruoli e le autorizzazioni degli account.
2. Nella finestra che contiene il testo *Cerca risorse*, nella parte superiore del portale di Azure, digitare *indirizzo ip pubblico*. Selezionare la voce **Indirizzi IP pubblici** visualizzata nei risultati della ricerca.
3. Nel pannello **Indirizzi IP pubblici** fare clic sul nome dell'indirizzo IP pubblico da visualizzare, eliminare o di cui modificare le impostazioni.
4. Nel pannello dell'indirizzo IP pubblico visualizzato, completare l'opzione pertinente in base alla necessità di visualizzare, eliminare o modificare l'indirizzo IP pubblico.
    - **Visualizza**: la sezione **Panoramica** del pannello visualizza impostazioni chiave per l'indirizzo IP pubblico, quali l'interfaccia di rete alla quale è associato (se impostata). Il portale non visualizza la versione dell'indirizzo (IPv4 o IPv6). Per visualizzare le informazioni sulla versione, usare il comando di PowerShell o dell'interfaccia della riga di comando per visualizzare l'indirizzo IP pubblico. Se la versione dell'indirizzo IP è IPv6, l'indirizzo assegnato non viene visualizzato dal portale, da PowerShell o dall'interfaccia della riga di comando. 
    - **Elimina**: per eliminare l'indirizzo IP pubblico, fare clic su **Elimina** nella sezione **Panoramica** del pannello. Se l'indirizzo è associato a una configurazione IP, non può essere eliminato. Se l'indirizzo è associato a una configurazione, fare clic su **Annulla associazione** per annullare l'associazione dalla configurazione IP.
    - **Modifica**: fare clic su **Configurazione**. Modificare le impostazioni usando le informazioni fornite nel passaggio 4 della sezione [Creare un indirizzo IP pubblico](#create-a-public-ip-address) di questo articolo. Per modificare l'assegnazione per un indirizzo IPv4 da statico a dinamico, prima è necessario annullare l'associazione tra l'indirizzo IPv4 pubblico e la configurazione IP. È quindi possibile modificare il metodo di assegnazione in dinamico e fare clic su **Associa** per associare l'indirizzo IP alla stessa configurazione IP o a una configurazione diversa. È anche possibile lasciare l'indirizzo senza associazione. Per annullare l'associazione di un indirizzo IP pubblico, nella sezione **Panoramica** fare clic su **Annulla associazione**.

>[!WARNING]
>Quando si modifica il metodo di assegnazione da statico a dinamico, l'indirizzo IP assegnato all'indirizzo IP pubblico viene perso. Mentre i server DNS pubblici Azure mantengono l'associazione tra gli indirizzi statici o dinamici e qualsiasi etichetta del nome DNS (se ne è stato definito uno), un indirizzo IP dinamico può essere modificato quando la macchina virtuale viene avviata dopo essere stata arrestata (deallocata). Per evitare la modifica dell'indirizzo, assegnare un indirizzo IP statico.

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az network public-ip-list](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#list) per elencare gli indirizzi IP pubblici, [az network public-ip-show](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#show) per visualizzare le impostazioni; [az network public-ip update](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#update) per aggiornare; [az network public-ip delete](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#delete) per eliminare|
|PowerShell|[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) per recuperare un oggetto indirizzo IP pubblico e visualizzarne le impostazioni, [Set-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/set-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) per aggiornare le impostazioni; [Remove-AzureRmPublicIpAddress](/powershell/module/azurerm.network/remove-azurermpublicipaddress) per eliminare|

## <a name="next-steps"></a>Passaggi successivi
Assegnare gli indirizzi IP pubblici durante la creazione delle risorse di Azure seguenti:

- Macchine virtuali [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Servizio di bilanciamento del carico con connessione Internet](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Gateway applicazione Azure](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Connessione da sito a sito con gateway VPN di Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

