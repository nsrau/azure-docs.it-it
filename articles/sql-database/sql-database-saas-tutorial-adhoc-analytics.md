---
title: "Eseguire query di analisi ad-hoc su più database SQL di Azure | Microsoft Docs"
description: "Eseguire query di analisi ad-hoc su più database SQL nell&quot;app multi-tenant SaaS Wingtip."
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
ms.date: 05/22/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 31be50ca3f64cc183e516f1b0f06f5a4265f6103
ms.contentlocale: it-it
ms.lasthandoff: 05/25/2017


---
# <a name="run-ad-hoc-analytics-queries-across-all-wingtip-saas-tenants"></a>Eseguire query di analisi ad-hoc su tutti i tenant SaaS Wingtip

Questa esercitazione illustra come creare un database di analisi ad-hoc ed eseguire diverse query su tutti i tenant. Queste query consentono di estrarre informazioni approfondite nascoste nei dati operativi quotidiani dell'app WTP.

Per eseguire query di analisi ad hoc su più tenant, l'app SaaS Wingtip usa [query elastiche](sql-database-elastic-query-overview.md) insieme a un database di analisi.


In questa esercitazione si apprenderà come:

> [!div class="checklist"]

> * Distribuire un database di analisi ad hoc
> * Eseguire query distribuite su tutti i database tenant



Per completare questa esercitazione, verificare che siano soddisfatti i prerequisiti seguenti:

* L'app SaaS Wingtip viene distribuita. Per distribuire in meno di cinque minuti, vedere [Distribuire ed esplorare l'applicazione SaaS Wingtip](sql-database-saas-tutorial.md)
* Azure PowerShell è installato. Per informazioni dettagliate, vedere [Introduzione ad Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)


## <a name="ad-hoc-analytics-pattern"></a>Modello di analisi ad hoc

Una delle grandi opportunità offerte dalle applicazioni SaaS è la possibilità di usare enormi quantità di dati dei tenant archiviati in modo centralizzato nel cloud. Questi dati possono essere usati per ottenere informazioni approfondite sul funzionamento e l'utilizzo delle applicazioni, sui tenant, sui relativi utenti e le loro preferenze, i loro comportamenti e così via. Queste informazioni possono essere utili come base per progettare sviluppi futuri, miglioramenti dell'usabilità e altri investimenti per app e servizi.

L'accesso a questi dati è semplice quando sono raccolti in un singolo database multi-tenant, ma non è altrettanto semplice in una distribuzione su larga scala potenzialmente in migliaia di database. Uno degli approcci possibili prevede l'uso di una query elastica, che consente di eseguire query ad-hoc su un set di database distribuiti con uno schema comune. La query elastica usa un singolo database *principale* in cui sono definite le tabelle esterne speculari alle tabelle o alle viste nei database (tenant) distribuiti. Le query inviate a questo database principale vengono compilate per produrre un piano di query distribuito, con il push di parti della query ai database tenant all'occorrenza. La query elastica usa la mappa partizioni nel database del catalogo per fornire la posizione dei database tenant. La configurazione e l'esecuzione di query sono semplici con il linguaggio [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference) standard e sono supportate query ad hoc da strumenti come Power BI ed Excel.

## <a name="get-the-wingtip-application-scripts"></a>Ottenere gli script dell'applicazione Wingtip

