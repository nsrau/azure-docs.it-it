---
title: Creare, modificare o eliminare indirizzi IP pubblici di Azure | Microsoft Docs
description: Informazioni su come creare, modificare o eliminare indirizzi IP pubblici.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 430a68ff6a01a53774c1b7843ed774b2ec0d0d36
ms.contentlocale: it-it
ms.lasthandoff: 05/09/2017


---

# <a name="create-change-or-delete-public-ip-addresses"></a>Creare, modificare o eliminare indirizzi IP pubblici

Informazioni sugli indirizzi IP pubblici e su come crearli, modificarli ed eliminarli. Un indirizzo IP pubblico è una risorsa con impostazioni proprie configurabili. L'assegnazione di un indirizzo IP pubblico ad altre risorse di Azure abilita le funzionalità seguenti:
- Connettività Internet in ingresso a risorse quali macchine virtuali di Azure, set di scalabilità di macchine virtuali di Azure, gateway VPN di Azure e servizi di bilanciamento del carico di Azure con connessione Internet.
- Connettività in uscita a Internet non NAT (con indirizzi di rete convertiti). Una macchina virtuale può ad esempio comunicare in uscita con Internet senza avere un indirizzo IP pubblico assegnato: il relativo indirizzo sarà l'indirizzo di rete convertito da Azure. Per altre informazioni sulle connessioni in uscita dalle risorse di Azure, leggere l'articolo [Informazioni sulle connessioni in uscita](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Questo articolo descrive come usare indirizzi IP pubblici distribuiti tramite il modello di distribuzione Azure Resource Manager.

## <a name="before"></a>Prima di iniziare

Prima di completare qualsiasi passaggio nelle altre sezioni di questo articolo, eseguire le operazioni seguenti:

- Leggere l'articolo [Limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) per informazioni sui limiti per gli indirizzi IP pubblici.
- Accedere al portale di Azure, all'interfaccia della riga di comando di Azure o ad Azure PowerShell con un account Azure. Se non si possiede già un account Azure, iscriversi per ottenere un [account di valutazione gratuito](https://azure.microsoft.com/free).
- Se si sceglie di usare i comandi di PowerShell per completare le attività indicate in questo articolo, installare e configurare Azure PowerShell seguendo i passaggi riportati nell'articolo [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Assicurarsi che sia installata la versione più recente dei cmdlet di Azure PowerShell. Per informazioni ed esempi relativi ai comandi di PowerShell, digitare `get-help <command> -full`.
- Se si sceglie di usare i comandi dell'interfaccia della riga di comando di Azure per completare le attività indicate in questo articolo, installare e configurare l'interfaccia della riga di comando di Azure seguendo i passaggi riportati nell'articolo [Come installare e configurare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Assicurarsi che sia installata la versione più recente dell'interfaccia della riga di comando di Azure. Per informazioni sui comandi dell'interfaccia della riga di comando, digitare `az <command> --help`.

Per gli indirizzi IP pubblici è previsto un addebito nominale. Per visualizzare i prezzi, consultare la pagina [Prezzi per gli indirizzi IP](https://azure.microsoft.com/pricing/details/ip-addresses). 

## <a name="create"></a>Creare un indirizzo IP pubblico

1. Accedere al [portale di Azure](https://portal.azure.com) con un account avente almeno le autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Leggere l'articolo sui [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione dei ruoli e le autorizzazioni degli account.
2. Nella finestra che contiene il testo *Cerca risorse*, nella parte superiore del portale di Azure, digitare *indirizzo ip pubblico*. Selezionare la voce **Indirizzi IP pubblici** visualizzata nei risultati della ricerca.
3. Fare clic su **+ Aggiungi** nel pannello **Indirizzi IP pubblici** visualizzato.
4. Immettere o selezionare i valori delle impostazioni seguenti nel pannello **Crea indirizzo IP pubblico**, quindi fare clic su **Crea**:

    |Impostazione|Obbligatorio?|Dettagli|
    |---|---|---|
    |Nome|Sì|Il nome deve essere univoco all'interno del gruppo di risorse selezionato.|
    |Assegnazione indirizzi IP|Sì|**Dinamico**: gli indirizzi dinamici vengono assegnati solo dopo che l'indirizzo IP pubblico è stato associato a una scheda di interfaccia di rete collegata a una macchina virtuale e che tale macchina virtuale è stata avviata per la prima volta. Gli indirizzi dinamici possono cambiare se la scheda di interfaccia di rete alla quale è collegata la macchina virtuale viene arrestata (deallocata). L'indirizzo rimane invariato se la macchina virtuale viene riavviata o arrestata, ma non deallocata. **Statico**: gli indirizzi statici vengono assegnati al momento della creazione dell'indirizzo IP pubblico. Non cambiano anche se la macchina virtuale viene arrestata (deallocata). L'indirizzo viene rilasciato solo quando viene eliminata la scheda di interfaccia di rete. È possibile modificare il metodo di assegnazione dopo aver creato la l'interfaccia di rete.|
    |Timeout di inattività (minuti)|No|Il numero di minuti in cui la connessione TCP o HTTP resta aperta senza affidarsi ai client per l'invio di messaggi Keep-Alive.|
    |Etichetta del nome DNS|No|Deve essere univoca all'interno della località di Azure nella quale viene creato il nome (incluse tutte le sottoscrizioni e tutti i clienti). Il servizio DNS pubblico di Azure registra automaticamente il nome e l'indirizzo IP, consentendo la connessione a una risorsa con tale nome. Per creare il nome DNS completo, Azure aggiunge *location.cloudapp.azure.com* al nome specificato, dove location è la località selezionata.|
    |Sottoscrizione|Sì|Deve essere inclusa nella stessa [sottoscrizione](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) della risorsa cui si vuole associare l'indirizzo IP pubblico.|
    |Gruppo di risorse|Sì|Può trovarsi nello stesso [gruppo di risorse](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) della risorsa cui si vuole associare l'indirizzo IP pubblico oppure in un gruppo di risorse diverso.|
    |Località|Sì|Deve trovarsi nella stessa [località](https://azure.microsoft.com/regions), chiamata anche area, di quella della risorsa cui si vuole associare l'indirizzo IP pubblico.|

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az network public-ip-create](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)|

## <a name="change"></a>Modificare le impostazioni per un indirizzo IP pubblico o eliminarne uno

1. Accedere al [portale di Azure](https://portal.azure.com) con un account avente almeno le autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Leggere l'articolo sui [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione dei ruoli e le autorizzazioni degli account.
2. Nella finestra che contiene il testo *Cerca risorse*, nella parte superiore del portale di Azure, digitare *indirizzo ip pubblico*. Selezionare la voce **Indirizzi IP pubblici** visualizzata nei risultati della ricerca.
3. Nel pannello **Indirizzi IP pubblici**, fare clic sul nome dell'indirizzo IP pubblico del quale modificare o eliminare le impostazioni.
4. Nel pannello dell'indirizzo IP pubblico visualizzato, completare l'opzione pertinente in base alla necessità di eliminare o modificare l'indirizzo IP pubblico.
    - **Elimina**: per eliminare l'indirizzo IP pubblico, fare clic su **Elimina** nella sezione **Panoramica** del pannello. Se l'indirizzo è associato a una configurazione IP, non può essere eliminato. Se l'indirizzo è associato a una configurazione, fare clic su **Annulla associazione** per annullare l'associazione dalla configurazione IP.
    - **Modifica**: fare clic su **Configurazione**. Modificare le impostazioni usando le informazioni fornite nel passaggio 4 della sezione [Creare un indirizzo IP pubblico](#create) di questo articolo. Per modificare l'assegnazione da statico a dinamico, è necessario innanzitutto annullare l'associazione tra l'indirizzo IP pubblico e la configurazione IP. È quindi possibile modificare il metodo di assegnazione in dinamico e fare clic su **Associa** per associare l'indirizzo IP alla stessa configurazione IP o a una configurazione diversa. È anche possibile lasciare l'indirizzo senza associazione. Per annullare l'associazione di un indirizzo IP pubblico, nella sezione **Panoramica** fare clic su **Annulla associazione**.

>[!WARNING]
>Quando si modifica il metodo di assegnazione da statico a dinamico, l'indirizzo IP assegnato all'indirizzo IP pubblico viene perso. Mentre i server DNS pubblici Azure mantengono l'associazione tra gli indirizzi statici o dinamici e qualsiasi etichetta del nome DNS (se ne è stato definito uno), un indirizzo IP dinamico può essere modificato quando la macchina virtuale viene avviata dopo essere stata arrestata (deallocata). Per evitare la modifica dell'indirizzo, assegnare un indirizzo IP statico.

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az network public-ip update](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#update) per aggiornare; [az network public-ip delete](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#delete) per eliminare|
|PowerShell|[Set-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) per aggiornare; [Remove-AzureRmPublicIpAddress](/powershell/module/azurerm.network/remove-azurermpublicipaddress) per eliminare|

## <a name="next-steps"></a>Passaggi successivi
Assegnare gli indirizzi IP pubblici durante la creazione delle risorse di Azure seguenti:

- Macchine virtuali [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Servizio di bilanciamento del carico con connessione Internet](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Gateway applicazione Azure](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Connessione da sito a sito con gateway VPN di Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

