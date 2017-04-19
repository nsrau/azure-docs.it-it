---
title: Informazioni su Log Analytics in Operations Management Suite (OMS) | Microsoft Docs
description: "Log Analytics è un servizio di Operations Management Suite (OMS) che consente di raccogliere e analizzare i dati operativi generati dalle risorse nel cloud e nell&quot;ambiente locale.  Questo articolo fornisce una breve panoramica dei diversi componenti di analisi di Log Analytics e collegamenti a contenuto dettagliato."
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
ms.date: 03/29/2017
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 762d8deac1f176e51b54295ef571ae529d338d2c
ms.lasthandoff: 03/31/2017


---
# <a name="what-is-log-analytics"></a>Cos'è Log Analytics?
Log Analytics è un servizio di [Operations Management Suite \(OMS\)](../operations-management-suite/operations-management-suite-overview.md) che consente di monitorare gli ambienti cloud e locali per garantirne la disponibilità e le prestazioni.  Raccoglie i dati generati dalle risorse negli ambienti cloud e locali e da altri strumenti di monitoraggio per analizzare più origini.  Questo articolo offre una breve descrizione del valore di Log Analytics, una panoramica del relativo funzionamento e collegamenti a contenuti più dettagliati per approfondire l'argomento.

## <a name="is-log-analytics-for-you"></a>Log Analytics è la scelta giusta?
Se l'ambiente Azure non è attualmente monitorato, è consigliabile iniziare con [Monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-overview.md), che raccoglie e analizza i dati di monitoraggio per le risorse di Azure.  Log Analytics può raccogliere dati da Monitoraggio di Azure per metterli in relazione con altri dati e offrire un'analisi ulteriore.

Per monitorare l'ambiente locale o fare in modo che il monitoraggio esistente usi servizi quali Monitoraggio di Azure o System Center Operations Manager, Log Anlalytics può risultare notevolmente utile.  Può raccogliere dati direttamente dagli agenti e da altri strumenti in un unico archivio.  Gli strumenti di analisi in Log Analytics, come le ricerche nei log, le visualizzazioni e le soluzioni, vengono applicati a tutti i dati raccolti per offrire un'analisi centralizzata dell'intero ambiente.


## <a name="using-log-analytics"></a>Uso di Log Analytics
È possibile accedere a Log Analytics attraverso il portale di OMS o il portale di Azure, disponibili con qualsiasi browser, che permettono di accedere alle impostazioni di configurazione e a diversi strumenti per analizzare e agire in base ai dati raccolti.  Dal portale è possibile sfruttare le [ricerche di log](log-analytics-log-searches.md) in cui si creano query per analizzare i dati raccolti, i [dashboard](log-analytics-dashboards.md) che è possibile personalizzare con rappresentazioni grafiche delle ricerche più importanti e le [soluzioni](log-analytics-add-solutions.md) che forniscono funzionalità e strumenti di analisi aggiuntivi.

L'immagine seguente è tratta dal portale di OMS e mostra il dashboard contenente informazioni di riepilogo per le [soluzioni](#add-functionality-with-management-solutions) installate nell'area di lavoro.  È possibile fare clic su qualsiasi riquadro per approfondire i dati per la relativa soluzione.

![Portale OMS](media/log-analytics-overview/portal.png)

Log Analytics include un linguaggio di query che permette di recuperare e consolidare rapidamente i dati nel repository.  È possibile creare e salvare le [ricerche nei log](log-analytics-log-searches.md) per analizzare i dati direttamente nel portale o eseguire automaticamente le ricerche di logo per creare un avviso, se i risultati della query indicano una condizione importante.

![Ricerca log](media/log-analytics-overview/log-search.png)

Per ottenere una rapida rappresentazione grafica dell'integrità dell'ambiente globale, è possibile aggiungere visualizzazioni per le ricerche nei log salvate nel [dashboard](log-analytics-dashboards.md).   

![Dashboard](media/log-analytics-overview/dashboard.png)

Per analizzare i dati all'esterno di Log Analytics, è possibile esportare i dati dal repository OMS in strumenti come [Power BI](log-analytics-powerbi.md) o Excel.  È anche possibile sfruttare l' [API di Ricerca log](log-analytics-log-search-api.md) per compilare soluzioni personalizzate che sfruttano i dati di Log Analytics o per eseguire l'integrazione con altri sistemi.

## <a name="add-functionality-with-management-solutions"></a>Aggiungere funzionalità con soluzioni di gestione
Le [soluzioni di gestione](log-analytics-add-solutions.md) aggiungono funzionalità a OMS e ulteriori dati e strumenti di analisi a Log Analytics.  Possono anche definire nuovi tipi di record da raccogliere, che possono essere analizzati con ricerche nei log o con l'interfaccia utente aggiuntiva fornita dalla soluzione nel dashboard.  L'immagine di esempio seguente mostra la [soluzione Rilevamento modifiche](log-analytics-change-tracking.md).

