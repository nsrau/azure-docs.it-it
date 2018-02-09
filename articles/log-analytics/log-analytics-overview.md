---
title: Informazioni su Log Analytics in Azure | Microsoft Docs
description: "Log Analytics è un servizio di Azure che consente di raccogliere e analizzare i dati operativi generati dalle risorse nel cloud e nell'ambiente locale.  Questo articolo fornisce una breve panoramica dei diversi componenti di analisi di Log Analytics e collegamenti a contenuto dettagliato."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: bd90b460-bacf-4345-ae31-26e155beac0e
ms.service: log-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/24/2018
ms.author: bwren
ms.openlocfilehash: a95528f5bd259a36ea96c7bc0660ca082c09d6e6
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2018
---
# <a name="what-is-log-analytics"></a>Cos'è Log Analytics?
Log Analytics è un servizio incluso in Azure che monitora gli ambienti cloud e locali per mantenerne la disponibilità e le prestazioni.  Raccoglie i dati generati dalle risorse negli ambienti cloud e locali e da altri strumenti di monitoraggio per analizzare più origini.  Questo articolo offre una breve descrizione del valore di Log Analytics, una panoramica del relativo funzionamento e collegamenti a contenuti più dettagliati per approfondire l'argomento.

## <a name="is-log-analytics-for-you"></a>Log Analytics è la scelta giusta?
Se l'ambiente Azure non è attualmente monitorato, è consigliabile iniziare con [Monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-overview.md), che raccoglie e analizza i dati di monitoraggio per le risorse di Azure.  Log Analytics può [raccogliere dati da Monitoraggio di Azure](log-analytics-azure-storage.md) per metterli in relazione con altri dati e offrire un'analisi ulteriore.

Per monitorare l'ambiente locale o fare in modo che il monitoraggio esistente usi servizi quali Monitoraggio di Azure o System Center Operations Manager, Log Analytics può risultare notevolmente utile.  Può raccogliere dati direttamente dagli agenti e da altri strumenti in un unico archivio.  Gli strumenti di analisi in Log Analytics, come le ricerche nei log, le visualizzazioni e le soluzioni, vengono applicati a tutti i dati raccolti per offrire un'analisi centralizzata dell'intero ambiente.


## <a name="using-log-analytics"></a>Uso di Log Analytics
È possibile accedere a Log Analytics tramite il portale di Azure, disponibili con qualsiasi browser, che consentono di accedere alle impostazioni di configurazione e a diversi strumenti per analizzare e agire in base ai dati raccolti  Dal portale è possibile sfruttare le [ricerche nei log](log-analytics-log-searches.md) in cui si creano query per analizzare i dati raccolti, i [dashboard](log-analytics-dashboards.md) che è possibile personalizzare con rappresentazioni grafiche delle ricerche più importanti e le [soluzioni](log-analytics-add-solutions.md) che forniscono funzionalità e strumenti di analisi aggiuntivi.

L'immagine seguente mostra la schermata Panoramica contenente informazioni di riepilogo per le [soluzioni](#add-functionality-with-management-solutions) installate nell'area di lavoro.  È possibile fare clic su qualsiasi riquadro per approfondire i dati per la relativa soluzione.

![Portale OMS](media/log-analytics-overview/portal.png)

Log Analytics include un linguaggio di query che permette di recuperare e consolidare rapidamente i dati nel repository.  È possibile creare e salvare le [ricerche nei log](log-analytics-log-searches.md) per analizzare i dati direttamente nel portale o eseguire automaticamente le ricerche di logo per creare un avviso, se i risultati della query indicano una condizione importante.

![Ricerca log](media/log-analytics-overview/log-search.png)

Per analizzare i dati all'esterno di Log Analytics, è possibile esportare i dati in strumenti come [Power BI](log-analytics-powerbi.md) o Excel.  È anche possibile sfruttare l' [API di Ricerca log](log-analytics-log-search-api.md) per compilare soluzioni personalizzate che sfruttano i dati di Log Analytics o per eseguire l'integrazione con altri sistemi.

## <a name="add-functionality-with-management-solutions"></a>Aggiungere funzionalità con soluzioni di gestione
Le [soluzioni di gestione](log-analytics-add-solutions.md) aggiungono funzionalità a Log Analytics e ulteriori dati e strumenti di analisi a Log Analytics.  Possono anche definire nuovi tipi di record da raccogliere, che possono essere analizzati con ricerche nei log o con l'interfaccia utente aggiuntiva fornita dalla soluzione nel dashboard.  L'immagine di esempio seguente mostra la [soluzione Rilevamento modifiche](log-analytics-change-tracking.md).

