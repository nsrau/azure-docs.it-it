---
title: Migliorare le prestazioni dell&quot;indice columnstore in Azure SQL | Documentazione Microsoft
description: Ridurre i requisiti di memoria o aumentare la memoria disponibile per accrescere al massimo il numero di righe che un indice columnstore comprime in ogni gruppo di righe.
services: sql-data-warehouse
documentationcenter: NA
author: shivaniguptamsft
manager: jhubbard
editor: 
ms.assetid: ef170f39-ae24-4b04-af76-53bb4c4d16d3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 11/18/2016
ms.author: shigu;barbkess
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 8d189256ed4c876859203406cda95ce0be36c96c
ms.lasthandoff: 03/29/2017


---

# <a name="memory-optimizations-for-columnstore-compression"></a>Ottimizzazioni di memoria per la compressione columnstore

Ridurre i requisiti di memoria o aumentare la memoria disponibile per accrescere al massimo il numero di righe che un indice columnstore comprime in ogni gruppo di righe.  Usare questi metodi per migliorare il tasso di compressione e le prestazioni delle query per gli indici columnstore.

## <a name="why-the-rowgroup-size-matters"></a>Perché sono importanti le dimensioni del gruppo di righe
Poiché un indice columnstore analizza una tabella eseguendo la scansione di segmenti di colonna di singoli gruppi di righe, accrescendo al massimo il numero di righe in ogni gruppo di righe le prestazioni delle query migliorano. Quando i gruppi di righe hanno un numero elevato di righe, la compressione dei dati migliora, il che significa meno dati da leggere dal disco.

