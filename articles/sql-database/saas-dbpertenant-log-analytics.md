---
title: Usare i log di monitoraggio di Azure con un'app multi-tenant del database SQL
description: Configurare e usare i log di monitoraggio di Azure con un'app SaaS multi-tenant del database SQL di Azure
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 01/25/2019
ms.openlocfilehash: d7b568424d5e33c19efc9d9d9c21d0023459b6c7
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692141"
---
# <a name="set-up-and-use-azure-monitor-logs-with-a-multitenant-sql-database-saas-app"></a>Configurare e usare i log di monitoraggio di Azure con un'app SaaS multi-tenant del database SQL

Questa esercitazione illustra come configurare e usare i [log di monitoraggio di Azure](/azure/log-analytics/log-analytics-overview) per monitorare i pool elastici e i database. Questa esercitazione, basata sull'[esercitazione Monitoraggio e gestione delle prestazioni](saas-dbpertenant-performance-monitoring.md), Viene illustrato come usare i log di monitoraggio di Azure per aumentare il monitoraggio e gli avvisi forniti nell'portale di Azure. Log di monitoraggio di Azure supporta il monitoraggio di migliaia di pool elastici e centinaia di migliaia di database. Log di monitoraggio di Azure offre un'unica soluzione di monitoraggio, che consente di integrare il monitoraggio di applicazioni e servizi di Azure diversi tra più sottoscrizioni di Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Installare e configurare i log di monitoraggio di Azure.
> * Usare i log di monitoraggio di Azure per monitorare i pool e i database.

Per completare questa esercitazione, verificare che siano soddisfatti i prerequisiti seguenti:

* È stata distribuita l'app di database per tenant SaaS Wingtip Tickets. Per eseguire la distribuzione in meno di cinque minuti, vedere [Distribuire ed esplorare l'applicazione di database per tenant SaaS Wingtip Tickets](saas-dbpertenant-get-started-deploy.md).
* Azure PowerShell è installato. Per altre informazioni, vedere [Get started with Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) (Introduzione ad Azure PowerShell).

Vedere l'[esercitazione Monitoraggio e gestione delle prestazioni](saas-dbpertenant-performance-monitoring.md) per una trattazione di scenari e modelli SaaS, nonché dei loro effetti sui requisiti per una soluzione di monitoraggio.

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-azure-monitor-logs"></a>Monitorare e gestire le prestazioni di database e pool elastici con i log di monitoraggio di Azure

Per il database SQL di Azure, nel portale di Azure sono disponibili monitoraggio e avvisi per i database e i pool. Queste funzionalità incorporate sono pratiche, ma sono anche specifiche delle risorse. Ciò significa che non sono molto adatte per monitorare installazioni di grandi dimensioni o per offrire una visualizzazione unificata delle risorse e delle sottoscrizioni.

Per gli scenari con volumi elevati, è possibile usare i log di monitoraggio di Azure per il monitoraggio e l'invio di avvisi. Monitoraggio di Azure è un servizio di Azure separato che consente di esaminare i log di diagnostica e i dati di telemetria raccolti in un'area di lavoro da potenzialmente molti servizi. Log di monitoraggio di Azure offre un linguaggio di query integrato e strumenti per la visualizzazione dei dati che consentono l'analisi dei dati operativi. La soluzione Analisi SQL include varie viste e query predefinite per il monitoraggio e gli avvisi di pool e database elastici. Log di monitoraggio di Azure offre anche una finestra di progettazione visualizzazioni personalizzata.

Le aree di lavoro OMS sono ora denominate aree di lavoro di Log Analytics. Le aree di lavoro e le soluzioni di analisi di Log Analytics possono essere aperte nel portale di Azure. Il portale di Azure è il punto di accesso più recente, ma in alcune aree può trovarsi dietro il portale di Operations Management Suite.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Creare dati di diagnostica delle prestazioni simulando un carico di lavoro nei tenant 

1. In PowerShell ISE aprire *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\Demo-PerformanceMonitoringAndManagement.ps1*. Tenere aperto lo script, perché durante l'esercitazione può essere necessario eseguire vari scenari di generazione del carico.
1. Se non lo si è ancora fatto, effettuare il provisioning di un batch di tenant per rendere più interessante il contesto di monitoraggio. Questo processo richiede qualche minuto.

   a. Impostare **$DemoScenario = 1**. _Effettuare il provisioning di un batch di tenant_.

   b. Per eseguire lo script e distribuire altri 17 tenant, premere F5.

