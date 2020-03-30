---
title: Log di Monitoraggio di Azure con un'app multi-tenantAzure Monitor logs with a multitenant app
description: Configurare e usare i log di Monitoraggio di Azure con un'app SaaS del database SQL di Azure multi-tenantSet up and use Azure Monitor logs with a multitenant Azure SQL Database SaaS app
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 01/25/2019
ms.openlocfilehash: 7429a9d5e9a803f0e9a6f900c5d81e77e7477a48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214497"
---
# <a name="set-up-and-use-azure-monitor-logs-with-a-multitenant-sql-database-saas-app"></a>Configurare e usare i log di Monitoraggio di Azure con un'app SaaS del database SQL multi-tenantSet up and use Azure Monitor logs with a multitenant SQL Database SaaS app

In questa esercitazione vengono impostati e utilizzati [i log](/azure/log-analytics/log-analytics-overview) di Monitoraggio di Azure per monitorare pool elastici e database. Questa esercitazione si basa sull'esercitazione sul [monitoraggio e la gestione delle prestazioni.](saas-dbpertenant-performance-monitoring.md) Viene illustrato come usare i log di Monitoraggio di Azure per aumentare il monitoraggio e gli avvisi forniti nel portale di Azure.It shows how to use Azure Monitor logs to augment the monitoring and alerting provided in the Azure portal. I log di Monitoraggio di Azure supportano il monitoraggio di migliaia di pool elastici e centinaia di migliaia di database. I log di Monitoraggio di Azure offrono un'unica soluzione di monitoraggio, che può integrare il monitoraggio di applicazioni e servizi di Azure diversi tra più sottoscrizioni di Azure.Azure Monitor logs provides a single monitoring solution, which can integrate monitoring of different applications and Azure services across multiple Azure subscriptions.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Installare e configurare i log di Monitoraggio di Azure.Install and configure Azure Monitor logs.
> * Usare i log di Monitoraggio di Azure per monitorare pool e database.

Per completare questa esercitazione, verificare che i prerequisiti seguenti siano completati:

* È stata distribuita l'app SaaS di database per tenant Wingtip Tickets. Per eseguire la distribuzione in meno di cinque minuti, vedere [Distribuire ed esplorare l'applicazione di database per tenant SaaS Wingtip Tickets](saas-dbpertenant-get-started-deploy.md).
* Azure PowerShell è installato. Per altre informazioni, vedere [Get started with Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) (Introduzione ad Azure PowerShell).

Vedere l'[esercitazione Monitoraggio e gestione delle prestazioni](saas-dbpertenant-performance-monitoring.md) per una trattazione di scenari e modelli SaaS, nonché dei loro effetti sui requisiti per una soluzione di monitoraggio.

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-azure-monitor-logs"></a>Monitorare e gestire le prestazioni del database ed è del pool elastico con i log di Monitoraggio di AzureMonitor and manage database and elastic pool performance with Azure Monitor logs

Per il database SQL di Azure, nel portale di Azure sono disponibili monitoraggio e avvisi per i database e i pool. Queste funzionalità incorporate sono pratiche, ma sono anche specifiche delle risorse. Ciò significa che non sono molto adatte per monitorare installazioni di grandi dimensioni o per offrire una visualizzazione unificata delle risorse e delle sottoscrizioni.

Per gli scenari con volumi elevati, è possibile usare i log di Monitoraggio di Azure per il monitoraggio e gli avvisi. Monitoraggio di Azure è un servizio di Azure separato che consente l'analisi dei log raccolti in un'area di lavoro da potenzialmente molti servizi. I log di Monitoraggio di Azure offrono un linguaggio di query incorporato e strumenti di visualizzazione dei dati che consentono l'analisi dei dati operativi. La soluzione Analisi SQL include varie viste e query predefinite per il monitoraggio e gli avvisi di pool e database elastici. I log di Monitoraggio di Azure offrono anche una finestra di progettazione di visualizzazioni personalizzata.

Le aree di lavoro OMS sono ora denominate aree di lavoro di Log Analytics. Le aree di lavoro e le soluzioni di analisi di Log Analytics possono essere aperte nel portale di Azure. Il portale di Azure è il punto di accesso più recente, ma in alcune aree può trovarsi dietro il portale di Operations Management Suite.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Creare dati di diagnostica delle prestazioni simulando un carico di lavoro nei tenant 

1. In PowerShell ISE aprire *.. WingtipTicketsSaaS-MultiTenantDb-master\\Moduli\\di apprendimento\\Monitoraggio e gestione Monitoraggio e gestione Demo-PerformanceMonitoringAndManagement.ps1 . \\* Tenere aperto lo script, perché durante l'esercitazione può essere necessario eseguire vari scenari di generazione del carico.
1. Se non lo si è ancora fatto, effettuare il provisioning di un batch di tenant per rendere più interessante il contesto di monitoraggio. Questo processo richiede qualche minuto.

   a. Impostare **$DemoScenario , 1**, _Effettuare il provisioning di un batch di tenant_.

   b. Per eseguire lo script e distribuire altri 17 tenant, premere F5.

1. Avviare quindi il generatore di carico per eseguire un carico simulato su tutti i tenant.

    a. Impostare **$DemoScenario 2**, Generare un carico di _intensità normale (circa 30 DTU)_.

    b. Per eseguire lo script, premere F5.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Ottenere gli script dell'applicazione SaaS di database per tenant Wingtip Tickets

