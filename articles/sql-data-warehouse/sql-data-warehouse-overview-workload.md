<properties
   pageTitle="Carico di lavoro del data warehouse"
   description="L'elasticità di SQL Data Warehouse consente di aumentare, ridurre o sospendere la potenza di calcolo usando una scala scorrevole di unità data warehouse (DWU). Questo articolo illustra le metriche del data warehouse e come sono correlate alle DWU."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/23/2015"
   ms.author="barbkess;JRJ@BigBangData.co.uk"/>

# Carico di lavoro del data warehouse
Per carico di lavoro del data warehouse si intendono tutte le operazioni che passano attraverso un data warehouse. Tale carico di lavoro include pertanto l'intero processo di caricamento dei dati nel data warehouse, l'esecuzione dell'analisi e la creazione dei report a esso relativi, la gestione dei dati in esso contenuti e l'esportazione dei dati da esso. La profondità e l'estensione di questi componenti sono spesso proporzionali al livello di maturità del data warehouse.


## Per gli utenti non esperti di data warehouse
Un data warehouse è una raccolta di dati caricata da una o più origini dati e usata per eseguire attività di business intelligence, quali la creazione di report e l'analisi dei dati.

I data warehouse sono caratterizzati da query che analizzano quantità più elevate di righe o grandi intervalli di dati e possono restituire risultati di dimensioni relativamente importanti ai fini dell'analisi e della creazione di report. I data warehouse sono anche caratterizzati da caricamenti di dati relativamente estesi anziché da inserimenti, aggiornamenti o eliminazioni a livello di transazione di piccole dimensioni.

- Un data warehouse funziona al meglio quando i dati sono archiviati in maniera da ottimizzare le query che devono analizzare grandi quantità di righe o grandi intervalli di dati. Questo tipo di analisi opera in modo ottimale quando i dati vengono archiviati e sottoposti a ricerche in base alle colonne invece che alle righe. 

>[AZURE.NOTE]L'indice columnstore in memoria, che si basa sull'archiviazione in colonne, offre una compressione fino a cinque volte superiore e prestazioni delle query anche dieci volte più elevate rispetto agli alberi binari tradizionali per le query di reporting e analisi. Gli indici columnstore vengono considerati uno standard per l'archiviazione e l'analisi di grandi quantità di dati in un data warehouse.

- Un data warehouse ha requisiti diversi rispetto a un sistema ottimizzato per l'elaborazione di transazioni online (OLTP). Il sistema OLTP prevede numerose operazioni di inserimento, aggiornamento ed eliminazione. Queste operazioni eseguono la ricerca fino a righe specifiche nella tabella. Le ricerche nelle tabelle vengono eseguite in modo ottimale quando i dati sono archiviati riga per riga. I dati possono essere ordinati e ricercati rapidamente secondo un approccio di tipo "divide et impera", noto anche come ricerca nell'albero binario o albero B.


## Caricamento dei dati
Il caricamento dei dati rappresenta una parte considerevole del carico di lavoro del data warehouse. Le aziende in genere dispongono di un sistema OLTP occupato che tiene traccia delle modifiche nel corso della giornata quando i clienti generano transazioni aziendali. Periodicamente, spesso di notte durante una finestra di manutenzione, le transazioni vengono spostate o copiate nel data warehouse. Quando i dati si trovano nel data warehouse, gli analisti possono eseguire l'analisi e prendere decisioni aziendali basandosi su di essi.

- Il processo di caricamento tradizionalmente viene indicato con l'acronimo ETL (Extract, Transform, Load), ovvero estrazione, trasformazione e caricamento. I dati solitamente devono essere trasformati per diventare coerenti con gli altri dati contenuti nel data warehouse. In passato le aziende usavano server ETL dedicati per eseguire le trasformazioni. Ora, grazie all'elevata velocità dell'elaborazione parallela massiva, è possibile prima caricare i dati in SQL Data Warehouse e quindi effettuare le trasformazioni. Tale processo è detto ELT (Extract, Load, Transform), ovvero estrazione, caricamento e trasformazione, e sta diventando un nuovo standard per il carico di lavoro del data warehouse.