Gli script dell'app SaaS Wingtip e il codice sorgente dell'applicazione sono disponibili nel repository GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). [Procedura per scaricare gli script dell'app SaaS Wingtip](sql-database-wtp-overview.md#download-the-wingtip-saas-scripts).


## <a name="explore-the-global-views-in-the-tenant-databases"></a>Esplorare le viste globali nei database tenant

L'applicazione SaaS Wingtip è compilata in base a un modello che usa un database per ogni tenant. Lo schema del database tenant è quindi definito dal punto di vista di un tenant singolo. Le informazioni specifiche sul tenant sono presenti nella tabella *Venue*, che contiene sempre una singola riga ed è progettata come un heap, senza una chiave primaria.  Non è necessario che le altre tabelle nello schema siano correlate alla tabella *Venue*, perché nell'uso normale l'appartenenza dei dati ai tenant non è mai dubbia.  Tuttavia, quando si eseguono query in tutti i database, diventa importante correlare i dati delle tabelle nel database a un tenant specifico. Per semplificare l'operazione, viene aggiunto un set di viste al database tenant per offrire una vista globale di ogni tenant. Le viste globali proiettano un ID tenant in ogni tabella su cui verranno eseguite query a livello globale. In questo modo è più facile identificare i dati da ogni tenant. Per maggiore comodità, le viste sono state create in precedenza in tutti i database tenant, così come la versione finale del database, in modo che le viste siano disponibili man mano che si effettua il provisioning di nuovi tenant.

1. Aprire SSMS e [connettersi al server tenants1-&lt;USER&gt;](sql-database-wtp-overview.md#explore-database-schema-and-execute-sql-queries-using-ssms).
1. Espandere **Database**, fare clic con il pulsante destro del mouse su **contosoconcerthall** e selezionare **Nuova query**.
1. Per esplorare le viste globali, eseguire queste query:

   ```T-SQL
   -- This is the base Venue table, that has no VenueId associated.
   SELECT * FROM Venue

   -- Notice the plural name 'Venues'. This view projects a VenueId column.
   -- In the sample database we calculated an integer id from a hash of the Venue name,
   -- but any approach could be used to introduce a unique value.
   -- This is similar to how we create the tenant key in the catalog,
   -- but there is no requirement that the catalog key and this id be the same.
   SELECT * FROM Venues

   -- The base Events table which has no VenueId column.
   SELECT * FROM Events

   -- This view projects the VenueId retrieved from the Venues table.
   SELECT * FROM VenueEvents
   ```

Per esaminare una vista e vedere come viene creata:

1. In **Esplora oggetti** espandere **contosoconcethall** > **Viste**:

   ![Viste](media/sql-database-saas-tutorial-adhoc-analytics/views.png)

1. Fare clic con il pulsante destro del mouse su **dbo.Venues**.
1. Selezionare **Crea script per vista** > **CREATE in** > **Nuova finestra editor di query**.

Ripetere questa procedura per ogni *vista* di cui si vuole esaminare la modalità di creazione.

## <a name="deploy-the-database-used-for-ad-hoc-analytics-queries"></a>Distribuire il database usato per le query di analisi ad hoc

Questo esercizio permette di distribuire il database *adhocanalytics*, che contiene lo schema usato per l'esecuzione di query in tutti i database tenant. Il database viene distribuito nel server di catalogo esistente, vale a dire il server che contiene tutti i database correlati alla gestione.

1. Aprire ...\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocAnalytics.ps1* in *PowerShell ISE* e impostare i valori seguenti:
   * **$DemoScenario** = 2, **Distribuire un database di analisi ad hoc**.

1. Scorrere verso il basso fino allo script SQL contenente lo schema per il database.  Esaminare lo script e tenere presente quanto segue:

   1. La query elastica usa credenziali con ambito database per accedere a ognuno dei database tenant. Tali credenziali devono essere disponibili in tutti i database e, in genere, devono avere i diritti minimi necessari per abilitare le query ad hoc.
   1. L'origine dati esterna, definita in modo da usare la mappa partizioni del tenant nel database del catalogo.  Usando questa origine dati esterna, le query verranno distribuite a tutti i database registrati nel catalogo nel momento in cui che viene eseguita la query.
   1. Le tabelle esterne che fanno riferimento alle viste globali descritte nella sezione precedente.
   1. La tabella locale *VenueTypes* che viene creata e popolata.  Dal momento che la tabella dei dati di riferimento è comune a tutti i database tenant, può essere rappresentata come una tabella locale. Per alcune query questo permette di ridurre la quantità di dati spostati tra i database tenant e il database *adhocanalytics*.

1. Premere **F5** per eseguire lo script e creare il database *adhocanalytics*.

   È possibile ignorare gli avvisi relativi al *server RPC non disponibile*.


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

Provare l'[esercitazione sull'analisi dei tenant](sql-database-saas-tutorial-tenant-analytics.md)

## <a name="additional-resources"></a>Risorse aggiuntive

* [Altre esercitazioni basate sulla distribuzione iniziale dell'applicazione SaaS Wingtip](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [Query elastica](sql-database-elastic-query-overview.md)

