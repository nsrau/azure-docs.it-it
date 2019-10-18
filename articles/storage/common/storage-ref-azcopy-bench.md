---
title: azcopy BENCH | Microsoft Docs
description: Questo articolo contiene informazioni di riferimento per il comando azcopy BENCH.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8570bce87aeea5473b4aadf9bd30bc0a648a6f0f
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518304"
---
# <a name="azcopy-bench"></a>azcopy panca

Esegue un benchmark delle prestazioni caricando i dati di test in una destinazione specificata. I dati di test vengono generati automaticamente.

Il comando benchmark esegue lo stesso processo di caricamento come ' Copy ', ad eccezione del fatto che:

  - Nessun parametro di origine.  Il comando richiede solo un URL di destinazione. Nella versione corrente, questo URL di destinazione deve fare riferimento a un contenitore BLOB.
  
  - Il payload viene descritto dai parametri della riga di comando, che controllano il numero di file generati automaticamente e la relativa dimensione. Il processo di generazione si verifica completamente in memoria. Il disco non viene utilizzato.
  
  - Sono supportati solo alcuni dei parametri facoltativi disponibili per il comando copy.
  
  - Vengono misurate e segnalate ulteriori informazioni di diagnostica.
  
  - Per impostazione predefinita, i dati trasferiti vengono eliminati alla fine dell'esecuzione dei test.

La modalità benchmark verrà automaticamente sintonizzata sul numero di connessioni TCP parallele che fornisce la velocità effettiva massima. Il numero verrà visualizzato alla fine. Per impedire l'ottimizzazione automatica, impostare la variabile di ambiente AZCOPY_CONCURRENCY_VALUE su un numero specifico di connessioni.

Sono supportati tutti i tipi di autenticazione usuali. Tuttavia, l'approccio più pratico per il benchmarking è in genere quello di creare un contenitore vuoto con un token di firma di accesso condiviso e di usare l'autenticazione SAS.

## <a name="examples"></a>esempi

```azcopy
azcopy bench [destination] [flags]
```

Eseguire un test di benchmark con i parametri predefiniti (idoneo per le reti di benchmarking fino a 1 Gbps):'

- azcopy Bench "https:///[account]. blob. Core. Windows. NET/[Container]? <SAS> "

Eseguire un test di benchmark che carica i file 100, ogni 2 GiB di dimensioni: (adatta per il benchmarking su una rete veloce, ad esempio 10 Gbps):'

- azcopy Bench "https:///[account]. blob. Core. Windows. NET/[Container]? <SAS> " --file-count 100--size per file 2G

Come sopra, ma usare i file 50.000, ogni 8 MiB di dimensioni e calcolare gli hash MD5, nello stesso modo in cui il flag--put-MD5 esegue questa operazione nel comando copy. Lo scopo di--put-MD5 quando il benchmarking consiste nel verificare se il calcolo MD5 influisca sulla velocità effettiva per il numero e le dimensioni dei file selezionati:

- azcopy Bench "https:///[account]. blob. Core. Windows. NET/[Container]? <SAS> " --file-count 50000--size-per-file 8--put-MD5

## <a name="options"></a>Opzioni

**--BLOB-type** String definisce il tipo di BLOB nella destinazione. Usato per consentire il benchmarking di diversi tipi di BLOB. Identico al parametro con lo stesso nome nel comando Copy (valore predefinito "Detect").

**--block-size-MB** float usa questa dimensione del blocco (specificata in MIB). Il valore predefinito viene calcolato automaticamente in base alle dimensioni del file. Sono consentite frazioni decimali, ad esempio 0,25. Identico al parametro con lo stesso nome nel comando copy.

**--Delete-test-data**  Se true, i dati del benchmark verranno eliminati al termine dell'esecuzione del benchmark.  Impostarla su false se si desidera che i dati vengano mantenuti nella destinazione, ad esempio per utilizzarli per i test manuali al di fuori della modalità benchmark (valore predefinito true).

**--file-count** uint il numero di file di dati generati automaticamente da usare (valore predefinito 100).

**-h,--Help**  Guida per il banco

**--** la stringa a livello di log definisce il livello di dettaglio del log per il file di log, i livelli disponibili: info (tutte le richieste/risposte), avviso (risposte lente), errore (solo richieste non riuscite) e nessuno (nessun log di output). (impostazione predefinita "INFO")

**--put-MD5**  Creare un hash MD5 di ogni file e salvare l'hash come proprietà Content-MD5 del BLOB o del file di destinazione. Per impostazione predefinita, l'hash non viene creato. Identico al parametro con lo stesso nome nel comando copy.

**--** dimensioni della stringa per ogni file di ogni file di dati generato automaticamente. Deve essere un numero immediatamente seguito da K, M o G, ad esempio 12K o 200G (valore predefinito "250 milioni").

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

**--Cap-Mbps UInt32**  Viene riversata la velocità di trasferimento, in megabit al secondo. Una velocità effettiva momentanea potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.

**--output-** formato stringa di tipo dell'output del comando. Le scelte includono: text, JSON. Il valore predefinito è "Text". (impostazione predefinita "testo").

## <a name="see-also"></a>Vedi anche

- [azcopy](storage-ref-azcopy.md)