![Soluzione di Rilevamento modifiche](media/log-analytics-overview/change-tracking.png)

Sono disponibili soluzioni per numerose funzioni e vengono aggiunte continuamente altre soluzioni.  È possibile accedere facilmente alle soluzioni disponibili e [aggiungerle all'area di lavoro](log-analytics-add-solutions.md) da Azure Marketplace.  Molte vengono distribuite automaticamente e iniziano a funzionare immediatamente, mentre altre possono richiedere qualche attività di configurazione.

![Raccolta soluzioni](media/log-analytics-overview/solution-gallery.png)

## <a name="log-analytics-components"></a>Componenti di Log Analytics
Al centro di Log Analytics è presente il repository di dati raccolti, ospitato nel cloud di Azure.  I dati vengono raccolti da origini connesse configurando le origini dati e aggiungendo soluzioni alla sottoscrizione.  Le origini dati e le soluzioni creeranno diversi tipi di record con uno specifico set di proprietà, ma che possono comunque essere analizzati insieme nelle query al repository.  In questo modo è possibile usare gli stessi strumenti e metodi per lavorare con diversi tipi di dati raccolti da diverse origini.

![Componenti di Log Analytics](media/log-analytics-overview/overview.png)

Le origini connesse sono i computer e altre risorse che generano dati raccolti da Log Analytics.  Sono inclusi gli agenti installati nei computer [Windows](log-analytics-windows-agent.md) e [Linux](log-analytics-linux-agents.md) che si connettono direttamente o gli agenti in un [gruppo di gestione di System Center Operations Manager connesso](log-analytics-om-agents.md).  Per le risorse di Azure, Log Analytics raccoglie dati da [Monitoraggio di Azure e Diagnostica di Azure](log-analytics-azure-storage.md).

[origini dati](log-analytics-data-sources.md) sono i diversi tipi di dati raccolti da ogni origine connessa.  Sono inclusi gli [eventi](log-analytics-data-sources-windows-events.md) e i [dati sulle prestazioni](log-analytics-data-sources-performance-counters.md) ricavati dagli agenti [Windows](log-analytics-data-sources-windows-events.md) e Linux, oltre alle origini quali [log di IIS](log-analytics-data-sources-iis-logs.md) e [log di testo personalizzati](log-analytics-data-sources-custom-logs.md).  È possibile configurare ciascuna origine dati che si vuole raccogliere e la configurazione viene inviata automaticamente a ogni origine connessa.

Per requisiti personalizzati, è possibile usare l'[API di raccolta dati HTTP](log-analytics-data-collector-api.md) per scrivere dati nel repository da un client dell'API REST.

## <a name="log-analytics-architecture"></a>Architettura di Log Analytics
I requisiti di distribuzione di Log Analytics sono minimi, perché i componenti centrali sono ospitati nel cloud di Azure.  Ciò include il repository in aggiunta ai servizi che consentono di correlare e analizzare i dati raccolti.  Il portale è accessibile da qualsiasi browser, dunque non esiste alcun requisito per il software client.

È necessario installare gli agenti nei computer [Windows](log-analytics-windows-agent.md) e [Linux](log-analytics-linux-agents.md) , ma non sono richiesti agenti aggiuntivi per i computer che fanno già parte di un [gruppo di gestione di System Center Operations Manager connesso](log-analytics-om-agents.md).  Gli agenti di Operations Manager continueranno a comunicare con i server di gestione che inoltreranno i propri dati a Log Analytics.  Alcune soluzioni richiederanno tuttavia che gli agenti comunichino direttamente con Log Analytics.  La documentazione per ogni soluzione specificherà i relativi requisiti di comunicazione.

Quando ci si [iscrive a Log Analytics](log-analytics-get-started.md), verrà creata un'area di lavoro.  L'area di lavoro può essere considerata come un ambiente di Log Analytics univoco con un archivio dati, origini dati e soluzioni personalizzati. È possibile creare più aree di lavoro nella sottoscrizione per supportare più ambienti, ad esempio di produzione e test.

![Architettura di Log Analytics](media/log-analytics-overview/architecture.png)

## <a name="next-steps"></a>Passaggi successivi
* [Iscriversi per ottenere un account gratuito di Log Analytics](log-analytics-get-started.md) da testare nel proprio ambiente.
* Visualizzare le diverse [origini dati](log-analytics-data-sources.md) disponibili per raccogliere dati in Log Analytics.
* [Esplorare le soluzioni disponibili nella Raccolta soluzioni](log-analytics-add-solutions.md) per aggiungere funzionalità a Log Analytics.

