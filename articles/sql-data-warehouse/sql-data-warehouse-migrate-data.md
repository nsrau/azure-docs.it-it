<properties
   pageTitle="Eseguire la migrazione dei dati in SQL Data Warehouse | Microsoft Azure"
   description="Suggerimenti per la migrazione dei dati in Azure SQL Data Warehouse per lo sviluppo di soluzioni."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Eseguire la migrazione dei dati
L'obiettivo primario durante la migrazione dei dati consiste nel popolare il database SQLDW. Questo processo può essere eseguito in vari modi, ad esempio usando ADF Copy, SSIS e bcp. Tuttavia, con l'aumento della quantità di dati, prendere in considerazione la possibilità di suddividere il processo di migrazione in passaggi. Ciò consente di ottimizzare ogni passaggio sia per le prestazioni che per la resilienza in modo da garantire una migrazione uniforme dei dati.

Questo articolo descrive in primo luogo gli scenari di migrazione semplici di ADF Copy, SSIS e bcp, per poi analizzare come ottimizzare la migrazione.

## ADF Copy
[ADF Copy][] fa parte di [Data factory di Azure][]. È possibile usare ADF Copy per esportare i dati in file flat che si trovano in un'archiviazione locale, in file flat remoti contenuti nell'archiviazione BLOB di Azure o direttamente in SQL Data Warehouse.

Se i dati sono contenuti in file flat, è necessario trasferirli nei BLOB di archiviazione di Azure prima di avviare un caricamento in SQL Data Warehouse. Dopo il trasferimento dei dati nell'archiviazione BLOB di Azure, è possibile scegliere di usare [ADF Copy][] di nuovo per effettuare il push dei dati in SQL Data Warehouse.

Anche PolyBase rappresenta un'opzione a prestazioni molto elevate per il caricamento dei dati. Questo non significa però che debbano essere usati due strumenti anziché uno. Se si vuole ottenere prestazioni ottimali, usare PolyBase. Se invece si vuole usare un unico strumento (e il volume di dati non è elevato), ADF è la soluzione ideale.

> [AZURE.NOTE]PolyBase richiede file di dati in formato UTF-8. Questa è la codifica predefinita di ADF Copy, pertanto non è necessario apportare modifiche. Questo è solo un promemoria che indica di non modificare il comportamento predefinito di ADF Copy.

Visitare l'articolo seguente per alcuni [esempi di ADF Copy].

## Integration Services ##
Integration Services (SSIS) è uno strumento sofisticato e flessibile di Extract Transform and Load (ETL) che supporta flussi di lavoro complessi, la trasformazione dei dati e diverse opzioni di caricamento dei dati. Usare SSIS per trasferire semplicemente dati in Azure o come parte di una migrazione più ampia.

> [AZURE.NOTE]SSIS può esportare in UTF-8 senza byte order mark nel file. Per configurare questa funzionalità, è necessario prima usare il componente colonna derivata per convertire i dati di tipo carattere nel flusso di dati per usare la tabella codici 65001 UTF-8. Dopo la conversione delle colonne, scrivere i dati nell'adattatore di destinazione di file flat verificando che sia stata selezionata la tabella codici 65001 per il file.

SSIS si connette a SQL Data Warehouse nello stesso modo in cui si connetterebbe a una distribuzione di SQL Server. La connessione tuttavia dovrà usare una gestione connessioni ADO.NET. È anche necessario configurare l'impostazione per "utilizzare l'inserimento di massa quando disponibile" per ottimizzare la velocità effettiva. Consultare l'articolo relativo all'[adattatore di destinazione ADO.NET][] per ulteriori informazioni su questa proprietà

> [AZURE.NOTE]La connessione ad Azure SQL Data Warehouse mediante OLEDB non è supportata.

Esiste inoltre sempre la possibilità che si verifichi l'errore di un pacchetto per problemi di rete o limitazione delle richieste. Progettare i pacchetti in modo che sia possibile ripartire dal punto in cui si è verificato l'errore, senza dover ripetere il lavoro completato prima dell'errore.