1. Avviare quindi il generatore di carico per eseguire un carico simulato su tutti i tenant.

    a. Impostare **$DemoScenario = 2**, _generare un carico di intensità normale (circa 30 DTU)_ .

    b. Premere F5 per eseguire lo script.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Ottenere gli script dell'applicazione del database per tenant SaaS Wingtip Tickets

Gli script e il codice sorgente dell'applicazione del database multi-tenant SaaS Wingtip Tickets sono disponibili nel repository [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) di GitHub. Per i passaggi da eseguire per scaricare e sbloccare gli script PowerShell di Wingtip Tickets, vedere le [linee guida generali](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="install-and-configure-log-analytics-workspace-and-the-azure-sql-analytics-solution"></a>Installare e configurare Log Analytics area di lavoro e la soluzione Analisi SQL di Azure

Monitoraggio di Azure è un servizio separato che deve essere configurato. Log di monitoraggio di Azure raccoglie dati di log, telemetria e metriche in un'area di lavoro Log Analytics. Come per altre risorse in Azure, è necessario creare un'area di lavoro Log Analytics. L'area di lavoro non deve essere necessariamente creata nello stesso gruppo di risorse delle applicazioni da monitorare, anche se spesso questa è la scelta più sensata. Per l'app Wingtip Tickets, usare un unico gruppo di risorse per garantire che l'area di lavoro venga eliminata con l'applicazione.

1. In PowerShell ISE aprire *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\Log Analytics\\Demo-LogAnalytics.ps1*.
1. Premere F5 per eseguire lo script.

A questo punto è possibile aprire i log di monitoraggio di Azure nel portale di Azure. Per raccogliere i dati di telemetria e visualizzarli nell'area di lavoro Log Analytics è necessario qualche minuto. Maggiore il tempo concesso al sistema per la raccolta di dati di diagnostica, più interessante sarà l'esperienza. 

## <a name="use-log-analytics-workspace-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Usare Log Analytics area di lavoro e la soluzione analisi SQL per monitorare i pool e i database


In questo esercizio, aprire Log Analytics area di lavoro nel portale di Azure per esaminare i dati di telemetria raccolti per i database e i pool.

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **tutti i servizi** per aprire log Analytics area di lavoro. e quindi cercare Log Analytics.

   ![Apri area di lavoro Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Selezionare l'area di lavoro denominata _wtploganalytics-&lt;utente&gt;_ .

1. Selezionare **Panoramica** per aprire la soluzione log analytics nel portale di Azure.

   ![Panoramica](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > L'attivazione della soluzione può richiedere qualche minuto. 

1. Fare clic sul riquadro **Analisi SQL di Azure** per aprirlo.

    ![Riquadro di panoramica](media/saas-dbpertenant-log-analytics/overview.png)

1. Le visualizzazioni nella soluzione scorrono lateralmente quando si usa la barra di scorrimento presente nella parte inferiore. Se necessario, aggiornare la pagina.

1. Per esplorare la pagina di riepilogo tramite uno strumento di esplorazione drill-down, selezionare i riquadri o i singoli database.

    ![Dashboard di log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

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


1. Tornare all'area di lavoro Log Analytics e selezionare **Portale di OMS** per aprire l'area di lavoro.

    ![Area di lavoro Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

Nell'area di lavoro Log Analytics è possibile esplorare ulteriormente i dati di log e delle metriche. 

Il monitoraggio e gli avvisi nei log di monitoraggio di Azure si basano sulle query sui dati nell'area di lavoro, a differenza degli avvisi definiti per ogni risorsa nel portale di Azure. Basando gli avvisi sulle query, è possibile definire un singolo avviso per monitorare tutti i database, invece di definirne uno per ogni database. Le query sono limitate solo dai dati disponibili nell'area di lavoro.

Per altre informazioni su come usare i log di monitoraggio di Azure per eseguire query e impostare gli avvisi, vedere usare [le regole di avviso nei log di monitoraggio di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating).

Monitoraggio di Azure registra gli addebiti per il database SQL in base al volume di dati nell'area di lavoro. In questa esercitazione è stata creata un'area di lavoro gratuita, limitata a 500 MB al giorno. Dopo il raggiungimento di tale limite, non vengono aggiunti altri dati all'area di lavoro.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Installare e configurare i log di monitoraggio di Azure.
> * Usare i log di monitoraggio di Azure per monitorare i pool e i database.

Provare l'[esercitazione sull'analisi dei tenant](saas-dbpertenant-log-analytics.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Altre esercitazioni basate sulla distribuzione iniziale dell'applicazione di database per tenant SaaS Wingtip Tickets](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Log di Monitoraggio di Azure](../azure-monitor/insights/azure-sql.md)
