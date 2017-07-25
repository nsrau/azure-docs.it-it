---
title: Eseguire la migrazione dei dati in SQL Data Warehouse | Microsoft Docs
description: Suggerimenti per la migrazione dei dati in Azure SQL Data Warehouse per lo sviluppo di soluzioni.
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: d78f954a-f54c-4aa4-9040-919bc6414887
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 06/29/2017
ms.author: joeyong;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 7835797bc353a64c934b362c31fdcd9b448aca34
ms.contentlocale: it-it
ms.lasthandoff: 03/17/2017


---
<a id="migrate-your-data" class="xliff"></a>
# Eseguire la migrazione dei dati
È possibile spostare dati da differenti origini a SQL Data Warehouse con diversi strumenti,  ad esempio usando ADF Copy, SSIS e bcp. Tuttavia, con l'aumento della quantità di dati, prendere in considerazione la possibilità di suddividere il processo di migrazione in passaggi. Ciò consente di ottimizzare ogni passaggio sia per le prestazioni che per la resilienza in modo da garantire una migrazione uniforme dei dati.

Questo articolo descrive in primo luogo i semplici scenari di migrazione di ADF Copy, SSIS e bcp, per poi analizzare come ottimizzare la migrazione.

<a id="azure-data-factory-adf-copy" class="xliff"></a>
## ADF Copy
[ADF Copy][ADF Copy] fa parte di [Azure Data Factory][Azure Data Factory]. È possibile usare ADF Copy per esportare i dati in file flat che si trovano in un'archiviazione locale, in file flat remoti contenuti nell'archiviazione BLOB di Azure o direttamente in SQL Data Warehouse.

Se i dati sono contenuti in file flat, è necessario trasferirli nel BLOB di archiviazione di Azure prima di avviare un caricamento in SQL Data Warehouse. Dopo il trasferimento dei dati nell'archiviazione BLOB di Azure, è possibile scegliere di usare nuovamente [ADF Copy][ADF Copy] per effettuare il push dei dati in SQL Data Warehouse.

Anche PolyBase rappresenta un'opzione a prestazioni elevate per il caricamento dei dati. Questo non significa però che debbano essere usati due strumenti anziché uno. Se si vuole ottenere prestazioni ottimali, usare PolyBase. Se invece si vuole usare un unico strumento (e il volume di dati non è elevato), ADF è la soluzione ideale.


> 
> 

Leggere l'articolo seguente per alcuni [esempi di ADF][ADF samples].

<a id="integration-services" class="xliff"></a>
## Integration Services
Integration Services (SSIS) è uno strumento sofisticato e flessibile di Extract Transform and Load (ETL) che supporta flussi di lavoro complessi, la trasformazione dei dati e diverse opzioni di caricamento dei dati. Usare SSIS per trasferire semplicemente dati in Azure o come parte di una migrazione più ampia.

> [!NOTE]
> SSIS può esportare in UTF-8 senza byte order mark nel file. Per configurare questa funzionalità, è necessario prima usare il componente colonna derivata per convertire i dati di tipo carattere nel flusso di dati per usare la tabella codici 65001 UTF-8. Dopo la conversione delle colonne, scrivere i dati nell'adattatore di destinazione di file flat verificando che sia stata selezionata la tabella codici 65001 per il file.
> 
> 

SSIS si connette a SQL Data Warehouse nello stesso modo in cui si connetterebbe a una distribuzione di SQL Server. La connessione tuttavia dovrà usare una gestione connessioni ADO.NET. È anche necessario configurare l'impostazione per "utilizzare l'inserimento di massa quando disponibile" per ottimizzare la velocità effettiva. Fare riferimento all'articolo relativo all'[adattatore di destinazione ADO.NET][ADO.NET destination adapter] per altre informazioni su questa proprietà

> [!NOTE]
> La connessione ad Azure SQL Data Warehouse mediante OLEDB non è supportata.
> 
> 

