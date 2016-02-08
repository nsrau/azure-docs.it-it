<properties
    pageTitle="Panoramica delle query su database elastico del database SQL di Azure | Microsoft Azure"
    description="Annuncia la funzionalità di query elastica"    
    services="sql-database"
    documentationCenter=""  
    manager="jeffreyg"
    authors="sidneyh"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/22/2016"
    ms.author="torsteng" />

# Panoramica delle query su database elastico del database SQL di Azure (anteprima)

La funzionalità di query su database elastico, disponibile in anteprima, consente di eseguire una query Transact-SQL su più database del database SQL di Azure (SQLDB). Consente di eseguire query tra database per accedere a tabelle remote e di connettere strumenti Microsoft e di terze parti (Excel, PowerBI, Tableau e così via) a query tra livelli di dati con più database. Utilizzando questa funzionalità, è possibile scalare query ai livelli di dati di grandi dimensioni nel Database SQL e visualizzare i risultati nei report di business intelligence (BI). Per iniziare la creazione di un'applicazione di query su database elastico, vedere [Iniziare a usare le query su database elastico](sql-database-elastic-query-getting-started.md).

## Novità delle query su database elastico

* È ora possibile definire completamente in T-SQL gli scenari di query tra database con singoli database remoti. Ciò consente l'esecuzione di query di sola lettura sui database remoti e permette ai clienti locali di SQL Server di eseguire la migrazione delle applicazioni usando nomi in tre e quattro parti o un server collegato nel database SQL.
* La query elastica è ora supportata nel livello di prestazioni Standard, oltre che nel livello di prestazioni Premium. Per informazioni sulle limitazioni delle prestazioni per i livelli di prestazioni inferiori, vedere la sezione relativa alle limitazioni dell'anteprima.
* Le query elastiche possono ora eseguire il push dei parametri SQL nei database remoti per l'esecuzione.
* Le chiamate di stored procedure o di funzioni remote che usano sp\_execute\_fanout possono ora usare parametri analoghi a [sp\_executesql](https://msdn.microsoft.com/library/ms188001.aspx).
* Le prestazioni per il recupero di set di risultati di grandi dimensioni dal database remoto sono state migliorate.
* Le tabelle esterne con una query elastica possono ora fare riferimento a tabelle remote con nome di schema o tabella diverso.

## Scenari di query del database elastico

L'obiettivo consiste nel semplificare gli scenari di query in cui più database forniscono righe in un singolo risultato complessivo. La query può essere composta direttamente dall'utente o dall'applicazione oppure indirettamente tramite gli strumenti connessi al database. Ciò è particolarmente utile quando si creano report, si usano strumenti commerciali di Business Intelligence o di integrazione dei dati o si usa qualsiasi applicazione che non può essere modificata. La query elastica consente di eseguire query in diversi database usando l'esperienza di connettività nota di SQL Server in strumenti quali Excel, PowerBI, Tableau o Cognos. Una query elastica semplifica l'accesso a un'intera raccolta di database tramite query emesse da SQL Server Management Studio o Visual Studio e semplifica le query tra database da Entity Framework o altri ambienti ORM. La Figura 1 mostra uno scenario in cui un'applicazione cloud esistente, che usa la [libreria client dei database elastici](sql-database-elastic-database-client-library.md), si basa su un livello di dati con scalabilità orizzontale e una query elastica viene usata per la creazione di report tra database.

**Figura 1** Query su database elastico usata su un livello di dati con scalabilità orizzontale

![Query di database elastica utilizzato nel livello dati con scalabilità orizzontale][1]

Gli scenari relativi ai clienti per la query elastica sono caratterizzati dalle topologie seguenti:

* **Partizionamento verticale - Query tra database** (Topologia 1): i dati vengono partizionati verticalmente tra alcuni database in un livello di dati. In genere, diversi set di tabelle si trovano in diversi database. Lo schema risulta quindi diverso nei diversi database. Ad esempio, tutte le tabelle per l'inventario si trovano in un database, mentre le tabelle correlate alla contabilità si trovano in un altro database. I casi di utilizzo comuni con questa topologia richiedono l'esecuzione di query o la compilazione di report tra tabelle in diversi database.
* **Partizionamento orizzontale - Partizionamento orizzontale** (Topologia 2): i dati vengono partizionati orizzontalmente per distribuire le righe in un livello dati con partizionamento orizzontale. Con questo approccio lo schema risulta identico in tutti i database partecipanti. Questo approccio viene definito anche "partizionamento orizzontale". Il partizionamento orizzontale può essere eseguito e gestito mediante (1) le librerie di strumenti dei database elastici o (2) il partizionamento orizzontale automatico. Una query elastica viene usata per eseguire query o compilare report in molte partizioni.

> [AZURE.NOTE] La query di database elastico è adatta per scenari di reporting occasionali dove è possibile eseguire la maggior parte dell'elaborazione nel livello dati. Per carichi di lavoro di creazione di report elevati o per scenari di data warehousing con query più complesse è possibile usare anche [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).


## Topologie di query su database elastico

### Topologia 1: Partizionamento verticale - Query tra database

Per iniziare a scrivere codice, vedere [Introduzione alle query tra database (partizionamento verticale)](sql-database-elastic-query-getting-started-vertical.md).

È possibile usare una query elastica per rendere disponibili i dati situati in un database SQL ad altri database SQL. Ciò consente alle query di un database di fare riferimento alle tabelle in qualsiasi altro database SQL remoto. Il primo passaggio consiste nel definire un'origine dati esterna per ogni database remoto. L'origine dati esterna viene definita nel database locale da cui si vuole ottenere l'accesso alle tabelle situate nel database remoto. Non è necessaria alcuna modifica al database remoto. Per scenari tipici di partizionamento verticale in cui i diversi database hanno schemi diversi, è possibile usare le query elastiche per implementare casi di utilizzo comuni, ad esempio l'accesso a dati di riferimento e l'esecuzione di query tra database.

**Dati di riferimento**: la topologia 1 viene usata per la gestione dei dati di riferimento. Nella figura seguente due tabelle (T1 e T2) con dati di riferimento vengono mantenute in un database dedicato. Usando una query elastica è ora possibile accedere alle tabelle T1 e T2 in modalità remota da altri database, come illustrato nella figura. Usare la topologia 1 se le tabelle di riferimento hanno dimensioni ridotte o se le query nella tabella di riferimento hanno predicati selettivi.

**Figura 2** Partizionamento verticale - Uso della query elastica per eseguire query nei dati di riferimento

![Partizionamento verticale - Uso della query elastica per eseguire query nei dati di riferimento][3]

**Query tra database**: le query elastiche consentono casi di utilizzo che richiedono l'esecuzione di query in diversi database SQL. La Figura 3 mostra quattro database diversi, ovvero CRM, Inventario, Risorse umane e Prodotti. Le query eseguite in uno dei database devono accedere anche a uno o a tutti gli altri database. Usando una query elastica, è possibile configurare il database per questa situazione eseguendo alcune semplici istruzioni DDL in ogni database. Dopo avere eseguito una volta questa configurazione, l'accesso a una tabella remota sarà semplice quando fare riferimento a una tabella locale dalle query T-SQL o dagli strumenti di Business Intelligence. Questo approccio è consigliato se le query remote non restituiscono risultati di grandi dimensioni.

**Figura 3** Partizionamento verticale - Uso delle query elastiche per eseguire query in diversi database

![Partizionamento verticale - Uso delle query elastiche per eseguire query in diversi database][4]

### Topologia 2: Partizionamento orizzontale - Partizionamento orizzontale

Per iniziare a scrivere codice [Introduzione alle query su database elastico per il partizionamento orizzontale](sql-database-elastic-query-getting-started.md)

L'uso delle query elastiche per eseguire attività di reporting su un livello dati con partizionamento orizzontale richiede una [mappa partizioni dei database elastici](sql-database-elastic-scale-shard-map-management.md) per rappresentare i database del livello dati. In genere in questo scenario viene usata solo una mappa partizioni e un database dedicato con capacità di query elastiche viene usato come punto di ingresso per le query di reporting. L'accesso alla mappa partizioni è necessario solo per questo database dedicato. La Figura 2 illustra questa topologia e la relativa configurazione con query su database elastico e la mappa partizioni. Si noti che solo il database elastico sottoposto a query deve essere un database SQL di Azure versione 12. I database del livello dati possono essere database SQL di Azure di qualsiasi versione o edizione. Per altre informazioni sulla libreria client dei database elastici e sulla creazione di mappe partizioni, vedere [Gestione delle mappe partizioni](sql-database-elastic-scale-shard-map-management.md).

**Figura 4** Partizionamento orizzontale - Uso delle query elastiche per la creazione di report relativi ai livelli dati con partizionamento orizzontale

![Partizionamento orizzontale - Uso delle query elastiche per la creazione di report relativi ai livelli dati con partizionamento orizzontale][5]

> [AZURE.NOTE] Il database elastico sottoposto a query dedicato deve essere un database SQL versione 12. Non esistono restrizioni su partizioni stesse.


## Implementazione delle query su database elastico

I passaggi per l'implementazione delle query elastiche per gli scenari di partizionamento verticale e orizzontale sono illustrati nelle sezioni seguenti, che includono anche riferimenti a documentazione più dettagliata per i diversi scenari di partizionamento.

### Partizionamento verticale - Query tra database

I passaggi seguenti configurano le query su database elastici per scenari di partizionamento verticale che richiedono l'accesso a una tabella situata in un database SQL remoto:

*    [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
*    [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
*    [CREATE/DROP EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource of type **RDBMS**
*    [CREATE/DROP EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx) mytable

Dopo l'esecuzione delle istruzioni DDL, sarà possibile accedere alla tabella remota "mytable" come se fosse una tabella locale. Il database SQL di Azure apre automaticamente una connessione al database remoto, elabora la richiesta nel database remoto e restituisce i risultati. Per altre informazioni sui passaggi necessari per lo scenario di partizionamento verticale, vedere [Query elastiche per il partizionamento verticale](sql-database-elastic-query-vertical-partitioning.md).

### Partizionamento orizzontale - Partizionamento orizzontale

I passaggi seguenti configurano le query su database elastico per scenari di partizionamento orizzontale che richiedono l'accesso a un set di tabelle situate, in genere, in alcuni database SQL remoti:

*    [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
*    [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
*    Creare una [mappa partizioni](sql-database-elastic-scale-shard-map-management.md) che rappresenta il livello dati usando la libreria client dei database elastici.   
*    [CREATE/DROP EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource of type **SHARD\_MAP\_MANAGER**
*    [CREATE/DROP EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx) mytable

Dopo avere eseguito questi passaggi, sarà possibile accedere alla tabella con partizionamento orizzontale "mytable" come se fosse una tabella locale. Il database SQL di Azure apre automaticamente più connessioni parallele ai database remoti in cui vengono archiviate fisicamente le tabelle, elabora le richieste nei database remoti e restituisce i risultati. Per altre informazioni sui passaggi necessari per gli scenari di partizionamento orizzontale, vedere [Query elastiche per il partizionamento orizzontale](sql-database-elastic-query-horizontal-partitioning.md).

## Query T-SQL
Dopo avere definito le origini dati esterne e le tabelle esterne, è possibile usare le normali stringhe di connessione di SQL Server per connettersi ai database in cui sono state definite le tabelle esterne. È quindi possibile eseguire le istruzioni T-SQL sulle tabelle esterne in tale connessioni con le limitazioni illustrate di seguito. Per altre informazioni ed esempi di query T-SQL, vedere gli argomenti relativi alla documentazione sul [partizionamento orizzontale](sql-database-elastic-query-horizontal-partitioning.md) e sul [partizionamento verticale](sql-database-elastic-query-vertical-partitioning.md).

## Connettività per gli strumenti
È possibile usare le normali stringhe di connessione di SQL Server per connettere le applicazioni e gli strumenti di Business Intelligence o di integrazione dei dati ai database con tabelle esterne. Assicurarsi che SQL Server sia supportato come origine dati per lo strumento. Dopo la connessione, fare riferimento al database elastico sottoposto a query e alle tabelle esterne in tale database in modo analogo a qualsiasi altro database di SQL Server a cui ci si connette con lo strumento specifico.

## Costi

Le query elastiche sono incluse nei costi dei database SQL di Azure. Si noti che sono supportate le topologie in cui i database remoti si trovano in un data center diverso rispetto all'endpoint delle query elastiche, ma per i dati in uscita da database remoti vengono addebitate le normali [tariffe di Azure](https://azure.microsoft.com/pricing/details/data-transfers/).

## Limiti di anteprima
* L'esecuzione della prima query elastica può richiedere alcuni minuti nel livello di prestazioni Standard. Questo intervallo di tempo è necessario per caricare le funzionalità delle query elastiche. Le prestazioni di caricamento risultano migliori nei livelli di prestazioni più elevati.
* La creazione di script di origini dati esterne o tabelle esterne da SSMS o SSDT non è ancora supportata.
* L'importazione/esportazione per il database SQL non supporta ancora origini dati esterne e tabelle esterne. Se è necessario usare l'importazione/esportazione, eliminare questi oggetti prima dell'esportazione e quindi crearli di nuovo dopo l'importazione.
* Le query su database elastico supportano attualmente solo l'accesso in sola lettura alle tabelle esterne. È tuttavia possibile usare la funzionalità T-SQL completa nel database in cui viene definita la tabella esterna. Ciò può risultare utile, ad esempio, per rendere permanenti i risultati temporanei usando SELECT <column_list> INTO <local_table> oppure per definire stored procedure nel database elastico sottoposto a query che fanno riferimento a tabelle esterne.
* Ad eccezione di nvarchar(max), i tipi LOB non sono supportati nelle definizioni di tabelle esterne. Come soluzione alternativa, è possibile creare una visualizzazione nel database remoto che esegue il cast del tipo LOB in nvarchar(max), definire una tabella esterna sulla visualizzazione invece della tabella di base e quindi eseguirne di nuovo il cast nel tipo LOB originale nelle query.
* Le statistiche di colonna sulle tabelle esterne non sono attualmente supportate. Le statistiche di tabella sono supportate ma devono essere create manualmente.

## Commenti e suggerimenti
Per condividere commenti e suggerimenti sull'esperienza con le query elastiche, usare Disqus, i forum MSDN o Stackoverflow. Siamo interessati a tutti i tipi di commenti e suggerimenti relativi al servizio (difetti, bordi approssimativo, gap di funzionalità).

## Altre informazioni

Altre informazioni sugli scenari di query tra database e di partizionamento verticale sono disponibili nei documenti seguenti:

* [Panoramica delle query tra database e del partizionamento verticale](sql-database-elastic-query-vertical-partitioning.md)
* Per ottenere un esempio completo funzionante in pochi minuti, provare a seguire l'esercitazione dettagliata disponibile in [Introduzione alle query tra database (partizionamento verticale)](sql-database-elastic-query-getting-started-vertical.md).


Per altre informazioni sugli scenari di partizionamento orizzontale, vedere:

* [Panoramica del partizionamento orizzontale](sql-database-elastic-query-horizontal-partitioning.md)
* Per ottenere un esempio completo funzionante in pochi minuti, provare a seguire l'esercitazione dettagliata disponibile in [Introduzione alle query dei database elastici per il partizionamento orizzontale](sql-database-elastic-query-getting-started.md).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->

<!---HONumber=AcomDC_0128_2016-->