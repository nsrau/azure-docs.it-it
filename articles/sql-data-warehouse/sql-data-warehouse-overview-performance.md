<properties
   pageTitle="Panoramica delle prestazioni e della scalabilità | Microsoft Azure"
   description="Introduzione alle funzionalità per le prestazioni e la scalabilità di SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
   ms.author="barbkess;JRJ@BigBangData.co.uk;mausher;nicw"/>

# Panoramica delle prestazioni e della scalabilità
Allestendo il proprio data warehouse nel cloud, non è più necessario occuparsi dei problemi hardware di base. Sono ormai passati i tempi in cui occorreva individuare il tipo di processori, la quantità di memoria o il tipo di archiviazione appropriati per garantire prestazioni ottimali nel data warehouse. SQL Data Warehouse invece chiede semplicemente con quale velocità si intende elaborare i dati.

SQL Data Warehouse è una piattaforma di database distribuita e basata sul cloud, progettata per offrire elevate prestazioni su scala e per consentire un controllo completo sulle risorse usate per la risoluzione delle query. Semplicemente modificando il numero di DWU allocate al data warehouse, è possibile scalare in modo elastico le dimensioni delle risorse di quest'ultimo in pochi secondi. Essendo una piattaforma di scalabilità orizzontale distribuita, SQL Data Warehouse consente al data warehouse di elaborare volumi di dati significativi in maniera efficiente ed efficace lasciando il controllo completo sul costo della soluzione.

*Le informazioni seguenti si applicano ad Azure SQL Data Warehouse in versione di anteprima. Tali informazioni verranno aggiornate continuamente durante il periodo di anteprima man mano che il servizio verrà migliorato fino a giungere alla fase di disponibilità generale.*

Gli obiettivi Microsoft per SQL Data Warehouse sono i seguenti: - Prestazioni prevedibili e scalabilità lineare fino a petabyte di dati - Elevata affidabilità per tutte le operazioni relative al data warehouse con il supporto di un contratto di servizio - Breve intervallo di tempo tra il caricamento e la comprensione dei dati, sia relazionali che non relazionali

Microsoft continuerà a lavorare per il raggiungimento di tali obiettivi durante il periodo di anteprima, in modo che possano essere realizzati prima di giungere alla fase di disponibilità generale.

>[AZURE.NOTE]Le sezioni seguenti illustrano il servizio Azure SQL Data Warehouse al momento del lancio dell'anteprima pubblica. Tali informazioni verranno aggiornate continuamente durante il periodo di anteprima man mano che il servizio verrà migliorato fino a giungere alla fase di disponibilità generale.

## Protezione dati
SQL Data Warehouse archivia tutti i dati in Archiviazione di Azure tramite BLOB con ridondanza geografica. Nell'area di Azure locale vengono mantenute tre copie sincrone dei dati per garantire una protezione trasparente degli stessi in caso di problemi localizzati, quali malfunzionamenti delle unità di archiviazione. Inoltre, in un'area di Azure remota vengono mantenute tre copie asincrone allo scopo di proteggere i dati qualora si verificassero problemi nell'area (ripristino di emergenza). L'area locale e quella remota vengono abbinate per mantenere latenze di sincronizzazione accettabili, ad esempio Stati Uniti orientali e Stati Uniti occidentali.

## Ripristino del database
SQL Data Warehouse esegue il backup di tutti i dati ogni quattro ore usando snapshot di Archiviazione di Azure. Questi snapshot vengono conservati per sette giorni senza alcun addebito. È quindi possibile ripristinare i dati tornando indietro di un massimo di 42 momenti specifici degli ultimi sette giorni e arrivando fino all'ora in cui è stato acquisito l'ultimo snapshot. Durante il periodo di anteprima verrà introdotta la possibilità di specificare valori di conservazione diversi. I dati possono essere ripristinati da uno snapshot tramite PowerShell o le API REST. Gli snapshot vengono copiati in modo asincrono in un'area di Azure remota per assicurare una maggiore recuperabilità in caso di errori a livello dell'area stessa (ripristino di emergenza).

## Affidabilità
SQL Data Warehouse è un sistema distribuito con più componenti, il cui numero aumenta man mano che aumenta il numero delle unità data warehouse (DWU). SQL Data Warehouse rileva e risolve automaticamente i problemi di calcolo, tuttavia un'operazione quale una query o un caricamento di dati può avere esito negativo a causa di errori di calcolo individuali. Durante il periodo di anteprima l'affidabilità delle query verrà migliorata costantemente per consentire un corretto completamento della maggior parte delle operazioni nonostante la presenza di problemi

