---
title: Usare Log Analytics con un&quot;app multi-tenant del database SQL | Microsoft Docs
description: Configurare e usare Log Analytics (OMS) con l&quot;app Wingtip Tickets (WTP) di esempio del database SQL di Azure
keywords: esercitazione database SQL
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: be747170a0d8a7a6defd790a3f8a122c4d397671
ms.openlocfilehash: 813a947ce4deb0755b44f4d287e00ae5218abfc4
ms.contentlocale: it-it
ms.lasthandoff: 05/23/2017


---
# <a name="setup-and-use-log-analytics-oms-with-the-wtp-sample-saas-app"></a>Configurare e usare Log Analytics (OMS) con l'app di esempio SaaS WTP

Questa esercitazione illustra come configurare e usare *Log Analytics([OMS](https://www.microsoft.com/cloud-platform/operations-management-suite))* con l'app WTP per il monitoraggio di pool elastici e database. Si basa sull'[esercitazione sul monitoraggio e la gestione delle prestazioni](sql-database-saas-tutorial-performance-monitoring.md) e mostra come usare *Log Analytics* per estendere le funzionalità di monitoraggio e avviso disponibili nel portale di Azure. Log Analytics è una soluzione particolarmente adatta alla gestione del monitoraggio e degli avvisi su larga scala, perché supporta centinaia di pool e centinaia di migliaia di database. Si tratta inoltre di una soluzione di monitoraggio singola che supporta l'integrazione del monitoraggio di applicazioni e servizi di Azure diversi tra più sottoscrizioni di Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Installare e configurare Log Analytics (OMS)
> * Usare Log Analytics per monitorare i pool e i database

Per completare questa esercitazione, verificare che siano soddisfatti i prerequisiti seguenti:

* L'app WTP è stata distribuita. Per eseguire la distribuzione in meno di cinque minuti, vedere [Distribuire ed esplorare un'applicazione SaaS multi-tenant di esempio che usa il database di SQL Azure](sql-database-saas-tutorial.md)
* Azure PowerShell è installato. Per informazioni dettagliate, vedere [Introduzione ad Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

Vedere l'[esercitazione sul monitoraggio e la gestione delle prestazioni](sql-database-saas-tutorial-performance-monitoring.md) per una presentazione degli scenari e dei modelli SaaS, nonché dei loro effetti sui requisiti per una soluzione di monitoraggio.

## <a name="monitoring-and-managing-performance-with-log-analytics-oms"></a>Monitoraggio e gestione delle prestazioni con Log Analytics (OMS)

Per il database SQL, il monitoraggio e gli avvisi sono disponibili per database e pool. Queste funzionalità predefinite di gestione del monitoraggio e degli avvisi sono destinate a risorse specifiche e risultano comode per un numero limitato di risorse. Sono invece meno efficaci per il monitoraggio di installazioni di grandi dimensioni o per ottenere una vista unificata su sottoscrizioni e risorse diverse.

Per scenari con volumi elevati, è possibile usare Log Analytics. Si tratta di un servizio di Azure separato che fornisce funzionalità di analisi per log di diagnostica e dati di telemetria raccolti in un'area di lavoro di Log Analytics, che consente di raccogliere dati di telemetria da molti servizi e supporta l'esecuzione di query e l'impostazione di avvisi. Log Analytics include un linguaggio di query predefinito e strumenti per la visualizzazione dei dati che consentono l'analisi e la visualizzazione di dati operativi. La soluzione Analisi SQL include varie viste e query predefinite per il monitoraggio e gli avvisi per pool elastici e database, oltre a consentire l'aggiunta e il salvataggio di query ad hoc all'occorrenza. OMS offre anche una finestra di progettazione viste personalizzata.

Le aree di lavoro e le soluzioni di analisi di Log Analytics possono essere aperte sia nel portale di Azure che in OMS. Il portale di Azure è il punto di accesso più recente, ma potrebbe trovarsi dietro il portale OMS in alcune aree.

### <a name="start-the-load-generator-to-create-data-to-analyze"></a>Avviare il generatore di carico per creare i dati da analizzare

1. Aprire **Demo-PerformanceMonitoringAndManagement.ps1** in **PowerShell ISE**. Mantenere lo script aperto perché potrebbe essere necessario eseguire vari scenari di generazione del carico durante l'esercitazione.
1. Se sono disponibili meno di cinque tenant, effettuare il provisioning di un batch di tenant per ottenere un contesto di monitoraggio più interessante:
   1. Impostare **$DemoScenario = 1,** **Effettuare il provisioning di un batch di tenant**
   1. Premere **F5** per eseguire lo script.

1. Impostare**$DemoScenario** = 2, **Generare un carico di intensità normale (circa 40 DTU)**.
1. Premere **F5** per eseguire lo script.

## <a name="get-the-wingtip-application-scripts"></a>Ottenere gli script dell'applicazione Wingtip

Gli script di Wingtip Tickets e il codice sorgente dell'applicazione sono disponibili nel repository GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). I file di script si trovano nella [cartella Learning Modules](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Scaricare la cartella **Learning Modules** nel computer locale, mantenendo la struttura delle cartelle.

## <a name="installing-and-configuring-log-analytics-and-the-azure-sql-analytics-solution"></a>Installazione e configurazione di Log Analytics e della soluzione Analisi SQL di Azure

Log Analytics è un servizio separato che deve essere configurato. Log Analytics raccoglie dati di log, dati di telemetria e metriche in un'area di lavoro di Log Analytics. Un'area di lavoro è una risorsa, analogamente ad altre risorse in Azure, e deve essere creata. Anche se l'area di lavoro non deve essere necessariamente creata nello stesso gruppo di risorse delle applicazioni da monitorare, questa è in genere la scelta più sensata. Nel caso dell'app WTP, in questo modo l'area di lavoro può essere facilmente eliminata con l'applicazione eliminando semplicemente il gruppo di risorse.

1. Aprire ...\\Learning Modules\\Performance Monitoring and Management\\Log Analytics\\*Demo-LogAnalytics.ps1* in **PowerShell ISE**.
1. Premere **F5** per eseguire lo script.

A questo punto dovrebbe essere possibile aprire Log Analytics nel portale di Azure (o nel portale OMS). Saranno necessari alcuni minuti per raccogliere i dati di telemetria e visualizzarli nell'area di lavoro di Log Analytics. L'esperienza diventerà più interessante raccogliendo una maggiore quantità di dati dal sistema, quindi si consiglia di lasciare in esecuzione il generatore di carico per un po' di tempo.


## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Usare Log Analytics e la soluzione Analisi SQL per monitorare i pool e i database


Per questo esercizio, aprire Log Analytics e il portale OMS per esaminare i dati di telemetria raccolti per i database e i pool WTP.

1. Passare al [portale di Azure](https://portal.azure.com) e aprire Log Analytics facendo clic su Altri servizi e cercando Log Analytics:

   ![aprire log analytics](media/sql-database-saas-tutorial-log-analytics/log-analytics-open.png)

1. Selezionare l'area di lavoro denominata *wtploganalytics-&lt;UTENTE&gt;*.

1. Selezionare **Panoramica** per aprire la soluzione Log Analytics nel portale di Azure.
   ![overview-link](media/sql-database-saas-tutorial-log-analytics/click-overview.png)

    **IMPORTANTE**: attendere perché potrebbero essere necessari un paio di minuti per attivare la soluzione.

1. Fare clic sul riquadro Analisi SQL di Azure per aprirlo.

    ![panoramica](media/sql-database-saas-tutorial-log-analytics/overview.png)

    ![analisi](media/sql-database-saas-tutorial-log-analytics/analytics.png)

1. La visualizzazione nel pannello della soluzione scorre lateralmente ed è disponibile una barra di scorrimento apposita nella parte inferiore (aggiornare il pannello se necessario).

1. Esplorare le diverse visualizzazioni, fare clic su di esse o sulle singole risorse per aprire una finestra di esplorazione di drill-down, in cui è possibile usare il dispositivo di scorrimento del tempo in alto a sinistra o fare clic su una barra verticale per concentrarsi su un intervallo di tempo più ristretto. Con questa visualizzazione è possibile selezionare singoli database o pool per concentrarsi su risorse specifiche:

    ![grafico](media/sql-database-saas-tutorial-log-analytics/chart.png)

1. Tornando al pannello della soluzione, se si scorre fino all'estrema destra verranno visualizzate alcune query salvate su cui è possibile fare clic per aprirle ed esplorarle. È possibile sperimentare modificandole e salvare eventuali query interessanti prodotte, in modo da poterle riaprire e usare con altre risorse.

1. Tornare al pannello dell'area di lavoro di Log Analytics e selezionare il portale OMS per aprire la soluzione in questo portale.

    ![oms](media/sql-database-saas-tutorial-log-analytics/oms.png)

1. Nel portale OMS è possibile configurare gli avvisi. Fare clic nella parte relativa agli avvisi della visualizzazione DTU dei database.

1. Nella visualizzazione Ricerca log che viene aperta si vedrà un grafico a barre delle metriche rappresentate.

    ![ricerca log](media/sql-database-saas-tutorial-log-analytics/log-search.png)

1. Se fa clic su Avviso sulla barra degli strumenti verrà visualizzata la configurazione dell'avviso ed è possibile modificarla.

    ![aggiungi regola di avviso](media/sql-database-saas-tutorial-log-analytics/add-alert.png)

Le funzionalità di monitoraggio e avviso in Log Analytics e in OMS si basano sull'esecuzione di query sui dati nell'area di lavoro, diversamente dagli avvisi per ogni pannello di risorse, specifici per le risorse. È quindi possibile definire un avviso per monitorare tutti i database, ad esempio, invece di definirne uno per ogni database oppure scrivere un avviso che usa una query composita su più tipi di risorsa. Le query sono limitate solo dai dati disponibili nell'area di lavoro.

Gli addebiti relativi a Log Analytics per database SQL sono basati sul volume di dati nell'area di lavoro. In questa esercitazione viene creata un'area di lavoro gratuita, limitata a 500 MB al giorno. Una volta raggiunto tale limite, non vengono aggiunti altri dati all'area di lavoro.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Installare e configurare Log Analytics (OMS)
> * Usare Log Analytics per monitorare i pool e i database

[Esercitazione sull'analisi dei tenant](sql-database-saas-tutorial-tenant-analytics.md)

## <a name="additional-resources"></a>Risorse aggiuntive

* [Altre esercitazioni basate sulla distribuzione iniziale dell'applicazione Wingtip Tickets Platform (WTP)](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [Log Analytics di Azure](../log-analytics/log-analytics-azure-sql.md)
* [OMS](https://blogs.technet.microsoft.com/msoms/2017/02/21/azure-sql-analytics-solution-public-preview/)

