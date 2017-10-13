---
title: "Eseguire query di analisi ad-hoc su più database SQL di Azure | Microsoft Docs"
description: "Eseguire query di analisi ad hoc su più database SQL in un esempio di app multi-tenant."
keywords: esercitazione database SQL
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: billgib; sstein
ms.openlocfilehash: a27a65627fecf35b59122110250a40c6fe8077b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-azure-sql-databases"></a>Eseguire query di analisi ad hoc su più database SQL di Azure

In questa esercitazione verranno eseguite query distribuite sull'intero set di database multi-tenant per consentire analisi ad hoc. Viene usata la funzionalità di query elastica per abilitare le query distribuite, che richiede la distribuzione di un database di analisi aggiuntivo nel server di catalogo. Queste query consentono di estrarre informazioni approfondite nascoste nei dati operativi quotidiani dell'app SaaS Wingtip.


In questa esercitazione si apprenderà:

> [!div class="checklist"]

> * Informazioni sulle viste globali in ogni database, che consentono l'esecuzione di query efficienti tra i tenant
> * Come distribuire un database di analisi ad hoc
> * Come eseguire query distribuite su tutti i database tenant



Per completare questa esercitazione, verificare che siano soddisfatti i prerequisiti seguenti:

* L'app SaaS Wingtip viene distribuita. Per distribuire in meno di cinque minuti, vedere [Distribuire ed esplorare l'applicazione SaaS Wingtip](sql-database-saas-tutorial.md)
* Azure PowerShell è installato. Per informazioni dettagliate, vedere [Introduzione ad Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* SQL Server Management Studio (SSMS) è installato. Per scaricare e installare SSMS, vedere [Scaricare SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="ad-hoc-analytics-pattern"></a>Modello di analisi ad hoc

Una delle grandi opportunità offerte dalle applicazioni SaaS è la possibilità di usare enormi quantità di dati dei tenant archiviati in modo centralizzato nel cloud. Questi dati possono essere usati per ottenere informazioni dettagliate sul funzionamento e l'uso dell'applicazione. Queste informazioni possono essere utili come base per progettare sviluppi futuri, miglioramenti dell'usabilità e altri investimenti per app e servizi.

L'accesso a questi dati è semplice quando sono raccolti in un singolo database multi-tenant, ma non è altrettanto semplice in una distribuzione su larga scala potenzialmente in migliaia di database. Uno degli approcci possibili prevede l'uso di una [query elastica](sql-database-elastic-query-overview.md), che consente di eseguire query su un set di database distribuiti con uno schema comune. La query elastica usa un singolo database *principale* in cui sono definite le tabelle esterne speculari alle tabelle o alle viste nei database (tenant) distribuiti. Le query inviate a questo database principale vengono compilate per produrre un piano di query distribuito, con il push di parti della query ai database tenant all'occorrenza. La query elastica usa la mappa partizioni nel database del catalogo per fornire la posizione dei database tenant. La configurazione e l'esecuzione di query sono semplici con il linguaggio [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference) standard e sono supportate query ad hoc da strumenti come Power BI ed Excel.

Grazie alla distribuzione delle query tra i database tenant, la query elastica offre informazioni dettagliate immediate sui dati di produzione attivi. Tuttavia, dato che la query elastica esegue il pull dei dati potenzialmente da molti database, la latenza delle query può talvolta essere superiore a quella di query equivalenti inviate a un singolo database multi-tenant. Assicurarsi di progettare query che riducano al minimo i dati restituiti. La query elastica è spesso la soluzione ideale per eseguire query su piccole quantità di dati in tempo reale, in alternativa alla creazione di query analitiche o report usati di frequente e complessi. Se le query non offrono prestazioni adeguate, esaminare il [piano di esecuzione](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) per scoprire quale parte della query è stata inviata al database remoto e quanti dati vengono restituiti. Per ottenere un servizio migliore per le query che richiedono un'elaborazione analitica complessa, in alcuni casi è consigliabile estrarre i dati tenant in un database dedicato o in un data warehouse ottimizzato per le query analitiche. Questo modello è spiegato nell'[esercitazione sull'analisi dei tenant](sql-database-saas-tutorial-tenant-analytics.md). 

## <a name="get-the-wingtip-application-scripts"></a>Ottenere gli script dell'applicazione Wingtip

Gli script dell'app SaaS Wingtip e il codice sorgente dell'applicazione sono disponibili nel repository GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). [Procedura per scaricare gli script dell'app SaaS Wingtip](sql-database-wtp-overview.md#download-and-unblock-the-wingtip-saas-scripts).

## <a name="create-ticket-sales-data"></a>Creare i dati di vendita dei biglietti

Per eseguire query su un set di dati più interessante, creare i dati di vendita dei biglietti eseguendo il generatore di biglietti.

1. In *PowerShell ISE* aprire lo script \\Learning Modules\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocAnalytics.ps1* e impostare i valori seguenti:
   * **$DemoScenario** = 1, **Acquistare biglietti per gli eventi in tutte le sedi**.
2. Premere **F5** per eseguire lo script e generare i dati di vendita dei biglietti. Durante l'esecuzione dello script, è possibile continuare la procedura in questa esercitazione. I dati sui biglietti vengono recuperati nella sezione *Eseguire query distribuite ad hoc*, quindi attendere che il generatore di biglietti completi le operazioni.

## <a name="explore-the-global-views"></a>Esplorare le viste globali

L'applicazione SaaS Wingtip è compilata in base a un modello che usa un database per ogni tenant. Lo schema del database tenant è quindi definito dal punto di vista di un tenant singolo. Le informazioni specifiche sul tenant sono presenti in una sola tabella *Venue*, che contiene sempre una singola riga ed è implementata come heap, senza una chiave primaria. Non è necessario che le altre tabelle nello schema siano correlate alla tabella *Venue*, perché nell'uso normale l'appartenenza dei dati ai tenant non è mai dubbia.

Quando si eseguono query su tutti i database, tuttavia, è importante che la query elastica possa trattare i dati come se facessero parte di un singolo database logico partizionato dal tenant. Per simulare questo modello, viene aggiunto un set di viste "globali" al database tenant che proiettano un ID tenant in ogni tabella sottoposta a query a livello globale. Ad esempio, la vista *VenueEvents* aggiunge un *VenueId* calcolato alle colonne proiettate dalla tabella *Events*. Tramite la definizione della tabella esterna nel database principale su *VenueEvents* (invece della tabella sottostante *Events*), la query elastica può eseguire il push dei join in base a *VenueId*, in modo che possano essere eseguiti in parallelo su ogni database remoto, anziché sul database principale. Questo riduce drasticamente la quantità di dati restituita, con conseguente miglioramento sostanziale delle prestazioni per molte query. Queste viste globali sono state già create in tutti i database tenant e in *basetenantdb*.

1. Aprire SSMS e [connettersi al server tenants1-&lt;USER&gt;](sql-database-wtp-overview.md#explore-database-schema-and-execute-sql-queries-using-ssms).
2. Espandere **Database**, fare clic con il pulsante destro del mouse su **contosoconcerthall** e selezionare **Nuova query**.
3. Eseguire le query seguenti per esplorare la differenza tra le tabelle a tenant singolo e le viste globali:

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

1. In **Esplora oggetti** espandere **contosoconcethall** > **Viste**:

   ![Viste](media/sql-database-saas-tutorial-adhoc-analytics/views.png)

2. Fare clic con il pulsante destro del mouse su **dbo.Venues**.
3. Selezionare **Crea script per vista** > **CREATE in** > **Nuova finestra editor di query**.

Creare uno script per qualsiasi altra vista *Venue* per scoprire come viene aggiunto *VenueId*.

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>Distribuire il database usato per le query distribuite ad hoc

Questo esercizio permette di distribuire il database *adhocanalytics*, ovvero il database principale che contiene lo schema usato per l'esecuzione di query su tutti i database tenant. Il database viene distribuito nel server di catalogo esistente, vale a dire il server usato per tutti i database correlati alla gestione nell'app di esempio.

1. Aprire ...\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocAnalytics.ps1* in *PowerShell ISE* e impostare i valori seguenti:
   * **$DemoScenario** = 2, **Distribuire un database di analisi ad hoc**.

2. Premere **F5** per eseguire lo script e creare il database *adhocanalytics*.

Nella prossima sezione si aggiungerà lo schema al database in modo da poterlo usare per eseguire query distribuite.

## <a name="configure-the-database-for-running-distributed-queries"></a>Configurare il database per l'esecuzione di query distribuite

Questo esercizio aggiunge lo schema (definizioni dell'origine dati esterna e della tabella esterna) al database di analisi ad hoc che consente l'esecuzione di query su tutti i database tenant.

1. Aprire SQL Server Management Studio e connettersi al database di analisi ad hoc creato nel passaggio precedente. Il nome del database è *adhocanalytics*.
2. Aprire ...\Learning Modules\Operational Analytics\Adhoc Analytics\ *Initialize-AdhocAnalyticsDB.sql* in SSMS.
3. Esaminare lo script SQL e tenere presente quanto segue:

   La query elastica usa credenziali con ambito database per accedere a ognuno dei database tenant. Tali credenziali devono essere disponibili in tutti i database e, in genere, devono avere i diritti minimi necessari per abilitare le query ad hoc.

    ![creare le credenziali](media/sql-database-saas-tutorial-adhoc-analytics/create-credential.png)

   L'origine dati esterna, definita in modo da usare la mappa partizioni del tenant nel database del catalogo. Usando questa come origine dati esterna, le query verranno distribuite a tutti i database registrati nel catalogo nel momento in cui viene eseguita la query. Dato che i nomi dei server sono diversi per ogni distribuzione, questo script di inizializzazione ottiene la posizione del database del catalogo recuperando il server corrente (@@servername) in cui viene eseguito lo script.

    ![creare l'origine dati esterna](media/sql-database-saas-tutorial-adhoc-analytics/create-external-data-source.png)

   Le tabelle esterne che fanno riferimento alle viste globali descritte nella sezione precedente e definite con **DISTRIBUTION = SHARDED(VenueId)**. Dato che ogni *VenueId* corrisponde a un singolo database, ciò migliora le prestazioni per molti scenari, come illustrato nella sezione successiva.

    ![creare tabelle esterne](media/sql-database-saas-tutorial-adhoc-analytics/external-tables.png)

   La tabella locale *VenueTypes* che viene creata e popolata. Questa tabella di dati di riferimento è comune in tutti i database tenant, pertanto può essere rappresentata come tabella locale e popolata con i dati comuni. Per alcune query questo potrebbe ridurre la quantità di dati spostati tra i database tenant e il database *adhocanalytics*.

    ![creare la tabella](media/sql-database-saas-tutorial-adhoc-analytics/create-table.png)

   Se si includono tabelle di riferimento in questo modo, assicurarsi di aggiornare lo schema di tabella e i dati ogni volta che si aggiornano i database tenant.

4. Premere **F5** per eseguire lo script e inizializzare il database *adhocanalytics*. 

È ora possibile eseguire query distribuite e ottenere informazioni dettagliate da tutti i tenant.

## <a name="run-ad-hoc-distributed-queries"></a>Eseguire query distribuite ad hoc

Ora che il database *adhocanalytics* è configurato, è possibile procedere ed eseguire alcune query distribuite. Includere il piano di esecuzione per una migliore comprensione della posizione in cui avviene l'elaborazione delle query. 

Quando si esamina il piano di esecuzione, passare il mouse sulle icone del piano per visualizzare i dettagli. 

È importante sottolineare che l'impostazione **DISTRIBUTION = SHARDED(VenueId)**, usata per la definizione dell'origine dati esterna, consente di migliorare le prestazioni in molti scenari. Dato che ogni *VenueId* corrisponde a un singolo database, è possibile filtrare facilmente i dati in remoto e restituire solo i dati necessari.

1. Aprire ...\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocAnalyticsQueries.sql* in SSMS.
2. Assicurarsi di essere connessi al database **adhocanalytics**.
3. Selezionare il menu **Query** e fare clic su **Includi piano di esecuzione effettivo**
4. Evidenziare la query *Which venues are currently registered?* e premere **F5**.

   La query restituisce l'intero elenco di sedi, dimostrando come sia semplice e veloce eseguire una query su tutti i tenant e restituire i dati da ogni tenant.

   Esaminare il piano e notare che l'intero costo è la query remota, perché l'operazione prevede semplicemente il passaggio a ogni database tenant e la selezione delle informazioni sulle sedi degli eventi.

   ![SELECT * FROM dbo.Venues](media/sql-database-saas-tutorial-adhoc-analytics/query1-plan.png)

5. Selezionare la query successiva e premere **F5**.

   La query unisce i dati dai database tenant e dalla tabella *VenueTypes* locale (locale perché è una tabella nel database *adhocanalytics*).

   Esaminare il piano e verificare che la maggior parte del costo è rappresentato dalla query remota, perché vengono recuperate le informazioni sulle sedi di ogni tenant (dbo.Venues) e quindi si esegue un rapido join locale con la tabella *VenueTypes* locale per visualizzare il nome descrittivo.

   ![Creare un join su dati remoti e locali](media/sql-database-saas-tutorial-adhoc-analytics/query2-plan.png)

6. Selezionare ora la query *On which day were the most tickets sold?* e premere **F5**.

   Questa query esegue operazioni un po' più complesse di join e aggregazione. È importante tenere presente che la maggior parte dell'elaborazione viene eseguita in remoto e ancora una volta vengono recuperate solo le righe necessarie, restituendo una singola riga per il totale delle vendite di biglietti aggregato al giorno di ogni sede.

   ![query](media/sql-database-saas-tutorial-adhoc-analytics/query3-plan.png)


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]

> * Eseguire query distribuite su tutti i database tenant
> * Distribuire un database analitico ad hoc e aggiungere lo schema per eseguire query distribuite.


Provare ora l'[esercitazione sull'analisi dei tenant](sql-database-saas-tutorial-tenant-analytics.md) per esplorare l'estrazione dei dati in un database di analisi separato per elaborazioni analitiche più complesse.

## <a name="additional-resources"></a>Risorse aggiuntive

* Altre [esercitazioni basate sull'applicazione SaaS Wingtip](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [Query elastica](sql-database-elastic-query-overview.md)