![Soluzione di Rilevamento modifiche](media/log-analytics-overview/change-tracking.png)

Sono disponibili soluzioni per numerose funzioni e vengono aggiunte continuamente altre soluzioni.  È possibile accedere facilmente alle soluzioni disponibili e [aggiungerle all'area di lavoro OMS](log-analytics-add-solutions.md) dalla Raccolta soluzioni o da Azure Marketplace.  Molte vengono distribuite automaticamente e iniziano a funzionare immediatamente, mentre altre possono richiedere qualche attività di configurazione.

![Raccolta soluzioni](media/log-analytics-overview/solution-gallery.png)

## <a name="log-analytics-components"></a>Componenti di Log Analytics
Al centro di Log Analytics è presente il repository OMS, ospitato nel cloud di Azure.  I dati vengono raccolti nel repository da origini connesse configurando le origini dati e aggiungendo soluzioni alla sottoscrizione.  Le origini dati e le soluzioni creeranno diversi tipi di record con uno specifico set di proprietà, ma che possono comunque essere analizzati insieme nelle query al repository.  In questo modo è possibile usare gli stessi strumenti e metodi per lavorare con diversi tipi di dati raccolti da diverse origini.

![Repository OMS](media/log-analytics-overview/overview.png)

Le origini connesse sono i computer e altre risorse che generano dati raccolti da Log Analytics.  Sono inclusi gli agenti installati nei computer [Windows](log-analytics-windows-agents.md) e [Linux](log-analytics-linux-agents.md) che si connettono direttamente o gli agenti in un [gruppo di gestione di System Center Operations Manager connesso](log-analytics-om-agents.md).  Per le risorse di Azure, Log Analytics raccoglie dati da [Monitoraggio di Azure e Diagnostica di Azure](log-analytics-azure-storage.md).

[origini dati](log-analytics-data-sources.md) sono i diversi tipi di dati raccolti da ogni origine connessa.  Sono inclusi gli [eventi](log-analytics-data-sources-windows-events.md) e i [dati sulle prestazioni](log-analytics-data-sources-performance-counters.md) ricavati dagli agenti [Windows](log-analytics-data-sources-windows-events.md) e Linux, oltre alle origini quali [log di IIS](log-analytics-data-sources-iis-logs.md) e [log di testo personalizzati](log-analytics-data-sources-custom-logs.md).  È possibile configurare ciascuna origine dati che si vuole raccogliere e la configurazione viene inviata automaticamente a ogni origine connessa.

Per requisiti personalizzati, è possibile usare l'[API di raccolta dati HTTP](log-analytics-data-collector-api.md) per scrivere dati nel repository da un client dell'API REST.

## <a name="log-analytics-architecture"></a>Architettura di Log Analytics
I requisiti di distribuzione di Log Analytics sono minimi, perché i componenti centrali sono ospitati nel cloud di Azure.  Ciò include il repository in aggiunta ai servizi che consentono di correlare e analizzare i dati raccolti.  Il portale è accessibile da qualsiasi browser, dunque non esiste alcun requisito per il software client.

È necessario installare gli agenti nei computer [Windows](log-analytics-windows-agents.md) e [Linux](log-analytics-linux-agents.md), ma non sono richiesti agenti aggiuntivi per i computer che fanno già parte di un [gruppo di gestione SCOM connesso](log-analytics-om-agents.md).  Gli agenti SCOM continueranno a comunicare con i server di gestione che inoltreranno i propri dati a Log Analytics.  Alcune soluzioni richiederanno tuttavia che gli agenti comunichino direttamente con Log Analytics.  La documentazione per ogni soluzione specificherà i relativi requisiti di comunicazione.

Quando ci si [iscrive a Log Analytics](log-analytics-get-started.md), verrà creata un'area di lavoro OMS.  L'area di lavoro può essere considerata come un ambiente di Log Analytics univoco con un archivio dati, origini dati e soluzioni personalizzati. È possibile creare più aree di lavoro nella sottoscrizione per supportare più ambienti, ad esempio di produzione e test.

![Architettura di Log Analytics](media/log-analytics-overview/architecture.png)

## <a name="next-steps"></a>Passaggi successivi
* [Iscriversi per ottenere un account gratuito di Log Analytics](log-analytics-get-started.md) da testare nel proprio ambiente.
* Per raccogliere i dati nel repository OMS visualizzare le diverse [origini dati](log-analytics-data-sources.md) disponibili.
* [Esplorare le soluzioni disponibili nella Raccolta soluzioni](log-analytics-add-solutions.md) per aggiungere funzionalità a Log Analytics.


