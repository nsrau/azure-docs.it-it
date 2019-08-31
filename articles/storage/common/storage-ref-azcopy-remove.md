---
title: azcopy rimuovere | Microsoft Docs
description: Questo articolo contiene informazioni di riferimento per il comando azcopy Remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 0cc366ab2cdad9c7258dca905d8f4a06472119fe
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195906"
---
# <a name="azcopy-remove"></a>azcopy rimuovere

Elimina entità da BLOB del servizio di archiviazione di Azure, file e Azure Data Lake Storage Gen2.

## <a name="synopsis"></a>Sinossi

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="examples"></a>Esempi

Rimuovere un singolo BLOB con SAS:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Rimuovere un'intera directory virtuale con una firma di accesso condiviso:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Rimuovere solo i BLOB principali all'interno di una directory virtuale ma non le relative sottodirectory:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Rimuovere un subset di BLOB in una directory virtuale (ad esempio: solo file jpg e PDF o se il nome del BLOB è "exactname"):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include="*.jpg;*.pdf;exactName"
```

Rimuovere un'intera directory virtuale, ma escludere determinati BLOB dall'ambito, ad esempio: ogni BLOB che inizia con foo o termina con la barra:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude="foo*;*bar"
```

Rimuovere un singolo file dal Data Lake Storage Gen2 (includere ed escludere non supportato):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Rimuovere una singola directory da Data Lake Storage Gen2 (Includi ed Escludi non supportati):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Opzioni

|Opzione|Descrizione|
|--|--|
|--Escludi stringa|escludere i file in cui il nome corrisponde all'elenco di modelli. Ad esempio: *. jpg;* . PDF; exactname|
|-h, --help|Mostra il contenuto della Guida per il comando Rimuovi.|
|--Includi stringa|Includere solo i file in cui il nome corrisponde all'elenco di modelli. Ad esempio: *. jpg;* . PDF; exactname|
|--stringa a livello di log|Definire il livello di dettaglio del log per il file di log. I livelli disponibili includono: INFO (tutte le richieste/risposte), avviso (risposte lente), errore (solo richieste non riuscite) e nessuno (nessun log di output). (impostazione predefinita "INFO")|
|--ricorsivo|Esaminare in modo ricorsivo le sottodirectory durante la sincronizzazione tra le directory.|

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|Descrizione|
|---|---|
|--Cap-Mbps UInt32|Viene riversata la velocità di trasferimento, in megabit al secondo. Una velocità effettiva momentanea potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.|
|--output-tipo stringa|Formato dell'output del comando. Le scelte includono: text, JSON. Il valore predefinito è "Text".|

## <a name="see-also"></a>Vedere anche

- [azcopy](storage-ref-azcopy.md)