> [NOTA PER AZURE] Con SQL Server CTP2, è ora possibile eseguire l'analisi in tempo reale su una tabella OLTP. Il data warehouse sarà comunque necessario per archiviare e analizzare i dati, ma l'analisi sarà possibile in tempo reale.
 
### Query di reporting e analisi
Le query di reporting e analisi vengono spesso classificate come di piccole, medie e grandi dimensioni in base al numero di criteri, ma in genere si basano sul tempo. Nella maggior parte dei data warehouse è presente un carico di lavoro misto costituito da una combinazione di query a esecuzione rapida e query a esecuzione prolungata. In ogni caso è importante determinare tale combinazione e la relativa frequenza (oraria, giornaliera, a fine mese, a fine trimestre e così via). È fondamentale comprendere che il carico di lavoro con query miste, insieme alla concorrenza, porta a una pianificazione appropriata della capacità per un data warehouse.

- La pianificazione della capacità può essere un'attività complessa per un carico di lavoro con query miste, soprattutto se è necessario un lungo lead time per aggiungere capacità al data warehouse. Con SQL Data Warehouse la pianificazione della capacità non è più indispensabile, in quanto è possibile aumentare e ridurre la capacità di calcolo in qualsiasi momento e tale capacità e quella di archiviazione vengono dimensionate in modo indipendente.

### Gestione dati
La gestione dati è importante, soprattutto quando si sa che a breve lo spazio su disco potrebbe esaurirsi. I data warehouse in genere suddividono i dati in intervalli significativi, che vengono archiviati come partizioni in una tabella. Tutti i prodotti basati su SQL Server consentono di spostare le partizioni all'interno e all'esterno della tabella. Tale spostamento delle partizioni consente di spostare i dati meno recenti in aree di archiviazione meno costose e di mantenere disponibili i dati più recenti nello spazio di archiviazione online.

- Gli indici columnstore supportano le tabelle partizionate. Per questi indici, le tabelle partizionate vengono usate per la gestione e l'archiviazione dei dati. Per le tabelle archiviate riga per riga, le partizioni hanno un peso maggiore sulle prestazioni delle query.  
 
- PolyBase svolge un ruolo importante nella gestione dati. Usando PolyBase, si ha la possibilità di archiviare i dati meno recenti in Hadoop o nell'archivio BLOB di Azure. Sono così disponibili numerose opzioni, dal momento che i dati sono ancora online. Il recupero dei dati da Hadoop potrebbe richiedere più tempo, ma il costo di archiviazione potrebbe rivelarsi più vantaggioso.
 
### Esportazione dei dati
Un modo per rendere disponibili i dati per i report e l'analisi consiste nell'inviarli dal data warehouse ai server dedicati all'esecuzione di report e analisi. Tali server sono denominati data mart. Ad esempio, è possibile pre-elaborare i dati dei report e quindi esportarli dal data warehouse in numerosi server di tutto il mondo per renderli ampiamente disponibili per clienti e analisti.

- Per generare i report, ogni notte si potrebbe inserire uno snapshot dei dati giornalieri in server di report di sola lettura. Questo garantisce ai clienti una larghezza di banda superiore e diminuisce la richiesta di risorse di calcolo nel data warehouse. Dal punto di vista della sicurezza, i data mart consentono di ridurre il numero degli utenti che hanno accesso al data warehouse.
- Per l'analisi, è possibile creare un apposito cubo nel data warehouse ed eseguire l'analisi su quest'ultimo oppure pre-elaborare i dati ed esportarli nel server di analisi in modo che possano essere analizzati ulteriormente. 

## Passaggi successivi
Per iniziare a sviluppare il data warehouse, vedere la [panoramica sullo sviluppo][].

<!--Image references-->

<!--Article references-->
[panoramica sullo sviluppo]: sql-data-warehouse-overview-development.md

<!--MSDN references-->

<!--Other web references-->

<!---HONumber=July15_HO3-->