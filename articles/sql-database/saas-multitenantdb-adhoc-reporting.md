---
title: "Eseguire query di reporting ad hoc su più database SQL di Azure | Microsoft Docs"
description: "Eseguire query di reporting ad hoc su più database SQL in un esempio di app multi-tenant."
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
ms.author: AyoOlubeko
ms.openlocfilehash: c0ed3eb344ea8ec7e2d3e86125d60c8cc28f723d
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2017
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-azure-sql-databases"></a>Eseguire query di reporting ad hoc su più database SQL di Azure

In questa esercitazione si eseguono query distribuite sull'intero set di database tenant per consentire il reporting ad hoc interattivo. Queste query consentono di estrarre informazioni approfondite nascoste nei dati operativi quotidiani dell'app SaaS Wingtip Tickets. Si richiede a tal fine la distribuzione di un database di analisi aggiuntivo nel server di catalogo e l'uso di query elastica per abilitare le query distribuite.


In questa esercitazione si apprenderà:

> [!div class="checklist"]

> * Come distribuire un database di reporting ad hoc
> * Come eseguire query distribuite su tutti i database tenant


Per completare questa esercitazione, verificare che siano soddisfatti i prerequisiti seguenti:

* È stata distribuita l'app SaaS di database multi-tenant Wingtip Tickets. Per eseguire la distribuzione in meno di cinque minuti, vedere [Distribuire ed esplorare l'applicazione SaaS di database multi-tenant Wingtip Tickets](saas-multitenantdb-get-started-deploy.md)
* Azure PowerShell è installato. Per informazioni dettagliate, vedere [Introduzione ad Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* SQL Server Management Studio (SSMS) è installato. Per scaricare e installare SSMS, vedere [Scaricare SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="ad-hoc-reporting-pattern"></a>Modello di reporting ad hoc

![modello di reporting ad hoc](media/saas-multitenantdb-adhoc-reporting/adhocreportingpattern_shardedmultitenantDB.png)

Le applicazioni SaaS possono analizzare la grande quantità di dati del tenant archiviati in modo centralizzato nel cloud. Le analisi consentono di ottenere informazioni dettagliate sul funzionamento e l'uso dell'applicazione. Queste informazioni possono essere utili come base per progettare sviluppi futuri, miglioramenti dell'usabilità e altri investimenti per app e servizi.

L'accesso a questi dati è semplice quando sono raccolti in un singolo database multi-tenant, ma non è altrettanto semplice in una distribuzione su larga scala potenzialmente in migliaia di database. Uno degli approcci possibili prevede l'uso di una [query elastica](sql-database-elastic-query-overview.md), che consente di eseguire query su un set di database distribuiti con uno schema comune. Questi database possono essere distribuiti in sottoscrizioni e gruppi di risorse diversi, ma devono condividere un account di accesso comune per poter estrarre dati da tutti i database. La query elastica usa un singolo database *principale* in cui sono definite le tabelle esterne speculari alle tabelle o alle viste nei database (tenant) distribuiti. Le query inviate a questo database principale vengono compilate per produrre un piano di query distribuito, con il push di parti della query ai database tenant all'occorrenza. La query elastica usa la mappa partizioni nel database del catalogo per determinare la posizione di tutti i database tenant. La configurazione e l'esecuzione di query sono semplici con il linguaggio [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference) standard e sono supportate query ad hoc da strumenti come Power BI ed Excel.

Grazie alla distribuzione delle query tra i database tenant, la query elastica offre informazioni dettagliate immediate sui dati di produzione attivi. Tuttavia, dato che la query elastica esegue il pull dei dati potenzialmente da molti database, la latenza delle query può talvolta essere superiore a quella di query equivalenti inviate a un singolo database multi-tenant. Assicurarsi di progettare query che riducano al minimo i dati restituiti. La query elastica è spesso la soluzione ideale per eseguire query su piccole quantità di dati in tempo reale, in alternativa alla creazione di query analitiche o report usati di frequente e complessi. Se le query non offrono prestazioni adeguate, esaminare il [piano di esecuzione](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) per scoprire quale parte della query è stata inviata al database remoto e quanti dati vengono restituiti. Per ottenere un servizio migliore per le query che richiedono un'elaborazione analitica complessa, in alcuni casi è consigliabile salvare i dati tenant estratti in un database ottimizzato per le query analitiche. Database SQL e SQL Data Warehouse possono ospitare un database analitico di questo tipo.

Questo modello di analisi è spiegato nell'[esercitazione sull'analisi dei tenant](saas-multitenantdb-tenant-analytics.md).

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Ottenere gli script e il codice sorgente dell'applicazione SaaS di database multi-tenant Wingtip Tickets

Gli script e il codice sorgente dell'applicazione SaaS di database multi-tenant Wingtip Tickets sono disponibili nel repository [WingtipTicketsSaaS-MultiTenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) di GitHub. Leggere le [linee guida generali](saas-tenancy-wingtip-app-guidance-tips.md) per i passaggi da seguire per scaricare e sbloccare gli script dell'app SaaS Wingtip Tickets.

## <a name="create-ticket-sales-data"></a>Creare i dati di vendita dei biglietti

Per eseguire query su un set di dati più interessante, creare i dati di vendita dei biglietti eseguendo il generatore di biglietti.

1. In *PowerShell ISE* aprire lo script ...\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocReporting.ps1* e impostare i valori seguenti:
   * **$DemoScenario** = 1, **Acquistare biglietti per gli eventi in tutte le sedi**.
2. Premere **F5** per eseguire lo script e generare i dati di vendita dei biglietti. Durante l'esecuzione dello script, è possibile continuare la procedura in questa esercitazione. I dati sui biglietti vengono recuperati nella sezione *Eseguire query distribuite ad hoc*, quindi attendere che il generatore di biglietti completi le operazioni.

## <a name="explore-the-tenant-tables"></a>Esplorare le tabelle tenant 

Nell'applicazione SaaS di database multi-tenant Wingtip Tickets, i tenant vengono archiviati in un modello di gestione dei tenant ibrido, nel quale i dati tenant sono archiviati in un database multi-tenant o in un database a singolo tenant e possono essere spostati tra i due. Quando si eseguono query su tutti i database tenant, è importante che la query elastica possa trattare i dati come se facessero parte di un singolo database logico partizionato dal tenant. 

Per ottenere questo modello, tutte le tabelle tenant includono una colonna *VenueId* che identifica il tenant a cui appartengono i dati. Il valore *VenueId* viene calcolato come hash del nome Venue, ma si può usare qualsiasi approccio per introdurre un valore univoco per questa colonna. Questo approccio è simile al modo in cui viene calcolata la chiave del tenant per l'uso nel catalogo. Le tabelle contenenti *VenueId* vengono usate dalla query elastica per parallelizzare le query ed eseguirne il push al database tenant remoto appropriato. Questo riduce notevolmente la quantità di dati restituiti e garantisce un aumento delle prestazioni in particolare in presenza di più tenant con dati archiviati in database a singolo tenant.

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>Distribuire il database usato per le query distribuite ad hoc

Questo esercizio distribuisce il database *adhocreporting*, ovvero il database principale che contiene lo schema usato per l'esecuzione di query su tutti i database tenant. Il database viene distribuito nel server di catalogo esistente, vale a dire il server usato per tutti i database correlati alla gestione nell'app di esempio.

1. Aprire ...\\Learning Modules\\Operational Analytics\\Adhoc Reporting\\*Demo-AdhocReporting.ps1* in *PowerShell ISE* e impostare i valori seguenti:
   * **$DemoScenario** = 2, **Distribuire un database di analisi ad hoc**.

2. Premere **F5** per eseguire lo script e creare il database *adhocreporting*.

Nella prossima sezione si aggiungerà lo schema al database in modo da poterlo usare per eseguire query distribuite.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Configurare il database 'head' per l'esecuzione di query distribuite

Questo esercizio aggiunge lo schema (definizioni dell'origine dati esterna e della tabella esterna) al database di reporting ad hoc che consente l'esecuzione di query su tutti i database tenant.

1. Aprire SQL Server Management Studio e connettersi al database di reporting ad hoc creato nel passaggio precedente. Il nome del database è *adhocreporting*.
2. Aprire ...\Learning Modules\Operational Analytics\Adhoc Reporting\ *Initialize-AdhocReportingDB.sql* in SQL Server Management Studio.
3. Esaminare lo script SQL e tenere presente quanto segue:

   La query elastica usa credenziali con ambito database per accedere a ognuno dei database tenant. Tali credenziali devono essere disponibili in tutti i database e, in genere, devono avere i diritti minimi necessari per abilitare le query ad hoc.

    ![creare le credenziali](media/saas-multitenantdb-adhoc-reporting/create-credential.png)

   Usando questo database di catalogo come origine dati esterna, le query vengono distribuite a tutti i database registrati nel catalogo nel momento in cui viene eseguita la query. Dato che i nomi dei server sono diversi per ogni distribuzione, questo script di inizializzazione ottiene la posizione del database del catalogo recuperando il server corrente (@@servername) in cui viene eseguito lo script.

    ![creare l'origine dati esterna](media/saas-multitenantdb-adhoc-reporting/create-external-data-source.png)

   Le tabelle esterne che fanno riferimento a tabelle tenant sono definite con **DISTRIBUTION = SHARDED(VenueId)**. Ciò consente di indirizzare una query per un particolare *VenueId* al database appropriato e di migliorare le prestazioni per molti scenari, come illustrato nella sezione successiva.

    ![creare tabelle esterne](media/saas-multitenantdb-adhoc-reporting/external-tables.png)

   La tabella locale *VenueTypes* che viene creata e popolata. Questa tabella di dati di riferimento è comune in tutti i database tenant, pertanto può essere rappresentata come tabella locale e popolata con i dati comuni. Per alcune query questo potrebbe ridurre la quantità di dati spostati tra i database tenant e il database *adhocreporting*.

    ![creare la tabella](media/saas-multitenantdb-adhoc-reporting/create-table.png)

   Se si includono tabelle di riferimento in questo modo, assicurarsi di aggiornare lo schema di tabella e i dati ogni volta che si aggiornano i database tenant.

4. Premere **F5** per eseguire lo script e inizializzare il database *adhocreporting*. 

È ora possibile eseguire query distribuite e ottenere informazioni dettagliate da tutti i tenant.

## <a name="run-ad-hoc-distributed-queries"></a>Eseguire query distribuite ad hoc

Dopo avere configurato il database *adhocreporting*, è possibile procedere ed eseguire alcune query distribuite. Includere il piano di esecuzione per una migliore comprensione della posizione in cui avviene l'elaborazione delle query. 

Quando si esamina il piano di esecuzione, passare il mouse sulle icone del piano per visualizzare i dettagli. 

1. In *SSMS* aprire ...\\Learning Modules\\Operational Analytics\\Adhoc Reporting\\*Demo-AdhocReportingQueries.sql*.
2. Assicurarsi di essere connessi al database **adhocreporting**.
3. Selezionare il menu **Query** e fare clic su **Includi piano di esecuzione effettivo**
4. Evidenziare la query *Which venues are currently registered?* e premere **F5**.

   La query restituisce l'intero elenco di sedi, dimostrando come sia semplice e veloce eseguire una query su tutti i tenant e restituire i dati da ogni tenant.

   Esaminare il piano e notare che l'intero costo è la query remota, perché l'operazione prevede semplicemente il passaggio a ogni database tenant e la selezione delle informazioni sulle sedi degli eventi.

   ![SELECT * FROM dbo.Venues](media/saas-multitenantdb-adhoc-reporting/query1-plan.png)

5. Selezionare la query successiva e premere **F5**.

   La query unisce i dati dai database tenant e dalla tabella locale *VenueTypes* (locale perché è una tabella nel database *adhocreporting*).

   Esaminare il piano e verificare che la maggior parte del costo è rappresentato dalla query remota, perché vengono recuperate le informazioni sulle sedi di ogni tenant (dbo.Venues) e quindi si esegue un rapido join locale con la tabella *VenueTypes* locale per visualizzare il nome descrittivo.

   ![Creare un join su dati remoti e locali](media/saas-multitenantdb-adhoc-reporting/query2-plan.png)

6. Selezionare ora la query *On which day were the most tickets sold?* e premere **F5**.

   Questa query esegue operazioni un po' più complesse di join e aggregazione. È importante tenere presente che la maggior parte dell'elaborazione viene eseguita in remoto e ancora una volta vengono recuperate solo le righe necessarie, restituendo una singola riga per il totale delle vendite di biglietti aggregato al giorno di ogni sede.

   ![query](media/saas-multitenantdb-adhoc-reporting/query3-plan.png)


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]

> * Eseguire query distribuite su tutti i database tenant
> * Distribuire un database di reporting ad hoc e aggiungervi lo schema per eseguire query distribuite.

Eseguire ora l'[esercitazione sull'analisi dei tenant](saas-multitenantdb-tenant-analytics.md) per esaminare l'estrazione dei dati in un database di analisi separato per elaborazioni analitiche più complesse.

## <a name="additional-resources"></a>Risorse aggiuntive

<!-- ??
* Additional [tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->

* [Query elastica](sql-database-elastic-query-overview.md)