Per altre informazioni, vedere la [documentazione relativa a SSIS][].

## bcp
L'utilità della riga di comando bcp è progettata per l'importazione e l'esportazione di dati di file flat. Durante l'esportazione di dati possono essere eseguite trasformazioni. Per eseguire trasformazioni semplici, usare una query per selezionare e trasformare i dati. Dopo l'esportazione, i file flat possono quindi essere caricati direttamente nel database di SQL Data Warehouse di destinazione.

> [AZURE.NOTE]Spesso è una buona idea incapsulare le trasformazioni usate durante l'esportazione di dati in una vista nel sistema di origine. In questo modo viene mantenuta la logica e il processo è ripetibile.

I vantaggi derivanti dall'uso dell'utilità bcp sono i seguenti:

- Semplicità. I comandi di bcp sono semplici da creare ed eseguire.
- Possibilità di riavviare il processo di caricamento. Dopo l'esportazione, il caricamento può essere eseguito un numero illimitato di volte.

Le limitazioni dell'utilità bcp sono le seguenti:

- Funziona solo con file flat tabulari. Non funziona ad esempio con file XML o JSON.
- Non supporta l'esportazione in UTF-8. Questo può impedire l'uso di PolyBase sui dati esportati con bcp.
- Le funzionalità di trasformazione dei dati sono limitate alla sola fase di esportazione e sono semplici per natura.
- L'utilità bcp non è stata adattata per essere efficace durante il caricamento di dati su Internet. Qualsiasi instabilità della rete può causare un errore di caricamento.
- L'utilità bcp si basa sullo schema presente nel database di destinazione prima del caricamento.

Per altre informazioni, vedere l'articolo relativo all'[uso di bcp per caricare dati in SQL Data Warehouse][].

## Ottimizzazione della migrazione dei dati
Un processo di migrazione di dati SQLDW può essere suddiviso in modo efficace in tre passaggi discreti:

1. Esportazione dei dati di origine
2. Trasferimento dei dati in Azure
3. Caricamento nel database SQLDW di destinazione

È possibile ottimizzare singolarmente ogni passaggio per creare un processo di migrazione solido, nuovamente avviabile e resiliente che ottimizza le prestazioni a ogni passaggio.

## Ottimizzazione del caricamento dei dati
Esaminando per un momento queste operazioni in ordine inverso, il modo più rapido per caricare i dati è tramite PolyBase. L'ottimizzazione per un processo di caricamento PolyBase prevede prerequisiti per i passaggi precedenti, pertanto è consigliabile capire questo aspetto dall'inizio. Sono:

1. Codifica dei file di dati
2. Formato dei file di dati
3. Percorso dei file di dati

### Codifica
PolyBase richiede file di dati con la codifica UTF-8. Ciò significa che quando vengono esportati, i dati devono essere conformi a questo requisito. Se i dati contengono solo caratteri ASCII di base (non ASCII estesi), vengono mappati direttamente allo standard UTF-8 e non è necessario preoccuparsi della codifica. Se però i dati contengono caratteri speciali, ad esempio umlaut, accenti o simboli oppure supportano lingue non appartenenti all'alfabeto latino, sarà necessario verificare che i file di esportazione siano codificati correttamente nel formato UTF-8.

> [AZURE.NOTE]L'utilità bcp non supporta l'esportazione di dati in UTF-8. La soluzione migliore pertanto consiste nell'usare Integration Services o ADF Copy per l'esportazione di dati. È importante sottolineare che nel file di dati non è richiesto il carattere byte order mark (BOM) di UTF-8.

Tutti i file codificati con UTF-16 dovranno essere riscritti ***prima*** del trasferimento dei dati.

### Formato dei file di dati
PolyBase impone un carattere di terminazione di riga fisso \\n o una nuova riga. I file di dati devono essere conformi a questo standard. Non esistono restrizioni per i caratteri di terminazione di colonna o stringa.