Per altre informazioni sui gruppi di righe, vedere [Descrizione degli indici columnstore](https://msdn.microsoft.com/library/gg492088.aspx).

## <a name="target-size-for-rowgroups"></a>Dimensioni di destinazione per i gruppi di righe
Per ottimizzare le prestazioni delle query, l'obiettivo è accrescere al massimo il numero di righe per ogni gruppo di righe in un indice columnstore. Un gruppo di righe può avere un massimo di 1.048.576 righe. È accettabile non avere il numero massimo di righe per gruppo di righe. Gli indici columnstore ottengono buone prestazioni quando i gruppi di righe hanno almeno 100.000 righe.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>I gruppi di righe possono essere tagliati durante la compressione

Durante un caricamento bulk o la ricompilazione di un indice columnstore, talvolta non è disponibile memoria sufficiente per comprimere tutte le righe per ogni gruppo di righe. Quando la memoria disponibile è scarsa, gli indici columnstore riducono le dimensioni del gruppo di righe in modo da consentire la compressione nel columnstore.

Quando la memoria è insufficiente per la compressione di almeno 10.000 righe in ogni gruppo di righe, SQL Data Warehouse genera un errore.

Per altre informazioni sul caricamento bulk, vedere [Caricamento bulk in un indice columnstore cluster](https://msdn.microsoft.com/en-us/library/dn935008.aspx#Bulk load into a clustered columnstore index).

## <a name="how-to-estimate-memory-requirements"></a>Come stimare i requisiti di memoria

<!--
To view an estimate of the memory requirements to compress a rowgroup of maximum size into a columnstore index, download and run the view [dbo.vCS_mon_mem_grant](). This view shows the size of the memory grant that a rowgroup requires for compression in to the columnstore.
-->

La memoria massima necessaria per comprimere un gruppo di righe è circa

- 72 MB +
- \#righe \* \#colonne \* 8 byte +
- \#righe \* \#colonne stringa breve \* 32 byte +
- \#colonne stringa lunga \* 16 MB per il dizionario di compressione

dove le colonne stringa breve usano tipi di dati stringa < = 32 byte e le colonne stringa lunga usano tipi di dati stringa > 32 byte.

Le stringhe lunghe vengono compresse con un metodo di compressione progettato per la compressione del testo. Questo metodo di compressione usa un *dizionario* per archiviare i modelli di testo. La dimensione massima di un oggetto dictionary è 16 MB. Esiste un solo dizionario per ogni colonna stringa lunga nel gruppo di righe.

Per un'analisi approfondita dei requisiti di memoria columnstore, vedere il video [Azure SQL Data Warehouse scaling: configuration and guidance](https://myignite.microsoft.com/videos/14822) (Scalabilità di Azure SQL Data Warehouse: configurazione e linee guida).

## <a name="ways-to-reduce-memory-requirements"></a>Modi per ridurre i requisiti di memoria

Usare le tecniche seguenti per ridurre i requisiti di memoria per la compressione dei gruppi di righe in indici columnstore.

### <a name="use-fewer-columns"></a>Usare meno colonne
Se possibile, progettare la tabella con meno colonne. Quando un gruppo di righe viene compresso nel columnstore, l'indice columnstore comprime ogni segmento di colonna separatamente. Pertanto i requisiti di memoria per comprimere un gruppo di righe aumentano con l'aumentare del numero di colonne.


### <a name="use-fewer-string-columns"></a>Usare meno colonne di stringhe
Le colonne di dati di tipo stringa richiedono una quantità di memoria maggiore rispetto ai tipi di dati numerici. Per ridurre i requisiti di memoria, prendere in considerazione la rimozione delle colonne di tipo stringa dalle tabelle di dati e il loro inserimento in tabelle di dimensioni minori.

Requisiti di memoria aggiuntivi per la compressione di stringhe:

- I tipi di dati stringa fino a 32 caratteri possono richiedere 32 byte aggiuntivi per valore.
- I tipi di dati stringa con più di 32 caratteri vengono compressi mediante metodi di dizionario.  Ogni colonna del gruppo di righe può richiedere fino a 16 MB aggiuntivi per creare il dizionario.

### <a name="avoid-over-partitioning"></a>Evitare il partizionamento eccessivo

Gli indici columnstore creano uno o più gruppi di righe per partizione. In SQL Data Warehouse il numero di partizioni aumenta rapidamente perché i dati vengono distribuiti e ogni distribuzione è partizionata. Se la tabella ha troppe partizioni, potrebbero esserci abbastanza righe per riempire i gruppi di righe. La mancanza di righe non crea richiesta di memoria durante la compressione, ma alcuni gruppi di righe soffriranno di scarse prestazioni delle query columnstore.

Un altro motivo per evitare l'eccessivo partizionamento è che il caricamento di righe in un indice columnstore in una tabella partizionata comporta un sovraccarico della memoria. Durante il caricamento molte partizioni potrebbero ricevere le righe in ingresso, che vengono mantenute in memoria finché ogni partizione dispone di un numero di righe sufficiente da comprimere. Con un numero eccessivo di partizioni vengono create richieste di memoria aggiuntive.

### <a name="simplify-the-load-query"></a>Semplificare la query di caricamento

Il database condivide la concessione di memoria per una query tra tutti gli operatori della query. Quando una query di caricamento contiene ordinamenti complessi e join, la memoria disponibile per la compressione è ridotta.

Progettare la query di caricamento concentrandosi solo sul caricamento. Se è necessario eseguire trasformazioni sui dati, eseguirle separatamente dalla query di caricamento. Ad esempio, collocare temporaneamente i dati in una tabella heap, eseguire le trasformazioni e quindi caricare la tabella di gestione temporanea nell'indice columnstore. È possibile anche caricare prima i dati e poi usare il sistema MPP per trasformarli.

### <a name="adjust-maxdop"></a>Regolare MAXDOP

Ogni distribuzione comprime i gruppi di righe nel columnstore in parallelo quando c'è più di un core CPU disponibile per distribuzione. Il parallelismo richiede risorse di memoria aggiuntive che possono portare a richieste di memoria pesanti e al taglio del gruppo di righe.

Per ridurre le richieste di memoria, è possibile usare l'hint di query MAXDOP per forzare l'esecuzione seriale dell'operazione di caricamento in ogni distribuzione.

```
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQUota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Modi per allocare altra memoria

La dimensione delle DWU e la classe della risorsa utente insieme determinano la quantità di memoria disponibile per una query dell'utente. Per aumentare la concessione di memoria per una query di caricamento, è possibile aumentare il numero di DWU o aumentare la classe risorsa.

- Per aumentare le DWU, vedere [Ridimensionare le prestazioni](sql-data-warehouse-manage-compute-overview.md#scale-compute)
- Per cambiare la classe risorsa per una query, vedere [Esempio di modifica della classe di risorse di un utente](sql-data-warehouse-develop-concurrency.md#change-a-user-resource-class-example).

Ad esempio, sulla DWU 100 un utente nella classe risorsa smallrc può usare 100 MB di memoria per ogni distribuzione. Per informazioni dettagliate, vedere [Gestione della concorrenza e del carico di lavoro in SQL Data Warehouse](sql-data-warehouse-develop-concurrency.md).

Si supponga di determinare la necessità di 700 MB di memoria per ottenere le dimensioni di un gruppo di righe di alta qualità. Questi esempi illustrano come eseguire la query di caricamento con memoria sufficiente.

- Usando la DWU 1000 e mediumrc, la concessione di memoria è di 800 MB
- Usando la DWU 600 e largerc, la concessione di memoria è di 800 MB.


## <a name="next-steps"></a>Passaggi successivi

Per trovare altri modi con cui migliorare le prestazioni in SQL Data Warehouse, vedere la sezione [Panoramica](sql-data-warehouse-overview-manage-user-queries.md) relativa alle prestazioni.

<!--Image references-->

<!--Article references-->


<!--MSDN references-->

<!--Other Web references-->

