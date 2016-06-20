<properties
   pageTitle="Gestire la potenza di calcolo in Azure SQL Data Warehouse (Panoramica) | Microsoft Azure"
   description="Funzionalità relative alla scalabilità orizzontale delle prestazioni in Azure SQL Data Warehouse. Eseguire il ridimensionamento modificando le impostazioni DWU o sospendendo e riavviando le risorse di calcolo per ridurre i costi."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/01/2016"
   ms.author="barbkess;sonyama"/>

# Gestire la potenza di calcolo in Azure SQL Data Warehouse (Panoramica)

> [AZURE.SELECTOR]
- [Panoramica](sql-data-warehouse-manage-compute-overview.md)
- [Portale](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [REST](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)

L'architettura di SQL Data Warehouse separa le risorse di archiviazione e calcolo consentendo a entrambe di eseguire il ridimensionamento in modo indipendente. Di conseguenza, è possibile ridimensionare le prestazioni e ridurre i costi pagando solo per le prestazioni necessarie.

Questa panoramica descrive le funzionalità di ridimensionamento delle prestazioni di SQL Data Warehouse seguenti e fornisce indicazioni su come e quando usarle.

- Ridimensionare la potenza di calcolo modificando le Unità Data Warehouse (DWU)
- Sospendere o riavviare le risorse di calcolo

<a name="scale-performance-bk"></a>

## Ridimensionare le prestazioni

In SQL Data Warehouse è possibile ridimensionare rapidamente le prestazioni aumentando o riducendo le risorse di calcolo di CPU, memoria e larghezza di banda I/O. Per ridimensionare le prestazioni, è sufficiente modificare il numero di Unità Data Warehouse (DWU) allocate al proprio database da SQL Data Warehouse. SQL Data Warehouse apporta rapidamente la modifica e gestisce tutte le modifiche sottostanti all'hardware o al software.

>[AZURE.NOTE] Sono ormai passati i tempi in cui occorreva individuare il tipo di processori, la quantità di memoria o il tipo di archiviazione appropriati per garantire prestazioni ottimali nel data warehouse. Allestendo il proprio data warehouse nel cloud, non è più necessario occuparsi dei problemi hardware di base. SQL Data Warehouse invece chiede semplicemente con quale velocità si intende elaborare i dati.

### Che cos'è una DWU?

Un'*Unità Data Warehouse* (DWU) è un parametro che consente di misurare le funzionalità di calcolo sottostanti il database in qualsiasi momento. Man mano che aumenta il numero di DWU, SQL Data Warehouse esegue operazioni in parallelo, ad esempio caricamento dati o query, tra le risorse più distribuite della memoria e della CPU. Questo riduce la latenza e migliora le prestazioni.

Le DWU si basano sulle frequenze di caricamento e analisi. Man mano che aumentano le DWU, aumenta anche la frequenza di caricamento e analisi.

- **Frequenza di caricamento**. Il numero di record che SQL Data Warehouse può inserire al secondo. In particolare, questo è il numero di record che SQL Data Warehouse può importare dall'archiviazione BLOB di Azure in un indice cluster columnstore tramite PolyBase. 

- **Frequenza di analisi**. Il numero di record che una query può recuperare da SQL Data Warehouse al secondo. In particolare, questo è il numero di record che SQL Data Warehouse può restituire eseguendo una query di data warehousing in un indice cluster columnstore. Per verificare la frequenza di analisi, viene usata una query di data warehousing standard che esegue l'analisi di un numero elevato di righe e quindi un'aggregazione complessa. Si tratta di un'operazione con utilizzo intensivo di I/O e CPU.

>[AZURE.NOTE] Sono in corso alcune modifiche e miglioramenti importanti sulle prestazioni e le frequenze previste saranno presto rese disponibili. Durante il periodo di anteprima verranno apportati miglioramenti (ad esempio, aumento della compressione e della memorizzazione nella cache) per aumentare i tassi e per garantire una scalabilità prevedibile.

Per un elenco delle DWU, vedere gli obiettivi del livello di servizio nell'articolo relativo ai [limiti di capacità][].

### Come ridimensionare le prestazioni?

Per aumentare o diminuire la potenza di calcolo in modo elastico, è sufficiente modificare l'impostazione Unità Data Warehouse (DWU) per il database. In background, SQL Data Warehouse modifica le allocazioni di memoria e CPU tramite funzionalità di distribuzione semplici e rapide del database SQL.

Le DWU vengono allocate in blocchi di 100, ma non tutti i blocchi sono disponibili. Man mano che le DWU aumentano anche le prestazioni aumentano in modo lineare. A livelli superiori di DWU, è necessario aggiungere più di 100 DWU per osservare un miglioramento significativo delle prestazioni. Per consentire la selezione di variazioni di DWU significative, vengono forniti livelli di DWU in grado di offrire i migliori risultati.
 
Per modificare le DWU, è possibile usare uno di questi metodi singoli.

- [Scale compute power with Azure portal (Ridimensionare la potenza di calcolo con il portale di Azure)][]
- [Scale compute power with PowerShell (Ridimensionare la potenza di calcolo con PowerShell)][]
- [Scale compute power with REST APIs (Ridimensionare la potenza di calcolo con le API REST)][]
- [Scale compute power with TSQL (Ridimensionare la potenza di calcolo con TSQL)][]

### Quante DWU è consigliabile usare?
 
Le prestazioni in SQL Data Warehouse vengono scalate in modo lineare e il passaggio da una scala di calcolo a un'altra (ad esempio, da 100 DWU a 2000 DWU) avviene nel giro di pochi secondi. Ciò consente di sperimentare diverse impostazioni DWU fino a determinare lo scenario più adatto alle proprie esigenze.

> [AZURE.NOTE] È possibile che il ridimensionamento delle prestazioni previsto non sia evidente con volumi di dati inferiori. È consigliabile iniziare con volumi di dati minimi di 1 TB per ottenere risultati dei test delle prestazioni accurati.

Raccomandazioni per individuare l'impostazione DWU più adatta al proprio carico di lavoro:

1. Per un data warehouse in sviluppo, iniziare con un numero limitato di DWU.
2. Monitorare le prestazioni dell'applicazione, osservare che il numero di DWUs selezionato in relazione alle prestazioni che si osservano.
3. Determinare di quanto si vogliono aumentare o ridurre le prestazioni per raggiungere il livello ottimale per i propri requisiti presupponendo una scalabilità lineare.
4. Aumentare o diminuire il numero di DWU proporzionalmente alla velocità desiderata per le prestazioni del proprio carico di lavoro. Il servizio risponderà rapidamente e adatterà le risorse di calcolo in modo che soddisfino i nuovi requisiti di DWU.
5. Continuare ad apportare modifiche finché non si raggiunge un livello di prestazioni ottimale per i propri requisiti aziendali.

### Quando è necessario ridimensionare le DWU?

In generale, è necessario che le DWU siano semplici. Quando sono necessari risultati più rapidi, aumentare le DWUs e pagare il costo di prestazioni più elevate. Quando è richiesta una minore quantità di potenza di calcolo, diminuire le DWU e pagare solo per le prestazioni necessarie

Raccomandazioni sul momento più indicato per il ridimensionamento delle DWU:

1. Se l'applicazione ha un carico di lavoro molto variabile, aumentare o diminuire i livelli di DWU per far fronte ai picchi positivi e negativi. Ad esempio, se il carico di lavoro in genere fa registrare un picco alla fine del mese, pianificare l'aggiunta di altre DWU durante questi giorni di picco e quindi ridurre le dimensioni al termine del periodo critico.
1. Prima di eseguire un'operazione di caricamento o trasformazione di quantità di dati elevate, ridimensionare le DWU in modo che i dati siano disponibili più rapidamente.

<a name="pause-compute-bk"></a>

## Sospendere le risorse di calcolo

[AZURE.INCLUDE [Descrizione della sospensione di SQL Data Warehouse](../../includes/sql-data-warehouse-pause-description.md)]

Per sospendere un database, usare uno di questi metodi singoli.

- [Sospendere le risorse di calcolo con il portale di Azure][]
- [Sospendere le risorse di calcolo con PowerShell][]
- [Sospendere le risorse di calcolo con le API REST][]

<a name="resume-compute-bk"></a>

## Riavviare le risorse di calcolo

[AZURE.INCLUDE [Descrizione del riavvio di SQL Data Warehouse](../../includes/sql-data-warehouse-resume-description.md)]

Per riavviare un database, usare uno di questi metodi singoli.

- [Riavviare le risorse di calcolo con il portale di Azure][]
- [Riavviare le risorse di calcolo con PowerShell][]
- [Riavviare le risorse di calcolo con le API REST][]

<a name="next-steps-bk"></a>

## Passaggi successivi
Per comprendere più facilmente alcuni ulteriori concetti importanti sulle prestazioni e la scalabilità, fare riferimento agli articoli seguenti:

- [Modello di concorrenza][]
- [Progettazione di tabelle][]
- [Scegliere una chiave di distribuzione hash per la tabella][]
- [Statistiche per migliorare le prestazioni][]

<!--Image reference-->

<!--Article references-->

[Scale compute power with Azure portal (Ridimensionare la potenza di calcolo con il portale di Azure)]: ./sql-data-warehouse-manage-compute-portal.md#scale-compute-bk
[Scale compute power with PowerShell (Ridimensionare la potenza di calcolo con PowerShell)]: ./sql-data-warehouse-manage-compute-powershell.md#scale-compute-bk
[Scale compute power with REST APIs (Ridimensionare la potenza di calcolo con le API REST)]: ./sql-data-warehouse-manage-compute-rest-api.md#scale-compute-bk
[Scale compute power with TSQL (Ridimensionare la potenza di calcolo con TSQL)]: ./sql-data-warehouse-manage-compute-tsql.md#scale-compute-bk

[limiti di capacità]: ./sql-data-warehouse-service-capacity-limits.md

[Sospendere le risorse di calcolo con il portale di Azure]: ./sql-data-warehouse-manage-compute-portal.md#pause-compute-bk
[Sospendere le risorse di calcolo con PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#pause-compute-bk
[Sospendere le risorse di calcolo con le API REST]: ./sql-data-warehouse-manage-compute-rest-api.md#pause-compute-bk

[Riavviare le risorse di calcolo con il portale di Azure]: ./sql-data-warehouse-manage-compute-portal.md#resume-compute-bk
[Riavviare le risorse di calcolo con PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#resume-compute-bk
[Riavviare le risorse di calcolo con le API REST]: ./sql-data-warehouse-manage-compute-rest-api.md#resume-compute-bk

[Modello di concorrenza]: sql-data-warehouse-develop-concurrency.md
[Progettazione di tabelle]: sql-data-warehouse-develop-table-design.md
[Scegliere una chiave di distribuzione hash per la tabella]: sql-data-warehouse-develop-hash-distribution-key.md
[Statistiche per migliorare le prestazioni]: sql-data-warehouse-develop-statistics.md
[development overview]: sql-data-warehouse-overview-develop.md



<!--MSDN references-->


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0608_2016-->