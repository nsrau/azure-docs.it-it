---
title: "Monitorare l'integrità e avvisi nello Stack di Azure | Documenti Microsoft"
description: "Informazioni su come monitorare l'integrità e avvisi nello Stack di Azure."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 69901c7b-4673-4bd8-acf2-8c6bdd9d1546
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: b2ba7ec922341464ea7160d08e475999c941c42a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-health-and-alerts-in-azure-stack"></a>Monitoraggio dell'integrità e avvisi nello Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Azure Stack include il monitoraggio di funzionalità che consentono di visualizzare avvisi per un'area dello Stack di Azure e l'integrità dell'infrastruttura. Il **Gestione area** stato aggiunto per impostazione predefinita nel portale di amministrazione per la sottoscrizione del Provider predefinito, riquadro, vengono elencate tutte le aree distribuite dello Stack di Azure. Nel riquadro mostra il numero di avvisi critici e di avviso attivi per ogni area ed è il punto di ingresso nell'integrità e le funzionalità di avviso dello Stack di Azure.

 ![Il riquadro di gestione di area](media/azure-stack-monitor-health/image1.png)

 ## <a name="understand-health-in-azure-stack"></a>Comprendere l'integrità nello Stack di Azure

 Integrità e gli avvisi vengono gestiti dal provider di risorse di integrità. Componenti dell'infrastruttura di Azure Stack registrano con il provider di risorse di integrità durante la configurazione e distribuzione di Azure Stack. Questa registrazione consente la visualizzazione dell'integrità e avvisi per ogni componente. Integrità nello Stack di Azure è un concetto semplice. Se sono presenti avvisi per un'istanza di un componente registrato, lo stato di integrità di tale componente riflette la gravità dell'avviso attiva peggiore; avviso o critico.
 
 ## <a name="view-and-manage-component-health-state"></a>Consente di visualizzare e gestire lo stato di integrità di componente
 
 Come operatore di Stack di Azure, è possibile visualizzare lo stato di integrità dei componenti nel portale di amministrazione e tramite l'API REST e PowerShell.
 
Per visualizzare lo stato di integrità nel portale, fare clic sull'area che si desidera visualizzare il **Gestione area** riquadro. È possibile visualizzare lo stato di integrità dei ruoli di infrastruttura e dei provider di risorse. In questa versione, il provider di risorse di calcolo non segnala lo stato di integrità.

![Elenco di ruoli di infrastruttura](media/azure-stack-monitor-health/image2.png)

È possibile fare clic su un provider di risorse o a un ruolo di infrastruttura per visualizzare informazioni più dettagliate.

> [!WARNING]
>Fare clic su un ruolo di infrastruttura, quindi l'istanza del ruolo, sono disponibili opzioni per iniziare, riavviare o l'arresto. Non utilizzare queste azioni quando si applicano gli aggiornamenti a un sistema integrato. Inoltre, **non** utilizzare queste opzioni in un ambiente Azure Stack Development Kit. Queste opzioni sono progettate solo per un ambiente di sistemi integrati, in cui è presente più di un'istanza di ruolo per ogni ruolo di infrastruttura. Il riavvio di un'istanza del ruolo (in particolare AzS Xrp01) nel kit di sviluppo causa l'instabilità del sistema. Per la risoluzione dei problemi di assistenza, registrare il problema per il [forum di Azure Stack](https://aka.ms/azurestackforum).
>
 
## <a name="view-alerts"></a>Visualizzare gli avvisi

L'elenco di avvisi attivi per ogni area dello Stack di Azure è disponibile direttamente il **Gestione area** blade. Il primo riquadro nella configurazione predefinita è il **avvisi** riquadro, che consente di visualizzare un riepilogo delle critici e notifiche di avviso per l'area. È possibile aggiungere il riquadro di avvisi, come qualsiasi altra tessera in questo pannello, al dashboard per l'accesso rapido.   

![Gli avvisi che presenta un avviso](media/azure-stack-monitor-health/image3.png)

Selezionando la parte superiore di **avvisi** riquadro, passare all'elenco di tutti gli avvisi attivi per l'area. Se si seleziona il **critico** o **avviso** voce di riga all'interno di riquadro, passare a un elenco filtrato di avvisi (critico o avviso). 

![Avvertenze filtrati](media/azure-stack-monitor-health/image4.png)
  
Il **avvisi** pannello supporta la possibilità di filtrare sia a livello di gravità (critico o avviso) e dello stato (attivo o chiuso). La visualizzazione predefinita Mostra tutti gli avvisi attivi. Tutti gli avvisi chiusi vengono rimossi dal sistema dopo sette giorni.

![Riquadro filtro per filtrare per stato critico o di stato di avviso](media/azure-stack-monitor-health/image5.png)

Il **API vista** azione consente di visualizzare l'API REST che è stato utilizzato per generare la visualizzazione elenco. Questa azione fornisce un modo rapido per acquisire familiarità con la sintassi dell'API REST che è possibile utilizzare per gli avvisi di query. È possibile utilizzare questa API in automazione o per l'integrazione con il Data Center esistenti di monitoraggio, reporting e soluzioni per le richieste. 

![L'opzione di API di visualizzazione che mostra l'API REST](media/azure-stack-monitor-health/image6.png)

È possibile fare clic su un avviso specifico per visualizzare i dettagli dell'avviso. I dettagli dell'avviso mostrano tutti i campi che sono associati all'avviso, attivare la navigazione rapida per il componente interessato e l'origine dell'avviso. Ad esempio, l'avviso seguente si verifica se una delle istanze del ruolo infrastruttura passa alla modalità offline o non è accessibile.  

![Nel pannello Dettagli avviso](media/azure-stack-monitor-health/image7.png)

Dopo aver riportato online l'istanza del ruolo di infrastruttura, questo avviso viene chiuso automaticamente. Molte, ma non a ogni avviso viene chiuso automaticamente quando viene risolto il problema sottostante. È consigliabile selezionare **Chiudi avviso** dopo aver eseguito i passaggi correttivi. Se il problema persiste, Stack di Azure genera un nuovo avviso. Se si risolve il problema, l'avviso rimane chiuso e non richiede operazioni aggiuntive.

## <a name="next-steps"></a>Passaggi successivi

[Gestire gli aggiornamenti in Azure Stack](azure-stack-updates.md)

[Gestione area nello Stack di Azure](azure-stack-region-management.md)