In base ai dati di telemetria raccolti, l'affidabilità di SQL Data Warehouse è stimata al 98% per i carichi di lavoro di data warehouse tipici. Questo significa che, in media, 2 su 100 query potrebbero non riuscire a seguito di errori di sistema. Non si tratta di un contratto di servizio per l'anteprima, bensì di un indicatore dell'affidabilità prevista per le query eseguite. Si noti che la probabilità di esito negativo di una query cresce con l'aumentare del relativo tempo di esecuzione (ad esempio, una query che richiede più di 2 ore presenta molte più possibilità di errore rispetto a una query di una durata inferiore a 10 minuti). Durante il periodo di anteprima verranno apportati miglioramenti continui per garantire lo stesso livello di affidabilità per le operazioni, indipendentemente dal tempo di esecuzione. L'affidabilità prevista verrà aggiornata man mano che tali miglioramenti verranno effettuati, con l'obiettivo di fornire un contratto di servizio completo insieme alla versione di disponibilità generale.

Nel corso del periodo di anteprima SQL Data Warehouse può essere soggetto a un massimo di cinque eventi di manutenzione al mese per l'installazione di correzioni critiche. Ogni evento può causare problemi di esecuzione delle query per un massimo di 2 ore, intervallo di tempo che dipende dalla quantità di DWU usate dall'istanza di SQL Data Warehouse. Microsoft tenterà in tutti i modi di informare i clienti del verificarsi di tali eventi con un preavviso di 48 ore per consentire una pianificazione appropriata.

## Prestazioni e scalabilità
In SQL Data Warehouse sono state introdotte le unità data warehouse (DWU) come astrazione delle risorse di calcolo (CPU, memoria, I/O di archiviazione) aggregate tra diversi nodi. Aumentando il numero di DWU, aumentano le risorse di calcolo aggregate di un'istanza di SQL Data Warehouse. Quest'ultimo distribuisce le operazioni, ad esempio una query o un caricamento di dati, all'interno dell'intera infrastruttura di calcolo nell'istanza per aumentare o ridurre le prestazioni dei caricamenti e delle query man mano che il sistema viene scalato verticalmente o orizzontalmente.

Qualsiasi data warehouse prevede principalmente due metriche relative alle prestazioni: - **Velocità di caricamento**, ovvero il numero di record che è possibile caricare al secondo nel data warehouse. Nello specifico, viene misurato il numero di record che può essere importato, tramite PolyBase, dall'archivio BLOB di Azure a una tabella con un indice cluster columnstore. - **Velocità di analisi**, ovvero il numero di record che è possibile recuperare sequenzialmente al secondo dal data warehouse. Questa metrica misura in modo specifico il numero di record restituito da una query a partire da una tabella con un indice cluster columnstore.

Durante il periodo di anteprima verranno apportati miglioramenti continui per aumentare tali velocità e assicurare una scalabilità prevedibile.

## Principali concetti relativi alle prestazioni

Per comprendere più facilmente alcuni ulteriori concetti importanti sulle prestazioni e la scalabilità, fare riferimento agli articoli seguenti:

- [Prestazioni e scalabilità][]
- [Modello di concorrenza][]
- [Progettazione di tabelle][]
- [Scegliere una chiave di distribuzione hash per la tabella][]
- [Statistiche per migliorare le prestazioni][]

## Passaggi successivi
Per alcune indicazioni sulla creazione della soluzione SQL Data Warehouse, vedere l'articolo di [panoramica sullo sviluppo][].

<!--Image references-->

<!--Article references-->

[Prestazioni e scalabilità]: sql-data-warehouse-performance-scale.md
[Modello di concorrenza]: sql-data-warehouse-develop-concurrency.md
[Progettazione di tabelle]: sql-data-warehouse-develop-table-design.md
[Scegliere una chiave di distribuzione hash per la tabella]: sql-data-warehouse-develop-hash-distribution-key
[Statistiche per migliorare le prestazioni]: sql-data-warehouse-develop-statistics.md
[panoramica sullo sviluppo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other web references-->

<!---HONumber=Sept15_HO4-->