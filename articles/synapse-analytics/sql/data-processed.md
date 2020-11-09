---
title: Dati elaborati tramite un pool SQL senza server
description: Questo documento descrive il modo in cui viene calcolata la quantità elaborata dai dati quando si esegue una query sui dati in data Lake.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/05/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: a108e5fdd30c21cdb7771e3f683dad22773653a4
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381202"
---
# <a name="data-processed-by-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Dati elaborati usando un pool SQL senza server in Azure sinapsi Analytics

I *dati elaborati* corrispondono alla quantità di dati archiviata temporaneamente dal sistema durante l'esecuzione di una query. I dati elaborati sono costituiti dalle quantità seguenti:

- Quantità di dati letti dall'archivio. Questo importo include:
  - Dati letti durante la lettura dei dati.
  - Dati letti durante la lettura dei metadati (per i formati di file che contengono metadati, ad esempio parquet).
- Quantità di dati nei risultati intermedi. Questi dati vengono trasferiti tra i nodi durante l'esecuzione della query. Include il trasferimento dei dati all'endpoint in un formato non compresso. 
- Quantità di dati scritti nell'archivio. Se si usa CETAS per esportare il set di risultati nella risorsa di archiviazione, la quantità di dati scritti viene aggiunta alla quantità di dati elaborati per la parte SELECT di CETAS.

La lettura dei file dalla risorsa di archiviazione è altamente ottimizzata. Il processo USA:

- Prelettura, che potrebbe aggiungere un sovraccarico alla quantità di dati letti. Se una query legge un intero file, non si verifica alcun overhead. Se un file viene letto parzialmente, ad esempio nelle prime N query, viene letto un po' più dati usando la prelettura.
- Parser ottimizzato con valori delimitati da virgole (CSV). Se si usa PARSER_VERSION = "2.0" per leggere i file CSV, le quantità di dati letti dall'archiviazione aumenteranno leggermente. Un parser CSV ottimizzato legge i file in parallelo, in blocchi di uguale dimensione. I blocchi non contengono necessariamente intere righe. Per assicurarsi che tutte le righe vengano analizzate, il parser CSV ottimizzato legge anche piccoli frammenti di blocchi adiacenti. Questo processo aggiunge una piccola quantità di overhead.

## <a name="statistics"></a>Statistiche

Il pool SQL senza Server Query Optimizer si basa sulle statistiche per generare piani di esecuzione di query ottimali. È possibile creare manualmente le statistiche. In caso contrario, il pool SQL senza server li crea automaticamente. In entrambi i casi, le statistiche vengono create eseguendo una query separata che restituisce una colonna specifica a una frequenza di campionamento specificata. Alla query è associata una quantità di dati elaborata.

Se si esegue la stessa query o qualsiasi altra query che trarrebbe vantaggio dalle statistiche create, le statistiche vengono riutilizzate, se possibile. Non sono stati elaborati dati aggiuntivi per la creazione delle statistiche.

Quando si creano statistiche per una colonna parquet, solo la colonna pertinente viene letta dai file. Quando si creano statistiche per una colonna CSV, i file interi vengono letti e analizzati.

## <a name="rounding"></a>Rounding

La quantità di dati elaborati viene arrotondata per eccesso ai MB più vicini per ogni query. Ogni query ha un minimo di 10 MB di dati elaborati.

## <a name="what-data-processed-doesnt-include"></a>Quali dati elaborati non includono

- Metadati a livello di server, ad esempio account di accesso, ruoli e credenziali a livello di server.
- Database creati nell'endpoint. Tali database contengono solo metadati, ad esempio utenti, ruoli, schemi, viste, funzioni inline con valori di tabella [funzioni con valori], stored procedure, credenziali con ambito database, origini dati esterne, formati di file esterni e tabelle esterne.
  - Se si utilizza l'inferenza dello schema, i frammenti di file vengono letti per dedurre i nomi di colonna e i tipi di dati e la quantità di dati letti viene aggiunta alla quantità di dati elaborati.
- Istruzioni DDL (Data Definition Language), ad eccezione dell'istruzione CREATE STATISTICs, perché elabora i dati dall'archiviazione in base alla percentuale di campionamento specificata.
- Query solo sui metadati.

## <a name="reducing-the-amount-of-data-processed"></a>Riduzione della quantità di dati elaborati

È possibile ottimizzare la quantità di dati elaborati per query e migliorare le prestazioni tramite il partizionamento e la conversione dei dati in un formato basato su colonne compresso come parquet.

## <a name="examples"></a>Esempi

Immaginate tre tabelle.

- La tabella population_csv è supportata da 5 TB di file CSV. I file sono organizzati in cinque colonne di dimensioni uguali.
- La tabella population_parquet presenta gli stessi dati della tabella population_csv. È supportato da 1 TB di file parquet. Questa tabella è più piccola rispetto a quella precedente perché i dati sono compressi in formato parquet.
- Il very_small_csv tabella è supportato da 100 KB di file CSV.

**Query 1** : selezionare SUM (popolamento) da population_csv

Questa query consente di leggere e analizzare interi file per ottenere i valori per la colonna population. I nodi elaborano i frammenti della tabella e la somma della popolazione per ogni frammento viene trasferita tra i nodi. La somma finale viene trasferita all'endpoint. 

Questa query elabora 5 TB di dati, oltre a un sovraccarico ridotto per il trasferimento di somme di frammenti.

**Query 2** : selezionare SUM (popolamento) da population_parquet

Quando si esegue una query su formati compressi e basati su colonne, ad esempio parquet, viene letto un minor numero di dati rispetto a query 1. Questo risultato viene visualizzato perché il pool SQL senza server legge una singola colonna compressa anziché l'intero file. In questo caso, viene letto 0,2 TB. Cinque colonne di dimensioni equivalenti sono 0,2 TB ciascuna. I nodi elaborano i frammenti della tabella e la somma della popolazione per ogni frammento viene trasferita tra i nodi. La somma finale viene trasferita all'endpoint. 

Questa query elabora 0,2 TB più una piccola quantità di overhead per il trasferimento di somme di frammenti.

**Query 3** : selezionare * da population_parquet

Questa query legge tutte le colonne e trasferisce tutti i dati in un formato non compresso. Se il formato di compressione è 5:1, la query elabora 6 TB perché legge 1 TB e trasferisce 5 TB di dati non compressi.

**Query 4** : selezionare Count (*) da very_small_csv

Questa query legge i file interi. La dimensione totale dei file nella risorsa di archiviazione per questa tabella è 100 KB. I nodi elaborano i frammenti della tabella e la somma di ogni frammento viene trasferita tra i nodi. La somma finale viene trasferita all'endpoint. 

Questa query elabora leggermente più di 100 KB di dati. La quantità di dati elaborati per questa query viene arrotondata a un massimo di 10 MB, come specificato nella sezione [arrotondamento](#rounding) di questo articolo.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come ottimizzare le prestazioni delle query, vedere [procedure consigliate per il pool SQL senza server](best-practices-sql-on-demand.md).
