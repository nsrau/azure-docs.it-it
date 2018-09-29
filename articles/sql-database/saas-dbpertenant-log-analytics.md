---
title: Usare Log Analytics con un'app multi-tenant del database SQL | Microsoft Docs
description: Installare e usare Log Analytics con un'app SaaS multi-tenant del database SQL di Azure
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 60139915e8d8dca382f4ef62b5129f1a84e7e80d
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47056710"
---
# <a name="set-up-and-use-log-analytics-with-a-multitenant-sql-database-saas-app"></a>Installare e usare Log Analytics con un'app SaaS multi-tenant del database SQL

Questa esercitazione illustra come installare e usare [Log Analytics](/azure/log-analytics/log-analytics-overview) per il monitoraggio di database e pool elastici. Questa esercitazione, basata sull'[esercitazione Monitoraggio e gestione delle prestazioni](saas-dbpertenant-performance-monitoring.md), illustra come usare Log Analytics per estendere le funzionalità di monitoraggio e avviso disponibili nel portale di Azure. Log Analytics supporta il monitoraggio di migliaia di pool elastici e di centinaia di migliaia di database. Log Analytics offre una soluzione di monitoraggio singola che supporta l'integrazione del monitoraggio di applicazioni e servizi di Azure diversi tra più sottoscrizioni di Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Installare e configurare Log Analytics.
> * Usare Log Analytics per monitorare i pool e i database.

Per completare questa esercitazione, verificare che i prerequisiti seguenti siano completati:

* È stata distribuita l'app SaaS di database per tenant Wingtip Tickets. Per eseguire la distribuzione in meno di cinque minuti, vedere [Distribuire ed esplorare l'applicazione di database per tenant SaaS Wingtip Tickets](saas-dbpertenant-get-started-deploy.md).
* Azure PowerShell è installato. Per altre informazioni, vedere [Get started with Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) (Introduzione ad Azure PowerShell).

Vedere l'[esercitazione Monitoraggio e gestione delle prestazioni](saas-dbpertenant-performance-monitoring.md) per una trattazione di scenari e modelli SaaS, nonché dei loro effetti sui requisiti per una soluzione di monitoraggio.

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-log-analytics"></a>Monitorare e gestire le prestazioni di database e pool elastici con Log Analytics

Per il database SQL di Azure, nel portale di Azure sono disponibili monitoraggio e avvisi per i database e i pool. Queste funzionalità incorporate sono pratiche, ma sono anche specifiche delle risorse. Ciò significa che non sono molto adatte per monitorare installazioni di grandi dimensioni o per offrire una visualizzazione unificata delle risorse e delle sottoscrizioni.

Negli scenari con volumi elevati, è possibile eseguire il monitoraggio e la generazione di avvisi tramite Log Analytics. Log Analytics è un servizio di Azure separato che consente l'analisi dei registri diagnostici e dei dati di telemetria forniti da un numero di servizi potenzialmente elevato e raccolti in un'area di lavoro. Log Analytics include un linguaggio di query predefinito e strumenti per la visualizzazione dei dati che consentono l'analisi di dati operativi. La soluzione Analisi SQL include varie viste e query predefinite per il monitoraggio e gli avvisi di pool e database elastici. Log Analytics offre anche una finestra di progettazione delle visualizzazioni personalizzata.

Le aree di lavoro e le soluzioni di analisi di Log Analytics possono essere aperte sia nel portale di Azure che in Operations Management Suite. Il portale di Azure è il punto di accesso più recente, ma in alcune aree può trovarsi dietro il portale di Operations Management Suite.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Creare dati di diagnostica delle prestazioni simulando un carico di lavoro nei tenant 

1. In PowerShell ISE aprire *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\Demo-PerformanceMonitoringAndManagement.ps1*. Tenere aperto lo script, perché durante l'esercitazione può essere necessario eseguire vari scenari di generazione del carico.
1. Se non lo si è ancora fatto, effettuare il provisioning di un batch di tenant per rendere più interessante il contesto di monitoraggio. Questo processo richiede qualche minuto.

   a. Impostare **$DemoScenario = 1**. _Effettuare il provisioning di un batch di tenant_.

   b. Per eseguire lo script e distribuire altri 17 tenant, premere F5.

1. Avviare quindi il generatore di carico per eseguire un carico simulato su tutti i tenant.

    a. Impostare **$DemoScenario = 2**, _Generare un carico di intensità normale (circa 30 DTU)_.

    b. Per eseguire lo script, premere F5.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Ottenere gli script dell'applicazione del database per tenant SaaS Wingtip Tickets

Gli script e il codice sorgente dell'applicazione del database multi-tenant SaaS Wingtip Tickets sono disponibili nel repository [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) di GitHub. Per i passaggi da eseguire per scaricare e sbloccare gli script PowerShell di Wingtip Tickets, vedere le [linee guida generali](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="install-and-configure-log-analytics-and-the-azure-sql-analytics-solution"></a>Installare e configurare Log Analytics e la soluzione Analisi SQL di Azure

