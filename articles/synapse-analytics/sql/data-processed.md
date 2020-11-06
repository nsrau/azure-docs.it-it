---
title: Dati elaborati con pool SQL senza server
description: Questo documento descrive il modo in cui vengono calcolati i dati elaborati durante l'esecuzione di query sui dati in archiviazione di Azure usando un pool SQL senza server
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/05/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 06eb02aa3dd4d5fc8bd3605dac480d5afa52d5fa
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424220"
---
# <a name="data-processed-with-serverless-sql-pool-in-azure-synapse-analytics"></a>Dati elaborati con pool SQL senza server in Azure sinapsi Analytics

I dati elaborati sono la quantità di dati archiviati temporaneamente nel sistema durante l'esecuzione di una query ed è costituito da:

- Quantità di dati letti dalla risorsa di archiviazione, inclusi i seguenti:
  - Quantità di dati letti durante la lettura dei dati
  - Quantità di dati letti durante la lettura dei metadati (per i formati di file che contengono metadati, ad esempio parquet)
- Quantità di dati nei risultati intermedi: dati trasferiti tra i nodi durante l'esecuzione della query, incluso il trasferimento dei dati all'endpoint, in formato non compresso. 
- Quantità di dati scritti nell'archiviazione: se si usa CETAS per esportare il set di risultati nell'archivio, verranno addebitati i costi per i byte scritti e la quantità di dati elaborati per la parte SELECT di CETAS.

La lettura dei file dalla risorsa di archiviazione è altamente ottimizzata e USA:

- Prelettura, che può aggiungere un overhead ridotto alla quantità di dati letti. Se una query legge un intero file, non si verifica alcun overhead. Se un file viene letto parzialmente, ad esempio nelle prime N query, verrà letta una quantità maggiore di dati con la prelettura.
- Parser CSV ottimizzato: se si usa PARSER_VERSION = "2.0" per leggere i file CSV, comporterà una quantità leggermente maggiore di dati letti dall'archiviazione.  Il parser CSV ottimizzato legge i file in parallelo in blocchi di uguale dimensione. Non vi è alcuna garanzia che i blocchi contengano intere righe. Per assicurarsi che tutte le righe vengano analizzate, verranno letti anche piccoli frammenti di blocchi adiacenti, aggiungendo una piccola quantità di overhead.

## <a name="statistics"></a>Statistiche

Il pool SQL senza Server Query Optimizer si basa sulle statistiche per generare piani di esecuzione di query ottimali. È possibile creare le statistiche manualmente, altrimenti verranno create automaticamente dal pool SQL senza server. In entrambi i casi, le statistiche vengono create eseguendo una query separata che restituisce una colonna specifica alla frequenza di campionamento specificata. Alla query è associata una quantità di dati elaborata.

Se si esegue la stessa query o qualsiasi altra query che trarrebbe vantaggio dalle statistiche create, le statistiche verranno riutilizzate se possibile e non verranno elaborati dati aggiuntivi per la creazione delle statistiche.

La creazione di statistiche per una colonna parquet comporterà la lettura solo della colonna pertinente dai file. La creazione di statistiche per una colonna CSV comporterà la lettura e l'analisi di interi file.

## <a name="rounding"></a>Rounding

La quantità di dati elaborati verrà arrotondata per eccesso ai MB più vicini per ogni query, con un minimo di 10 MB di dati elaborati per ogni query.

## <a name="what-is-not-included-in-data-processed"></a>Elementi non inclusi nei dati elaborati

- Metadati a livello di server (ad esempio account di accesso, ruoli, credenziali a livello di server)
- I database creati nell'endpoint come tali database contengono solo metadati (ad esempio utenti, ruoli, schemi, viste, funzioni con valori inline, stored procedure, credenziali con ambito database, origini dati esterne, formati di file esterni, tabelle esterne)
  - Se si utilizza l'inferenza dello schema, i frammenti di file verranno letti per dedurre i nomi di colonna e i tipi di dati
- Le istruzioni DDL, ad eccezione di CREATE STATISTICs, elaborano i dati dall'archiviazione in base alla percentuale di campionamento specificata
- Query solo sui metadati

## <a name="reduce-amount-of-data-processed"></a>Ridurre la quantità di dati elaborati

È possibile ottimizzare la quantità di dati elaborati per query e ottenere prestazioni migliori tramite il partizionamento e la conversione dei dati in un formato a colonne compresso come parquet.

## <a name="examples"></a>Esempi

Supponiamo che siano presenti due tabelle, ognuna con gli stessi dati in cinque colonne di dimensioni uguali:

- population_csv tabella supportata da 5 TB di file CSV
- population_parquet tabella supportata da 1 TB di file parquet: questa tabella è più piccola del precedente, perché il parquet contiene dati compressi
- tabella very_small_csv supportata da 100 KB di file CSV

**#1 query** : selezionare SUM (popolamento) da population_csv

Questa query consente di leggere e analizzare interi file per ottenere i valori per la colonna population. I nodi elaborano i frammenti della tabella, la somma della popolazione per ogni frammento verrà trasferita tra i nodi e la somma finale verrà trasferita all'endpoint. Questa query elaborerà 5 TB di dati, oltre a un sovraccarico ridotto per il trasferimento di somme di frammenti.

**#2 query** : selezionare SUM (popolamento) da population_parquet

L'esecuzione di query su formati compressi e orientati alle colonne, ad esempio parquet, comporta la lettura di meno dati rispetto alla query precedente, poiché il pool SQL senza server leggerà una singola colonna compressa anziché l'intero file. In questo caso verrà letto 0,2 TB (cinque colonne di uguale dimensioni, 0,2 TB ciascuna). I nodi elaborano i frammenti della tabella, la somma della popolazione per ogni frammento verrà trasferita tra i nodi e la somma finale verrà trasferita all'endpoint. Questa query elaborerà 0,2 TB più un sovraccarico ridotto per il trasferimento di somme di frammenti.

**#3 query** : selezionare * da population_parquet

Questa query leggerà tutte le colonne e trasferirà tutti i dati in formato non compresso. Se il formato di compressione è 5:1, verrà elaborato 6 TB perché verrà letto 1 TB + trasferimento di 5 TB di dati non compressi.

**#4 query** : selezionare conteggio (*) da very_small_csv

Questa query leggerà interi file. La dimensione totale dei file nella risorsa di archiviazione per questa tabella è 100 KB. I nodi elaborano i frammenti della tabella, la somma di ogni frammento verrà trasferita tra i nodi e la somma finale verrà trasferita all'endpoint. Questa query verrà elaborata leggermente più di 100 KB di dati. La quantità di dati elaborati per questa query verrà arrotondata a un massimo di 10 MB come specificato nell' [arrotondamento](#rounding).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come ottimizzare le prestazioni delle query, vedere [procedure consigliate per il pool SQL senza server](best-practices-sql-on-demand.md).