Sarà necessario definire ogni colonna nel file come parte della tabella esterna in PolyBase. Assicurarsi che tutte le colonne esportate siano necessarie e che i tipi siano conformi agli standard richiesti.

Vedere l'articolo sulla [migrazione di uno schema] per informazioni su tipi di dati supportati.

### Percorso dei file di dati
SQL Data Warehouse usa PolyBase per caricare i dati esclusivamente dall'archiviazione BLOB di Azure. I dati pertanto devono essere stati prima trasferiti nell'archiviazione BLOB.

## Ottimizzazione del trasferimento dei dati
Una delle fasi più lente della migrazione dei dati è costituita dal trasferimento dei dati in Azure. Non solo la larghezza di banda di rete può costituire un problema, ma anche l'affidabilità della rete può gravemente compromettere lo stato di avanzamento. Per impostazione predefinita, la migrazione dei dati in Azure viene eseguita tramite Internet ed è pertanto probabile che si verifichino errori di trasferimento. Questi errori tuttavia possono richiedere che i dati vengano inviati di nuovo completamente o in parte.

Fortunatamente sono disponibili diverse opzioni per migliorare la velocità e la resilienza di questo processo:

### [ExpressRoute][]
È possibile provare a usare [ExpressRoute][] per velocizzare il trasferimento. [ExpressRoute][] consente di stabilire una connessione privata ad Azure in modo che la connessione non passi attraverso la rete Internet pubblica. Non si tratta di un passaggio obbligatorio. Determina tuttavia un miglioramento della velocità effettiva quando si effettua il push dei dati in Azure da una struttura locale o da una condivisione di percorso.

I vantaggi derivanti dall'uso di [ExpressRoute][] sono i seguenti:

1. Maggiore affidabilità
2. Maggiore velocità di rete
3. Minore latenza di rete
4. Maggiore sicurezza di rete

[ExpressRoute][] è utile per numerosi scenari e non solo per la migrazione.

Per altre informazioni e dati relativi ai prezzi, vedere la [documentazione relativa a ExpressRoute][].

### Servizio Importazione/Esportazione di Azure
Il servizio Importazione/Esportazione di Azure è un processo di trasferimento dati progettato per trasferimenti di un numero elevato (GB++) o ingente (TB++) di dati in Azure. Comporta la scrittura dei dati su dischi e l'invio in un data center di Azure. Il contenuto dei dischi verrà quindi caricato nei BLOB di Archiviazione di Azure per conto dell'utente.

Segue una panoramica del processo di importazione/esportazione:

1. Configurare un contenitore dell'archiviazione BLOB di Azure per ricevere i dati.
2. Esportare i dati nell'archiviazione locale.
2. Copiare i dati su unità disco rigido SATA II/III da 3,5 pollici usando lo [strumento Importazione/Esportazione di Azure].
3. Creare un processo di importazione usando il servizio Importazione/Esportazione di Azure fornendo i file journal generati dallo [strumento Importazione/Esportazione di Azure].
4. Inviare i dischi al data center di Azure prescelto.
5. I dati vengono trasferiti nel contenitore dell'archiviazione Blob di Azure.
6. Caricare i dati in SQLDW usando PolyBase.

### Utilità [AZCopy][]
L'utilità [AZCopy][] è uno strumento ideale per il trasferimento dei dati nei BLOB di Archiviazione di Azure. È progettato per il trasferimento di dati di piccole (MB++) e grandi dimensioni (GB+ +). [AZCopy] è stato progettato anche per garantire una velocità effettiva resiliente durante il trasferimento dei dati in Azure e pertanto rappresenta un'ottima scelta per il passaggio relativo al trasferimento dei dati. Dopo il trasferimento, è possibile caricare i dati con PolyBase in SQL Data Warehouse. È anche possibile incorporare AZCopy nei pacchetti SSIS usando un'attività "Execute Process".