Log Analytics è un servizio separato che deve essere configurato. Dati di log, dati di telemetria e metriche vengono raccolti da Log Analytics in un'area di lavoro del servizio stesso. Come per altre risorse in Azure, è necessario creare un'area di lavoro di Log Analytics. L'area di lavoro non deve essere necessariamente creata nello stesso gruppo di risorse delle applicazioni da monitorare, anche se spesso questa è la scelta più sensata. Per l'app Wingtip Tickets, usare un unico gruppo di risorse per garantire che l'area di lavoro venga eliminata con l'applicazione.

1. In PowerShell ISE aprire *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\Log Analytics\\Demo-LogAnalytics.ps1*.
1. Per eseguire lo script, premere F5.

È ora possibile aprire Log Analytics nel portale di Azure o nel portale di Operations Management Suite. Per raccogliere i dati di telemetria e visualizzarli nell'area di lavoro di Log Analytics è necessario qualche minuto. Maggiore il tempo concesso al sistema per la raccolta di dati di diagnostica, più interessante sarà l'esperienza. 

## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Usare Log Analytics e la soluzione Analisi SQL per monitorare i pool e i database


Per questa esercitazione, aprire Log Analytics e il portale di Operations Management Suite per esaminare i dati di telemetria raccolti per i database e i pool.

1. Accedere al [portale di Azure](https://portal.azure.com). Per aprire Log Analytics, selezionare **Tutti i servizi** e quindi cercare Log Analytics.

   ![Aprire Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Selezionare l'area di lavoro denominata _wtploganalytics-&lt;utente&gt;_.

1. Selezionare **Panoramica** per aprire la soluzione Log Analytics nel portale di Azure.

   ![Panoramica](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > L'attivazione della soluzione può richiedere qualche minuto. 

1. Fare clic sul riquadro **Analisi SQL di Azure** per aprirlo.

    ![Riquadro di panoramica](media/saas-dbpertenant-log-analytics/overview.png)

1. Le visualizzazioni nella soluzione scorrono lateralmente quando si usa la barra di scorrimento presente nella parte inferiore. Se necessario, aggiornare la pagina.

1. Per esplorare la pagina di riepilogo tramite uno strumento di esplorazione drill-down, selezionare i riquadri o i singoli database.

    ![Dashboard di Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. Modificare l'impostazione del filtro per modificare l'intervallo di tempo. Per questa esercitazione, selezionare **Ultima ora**.

    ![Filtro temporale](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. Selezionare un singolo database per esplorare l'uso delle query e le metriche per tale database.

    ![Analisi del database](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. Per visualizzare le metriche di utilizzo, scorrere la pagina di analisi verso destra.
 
     ![Metriche del database](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. Scorrere la pagina di analisi a sinistra e selezionare il riquadro del server nell'elenco **Informazioni sulla risorsa**.  

    ![Elenco Informazioni sulla risorsa](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    Si aprirà una pagina con i pool e i database nel server.

    ![Server con pool e database](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. Selezionare un pool. Nella pagina del pool che si apre, scorrere verso destra per visualizzare le metriche del pool. 

    ![Metriche del pool](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


1. Tornare all'area di lavoro di Log Analytics e selezionare **Portale di OMS** per aprire l'area di lavoro.

    ![Riquadro del portale di Operations Management Suite](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

Nel portale di Operations Management Suite è possibile esplorare ulteriormente i dati di log e delle metriche nell'area di lavoro. 

Le funzionalità di monitoraggio e avviso in Log Analytics si basano sull'esecuzione di query sui dati nell'area di lavoro, diversamente dagli avvisi definiti per ogni risorsa nel portale di Azure. Basando gli avvisi sulle query, è possibile definire un singolo avviso per monitorare tutti i database, invece di definirne uno per ogni database. Le query sono limitate solo dai dati disponibili nell'area di lavoro.

Per altre informazioni su come usare Log Analytics per eseguire query e impostare avvisi, vedere [Utilizzo delle regole di avviso in Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating).

Gli addebiti relativi a Log Analytics per il database SQL sono basati sul volume di dati nell'area di lavoro. In questa esercitazione è stata creata un'area di lavoro gratuita, limitata a 500 MB al giorno. Dopo il raggiungimento di tale limite, non vengono aggiunti altri dati all'area di lavoro.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Installare e configurare Log Analytics.
> * Usare Log Analytics per monitorare i pool e i database.

Provare l'[esercitazione sull'analisi dei tenant](saas-dbpertenant-log-analytics.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Altre esercitazioni basate sulla distribuzione iniziale dell'applicazione di database per tenant SaaS Wingtip Tickets](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Log Analytics di Azure](../log-analytics/log-analytics-azure-sql.md)