Esiste inoltre sempre la possibilità che si verifichi l'errore di un pacchetto per problemi di rete o limitazione delle richieste. Progettare i pacchetti in modo che sia possibile ripartire dal punto in cui si è verificato l'errore, senza dover ripetere il lavoro completato prima dell'errore.

Per altre informazioni, vedere la [documentazione relativa a SSIS][SSIS documentation].

<a id="bcp" class="xliff"></a>
## bcp
L'utilità della riga di comando bcp è progettata per l'importazione e l'esportazione di dati di file flat. Durante l'esportazione di dati possono essere eseguite trasformazioni. Per eseguire trasformazioni semplici, usare una query per selezionare e trasformare i dati. Dopo l'esportazione, i file flat possono quindi essere caricati direttamente nel database di SQL Data Warehouse di destinazione.

> [!NOTE]
> Spesso è una buona idea incapsulare le trasformazioni usate durante l'esportazione di dati in una vista nel sistema di origine. In questo modo viene mantenuta la logica e il processo è ripetibile.
> 
> 

I vantaggi derivanti dall'uso dell'utilità bcp sono i seguenti:

* Semplicità. I comandi di bcp sono semplici da creare ed eseguire.
* Possibilità di riavviare il processo di caricamento. Dopo l'esportazione, il caricamento può essere eseguito un numero illimitato di volte.

Le limitazioni dell'utilità bcp sono le seguenti:

* Funziona solo con file flat tabulari. Non funziona ad esempio con file XML o JSON.
* Le funzionalità di trasformazione dei dati sono limitate alla sola fase di esportazione e sono semplici per natura.
* L'utilità bcp non è stata adattata per essere efficace durante il caricamento di dati su Internet. Qualsiasi instabilità della rete può causare un errore di caricamento.
* L'utilità bcp si basa sullo schema presente nel database di destinazione prima del caricamento.

Per altre informazioni, vedere l'articolo relativo all'[uso di bcp per caricare dati in SQL Data Warehouse][Use bcp to load data into SQL Data Warehouse].

<a id="optimizing-data-migration" class="xliff"></a>
## Ottimizzazione della migrazione dei dati
Un processo di migrazione di dati SQLDW può essere suddiviso in modo efficace in tre passaggi discreti:

1. Esportazione dei dati di origine
2. Trasferimento dei dati in Azure
3. Caricamento nel database SQLDW di destinazione

È possibile ottimizzare singolarmente ogni passaggio per creare un processo di migrazione solido, nuovamente avviabile e resiliente che ottimizza le prestazioni a ogni passaggio.

<a id="optimizing-data-load" class="xliff"></a>
## Ottimizzazione del caricamento dei dati
Esaminando per un momento queste operazioni in ordine inverso, il modo più rapido per caricare i dati è tramite PolyBase. L'ottimizzazione per un processo di caricamento PolyBase prevede prerequisiti per i passaggi precedenti, pertanto è consigliabile capire questo aspetto dall'inizio. Vale a dire:

1. Codifica dei file di dati
2. Formato dei file di dati
3. Percorso dei file di dati

<a id="encoding" class="xliff"></a>
### Codifica
PolyBase richiede file di dati con codifica UTF-8 o UTF-16FE. 



<a id="format-of-data-files" class="xliff"></a>
### Formato dei file di dati
PolyBase impone un carattere di terminazione di riga fisso \n o una nuova riga. I file di dati devono essere conformi a questo standard. Non esistono restrizioni per i caratteri di terminazione di colonna o stringa.

Sarà necessario definire ogni colonna nel file come parte della tabella esterna in PolyBase. Assicurarsi che tutte le colonne esportate siano necessarie e che i tipi siano conformi agli standard richiesti.

Vedere l'articolo sulla [migrazione di uno schema] per informazioni su tipi di dati supportati.

