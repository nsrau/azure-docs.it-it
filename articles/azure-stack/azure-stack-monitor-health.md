---
title: Monitora stato e avvisi nello Stack di Azure | Documenti Microsoft
description: Informazioni su come monitorare l'integrità e avvisi nello Stack di Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 69901c7b-4673-4bd8-acf2-8c6bdd9d1546
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: mabrigg
ms.openlocfilehash: e20670476f4549a06d80d5f85e2ebd8ab1f50b05
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37047242"
---
# <a name="monitor-health-and-alerts-in-azure-stack"></a>Monitoraggio dell'integrità e avvisi nello Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Stack di Azure include funzionalità che consentono di visualizzare avvisi per un'area dello Stack di Azure e dello stato di monitoraggio dell'infrastruttura. Il **Gestione area** stato aggiunto per impostazione predefinita nel portale di amministrazione per la sottoscrizione di Provider predefinito, riquadro Elenca tutte le aree distribuite dello Stack di Azure. Il riquadro mostra il numero di avvisi critici e di avviso attivi per ogni area ed è il punto di ingresso nell'integrità e le funzionalità di avviso dello Stack di Azure.

 ![Il riquadro di gestione di area](media/azure-stack-monitor-health/image1.png)

 ## <a name="understand-health-in-azure-stack"></a>Comprendere integrità nello Stack di Azure

 Integrità e gli avvisi vengono gestiti dal provider di risorse di integrità. Componenti dell'infrastruttura di Azure Stack registrano con il provider di risorse di integrità durante la configurazione e distribuzione di Azure Stack. Questa registrazione consente la visualizzazione dell'integrità e avvisi per ogni componente. Integrità nello Stack di Azure è un concetto semplice. Se sono presenti avvisi per un'istanza di un componente registrato, lo stato di integrità di tale componente riflette la gravità dell'avviso attiva peggiore; avviso o critico.

## <a name="alert-severity-definition"></a>Definizione di gravità dell'avviso

Nello Stack di Azure vengono generati avvisi con solo due livelli di gravità: **avviso** e **critici**.

**Warning**  
Un operatore può indirizzare il messaggio di avviso in modo programmato. L'avviso non influisce in genere i carichi di lavoro di utente.

**Critical**  
Un operatore deve prendere in considerazione l'avviso critico con urgenza. Vi sono problemi che attualmente impatto o non appena avrà un impatto sulle utenti dello Stack di Azure. 

 
 ## <a name="view-and-manage-component-health-state"></a>Visualizzare e gestire lo stato di integrità componente
 
 Come operatore dello Stack di Azure, è possibile visualizzare lo stato di integrità dei componenti nel portale di amministrazione e tramite API REST e PowerShell.
 
Per visualizzare lo stato di integrità nel portale, fare clic sull'area che si desidera visualizzare il **Gestione area** riquadro. È possibile visualizzare lo stato di integrità dei ruoli di infrastruttura e dei provider di risorse.

![Elenco di ruoli di infrastruttura](media/azure-stack-monitor-health/image2.png)

È possibile fare clic su un provider di risorse o a un ruolo di infrastruttura per visualizzare informazioni più dettagliate.

> [!WARNING]
>Fare clic su un ruolo di infrastruttura, quindi l'istanza del ruolo, sono disponibili opzioni per iniziare, riavviare o arrestare. Non usare queste azioni quando si applicano gli aggiornamenti a un sistema integrato. Inoltre, eseguire operazioni **non** utilizzare queste opzioni in un ambiente Azure Stack Development Kit. Queste opzioni sono progettate solo per un ambiente di sistemi integrati, in cui è presente più di un'istanza di ruolo per ogni ruolo di infrastruttura. Il riavvio di un'istanza del ruolo (in particolare AzS-Xrp01) nel kit di sviluppo comporta l'instabilità del sistema. Per la risoluzione dei problemi di assistenza, registrare il problema per il [forum di Azure Stack](https://aka.ms/azurestackforum).
>
 
## <a name="view-alerts"></a>Visualizzare gli avvisi

L'elenco di avvisi attivi per ogni regione dello Stack di Azure è disponibile direttamente il **Gestione area** blade. Il primo riquadro nella configurazione predefinita è il **avvisi** riquadro, che visualizza un riepilogo delle critici e notifiche di avviso per l'area. È possibile aggiungere il riquadro avvisi, come qualsiasi altra tessera in questo pannello, al dashboard per l'accesso rapido.   

![Gli avvisi che presenta un avviso](media/azure-stack-monitor-health/image3.png)

Se si seleziona la parte superiore del **avvisi** riquadro, passare all'elenco di tutti gli avvisi attivi per l'area. Se si seleziona il **critico** oppure **avviso** voce di riga all'interno di riquadro, si passa a un elenco filtrato degli avvisi (critico o avviso). 

![Avvertenze filtrati](media/azure-stack-monitor-health/image4.png)
  
Il **avvisi** blade supporta la possibilità di filtrare sia gravità (critico o avviso) e dello stato (attivo o chiuso). La visualizzazione predefinita Mostra tutti gli avvisi attivi. Tutti gli avvisi chiusi vengono rimossi dal sistema dopo sette giorni.

![Riquadro filtro Filtra in base critici o lo stato di avviso](media/azure-stack-monitor-health/image5.png)

Il **API vista** azione consente di visualizzare l'API REST che è stato utilizzato per generare la visualizzazione elenco. Questa azione offre un modo rapido per acquisire familiarità con la sintassi dell'API REST che è possibile utilizzare per gli avvisi di query. È possibile utilizzare questa API in automazione o per l'integrazione con Data Center esistenti di monitoraggio, reporting e soluzioni per le richieste. 

![L'opzione di API di visualizzazione che mostra l'API REST](media/azure-stack-monitor-health/image6.png)

È possibile fare clic su un avviso specifico per visualizzare i dettagli dell'avviso. I dettagli dell'avviso mostrano tutti i campi che sono associati all'avviso e attivare la navigazione rapida per il componente interessato e l'origine dell'avviso. Ad esempio, l'avviso seguente si verifica se una delle istanze del ruolo infrastruttura passa alla modalità offline o non è accessibile.  

![Il pannello dei dettagli degli avvisi](media/azure-stack-monitor-health/image7.png)

Dopo che l'istanza del ruolo infrastruttura viene riportato online, questo avviso viene chiuso automaticamente. Molte, ma non a ogni avviso chiuso automaticamente quando viene risolto il problema sottostante. Si consiglia di selezionare **Chiudi avviso** dopo aver eseguito i passaggi correttivi. Se il problema persiste, Stack di Azure genera un nuovo avviso. Se il problema risolto, l'avviso rimane chiuso e non richiede alcuna azione aggiuntiva.

## <a name="next-steps"></a>Passaggi successivi

[Gestire gli aggiornamenti in Azure Stack](azure-stack-updates.md)

[Gestione area nello Stack di Azure](azure-stack-region-management.md)
