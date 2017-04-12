---
title: Indirizzi IP pubblici di Azure | Documentazione Microsoft
description: Informazioni su come creare, modificare ed eliminare indirizzi IP pubblici.
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
ms.date: 03/14/2017
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 5274ffe641768e0725623c2acf38d432a97eea9f
ms.lasthandoff: 04/03/2017


---

# <a name="public-ip-addresses"></a>Indirizzi IP pubblici

Informazioni sugli indirizzi IP pubblici e su come crearli, modificarli ed eliminarli. Un indirizzo IP pubblico è una risorsa con impostazioni proprie configurabili. L'assegnazione di un indirizzo IP pubblico ad altre risorse di Azure abilita le funzionalità seguenti:
- Connettività Internet in ingresso a risorse quali macchine virtuali di Azure, set di scalabilità di macchine virtuali di Azure, gateway VPN di Azure e servizi di bilanciamento del carico di Azure con connessione Internet.
- Connettività in uscita a Internet non NAT (con indirizzi di rete convertiti). Una macchina virtuale può ad esempio comunicare in uscita con Internet senza avere un indirizzo IP pubblico assegnato: il relativo indirizzo sarà l'indirizzo di rete convertito da Azure. Per altre informazioni sulle connessioni in uscita dalle risorse di Azure, leggere l'articolo [Informazioni sulle connessioni in uscita](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Questo articolo illustra come operare con gli indirizzi IP pubblici, e si applica alle risorse distribuite tramite il modello di distribuzione Azure Resource Manager. Sebbene gli indirizzi IP pubblici possano essere assegnati alle risorse distribuite tramite il modello di distribuzione classica, gli indirizzi vengono applicati in modo diverso rispetto a quanto accade con Azure Resource Manager. Per altre informazioni sulle differenze tra i due modelli, leggere l'articolo [Understand Azure deployment models](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Informazioni sui modelli di distribuzione di Azure).

Le sezioni seguenti di questo articolo illustrano come completare tutte le attività correlate agli indirizzi IP pubblici. Ogni sezione elenca:
- La procedura per completare l'attività nel portale di Azure. Per completare la procedura è necessario accedere al [portale di Azure](http://portal.azure.com). È possibile iscriversi per [ottenere un account di valutazione gratuito](https://azure.microsoft.com/free), se non se ne ha già uno.
- Comandi per completare l'attività usando Azure PowerShell con collegamenti al riferimento per il comando. Installare e configurare Azure PowerShell eseguendo i passaggi descritti nell'articolo sull'[installazione e la configurazione di Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Per informazioni ed esempi relativi ai comandi di PowerShell, digitare `get-help <command> -full`.
- Comandi per completare l'attività usando l'interfaccia della riga di comando di Azure con collegamenti al riferimento per il comando. Per installare l'interfaccia della riga di comando di Azure, seguire la procedura riportata nell'articolo sull'[installazione e la configurazione dell'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Per informazioni sui comandi dell'interfaccia della riga di comando, digitare `az <command> -h`.

Per gli indirizzi IP pubblici è previsto un addebito nominale. Per visualizzare i prezzi, consultare la pagina [Prezzi per gli indirizzi IP](https://azure.microsoft.com/pricing/details/ip-addresses). È previsto un limite al numero di indirizzi IP pubblici che possono essere usati in una sottoscrizione. Per informazioni su tali limiti, vedere l'articolo [Limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). 

Completare le procedure delle sezioni seguenti per creare, modificare o eliminare gli indirizzi IP pubblici.

## <a name="create"></a>Creare un indirizzo IP pubblico

Per creare un indirizzo IP pubblico, attenersi alla procedura seguente:
1. Accedere al [portale di Azure](https://portal.azure.com) con un account che disponga almeno delle autorizzazioni per il ruolo di Collaboratore rete per la sottoscrizione. Leggere l'articolo sui [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione dei ruoli e le autorizzazioni degli account.
2. Nella finestra che contiene il testo *Cerca risorse*, nella parte superiore del portale di Azure, digitare *indirizzo ip pubblico*. Selezionare la voce **Indirizzi IP pubblici** visualizzata nei risultati della ricerca.
3. Fare clic su **+ Aggiungi** nel pannello **Indirizzi IP pubblici** visualizzato.
4. Immettere o selezionare i valori delle impostazioni seguenti nel pannello **Crea indirizzo IP pubblico**, quindi fare clic su **Crea**:

    |**Impostazione**|Obbligatorio?|**Dettagli**|
    |---|---|---|
    |**Nome**|Sì|Il nome deve essere univoco all'interno del gruppo di risorse selezionato.|
    |**Assegnazione indirizzi IP**|Sì|**Dinamico**: gli indirizzi dinamici vengono assegnati solo dopo che l'indirizzo IP pubblico è stato associato a una scheda di interfaccia di rete collegata a una macchina virtuale e che tale macchina virtuale è stata avviata per la prima volta. Gli indirizzi dinamici possono cambiare se la scheda di interfaccia di rete alla quale è collegata la macchina virtuale viene arrestata (deallocata). L'indirizzo rimane invariato se la macchina virtuale viene riavviata o arrestata, ma non deallocata. **Statico**: gli indirizzi statici vengono assegnati al momento della creazione dell'indirizzo IP pubblico. Non cambiano anche se la macchina virtuale viene arrestata (deallocata). L'indirizzo viene rilasciato solo quando viene eliminata la scheda di interfaccia di rete. Dopo aver creato la scheda di rete, è possibile modificare il metodo di assegnazione.|
    |**Timeout di inattività (minuti)**|No|Il numero di minuti in cui la connessione TCP o HTTP resta aperta senza affidarsi ai client per l'invio di messaggi Keep-Alive.|
    |**Etichetta del nome DNS**|No|Deve essere univoca all'interno della località di Azure nella quale viene creato il nome (incluse tutte le sottoscrizioni e tutti i clienti). Il servizio DNS pubblico di Azure registra automaticamente il nome e l'indirizzo IP, consentendo la connessione a una risorsa con tale nome. Per creare il nome DNS completo, Azure aggiunge al nome specificato *location.cloudapp.azure.com*, in cui location indica la località selezionata. |
    |**Sottoscrizione**|Sì|Deve esistere nella stessa sottoscrizione della risorsa alla quale si vuole associare l'indirizzo IP pubblico.|
    |**Gruppo di risorse**|Sì|Può esistere nello stesso o in un altro gruppo di risorse della risorsa alla quale si vuole associare l'indirizzo IP pubblico.|
    |**Posizione**|Sì|Deve esistere nella stessa località della risorsa alla quale si vuole associare l'indirizzo IP pubblico.|

|**Strumento**|**Comando**|
|---|---|
|**CLI**|[az network public-ip-create](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|**PowerShell**|[New-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/v3.4.0/new-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change"></a>Modificare le impostazioni o eliminare un indirizzo IP pubblico

Per modificare o eliminare un indirizzo IP pubblico, attenersi alla procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com) con un account che disponga almeno delle autorizzazioni per il ruolo di Collaboratore rete per la sottoscrizione. Leggere l'articolo sui [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione dei ruoli e le autorizzazioni degli account.
2. Nella finestra che contiene il testo *Cerca risorse*, nella parte superiore del portale di Azure, digitare *indirizzo ip pubblico*. Selezionare la voce **Indirizzi IP pubblici** visualizzata nei risultati della ricerca.
3. Nel pannello **Indirizzi IP pubblici**, fare clic sul nome dell'indirizzo IP pubblico del quale modificare o eliminare le impostazioni.
4. Nel pannello dell'indirizzo IP pubblico visualizzato, completare l'opzione pertinente in base alla necessità di eliminare o modificare l'indirizzo IP pubblico.
    - **Elimina**: per eliminare l'indirizzo IP pubblico, fare clic su **Elimina** nella sezione **Panoramica** del pannello. Se l'indirizzo è associato a una configurazione IP, non può essere eliminato. Se l'indirizzo è associato a una configurazione, fare clic su **Annulla associazione** per annullare l'associazione dalla configurazione IP.
    - **Modifica**: fare clic su **Configurazione**. Modificare le impostazioni usando le informazioni fornite nel passaggio 4 della sezione [Creare un indirizzo IP pubblico](#create) di questo articolo. Per modificare l'assegnazione da statico a dinamico, è necessario innanzitutto annullare l'associazione tra l'indirizzo IP pubblico e la configurazione IP. È quindi possibile modificare il metodo di assegnazione in dinamico e fare clic su **Associa** per associare l'indirizzo IP alla stessa configurazione IP o a una configurazione diversa. È anche possibile lasciare l'indirizzo senza associazione. Per annullare l'associazione di un indirizzo IP pubblico, nella sezione **Panoramica** fare clic su **Annulla associazione**.

>[!WARNING]
>Quando si modifica il metodo di assegnazione da statico a dinamico, l'indirizzo IP assegnato all'indirizzo IP pubblico viene perso. Mentre i server DNS pubblici Azure mantengono l'associazione tra gli indirizzi statici o dinamici e qualsiasi etichetta del nome DNS (se ne è stato definito uno), un indirizzo IP dinamico può essere modificato quando la macchina virtuale viene avviata dopo essere stata arrestata (deallocata). Per evitare la modifica dell'indirizzo, assegnare un indirizzo IP statico.

|**Strumento**|**Comando**|
|---|---|
|**CLI**|[az network public-ip update](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#update) per aggiornare; [az network public-ip delete](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#delete) per eliminare|
|**PowerShell**|[Set-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) per aggiornare; [Remove-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/v3.4.0/remove-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) per eliminare|

## <a name="next-steps"></a>Passaggi successivi
Assegnare gli indirizzi IP pubblici durante la creazione delle risorse di Azure seguenti:

- Macchine virtuali [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Servizio di bilanciamento del carico con connessione Internet](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Gateway applicazione Azure](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Connessione da sito a sito con gateway VPN di Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