Gli script di database multitenant Wingtip Tickets e il codice sorgente dell'applicazione sono disponibili nel repository [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub. Per i passaggi da eseguire per scaricare e sbloccare gli script PowerShell di Wingtip Tickets, vedere le [linee guida generali](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="install-and-configure-log-analytics-workspace-and-the-azure-sql-analytics-solution"></a>Installare e configurare l'area di lavoro log Analytics e la soluzione Analisi SQL di AzureInstall and configure Log Analytics workspace and the Azure SQL Analytics solution

Monitoraggio di Azure è un servizio separato che deve essere configurato. I log di Monitoraggio di Azure raccolgono dati, dati di telemetria e metriche di log in un'area di lavoro di Log Analytics.Azure Monitor logs collects log data, telemetry, and metrics in a Log Analytics workspace. Come per altre risorse in Azure, è necessario creare un'area di lavoro Log Analytics. L'area di lavoro non deve essere necessariamente creata nello stesso gruppo di risorse delle applicazioni da monitorare, anche se spesso questa è la scelta più sensata. Per l'app Wingtip Tickets, usare un unico gruppo di risorse per garantire che l'area di lavoro venga eliminata con l'applicazione.

1. In PowerShell ISE aprire *.. WingtipTicketsSaaS-MultiTenantDb-master\\Moduli\\di apprendimento\\Monitoraggio\\delle prestazioni e Gestione Log Analytics Demo-LogAnalytics.ps1 . \\*
1. Per eseguire lo script, premere F5.

Ora è possibile aprire i log di Monitoraggio di Azure nel portale di Azure.Now you can open Azure Monitor logs in the Azure portal. Per raccogliere i dati di telemetria e visualizzarli nell'area di lavoro Log Analytics è necessario qualche minuto. Maggiore il tempo concesso al sistema per la raccolta di dati di diagnostica, più interessante sarà l'esperienza. 

## <a name="use-log-analytics-workspace-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Usare l'area di lavoro di Log Analytics e la soluzione Analisi SQL per monitorare pool e databaseUse Log Analytics workspace and the SQL Analytics solution to monitor pools and databases


In questo esercizio aprire l'area di lavoro Log Analytics nel portale di Azure per esaminare i dati di telemetria raccolti per i database e i pool.

1. Passare al [portale di Azure](https://portal.azure.com). Selezionare Tutti i servizi per aprire l'area di lavoro di Log **Analytics.Select All services** to open Log Analytics workspace. e quindi cercare Log Analytics.

   ![Aprire l'area di lavoro di Log AnalyticsOpen Log Analytics workspace](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Selezionare l'area di lavoro denominata _wtploganalytics-&lt;utente&gt;_.

1. Selezionare **Panoramica** per aprire la soluzione di analisi dei log nel portale di Azure.Select Overview to open the log analytics solution in the Azure portal.

   ![Panoramica](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > L'attivazione della soluzione può richiedere qualche minuto. 

1. Fare clic sul riquadro **Analisi SQL di Azure** per aprirlo.

    ![Riquadro di panoramica](media/saas-dbpertenant-log-analytics/overview.png)

1. Le visualizzazioni nella soluzione scorrono lateralmente quando si usa la barra di scorrimento presente nella parte inferiore. Se necessario, aggiornare la pagina.

1. Per esplorare la pagina di riepilogo tramite uno strumento di esplorazione drill-down, selezionare i riquadri o i singoli database.

    ![Dashboard di analisi dei logLog analytics dashboard](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

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

Il monitoraggio e gli avvisi nei log di Monitoraggio di Azure sono basati su query sui dati nell'area di lavoro, a differenza degli avvisi definiti in ogni risorsa nel portale di Azure.Monitoring and alerting in Azure Monitor logs are based on queries over the data in the workspace, unlike the alerting defined on each resource in the Azure portal. Basando gli avvisi sulle query, è possibile definire un singolo avviso per monitorare tutti i database, invece di definirne uno per ogni database. Le query sono limitate solo dai dati disponibili nell'area di lavoro.

Per altre informazioni su come usare i log di Monitoraggio di Azure per eseguire query e impostare avvisi, vedere Usare le regole di avviso nei log di Monitoraggio di Azure.For more information on how to use Azure Monitor logs to query and set alerts, see [Work with alert rules in Azure Monitor logs.](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating)

Log di Monitoraggio di Azure per gli addebiti del database SQL in base al volume di dati nell'area di lavoro. In questa esercitazione è stata creata un'area di lavoro gratuita, limitata a 500 MB al giorno. Dopo il raggiungimento di tale limite, non vengono aggiunti altri dati all'area di lavoro.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Installare e configurare i log di Monitoraggio di Azure.Install and configure Azure Monitor logs.
> * Usare i log di Monitoraggio di Azure per monitorare pool e database.

Provare l'[esercitazione sull'analisi dei tenant](saas-dbpertenant-log-analytics.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Altre esercitazioni basate sulla distribuzione iniziale dell'applicazione di database per tenant SaaS Wingtip Tickets](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Log di Monitoraggio di Azure](../azure-monitor/insights/azure-sql.md)
