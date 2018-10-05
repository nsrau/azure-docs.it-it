---
title: Mappa delle applicazioni in Azure Application Insights | Microsoft Docs
description: Monitorare topologie di applicazioni complesse con la mappa delle applicazioni
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/14/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 9b39eef5accec4764f61ab31dd894d368242ee3d
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2018
ms.locfileid: "47094651"
---
# <a name="application-map-triage-distributed-applications"></a>Mappa delle applicazioni: valutare le applicazioni distribuite

La mappa delle applicazioni consente di individuare i colli di bottiglia delle prestazioni o le aree sensibili agli errori in tutti i componenti dell'applicazione distribuita. Ogni nodo nella mappa rappresenta un componente dell'applicazione o le relative dipendenze e ha un indicatore KPI dell'integrità e uno stato degli avvisi. È possibile fare clic da qualsiasi componente per ottenere una diagnostica più dettagliata, ad esempio sugli eventi di Application Insights. Se l'app usa i servizi di Azure, è possibile anche fare clic sulla diagnostica di Azure, ad esempio per consigli di Advisor su database SQL.

## <a name="what-is-a-component"></a>Cos'è un componente?

I componenti sono parti dell'applicazione distribuita o di microservizi, distribuibili autonomamente. Gli sviluppatori e i team delle operazioni hanno visibilità o accesso a livello di codice ai dati di telemetria generati dai componenti di queste applicazioni. 

* I componenti sono diversi dalle dipendenze esterne "osservate" quali SQL, EventHub e così via, a cui il team o l'organizzazione potrebbero non avere accesso (codice o dati di telemetria).
* I componenti vengono eseguiti su diverse istanze di ruolo, server o contenitore.
* I componenti possono essere chiavi di strumentazione di Application Insights diverse tra loro (anche in caso di sottoscrizioni diverse) oppure ruoli diversi che creano report per una sola chiave di strumentazione di Application Insights. L'esperienza della mappa di anteprima mostra i componenti indipendentemente dalla loro configurazione.

## <a name="composite-application-map"></a>Mappa delle applicazioni composite

È possibile visualizzare la topologia completa dell'applicazione con più livelli di componenti dell'applicazione correlati. I componenti possono essere risorse di Application Insights diverse o ruoli diversi in una singola risorsa. La mappa delle app consente di trovare i componenti seguendo le chiamate di dipendenza HTTP inviate tra i server con Application Insights SDK installato. 

Questa esperienza inizia con la progressiva individuazione dei componenti. Quando si carica la mappa delle applicazioni per la prima volta, viene avviato un set di query per individuare i componenti correlati a questo componente. Un pulsante nell'angolo superiore sinistro viene aggiornato con il numero di componenti dell'applicazione individuati. 

Quando si fa clic sul pulsante per aggiornare i componenti della mappa, la mappa viene aggiornata con tutti i componenti individuati fino a quel momento.

Se tutti i componenti sono ruoli all'interno di una singola risorsa di Application Insights, questo passaggio di individuazione non è necessario. Il caricamento iniziale per tale applicazione includerà tutti i relativi componenti.

![Screenshot della mappa delle applicazioni](media/app-insights-app-map/001.png)

Uno dei principali obiettivi di questa esperienza è quello di consentire di visualizzare le topologie complesse con centinaia di componenti.

Fare clic su qualsiasi componente per visualizzare le informazioni dettagliate correlate e passare all'esperienza di valutazione delle prestazioni e degli errori per il componente.

![Riquadro a comparsa](media/app-insights-app-map/application-map-001.png)

### <a name="investigate-failures"></a>Esaminare gli errori

Selezionare **Esamina errori** per aprire il riquadro Errori.

![Screenshot del pulsante Esamina errori](media/app-insights-app-map/investigate-failures.png)

![Screenshot dell'esperienza Errori](media/app-insights-app-map/failures.png)

### <a name="investigate-performance"></a>Esaminare le prestazioni

Per risolvere i problemi relativi alle prestazioni, selezionare **Esamina prestazioni**

![Screenshot del pulsante Esamina prestazioni](media/app-insights-app-map/investigate-performance.png)

![Screenshot dell'esperienza Prestazioni](media/app-insights-app-map/performance.png)

### <a name="go-to-details"></a>Vai ai dettagli

Selezionare **Vai ai dettagli** per esplorare l'esperienza Transazione end-to-end che può offrire le visualizzazioni create a livello di stack di chiamate.

![Screenshot del pulsante Vai ai dettagli](media/app-insights-app-map/go-to-details.png)

![Screenshot dei dettagli delle transazioni end-to-end](media/app-insights-app-map/end-to-end-transaction.png)

### <a name="view-in-analytics"></a>Visualizza in Analisi

Per eseguire query ed esaminare ulteriormente i dati delle applicazioni, fare clic su **Visualizza in Analisi**.

![Screenshot del pulsante Visualizza in Analisi](media/app-insights-app-map/view-in-analytics.png)

![Screenshot dell'esperienza di analisi](media/app-insights-app-map/analytics.png)

### <a name="alerts"></a>Avvisi

Per visualizzare gli avvisi attivi e le regole sottostanti che attivano gli avvisi, selezionare **Avvisi**.

![Screenshot del pulsante Avvisi](media/app-insights-app-map/alerts.png)

![Screenshot dell'esperienza di analisi](media/app-insights-app-map/alerts-view.png)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="feedback"></a>Commenti e suggerimenti
Inviare commenti e suggerimenti tramite l'apposita opzione del portale.

![Immagine MapLink-1](./media/app-insights-app-map/13.png)

## <a name="next-steps"></a>Passaggi successivi

* [portale di Azure](https://portal.azure.com)
