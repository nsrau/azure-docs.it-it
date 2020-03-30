---
title: banco di azcopy Documenti Microsoft
description: In questo articolo vengono fornite informazioni di riferimento per il comando azcopy bench.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8570bce87aeea5473b4aadf9bd30bc0a648a6f0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72518304"
---
# <a name="azcopy-bench"></a>azcopy bench

Esegue un benchmark delle prestazioni caricando i dati di test in una destinazione specificata. I dati di test vengono generati automaticamente.

Il comando benchmark esegue lo stesso processo di caricamento di 'copy', ad eccezione del fatto che:

  - Non è presente alcun parametro di origine.  Il comando richiede solo un URL di destinazione. Nella versione corrente, questo URL di destinazione deve fare riferimento a un contenitore BLOB.
  
  - Il payload è descritto dai parametri della riga di comando, che controllano il numero di file generati automaticamente e le dimensioni. Il processo di generazione avviene interamente in memoria. Il disco non viene utilizzato.
  
  - Sono supportati solo alcuni dei parametri facoltativi disponibili per il comando copy.
  
  - Ulteriori diagnosi vengono misurate e segnalate.
  
  - Per impostazione predefinita, i dati trasferiti vengono eliminati al termine dell'esecuzione dei test.

La modalità Benchmark si sintonizza automaticamente al numero di connessioni TCP parallele che fornisce la velocità effettiva massima. Visualizzerà quel numero alla fine. Per impedire l'ottimizzazione automatica, impostare la variabile di ambiente AZCOPY_CONCURRENCY_VALUE su un numero specifico di connessioni.

Sono supportati tutti i tipi di autenticazione usuali. Tuttavia, l'approccio più pratico per il benchmarking consiste in genere nel creare un contenitore vuoto con un token di firma di accesso condiviso e usare l'autenticazione sAS.

## <a name="examples"></a>Esempi

```azcopy
azcopy bench [destination] [flags]
```

Eseguire un test di benchmark con parametri predefiniti (adatto per reti di benchmarking fino a 1 Gbps):'

- azcopy bench "https://[account].blob.core.windows.net/[container]? <SAS>"

Eseguire un test di benchmark che carica 100 file, ognuno di 2 GiB in termini di dimensioni: (adatto per il benchmarking su una rete veloce, ad esempio 10 Gbps):'

- azcopy bench "https://[account].blob.core.windows.net/[container]? <SAS>" --file-count 100 --size-per-file 2G

Come sopra, ma utilizzare 50.000 file, ogni 8 MiB in dimensione e calcolare i loro hash MD5 (nello stesso modo in cui il flag --put-md5 fa questo nel comando di copia). Lo scopo di --put-md5 durante il benchmarking consiste nel verificare se il calcolo MD5 influisce sulla velocità effettiva per il numero e le dimensioni di file selezionati:

- azcopy bench "https://[account].blob.core.windows.net/[container]? <SAS>" --file-count 50000 --size-per-file 8M --put-md5

## <a name="options"></a>Opzioni

**stringa di tipo --blob** Definisce il tipo di BLOB nella destinazione. Utilizzato per consentire il benchmarking di diversi tipi di BLOB. Identico al parametro con lo stesso nome nel comando copy (impostazione predefinita "Rileva").

**--block-size-mb** float Utilizzare questa dimensione del blocco (specificata in MiB). Il valore predefinito viene calcolato automaticamente in base alle dimensioni del file. Sono consentite frazioni decimali, ad esempio 0,25. Identico al parametro con lo stesso nome nel comando copy.

**--delete-test-data**  Se true, i dati di benchmark verranno eliminati alla fine dell'esecuzione del benchmark.  Impostarlo su false se si desidera mantenere i dati nella destinazione, ad esempio per utilizzarli per i test manuali al di fuori della modalità benchmark (impostazione predefinita true).

**--file-count** uint Il numero di file di dati generati automaticamente da utilizzare (impostazione predefinita 100).

**-h, --help**  Aiuto per panchina

**--log-level** string Definire il livello di dettaglio del log per il file di log, i livelli disponibili: INFO(tutte le richieste/risposte), AVVISO(risposte lente), ERRORE (solo richieste non riuscite) e NONE(nessun log di output). (impostazione predefinita "INFO")

**--put-md5**  Creare un hash MD5 di ogni file e salvare l'hash come proprietà Content-MD5 del BLOB/file di destinazione. (Per impostazione predefinita l'hash NON viene creato.) Identico al parametro con lo stesso nome nel comando copy.

**--size-per-file** stringa Dimensione di ogni file di dati generato automaticamente. Deve essere un numero immediatamente seguito da K, M o G. E.g. 12k o 200G (predefinito "250M").

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

**--cap-mbps uint32**  Limita la velocità di trasferimento, in megabit al secondo. La velocità effettiva momento per momento potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.

**--output-type** string Format dell'output del comando. Le opzioni disponibili includono: text, json. Il valore predefinito è 'text'. (testo predefinito).

## <a name="see-also"></a>Vedere anche

- [azcopy](storage-ref-azcopy.md)
