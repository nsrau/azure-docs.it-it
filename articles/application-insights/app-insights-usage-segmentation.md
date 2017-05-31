---
title: Analisi di utenti, sessioni ed eventi in Azure Application Insights | Microsoft Docs
description: Analisi demografica degli utenti dell&quot;app Web.
services: application-insights
documentationcenter: 
author: botatoes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/03/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 912e3e25680034828aee93d23c6a725c2cec0d45
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---

# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Analisi di utenti, sessioni ed eventi in Application Insights

Scoprire quando le persone usano l'app Web, a quali pagine sono più interessati, dove si trovano, quali browser e sistemi operativi usano. Analizzare i dati di telemetria sull'utilizzo e sull'azienda con [Azure Application Insights](app-insights-overview.md).

## <a name="get-started"></a>Attività iniziali

Se nei pannelli degli utenti, delle sessioni e degli eventi nel portale di Application Insights non vengono ancora visualizzati i dati, leggere le [informazioni su come iniziare a usare gli strumenti d'uso](app-insights-usage-overview.md).

## <a name="the-users-sessions-and-events-segmentation-tool"></a>Strumenti di segmentazione di Utenti, Sessioni ed Eventi

Tre pannelli d'uso usano lo stesso strumento per effettuare un'analisi approfondita dei dati di telemetria dell'app Web da tre diverse prospettive. Applicando i filtri e dividendo i dati, si possono scoprire informazioni dettagliate sull'uso di pagine e funzionalità diverse.

* **Strumento Utenti**: numero di persone che hanno usato l'app e le relative funzionalità.  Gli utenti vengono conteggiati tramite ID anonimi memorizzati nei cookie del browser. Una singola persona che usa browser o computer diversi verrà conteggiata più di una volta.
* **Strumento Sessioni**: il numero di sessioni delle attività dell'utente che include determinate pagine e funzionalità dell'app. Una sessione viene conteggiata dopo mezz'ora di inattività dell'utente o in seguito a un uso continuo di 24 ore.
* **Strumento Eventi**: la frequenza con cui vengono usate alcune pagine e funzionalità dell'app. La visualizzazione di una pagina viene conteggiata quando un browser carica la pagina dell'app, purché sia stata [instrumentata](app-insights-javascript.md). 

    Un evento personalizzato indica che nell'app si verifica un'operazione, spesso si tratta di un'interazione dell'utente ad esempio il clic su un pulsante o il completamento di alcune attività. Inserire codice nell'app per [generare eventi personalizzati](app-insights-api-custom-events-metrics.md#trackevent).

![Strumento Uso](./media/app-insights-usage-segmentation/users.png)

## <a name="querying-for-certain-users"></a>Esecuzione di query per determinati utenti 

Modificando le opzioni di query nella parte superiore dello strumento Utenti, è possibile esaminare diversi gruppi di utenti: 

* Who used (Usato da): scegliere gli eventi personalizzati e le visualizzazioni di pagina. 
* Durante: scegliere un intervallo di tempo. 
* By (Da): scegliere la modalità di ordinazione dei dati in base un intervallo di tempo o a un'altra proprietà, ad esempio la città o il browser. 
* Split By (Dividi per): scegliere una proprietà in base alla quale dividere o segmentare i dati. 
* Aggiungi filtri: limitare le query a determinati utenti, sessioni o eventi in base alle relative proprietà, ad esempio città o browser. 
 
## <a name="saving-and-sharing-reports"></a>Salvataggio e condivisione di report 
È possibile salvare i report Utenti, mantenendoli privati nella sezione My Reports (Report personali) o condividendoli con tutti gli utenti con accesso a questa risorsa di Application Insights nella sezione Report condivisi.  
 
Durante il salvataggio di un report o la modifica delle proprietà, scegliere "Intervallo di tempo relativo corrente" per salvare un report che continuerà ad aggiornare i dati, per un determinato periodo di tempo stabilito.  
 
Scegliere "Intervallo di tempo assoluto corrente" per salvare un report con un set di dati fisso. Tenere presente che i dati in Application Insights vengono conservati solo per 90 giorni, pertanto se sono trascorsi più di 90 giorni dal salvataggio di un report con intervallo di tempo assoluto, il report visualizzato sarà vuoto. 
 
## <a name="example-instances"></a>Istanze di esempio

La sezione Example instances (Istanze di esempio) mostra informazioni relative ad alcuni singoli utenti, sessioni o eventi che corrispondono alla query corrente. La valutazione e l'analisi dei comportamenti delle persone, oltre a creare aggregazioni, può offrire informazioni dettagliate sull'uso effettivo dell'app da parte degli utenti. 
 
## <a name="insights"></a>Informazioni dettagliate 

La barra laterale Informazioni dettagliate mostra cluster di grandi dimensioni di utenti che condividono proprietà comuni. Questi cluster consentono di individuare andamenti sorprendenti sulle modalità di uso dell'app. Ad esempio, se il 40% dell'uso dell'app deriva da utenti che usano una singola funzione.  


## <a name="next-steps"></a>Passaggi successivi

* [Panoramica sull'uso](app-insights-usage-overview.md)
* [Conservazione](app-insights-usage-retention.md)
* [Codifica di eventi personalizzati](app-insights-api-custom-events-metrics.md)