<a id="location-of-data-files" class="xliff"></a>
### Percorso dei file di dati
SQL Data Warehouse usa PolyBase per caricare i dati esclusivamente dall'archiviazione BLOB di Azure. I dati pertanto devono essere stati prima trasferiti nell'archiviazione BLOB.

<a id="optimizing-data-transfer" class="xliff"></a>
## Ottimizzazione del trasferimento dei dati
Una delle fasi più lente della migrazione dei dati è costituita dal trasferimento dei dati in Azure. Non solo la larghezza di banda di rete può costituire un problema, ma anche l'affidabilità della rete può gravemente compromettere lo stato di avanzamento. Per impostazione predefinita, la migrazione dei dati in Azure viene eseguita tramite Internet ed è pertanto probabile che si verifichino errori di trasferimento. Questi errori tuttavia possono richiedere che i dati vengano inviati di nuovo completamente o in parte.

Fortunatamente sono disponibili diverse opzioni per migliorare la velocità e la resilienza di questo processo:

<a id="expressrouteexpressroute" class="xliff"></a>
### [ExpressRoute][ExpressRoute]
È possibile provare a usare [ExpressRoute][ExpressRoute] per velocizzare il trasferimento. [ExpressRoute][ExpressRoute] consente di stabilire una connessione privata ad Azure in modo che la connessione non passi attraverso la rete Internet pubblica. Non si tratta di un passaggio obbligatorio. Determina tuttavia un miglioramento della velocità effettiva quando si effettua il push dei dati in Azure da una struttura locale o da una condivisione di percorso.

I vantaggi derivanti dall'uso di [ExpressRoute][ExpressRoute] sono i seguenti:

1. Maggiore affidabilità
2. Maggiore velocità di rete
3. Minore latenza di rete
4. Maggiore sicurezza di rete

[ExpressRoute][ExpressRoute] è utile per numerosi scenari e non solo per la migrazione.

Per altre informazioni e i dati relativi ai prezzi, vedere la [documentazione relativa a ExpressRoute][ExpressRoute documentation].

<a id="azure-import-and-export-service" class="xliff"></a>
### Servizio Importazione/Esportazione di Azure
Il servizio Importazione/Esportazione di Azure è un processo di trasferimento dati progettato per trasferimenti di un numero elevato (GB++) o ingente (TB++) di dati in Azure. Comporta la scrittura dei dati su dischi e l'invio in un data center di Azure. Il contenuto dei dischi verrà quindi caricato nei BLOB di archiviazione di Azure per conto dell'utente.

Segue una panoramica del processo di importazione/esportazione:

1. Configurare un contenitore dell'archiviazione BLOB di Azure per ricevere i dati.
2. Esportare i dati nell'archiviazione locale.
3. Copiare i dati su unità disco rigido SATA II/III da 3,5 pollici usando lo [strumento Importazione/Esportazione di Azure].
4. Creare un processo di importazione usando il servizio Importazione/Esportazione di Azure fornendo i file journal generati dallo [strumento Importazione/Esportazione di Azure].
5. Inviare i dischi al data center di Azure prescelto.
6. I dati vengono trasferiti nel contenitore dell'archiviazione Blob di Azure.
7. Caricare i dati in SQLDW usando PolyBase.

<a id="azcopyazcopy-utility" class="xliff"></a>
### Utilità [AZCopy][AZCopy]
L'utilità [AZCopy][AZCopy] è uno strumento ideale per il trasferimento dei dati nei BLOB di Archiviazione di Azure. È progettato per il trasferimento di dati di piccole (MB++) e grandi dimensioni (GB+ +). [AZCopy] è stato progettato anche per garantire una velocità effettiva resiliente durante il trasferimento dei dati in Azure e pertanto rappresenta un'ottima scelta per il passaggio relativo al trasferimento dei dati. Dopo il trasferimento, è possibile caricare i dati con PolyBase in SQL Data Warehouse. È anche possibile incorporare AZCopy nei pacchetti SSIS usando un'attività "Execute Process".