Per usare AZCopy, sarà necessario prima scaricarlo e installarlo. Sono disponibili una [versione di produzione][] e una [versione di anteprima][].

Per caricare un file dal file system, è necessario un comando simile al seguente:

```
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt
```

Segue un riepilogo generale del processo:

1. Configurare un contenitore di BLOB di archiviazione di Azure per ricevere i dati.
2. Esportare i dati nell'archiviazione locale.
3. Copiare tramite AZCopy i dati nel contenitore dell'archiviazione BLOB di Azure.
4. Caricare i dati in SQL Data Warehouse usando PolyBase.

Documentazione completa disponibile: [AZCopy][].

## Ottimizzazione dell'esportazione dei dati
Oltre a garantire che l'esportazione sia conforme ai requisiti previsti da PolyBase, è anche possibile cercare di ottimizzare l'esportazione dei dati per migliorare ulteriormente il processo.

> [AZURE.NOTE]Poiché PolyBase richiede che i dati siano in formato UTF-8, è improbabile che venga usata l'utilità bcp per eseguire l'esportazione dei dati. L'utilità bcp non supporta l'output dei file di dati in UTF-8. ADF Copy o SSIS sono più adatti per l'esecuzione di questo tipo di esportazione di dati.

### Compressione dei dati
PolyBase è in grado di leggere i dati compressi in file gzip. Se è possibile comprimere i dati in file gzip, verrà ridotta la quantità di dati di cui viene effettuato il push in rete.

### File multipli
La suddivisione di tabelle di grandi dimensioni in più file non solo consente di migliorare la velocità di esportazione, ma favorisce anche la possibilità di riavviare i trasferimenti e semplifica la gestibilità complessiva dei dati dopo che sono stati trasferiti nell'archiviazione BLOB di Azure. Una delle numerose funzionalità interessanti di PolyBase è costituita dalla possibilità di leggere tutti i file all'interno di una cartella considerandola come una tabella. È pertanto opportuno isolare i file per ogni tabella nella relativa cartella.

PolyBase supporta anche una funzionalità nota come "attraversamento di cartelle ricorsivo". È possibile usare questa funzionalità per migliorare ulteriormente l'organizzazione dei dati esportati e ottimizzare la gestione dei dati.

Per altre informazioni sul caricamento dei dati con PolyBase, vedere l'articolo relativo all'[uso di PolyBase per caricare i dati in SQL Data Warehouse][].


## Passaggi successivi
Per altre informazioni sulla migrazione, vedere l'articolo relativo alla [migrazione di una soluzione in SQL Data Warehouse][]. Per altri suggerimenti sullo sviluppo, vedere la [panoramica dello sviluppo][].

<!--Image references-->

<!--Article references-->
[AZCopy]: ../storage/storage-use-azcopy.md
[ADF Copy]: ../data-factory/data-factory-copy-activity.md
[esempi di ADF Copy]: ../data-factory/data-factory-copy-activity-examples.md
[panoramica dello sviluppo]: sql-data-warehouse-develop-overview.md
[migrazione di una soluzione in SQL Data Warehouse]: sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[uso di bcp per caricare dati in SQL Data Warehouse]: sql-data-warehouse-load-with-bcp.md
[uso di PolyBase per caricare i dati in SQL Data Warehouse]: sql-data-warehouse-load-with-polybase.md


<!--MSDN references-->

<!--Other Web references-->
[Data factory di Azure]: http://azure.microsoft.com/services/data-factory/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[documentazione relativa a ExpressRoute]: http://azure.microsoft.com/documentation/services/expressroute/

[versione di produzione]: http://aka.ms/downloadazcopy/
[versione di anteprima]: http://aka.ms/downloadazcopypr/
[adattatore di destinazione ADO.NET]: https://msdn.microsoft.com/IT-IT/library/bb934041.aspx
[documentazione relativa a SSIS]: https://msdn.microsoft.com/IT-IT/library/ms141026.aspx

<!---HONumber=Sept15_HO4-->