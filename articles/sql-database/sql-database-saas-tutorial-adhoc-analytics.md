---
title: Eseguire query di analisi ad-hoc su tutti i tenant (applicazione SaaS di esempio con database SQL di Azure) | Microsoft Docs
description: Eseguire query di analisi ad-hoc su tutti i tenant
keywords: esercitazione database SQL
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c00c25116002c46fc94c5ea59acc101a2055b689
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


---
# <a name="run-ad-hoc-analytics-queries-across-all-wtp-tenants"></a>Eseguire query analitiche ad-hoc su tutti i tenant WTP

Questa esercitazione illustra come creare un database di analisi ad-hoc ed eseguire diverse query su tutti i tenant. Queste query consentono di estrarre informazioni approfondite nascoste nei dati operativi quotidiani dell'app WTP.

Per eseguire query di analisi ad hoc su più tenant, l'app WTP usa [query elastiche](sql-database-elastic-query-overview.md) insieme a un database di analisi.


In questa esercitazione si apprenderà come:

> [!div class="checklist"]

> * Distribuire un database di analisi ad hoc
> * Eseguire query distribuite su tutti i database tenant



Per completare questa esercitazione, verificare che siano soddisfatti i prerequisiti seguenti:

* L'app WTP è stata distribuita. Per eseguire la distribuzione in meno di cinque minuti, vedere [Distribuire ed esplorare un'applicazione SaaS multi-tenant di esempio che usa il database di SQL Azure](sql-database-saas-tutorial.md)
* Azure PowerShell è installato. Per informazioni dettagliate, vedere [Introduzione ad Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)


## <a name="ad-hoc-analytics-pattern"></a>Modello di analisi ad hoc

Una delle grandi opportunità offerte dalle applicazioni SaaS è la possibilità di usare enormi quantità di dati dei tenant archiviati in modo centralizzato nel cloud. Questi dati possono essere usati per ottenere informazioni approfondite sul funzionamento e l'utilizzo delle applicazioni, sui tenant, i rispettivi utenti e le loro preferenze o i loro comportamenti. Queste informazioni possono essere utili come base per progettare sviluppi futuri, miglioramenti dell'usabilità e altri investimenti per app e servizi.

L'accesso a questi dati è semplice quando sono raccolti in un singolo database multi-tenant, ma non è altrettanto semplice in una distribuzione su larga scala potenzialmente in migliaia di database. Uno degli approcci possibili prevede l'uso di una query elastica, che consente di eseguire query ad-hoc su un set di database distribuiti con uno schema comune. La query elastica usa un singolo database *principale* in cui sono definite le tabelle esterne speculari delle tabelle nei database distribuiti (tenant). Le query inviate a questo database principale vengono compilate per produrre un piano di query distribuito, con il push di parti della query ai database tenant all'occorrenza. La query elastica usa la mappa partizioni nel database del catalogo per fornire la posizione dei database tenant. La configurazione e la definizione delle query sono semplici con il normale linguaggio T-SQL e sono supportate query ad hoc da strumenti come Power BI ed Excel.

## <a name="get-the-wingtip-application-scripts"></a>Ottenere gli script dell'applicazione Wingtip

Gli script di Wingtip Tickets e il codice sorgente dell'applicazione sono disponibili nel repository GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). I file di script si trovano nella [cartella Learning Modules](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Scaricare la cartella **Learning Modules** nel computer locale, mantenendo la struttura delle cartelle.

## <a name="deploy-the-database-used-for-ad-hoc-analytics-queries"></a>Distribuire il database usato per le query di analisi ad hoc

Questo esercizio consente di distribuire il database di analisi ad hoc che contiene lo schema usato per l'esecuzione di query ad hoc su tutti i database tenant.

1. Aprire ...\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocAnalytics.ps1* in *PowerShell ISE* e impostare i valori seguenti:
   * **$DemoScenario** = 2, **Distribuire un database di analisi ad hoc**.

1. Premere **F5** per eseguire lo script e creare un nuovo database SQL per l'esecuzione di analisi ad hoc e aggiungerlo al catalogo WTP. Le tabelle TicketPurchases, Tickets e Venues vengono aggiunte come tabelle esterne su cui è possibile eseguire query.
   Non è un problema se in questa fase vengono visualizzati avvisi del tipo *Server RPC non disponibile*.


È ora disponibile un database *adhocanalytics*, che può essere usato per eseguire query distribuite e raccogliere informazioni utili da tutti i tenant.

## <a name="run-ad-hoc-analytics-queries"></a>Eseguire query di analisi ad hoc

Questo esercizio descrive come eseguire query di analisi ad hoc per raccogliere informazioni dettagliate dall'applicazione WTP.

1. Aprire ...\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocAnalyticsQueries.sql* in SSMS.
1. Assicurarsi di essere connessi al database **adhocanalytics**
1. Selezionare la singole query da eseguire e quindi premere **F5**:

    ![query](media/sql-database-saas-tutorial-adhoc-analytics/query.png)




## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]

> * Distribuire un database di analisi ad hoc
> * Eseguire query distribuite su tutti i database tenant

[Esercitazione su Log Analytics (OMS)](sql-database-saas-tutorial-log-analytics.md)

## <a name="additional-resources"></a>Risorse aggiuntive

* [Altre esercitazioni basate sulla distribuzione iniziale dell'applicazione Wingtip Tickets Platform (WTP)](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [Query elastica](sql-database-elastic-query-overview.md)