Per usare AZCopy, sarà necessario prima scaricarlo e installarlo. Sono disponibili una [versione di produzione][production version] e una [versione di anteprima][preview version].

Per caricare un file dal file system, è necessario un comando simile al seguente:

```
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt
```

Segue un riepilogo generale del processo:

1. Configurare un contenitore di BLOB di archiviazione di Azure per ricevere i dati.
2. Esportare i dati nell'archiviazione locale.
3. Copiare tramite AZCopy i dati nel contenitore dell'archiviazione BLOB di Azure.
4. Caricare i dati in SQL Data Warehouse usando PolyBase.

Documentazione completa disponibile: [AZCopy][AZCopy].

<a id="optimizing-data-export" class="xliff"></a>
## Ottimizzazione dell'esportazione dei dati
Oltre a garantire che l'esportazione sia conforme ai requisiti previsti da PolyBase, è anche possibile cercare di ottimizzare l'esportazione dei dati per migliorare ulteriormente il processo.



<a id="data-compression" class="xliff"></a>
### Compressione dei dati
PolyBase è in grado di leggere i dati compressi in file gzip. Se è possibile comprimere i dati in file gzip, verrà ridotta la quantità di dati di cui viene effettuato il push in rete.

<a id="multiple-files" class="xliff"></a>
### File multipli
La suddivisione di tabelle di grandi dimensioni in più file non solo consente di migliorare la velocità di esportazione, ma favorisce anche la possibilità di riavviare i trasferimenti e semplifica la gestibilità complessiva dei dati dopo che sono stati trasferiti nell'archiviazione BLOB di Azure. Una delle numerose funzionalità interessanti di PolyBase è costituita dalla possibilità di leggere tutti i file all'interno di una cartella considerandola come una tabella. È pertanto opportuno isolare i file per ogni tabella nella relativa cartella.

PolyBase supporta anche una funzionalità nota come "attraversamento di cartelle ricorsivo". È possibile usare questa funzionalità per migliorare ulteriormente l'organizzazione dei dati esportati e ottimizzare la gestione dei dati.

Per altre informazioni sul caricamento dei dati con PolyBase, vedere [Caricare dati con PolyBase in SQL Data Warehouse][Use PolyBase to load data into SQL Data Warehouse].

<a id="next-steps" class="xliff"></a>
## Passaggi successivi
Per altre informazioni sulla migrazione, vedere [Eseguire la migrazione della soluzione in SQL Data Warehouse][Migrate your solution to SQL Data Warehouse].
Per altri suggerimenti sullo sviluppo, vedere la [panoramica dello sviluppo][development overview].

<!--Image references-->

<!--Article references-->
[AZCopy]: ../storage/storage-use-azcopy.md
[ADF Copy]: ../data-factory/data-factory-data-movement-activities.md 
[ADF samples]: ../data-factory/data-factory-samples.md
[ADF Copy examples]: ../data-factory/data-factory-copy-activity-tutorial-using-visual-studio.md
[development overview]: sql-data-warehouse-overview-develop.md
[Migrate your solution to SQL Data Warehouse]: sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[Use bcp to load data into SQL Data Warehouse]: sql-data-warehouse-load-with-bcp.md
[Use PolyBase to load data into SQL Data Warehouse]: sql-data-warehouse-get-started-load-with-polybase.md


<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory]: http://azure.microsoft.com/services/data-factory/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[ExpressRoute documentation]: http://azure.microsoft.com/documentation/services/expressroute/

[production version]: http://aka.ms/downloadazcopy/
[preview version]: http://aka.ms/downloadazcopypr/
[ADO.NET destination adapter]: https://msdn.microsoft.com/library/bb934041.aspx
[SSIS documentation]: https://msdn.microsoft.com/library/ms141026.aspx

