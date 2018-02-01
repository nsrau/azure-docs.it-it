---
title: Risolvere i problemi relativi al gateway di rete virtuale e alle connessioni di Azure - Portale | Microsoft Docs
description: Questa pagina spiega come usare il portale per la risoluzione dei problemi di Azure Network Watcher
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: f6f0a813-38b6-4a1f-8cfc-1dfdf979f595
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: fb8f60eea0eaa24649f584ae4183aa8e552a433e
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2018
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-network-watcher-from-azure-portal"></a>Risolvere i problemi relativi al gateway di rete virtuale e alle connessioni tramite Network Watcher dal portale di Azure

> [!div class="op_single_selector"]
> - [di Microsoft Azure](network-watcher-troubleshoot-manage-portal.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Interfaccia della riga di comando 1.0](network-watcher-troubleshoot-manage-cli-nodejs.md)
> - [Interfaccia della riga di comando 2.0](network-watcher-troubleshoot-manage-cli.md)
> - [API REST](network-watcher-troubleshoot-manage-rest.md)

Network Watcher offre numerose funzionalità che consentono di comprendere le risorse di rete in Azure. Una di queste funzionalità è la risoluzione dei problemi riscontrati con le risorse. La funzionalità può essere chiamata dal portale, da PowerShell, dall'interfaccia della riga di comando o dall'API REST. Quando chiamata, Network Watcher controlla l'integrità di un gateway di rete virtuale o di una connessione e restituisce i risultati.

## <a name="before-you-begin"></a>Prima di iniziare

Questo scenario presuppone il completamento dei passaggi descritti in [Creare un servizio Network Watcher](network-watcher-create.md) per creare un servizio Network Watcher.

Per un elenco dei tipi di gateway supportati, consultare i [tipi di gateway supportati](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Panoramica

La risoluzione dei problemi delle risorse consente di risolvere i problemi che si verificano con i gateway di rete virtuale e le connessioni. Quando viene inviata una richiesta di risoluzione dei problemi delle risorse, i log vengono sottoposti a query e controllati. Dopo aver completato l'ispezione, vengono restituiti i risultati. Le richieste di risoluzione dei problemi delle risorse sono richieste a esecuzione prolungata, che possono richiedere anche diversi minuti per restituire un risultato. I log risultati vengono archiviati in un contenitore in un account di archiviazione specificato.

## <a name="troubleshoot-a-gateway-or-connection"></a>Risolvere i problemi relativi a una connessione o a un gateway

1. Andare al [portale di Azure](https://portal.azure.com) e fare clic su **Rete** > **Network Watcher** > **Diagnostica VPN**
2. Selezionare una **Sottoscrizione**, un **Gruppo di risorse** e la **Località**.
3. Le risoluzione dei problemi relativi alle risorse restituisce i dati sullo stato della risorsa. Salva inoltre i log rilevanti in un account di archiviazione affinché vengano riesaminati. Fare clic su **Account di archiviazione** per selezionare un account di archiviazione.
4. Nel pannello **Account di archiviazione** selezionare un account di archiviazione esistente o fare clic su **+Account di archiviazione** per crearne uno nuovo.
5. Nel pannello **Contenitori** scegliere un contenitore esistente oppure fare clic su **+Contenitore** per creare un nuovo contenitore. Al termine, fare clic su **Seleziona**
6. Selezionare le risorse gateway e di connessione per risolvere i problemi e fare clic su **Avvia risoluzione dei problemi**

Se si selezionano più risorse, la risoluzione dei problemi viene eseguita contemporaneamente sulle risorse del gateway. Non è possibile eseguirla su una connessione e sul gateway associato allo stesso tempo. Si consiglia di risolvere innanzitutto i problemi relativi al gateway poiché la risoluzione dei problemi di connessione è un processo più lungo. Durante l'esecuzione di Diagnostica VPN in una risorsa la colonna **STATO RISOLUZIONE DEI PROBLEMI** mostrerà lo stato **In esecuzione**

Al termine, la colonna dello stato passa a **Integro** o **Danneggiato**.

![risoluzione dei problemi completa][2]

## <a name="understanding-the-results"></a>Informazioni sui risultati

Nella sezione **Dettagli** della finestra la scheda **Stato** mostra lo stato della risoluzione dei problemi più recente eseguita sulla risorsa selezionata. I risultati della diagnostica più recente sono mostrati xx minuti dopo l'ultima esecuzione.

|Proprietà  |DESCRIZIONE  |
|---------|---------|
|Risorsa     | Collegamento alla risorsa.        |
|Percorso di archiviazione     |  Percorso all'account di archiviazione e al contenitore che contengono i registri, se generati durante l'esecuzione. Questa impostazione non viene mantenuta alla chiusura del portale.        |
|Summary     | Riepilogo dell'integrità delle risorse.        |
|Dettagli     | Informazioni dettagliate sull'integrità delle risorse.        |
|Ultima esecuzione     | Ora dell'ultima esecuzione della risoluzione dei problemi.        |


La scheda **Azione** offre indicazioni generiche su come risolvere il problema. Se è possibile eseguire un'azione per il problema, viene fornito un collegamento con altre informazioni. Nel caso in cui non siano presenti altre indicazioni, la risposta include l'URL per aprire una richiesta di assistenza.  Per altre informazioni sulle proprietà della risposta e ciò che vie è incluso, consultare la [panoramica sulla risoluzione dei problemi in Network Watcher](network-watcher-troubleshoot-overview.md).


## <a name="next-steps"></a>Passaggi successivi

Se sono state modificate le impostazioni che arrestano la connettività VPN, vedere [Gestire gruppi di sicurezza di rete](../virtual-network/virtual-network-manage-nsg-arm-portal.md) per tenere traccia del gruppo di sicurezza di rete e delle regole di sicurezza in questione.


[2]: ./media/network-watcher-troubleshoot-manage-portal/2.png
