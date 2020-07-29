---
title: azcopy Load CLFS | Microsoft Docs
titleSuffix: Azure Storage
description: Questo articolo contiene informazioni di riferimento per il comando azcopy Load CLFS.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8f0a8903f90b134c35d9adb35a493d989d414b56
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294334"
---
# <a name="azcopy-load-clfs"></a>azcopy load clfs

Trasferisce i dati locali in un contenitore e li archivia nel formato CLFS (havee cloud FileSystem) di Microsoft.

## <a name="synopsis"></a>Riepilogo

Il comando Load copia i dati nei contenitori di archiviazione BLOB di Azure e quindi archivia i dati nel formato CLFS (havee cloud FileSystem) di Microsoft. Il formato CLFS proprietario viene usato dalla cache HPC di Azure e ha vFXT per i prodotti Azure.

Per utilizzare questo comando, installare l'estensione necessaria tramite: PIP3 install clfsload ~ = 1.0.23. Verificare che CLFSLoad.py sia presente nel percorso. Per ulteriori informazioni su questo passaggio, visitare il [https://aka.ms/azcopy/clfs](https://aka.ms/azcopy/clfs) .

Questo comando è una semplice opzione che consente di trasferire i dati esistenti nell'archiviazione cloud per l'uso con prodotti di cache di calcolo a prestazioni elevate Microsoft. 

Poiché questi prodotti usano un formato filesystem cloud proprietario per gestire i dati, i dati non possono essere caricati tramite il comando di copia nativo. 

Al contrario, i dati devono essere caricati tramite il prodotto della cache stesso o tramite questo comando Load, che usa il formato proprietario corretto.
Questo comando consente di trasferire i dati senza usare la cache. Ad esempio, per pre-popolare l'archiviazione o per aggiungere file a un working set senza aumentare il carico della cache.

La destinazione è un contenitore di archiviazione di Azure vuoto. Al termine del trasferimento, il contenitore di destinazione può essere usato con un'istanza di cache HPC di Azure o avere vFXT per il cluster di Azure.

> [!NOTE] 
> Si tratta di una versione di anteprima del comando Load. Segnalare eventuali problemi nel repository GitHub AzCopy.

```
azcopy load clfs [local dir] [container URL] [flags]
```

## <a name="related-conceptual-articles"></a>Articoli concettuali correlati

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [Trasferire i dati con AzCopy e l'archiviazione BLOB](storage-use-azcopy-blobs.md)
- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)
- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Esempi

Caricare un'intera directory in un contenitore con una firma di accesso condiviso nel formato CLFS:

```azcopy
azcopy load clfs "/path/to/dir" "https://[account].blob.core.windows.net/[container]?[SAS]" --state-path="/path/to/state/path"
```

## <a name="options"></a>Opzioni

**--Compression-Type** String specifica il tipo di compressione da usare per i trasferimenti. I valori disponibili sono: `DISABLED` , `LZ4` . (impostazione predefinita `LZ4` )

**--** guida per il `azcopy load clfs` comando.

**--** la stringa a livello di log definisce il livello di dettaglio del log per il file di log, i livelli disponibili: `DEBUG` , `INFO` , `WARNING` , `ERROR` . (impostazione predefinita `INFO` )

**--Max-Errors** UInt32 specifica il numero massimo di errori di trasferimento da tollerare. Se si verificano errori sufficienti, arrestare immediatamente il processo.

**--nuova sessione**   Avviare un nuovo processo anziché continuare con quello esistente le cui informazioni di rilevamento vengono conservate in `--state-path` . (valore predefinito true)

**--Preserve-hardlinks**    Mantieni relazioni di collegamenti reali.

**--state-Path** stringa obbligatoria percorso di una directory locale per il rilevamento dello stato del processo. Il percorso deve puntare a una directory esistente per poter riprendere un processo. Deve essere vuoto per un nuovo processo.

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|Descrizione|
|---|---|
|--Cap-Mbps float|Viene riversata la velocità di trasferimento, in megabit al secondo. Una velocità effettiva momentanea potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.|
|--output-tipo stringa|Formato dell'output del comando. Le scelte includono: text, JSON. Il valore predefinito è "Text".|
|--trusted-Microsoft-suffissi stringa   | Specifica i suffissi di dominio aggiuntivi in cui è possibile inviare i token di accesso Azure Active Directory.  Il valore predefinito è'*. Core.Windows.NET;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net ". Tutti gli elencati qui vengono aggiunti al valore predefinito. Per la sicurezza, è consigliabile inserire qui solo Microsoft Azure domini. Separare più voci con un punto e virgola.|

## <a name="see-also"></a>Vedere anche

- [azcopy](storage-ref-azcopy.md)
