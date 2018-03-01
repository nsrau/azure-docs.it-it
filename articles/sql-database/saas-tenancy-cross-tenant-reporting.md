---
title: "Eseguire query di reporting su più database SQL di Azure | Microsoft Docs"
description: Reporting tra tenant tramite query distribuite.
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
ms.topic: articles
ms.date: 11/13/2017
ms.author: billgib; sstein; AyoOlubeko
ms.openlocfilehash: 531d284798e455622faa1bfe7b0c8f178b3642fd
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2018
---
# <a name="cross-tenant-reporting-using-distributed-queries"></a>Reporting tra tenant tramite query distribuite

In questa esercitazione verranno eseguite query distribuite sull'intero set di database tenant per il reporting. Queste query consentono di estrarre informazioni approfondite nascoste nei dati operativi quotidiani dei tenant SaaS Wingtip Tickets. A questo scopo, si richiede la distribuzione di un database di reporting aggiuntivo nel server di catalogo e l'uso di query elastica per abilitare le query distribuite.


In questa esercitazione si apprenderà:

> [!div class="checklist"]

> * Come distribuire un database di reporting
> * Come eseguire query distribuite su tutti i database tenant
> * In che modo le viste globali in ogni database consentono l'esecuzione di query efficienti tra i tenant


Per completare questa esercitazione, verificare che i prerequisiti seguenti siano completati:


* È stata distribuita l'app del database per tenant SaaS Wingtip Tickets. Per eseguire la distribuzione in meno di cinque minuti, vedere [Deploy and explore the Wingtip Tickets SaaS Database Per Tenant application](saas-dbpertenant-get-started-deploy.md) (Distribuire ed esplorare l'applicazione del database per tenant SaaS Wingtip Tickets)
* Azure PowerShell è installato. Per informazioni dettagliate, vedere [Introduzione ad Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* SQL Server Management Studio (SSMS) è installato. Per scaricare e installare SSMS, vedere [Scaricare SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="cross-tenant-reporting-pattern"></a>Modello di reporting tra tenant

![modello di query distribuite tra tenant](media/saas-tenancy-cross-tenant-reporting/cross-tenant-distributed-query.png)

Una delle opportunità offerte con le applicazioni SaaS è l'uso di enormi quantità di dati dei tenant archiviati in modo centralizzato nel cloud per ottenere informazioni approfondite sul funzionamento e sull'utilizzo dell'applicazione in uso. Queste informazioni possono essere utili come base per progettare sviluppi futuri, miglioramenti dell'usabilità e altri investimenti per app e servizi.

L'accesso a questi dati è semplice quando sono raccolti in un singolo database multi-tenant, ma non è altrettanto semplice in una distribuzione su larga scala potenzialmente in migliaia di database. Uno degli approcci possibili prevede l'uso di una [query elastica](sql-database-elastic-query-overview.md), che consente di eseguire query su un set di database distribuiti con uno schema comune. Questi database possono essere distribuiti in sottoscrizioni e gruppi di risorse diversi, ma devono condividere un account di accesso comune. La query elastica usa un singolo database *principale* in cui sono definite le tabelle esterne speculari alle tabelle o alle viste nei database (tenant) distribuiti. Le query inviate a questo database principale vengono compilate per produrre un piano di query distribuito, con il push di parti della query ai database tenant all'occorrenza. La query elastica usa la mappa partizioni nel database del catalogo per determinare la posizione di tutti i database tenant. La configurazione e l'esecuzione di query del database principale sono semplici con il linguaggio [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference) standard e sono supportate query da strumenti come Power BI ed Excel.

Grazie alla distribuzione delle query tra i database tenant, la query elastica offre informazioni dettagliate immediate sui dati di produzione attivi. Dato che la query elastica esegue il pull dei dati potenzialmente da molti database, la latenza delle query può essere superiore a quella di query equivalenti inviate a un singolo database multi-tenant. Progettare query che riducano al minimo i dati restituiti al database principale. La query elastica è spesso la soluzione ideale per eseguire query su piccole quantità di dati in tempo reale, in alternativa alla creazione di query analitiche o report usati di frequente e complessi. Se le query non offrono prestazioni adeguate, esaminare il [piano di esecuzione](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) per scoprire quale parte della query è stata inviata al database remoto e quanti dati vengono restituiti. Per ottenere un servizio migliore per le query che richiedono un'aggregazione o un'elaborazione analitica complessa, è consigliabile estrarre i dati tenant in un database o in un data warehouse ottimizzato per le query analitiche. Questo modello è spiegato nell'[esercitazione sull'analisi dei tenant](saas-tenancy-tenant-analytics.md). 

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Ottenere gli script dell'applicazione del database per tenant SaaS Wingtip Tickets

Gli script e il codice sorgente dell'applicazione SaaS di database multi-tenant Wingtip Tickets sono disponibili nel repository [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) di GitHub. Leggere le [linee guida generali](saas-tenancy-wingtip-app-guidance-tips.md) per i passaggi da seguire per scaricare e sbloccare gli script dell'app SaaS Wingtip Tickets.

## <a name="create-ticket-sales-data"></a>Creare i dati di vendita dei biglietti

Per eseguire query su un set di dati più interessante, creare i dati di vendita dei biglietti eseguendo il generatore di biglietti.

1. In *PowerShell ISE* aprire lo script ...\\Learning Modules\\Operational Analytics\\Adhoc Reporting\\*Demo-AdhocReporting.ps1* e impostare il valore seguente:
   * **$DemoScenario** = 1, **Acquistare biglietti per gli eventi in tutte le sedi**.
2. Premere **F5** per eseguire lo script e generare i dati di vendita dei biglietti. Durante l'esecuzione dello script, è possibile continuare la procedura in questa esercitazione. I dati sui biglietti vengono recuperati nella sezione *Eseguire query distribuite ad hoc*, quindi attendere che il generatore di biglietti completi le operazioni.

## <a name="explore-the-global-views"></a>Esplorare le viste globali

Nell'applicazione database per tenant SaaS Wingtip Tickets a ogni tenant viene assegnato un database. I dati contenuti nelle tabelle del database rientrano quindi nell'ambito della prospettiva di un singolo tenant. Quando si eseguono query su tutti i database, tuttavia, è importante che la query elastica possa trattare i dati come se facessero parte di un singolo database logico partizionato dal tenant. 

Per simulare questo modello, viene aggiunto un set di viste "globali" al database tenant che proiettano un ID tenant in ogni tabella sottoposta a query a livello globale. Ad esempio, la vista *VenueEvents* aggiunge un *VenueId* calcolato alle colonne proiettate dalla tabella *Events*. Le viste *VenueTicketPurchases* e *VenueTickets*, analogamente, aggiungono una colonna *VenueId* calcolata proiettata dalle rispettive tabelle. Tali viste vengono usate dalla query elastica per parallelizzare le query ed eseguirne il push al database tenant remoto appropriato quando è presente una colonna *VenueId*. Questo riduce drasticamente la quantità di dati restituita e determina un miglioramento sostanziale delle prestazioni per molte query. Queste viste globali sono state già create in tutti i database tenant.

1. Aprire SSMS e [connettersi al server tenants1-&lt;USER&gt;](saas-tenancy-wingtip-app-guidance-tips.md#explore-database-schema-and-execute-sql-queries-using-ssms).
1. Espandere **Database**, fare clic con il pulsante destro del mouse su _contosoconcerthall_ e selezionare **Nuova query**.
1. Eseguire le query seguenti per esplorare la differenza tra le tabelle a tenant singolo e le viste globali:

   ```T-SQL
   -- The base Venue table, that has no VenueId associated.
   SELECT * FROM Venue

   -- Notice the plural name 'Venues'. This view projects a VenueId column.
   SELECT * FROM Venues

   -- The base Events table, which has no VenueId column.
   SELECT * FROM Events

   -- This view projects the VenueId retrieved from the Venues table.
   SELECT * FROM VenueEvents
   ```

In queste viste, *VenueId* viene calcolato come hash del nome Venue, ma si può usare qualsiasi approccio per introdurre un valore univoco. Questo approccio è simile al modo in cui viene calcolata la chiave del tenant per l'uso nel catalogo.

Per esaminare la definizione della vista *Venues*:

1. In **Esplora oggetti** espandere **contosoconcerthall** > **Viste**:

   ![Viste](media/saas-tenancy-cross-tenant-reporting/views.png)

2. Fare clic con il pulsante destro del mouse su **dbo.Venues**.
3. Selezionare **Crea script per vista** > **CREATE in** > **Nuova finestra editor di query**.

Creare uno script per qualsiasi altra vista *Venue* per scoprire come viene aggiunto *VenueId*.

## <a name="deploy-the-database-used-for-distributed-queries"></a>Distribuire il database usato per le query distribuite

Questo esercizio distribuisce il database _adhocreporting_, ovvero il database principale che contiene lo schema usato per l'esecuzione di query su tutti i database tenant. Il database viene distribuito nel server di catalogo esistente, vale a dire il server usato per tutti i database correlati alla gestione nell'app di esempio.

1. In *PowerShell ISE* aprire ...\\Learning Modules\\Operational Analytics\\Adhoc Reporting\\*Demo-AdhocReporting.ps1*. 

1. Impostare **$DemoScenario = 2**, _Distribuire un database di reporting ad hoc_.

1. Premere **F5** per eseguire lo script e creare il database *adhocreporting*.

Nella prossima sezione si aggiungerà lo schema al database in modo da poterlo usare per eseguire query distribuite.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Configurare il database 'head' per l'esecuzione di query distribuite

Questo esercizio aggiunge lo schema (definizioni dell'origine dati esterna e della tabella esterna) al database _adhocreporting_ che consente l'esecuzione di query su tutti i database tenant.

1. Aprire SQL Server Management Studio e connettersi al database di reporting ad hoc creato nel passaggio precedente. Il nome del database è *adhocreporting*.
2. Aprire ...\Learning Modules\Operational Analytics\Adhoc Reporting\ _Initialize-AdhocReportingDB.sql_ in SQL Server Management Studio.
3. Esaminare lo script SQL e annotare quanto segue:

   La query elastica usa credenziali con ambito database per accedere a ognuno dei database tenant. Tali credenziali devono essere disponibili in tutti i database e, in genere, devono avere i diritti minimi necessari per abilitare le query.

    ![creare le credenziali](media/saas-tenancy-cross-tenant-reporting/create-credential.png)

   Con questo database di catalogo come origine dati esterna, le query vengono distribuite a tutti i database registrati nel catalogo nel momento in cui viene eseguita la query. Dato che i nomi dei server sono diversi per ogni distribuzione, questo script ottiene la posizione del database del catalogo dal server corrente (@@servername) in cui viene eseguito lo script.

    ![creare l'origine dati esterna](media/saas-tenancy-cross-tenant-reporting/create-external-data-source.png)

   Le tabelle esterne che fanno riferimento alle viste globali descritte nella sezione precedente e definite con **DISTRIBUTION = SHARDED(VenueId)**. Dato che ogni *VenueId* corrisponde a un singolo database, ciò migliora le prestazioni per molti scenari, come illustrato nella sezione successiva.

    ![creare tabelle esterne](media/saas-tenancy-cross-tenant-reporting/external-tables.png)

   La tabella locale _VenueTypes_ che viene creata e popolata. Questa tabella di dati di riferimento è comune in tutti i database tenant, pertanto può essere rappresentata come tabella locale e popolata con i dati comuni. Per alcune query, definire questa tabella nel database principale può ridurre la quantità di dati da spostare nel database principale.

    ![creare la tabella](media/saas-tenancy-cross-tenant-reporting/create-table.png)

   Se si includono tabelle di riferimento in questo modo, assicurarsi di aggiornare lo schema di tabella e i dati ogni volta che si aggiornano i database tenant.

4. Premere **F5** per eseguire lo script e inizializzare il database *adhocreporting*. 

È ora possibile eseguire query distribuite e ottenere informazioni dettagliate da tutti i tenant.

## <a name="run-distributed-queries"></a>Eseguire query distribuite

Dopo avere configurato il database *adhocreporting*, è possibile procedere ed eseguire alcune query distribuite. Includere il piano di esecuzione per una migliore comprensione della posizione in cui avviene l'elaborazione delle query. 

Quando si esamina il piano di esecuzione, passare il mouse sulle icone del piano per visualizzare i dettagli. 

È importante sottolineare che l'impostazione **DISTRIBUTION = SHARDED(VenueId)**, usata per la definizione dell'origine dati esterna, consente di migliorare le prestazioni in molti scenari. Dato che ogni *VenueId* corrisponde a un singolo database, è possibile filtrare facilmente i dati in remoto e restituire solo i dati necessari.

1. Aprire ...\\Learning Modules\\Operational Analytics\\Adhoc Reporting\\*Demo-AdhocReportingQueries.sql* in SQL Server Management Studio.
2. Assicurarsi di essere connessi al database **adhocreporting**.
3. Selezionare il menu **Query** e fare clic su **Includi piano di esecuzione effettivo**
4. Evidenziare la query *Which venues are currently registered?* e premere **F5**.

   La query restituisce l'intero elenco di sedi, dimostrando come sia semplice e veloce eseguire una query su tutti i tenant e restituire i dati da ogni tenant.

   Esaminare il piano e notare che l'intero costo è costituito dalla query remota. Ogni database tenant esegue la query in remoto e restituisce informazioni sulle sedi degli eventi nel database principale.

   ![SELECT * FROM dbo.Venues](media/saas-tenancy-cross-tenant-reporting/query1-plan.png)

5. Selezionare la query successiva e premere **F5**.

   La query unisce i dati dai database tenant e dalla tabella locale *VenueTypes* (locale perché è una tabella nel database *adhocreporting*).

   Esaminare il piano e notare che la maggior parte del costo è rappresentato dalla query remota. Ogni database tenant restituisce informazioni sulle sedi degli eventi ed esegue un join locale con la tabella *VenueTypes* locale per visualizzare il nome descrittivo.

   ![Creare un join su dati remoti e locali](media/saas-tenancy-cross-tenant-reporting/query2-plan.png)

6. Selezionare ora la query *On which day were the most tickets sold?* e premere **F5**.

   Questa query esegue operazioni un po' più complesse di join e aggregazione. La maggior parte dell'elaborazione si verifica in modalità remota.  Solo le singole righe, contenenti il ​​conteggio delle vendite giornaliere dei ticket per ciascuna sede dell'evento, vengono restituite al database principale.

   ![query](media/saas-tenancy-cross-tenant-reporting/query3-plan.png)


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]

> * Eseguire query distribuite su tutti i database tenant
> * Distribuire un database di reporting e definire lo schema necessario per eseguire query distribuite.


Eseguire ora l'[esercitazione sull'analisi dei tenant](saas-tenancy-tenant-analytics.md) per esaminare l'estrazione dei dati in un database di analisi separato per elaborazioni analitiche più complesse.

## <a name="additional-resources"></a>Risorse aggiuntive

* Altre [esercitazioni basate sull'applicazione database per tenant SaaS Wingtip Tickets](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Query elastica](sql-database-elastic-query-overview.md)
