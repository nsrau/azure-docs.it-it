---
title: elenco azcopy | Microsoft Docs
description: Questo articolo contiene informazioni di riferimento per il comando azcopy List.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: f02c1afadf18a7d3170eb178696487464e4a0bd3
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034086"
---
# <a name="azcopy-list"></a>azcopy list

Elenca le entità in una determinata risorsa.

## <a name="synopsis"></a>Sinossi

Nella versione corrente sono supportati solo i contenitori BLOB.

```azcopy
azcopy list [containerURL] [flags]
```

## <a name="related-conceptual-articles"></a>Articoli concettuali correlati

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [Trasferire i dati con AzCopy e l'archiviazione BLOB](storage-use-azcopy-blobs.md)
- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)
- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>esempi

```azcopy
azcopy list [containerURL]
```

## <a name="options"></a>Opzioni

|Opzione|DESCRIZIONE|
|--|--|
|-h, --help|Mostra il contenuto della Guida per il comando list.|
|--Machine-Readable|Elenca le dimensioni dei file in byte.|
|--unità mega|Visualizza le unità in ordine 1000, non 1024.|
|--Running-Tally|Conta il numero totale di file e le relative dimensioni.|

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|DESCRIZIONE|
|---|---|
|--Cap-Mbps UInt32|Viene riversata la velocità di trasferimento, in megabit al secondo. Una velocità effettiva momentanea potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.|
|--output-tipo stringa|Formato dell'output del comando. Le scelte includono: text, JSON. Il valore predefinito è "Text".|

## <a name="see-also"></a>Vedere anche

- [azcopy](storage-ref-azcopy.md)
