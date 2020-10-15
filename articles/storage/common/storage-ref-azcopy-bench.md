---
title: azcopy BENCH | Microsoft Docs
description: Questo articolo contiene informazioni di riferimento per il comando azcopy BENCH.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c1028d0a4a458746c08fd6fa4f16aa952d9962a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87282008"
---
# <a name="azcopy-benchmark"></a>Benchmark azcopy

Esegue un benchmark delle prestazioni caricando o scaricando i dati di test da o verso una destinazione specificata. Per i caricamenti, i dati di test vengono generati automaticamente.

Il comando benchmark esegue lo stesso processo di "copy", ad eccezione del fatto che: 

  - Anziché richiedere parametri di origine e di destinazione, il benchmark ne accetta solo uno. Si tratta del contenitore BLOB, della condivisione File di Azure o Azure Data Lake Storage Gen2 file system da cui si desidera caricare o scaricare.

  - Il parametro ' Mode ' indica se AzCopy deve testare i caricamenti o i download dalla destinazione specificata. I valori validi sono ' upload ' è download '. Il valore predefinito è "upload".

  - Per i benchmark di caricamento, il payload viene descritto dai parametri della riga di comando, che controllano il numero di file generati automaticamente e l'importanza dei file. Il processo di generazione si verifica completamente in memoria. Il disco non viene utilizzato.

  - Per i download, il payload è costituito da qualsiasi file già esistente nell'origine. (Vedere l'esempio seguente su come generare file di test, se necessario).
  
  - Sono supportati solo alcuni dei parametri facoltativi disponibili per il comando copy.
  
  - Vengono misurate e segnalate ulteriori informazioni di diagnostica.
  
  - Per il caricamento, il comportamento predefinito consiste nell'eliminare i dati trasferiti alla fine dell'esecuzione dei test.  Per i download, i dati non vengono mai salvati localmente.

La modalità benchmark verrà automaticamente sintonizzata sul numero di connessioni TCP parallele che fornisce la velocità effettiva massima. Il numero verrà visualizzato alla fine. Per impedire l'ottimizzazione automatica, impostare la variabile di ambiente AZCOPY_CONCURRENCY_VALUE su un numero specifico di connessioni. 

Sono supportati tutti i tipi di autenticazione usuali. Tuttavia, l'approccio più pratico per il benchmarking del caricamento è in genere quello di creare un contenitore vuoto con un token di firma di accesso condiviso e di usare l'autenticazione SAS. Per la modalità di download è necessario che nel contenitore di destinazione sia presente un set di dati di test.

## <a name="examples"></a>Esempi

```azcopy
azcopy benchmark [destination] [flags]
```

Eseguire un test di benchmark con i parametri predefiniti (idoneo per le reti di benchmarking fino a 1 Gbps):'

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"
```
Eseguire un test di benchmark che carica i file 100, ogni 2 GiB: (adatta per il benchmarking su una rete veloce, ad esempio 10 Gbps):'

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"--file-count 100 --size-per-file 2G
```
Eseguire un test di benchmark, ma usare i file 50.000, ogni 8 MB di dimensioni e calcolare gli hash MD5 (in modo analogo al `--put-md5` flag che esegue questa operazione nel comando copy). Lo scopo di `--put-md5` quando il benchmarking consiste nel verificare se il calcolo MD5 influisca sulla velocità effettiva per il numero e le dimensioni dei file selezionati:

```azcopy
azcopy bench --mode='Upload' "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 50000 --size-per-file 8M --put-md5
```

Eseguire un test di benchmark che Scarica i file esistenti da una destinazione

```azcopy
azcopy bench --mode='Download' "https://[account].blob.core.windows.net/[container]?<SAS?"
```

Eseguire un caricamento che non elimina i file trasferiti. (Questi file possono quindi fungere da payload per un test di download)

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 100 --delete-test-data=false
```

## <a name="options"></a>Opzioni

**--BLOB-type** String definisce il tipo di BLOB nella destinazione. Usato per consentire il benchmarking di diversi tipi di BLOB. Identico al parametro con lo stesso nome nel comando Copy (valore predefinito "Detect").

**--block-size-MB** float usa questa dimensione del blocco (specificata in MIB). Il valore predefinito viene calcolato automaticamente in base alle dimensioni del file. Sono consentite frazioni decimali, ad esempio 0,25. Identico al parametro con lo stesso nome nel comando copy.

**--Check-length**  Controllare la lunghezza di un file nella destinazione dopo il trasferimento. In caso di mancata corrispondenza tra origine e destinazione, il trasferimento viene contrassegnato come non riuscito. (valore predefinito true)

**--Delete-test-data**  Se true, i dati del benchmark verranno eliminati al termine dell'esecuzione del benchmark.  Impostarla su false se si desidera che i dati vengano mantenuti nella destinazione, ad esempio per utilizzarli per i test manuali al di fuori della modalità benchmark (impostazione predefinita: true).

**--Conteggio file** uint.  Numero di file di dati generati automaticamente da usare (valore predefinito 100).

**--Guida**  Guida per il banco

**--** la stringa a livello di log definisce il livello di dettaglio del log per il file di log, i livelli disponibili: info (tutte le richieste/risposte), avviso (risposte lente), errore (solo richieste non riuscite) e nessuno (nessun log di output). (impostazione predefinita "INFO")

**--** la stringa della modalità definisce se Azcopy deve testare i caricamenti o i download da questa destinazione. I valori validi sono ' upload ' è download '. L'opzione predefinita è' upload '. (valore predefinito ' upload ')

**--Number-of-folders** uint se maggiore di 0, creare cartelle per dividere i dati.

**--put-MD5**  Creare un hash MD5 di ogni file e salvare l'hash come proprietà Content-MD5 del BLOB o del file di destinazione. Per impostazione predefinita, l'hash non viene creato. Identico al parametro con lo stesso nome nel comando copy.

**--** dimensioni della stringa per ogni file di ogni file di dati generato automaticamente. Deve essere un numero immediatamente seguito da K, M o G, ad esempio 12K o 200G (valore predefinito "250 milioni").

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

**--Cap-Mbps float**  Viene riversata la velocità di trasferimento, in megabit al secondo. Una velocità effettiva momentanea potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.

**--output-** formato stringa di tipo dell'output del comando. Le scelte includono: text, JSON. Il valore predefinito è "Text". (impostazione predefinita "testo").

**--trusted-Microsoft-suffissi** stringa specifica i suffissi di dominio aggiuntivi in cui è possibile inviare i token di accesso Azure Active Directory.  Il valore predefinito è'*. Core.Windows.NET;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net ". Tutti gli elencati qui vengono aggiunti al valore predefinito. Per la sicurezza, è consigliabile inserire qui solo Microsoft Azure domini. Separare più voci con un punto e virgola.


## <a name="see-also"></a>Vedere anche

- [azcopy](storage-ref-azcopy.md)
