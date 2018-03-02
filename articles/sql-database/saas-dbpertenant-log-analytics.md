---
title: Usare Log Analytics con un'app multi-tenant del database SQL | Microsoft Docs
description: Configurare e usare Log Analytics (OMS) con un'app SaaS multi-tenant del database SQL di Azure
keywords: esercitazione database SQL
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: billgib; sstein
ms.openlocfilehash: 90510520e5f6bbfa8aea4026d7437a4a4881984f
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2018
---
# <a name="set-up-and-use-log-analytics-oms-with-a-multi-tenant-azure-sql-database-saas-app"></a>Configurare e usare Log Analytics (OMS) con un'app SaaS multi-tenant del database SQL di Azure

Questa esercitazione illustra come configurare e usare *Log Analytics ([OMS](https://www.microsoft.com/cloud-platform/operations-management-suite))* per il monitoraggio di database e pool elastici. Questa esercitazione si basa sull'[esercitazione sul monitoraggio e la gestione delle prestazioni](saas-dbpertenant-performance-monitoring.md) e mostra come usare *Log Analytics* per estendere le funzionalità di monitoraggio e avviso disponibili nel portale di Azure. Log Analytics supporta il monitoraggio di migliaia di pool elastici e di centinaia di migliaia di database. Log Analytics offre una soluzione di monitoraggio singola che supporta l'integrazione del monitoraggio di applicazioni e servizi di Azure diversi tra più sottoscrizioni di Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Installare e configurare Log Analytics (OMS)
> * Usare Log Analytics per monitorare i pool e i database

Per completare questa esercitazione, verificare che i prerequisiti seguenti siano completati:

* È stata distribuita l'app del database per tenant SaaS Wingtip Tickets. Per eseguire la distribuzione in meno di cinque minuti, vedere [Deploy and explore the Wingtip Tickets SaaS Database Per Tenant application](saas-dbpertenant-get-started-deploy.md) (Distribuire ed esplorare l'applicazione del database per tenant SaaS Wingtip Tickets)
* Azure PowerShell è installato. Per informazioni dettagliate, vedere [Introduzione ad Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

Vedere l'[esercitazione sul monitoraggio e la gestione delle prestazioni](saas-dbpertenant-performance-monitoring.md) per una presentazione degli scenari e dei modelli SaaS, nonché dei loro effetti sui requisiti per una soluzione di monitoraggio.

## <a name="monitoring-and-managing-database-and-elastic-pool-performance-with-log-analytics-or-operations-management-suite-oms"></a>Monitoraggio e gestione delle prestazioni del pool elastico e dei database con Log Analytics o Operations Management Suite (OMS)

Per il database SQL, il monitoraggio e gli avvisi sono disponibili per database e pool nel portale di Azure. Queste funzionalità predefinite di gestione del monitoraggio e degli avvisi risultano comode, ma essendo destinate a risorse specifiche, sono meno efficaci per il monitoraggio di installazioni di grandi dimensioni o per ottenere una vista unificata su sottoscrizioni e risorse diverse.

Nei scenari con volumi elevati, è possibile usare Log Analytics per il monitoraggio e la gestione degli avvisi. Log Analytics è un servizio di Azure separato che consente l'analisi su registri diagnostici e dati di telemetria raccolti in un'area di lavoro da potenzialmente molti servizi. Log Analytics include un linguaggio di query predefinito e strumenti per la visualizzazione dei dati che consentono l'analisi di dati operativi. La soluzione Analisi SQL include varie viste e query predefinite per il monitoraggio e gli avvisi per pool elastici e database. OMS offre anche una finestra di progettazione viste personalizzata.

Le aree di lavoro e le soluzioni di analisi di Log Analytics possono essere aperte sia nel portale di Azure che in OMS. Il portale di Azure è il punto di accesso più recente, ma potrebbe trovarsi dietro il portale OMS in alcune aree.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Creare dati di diagnostica delle prestazioni simulando un carico di lavoro nei tenant 

1. In **PowerShell ISE** aprire *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\**Demo-PerformanceMonitoringAndManagement.ps1***. Mantenere lo script aperto perché potrebbe essere necessario eseguire vari scenari di generazione del carico durante l'esercitazione.
1. Se non ancora eseguito, effettuare il provisioning di un batch di tenant per ottenere un contesto di monitoraggio più interessante. Questa operazione richiede alcuni minuti.
   1. Impostare **$DemoScenario = 1**, _Effettuare il provisioning di un batch di tenant_
   1. Per eseguire lo script e distribuire altri 17 tenant, premere **F5**.  

1. Avviare quindi il generatore di carico per eseguire un carico simulato su tutti i tenant.  
    1. Impostare **$DemoScenario = 2**, _Generare un carico di intensità normale (circa 30 DTU)_.
    1. Premere **F5** per eseguire lo script.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Ottenere gli script dell'applicazione del database per tenant SaaS Wingtip Tickets

Gli script e il codice sorgente dell'applicazione SaaS di database multi-tenant Wingtip Tickets sono disponibili nel repository [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) di GitHub. Leggere le [linee guida generali](saas-tenancy-wingtip-app-guidance-tips.md) per i passaggi da seguire per scaricare e sbloccare gli script PowerShell Wingtip Tickets.

## <a name="installing-and-configuring-log-analytics-and-the-azure-sql-analytics-solution"></a>Installazione e configurazione di Log Analytics e della soluzione Analisi SQL di Azure

Log Analytics è un servizio separato che deve essere configurato. Log Analytics raccoglie dati di log, dati di telemetria e metriche in un'area di lavoro di Log Analytics. Un'area di lavoro di Log Analytics è una risorsa, analogamente ad altre risorse in Azure, e deve essere creata. Anche se l'area di lavoro non deve essere necessariamente creata nello stesso gruppo di risorse delle applicazioni da monitorare, questa è in genere la scelta più sensata. Per l'app Wingtip Tickets, usando un singolo gruppo di risorse si garantisce che l'area di lavoro venga eliminata con l'applicazione.

1. In **PowerShell ISE** aprire *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\Log Analytics\\**Demo-LogAnalytics.ps1***.
1. Premere **F5** per eseguire lo script.

A questo punto, dovrebbe essere possibile aprire Log Analytics nel portale di Azure (o nel portale OMS). Sono necessari alcuni minuti per raccogliere i dati di telemetria e visualizzarli nell'area di lavoro di Log Analytics. L'esperienza diventa più interessante raccogliendo una maggiore quantità di dati di diagnostica dal sistema, quindi si consiglia di lasciare in esecuzione il generatore di carico per un po' di tempo.

## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Usare Log Analytics e la soluzione Analisi SQL per monitorare i pool e i database


Per questo esercizio, aprire Log Analytics e il portale OMS per esaminare i dati di telemetria raccolti per i database e i pool.

1. Passare al [portale di Azure](https://portal.azure.com) e aprire Log Analytics facendo clic su **All services** (Tutti i servizi) e cercando Log Analytics:

   ![aprire log analytics](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Selezionare l'area di lavoro denominata _wtploganalytics-&lt;utente&gt;_.

1. Selezionare **Panoramica** per aprire la soluzione Log Analytics nel portale di Azure.

   ![overview-link](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Attendere perché potrebbe essere necessario qualche minuto per attivare la soluzione.

1. Fare clic sul riquadro Analisi SQL di Azure per aprirlo.

    ![panoramica](media/saas-dbpertenant-log-analytics/overview.png)

    ![analisi](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. Le visualizzazioni nella soluzione scorrono lateralmente ed è disponibile una barra di scorrimento apposita nella parte inferiore (aggiornare la pagina se necessario).

1. Esplorare la pagina di riepilogo facendo clic sui riquadri o su un singolo database per aprire una finestra di esplorazione drill-down.

1. Modificare l'impostazione del filtro per cambiare l'intervallo di tempo: per questa esercitazione selezionare _Ultima ora_

    ![filtro dell'ora](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. Selezionare un singolo database per esplorare l'uso delle query e le metriche per il database.

    ![analisi del database](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. Per visualizzare le metriche di utilizzo scorrere la pagina di analisi verso destra.
 
     ![metriche del database](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. Scorrere la pagina di analisi a sinistra e fare clic sul riquadro del server nell'elenco delle informazioni sulle risorse. Viene visualizzata una pagina con i pool e i database nel server. 

     ![informazioni sulle risorse](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

 
     ![server con i pool e i database](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. Nella pagina del server che si apre, in cui sono elencati i pool e i database del server, fare clic sul pool.  Nella pagina del pool che si apre, scorrere verso destra per visualizzare le metriche del pool.  

     ![metriche del pool](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)



1. Tornare all'area di lavoro di Log Analytics e selezionare il **portale OMS** per aprirvi l'area di lavoro.

    ![oms](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

Nel portale di OMS è possibile esplorare ulteriormente i dati di log e delle metriche nell'area di lavoro.  

Le funzionalità di monitoraggio e avviso in Log Analytics e in OMS si basano sull'esecuzione di query sui dati nell'area di lavoro, diversamente dagli avvisi definiti su ciascuna risorsa nel portale di Azure. Basando gli avvisi sulle query, è possibile definire un singolo avviso per monitorare tutti i database, invece di definirne uno per ogni database. Le query sono limitate solo dai dati disponibili nell'area di lavoro.

Per altre informazioni sull'uso di OMS per eseguire query e impostare avvisi, vedere [Utilizzo delle regole di avviso in Log Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-alerts-creating).

Gli addebiti relativi a Log Analytics per database SQL sono basati sul volume di dati nell'area di lavoro. In questa esercitazione è stata creata un'area di lavoro gratuita, limitata a 500 MB al giorno. Una volta raggiunto tale limite, non vengono aggiunti altri dati all'area di lavoro.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Installare e configurare Log Analytics (OMS)
> * Usare Log Analytics per monitorare i pool e i database

[Esercitazione sull'analisi dei tenant](saas-dbpertenant-log-analytics.md)

## <a name="additional-resources"></a>Risorse aggiuntive

* [Altre esercitazioni basate sulla distribuzione iniziale dell'applicazione del database per tenant SaaS Wingtip Tickets](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Log Analytics di Azure](../log-analytics/log-analytics-azure-sql.md)
* [OMS](https://blogs.technet.microsoft.com/msoms/2017/02/21/azure-sql-analytics-solution-public-preview/)
