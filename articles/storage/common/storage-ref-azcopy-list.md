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
ms.openlocfilehash: b7348e3790166e1a1aecab422e571b8f2fc7cd5f
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513513"
---
# <a name="azcopy-list"></a>azcopy list

Elenca le entità in una determinata risorsa.

## <a name="synopsis"></a>Sinossi

Nella versione corrente sono supportati solo i contenitori BLOB.

```azcopy
azcopy list [containerURL] [flags]
```

## <a name="examples"></a>esempi

```azcopy
azcopy list [containerURL]
```

## <a name="options"></a>Opzioni

|Opzione|Description|
|--|--|
|-h, --help|Mostra il contenuto della Guida per il comando list.|
|--Machine-Readable|Elenca le dimensioni dei file in byte.|
|--unità mega|Visualizza le unità in ordine 1000, non 1024.|
|--Running-Tally|Conta il numero totale di file e le relative dimensioni.|

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|Description|
|---|---|
|--Cap-Mbps UInt32|Viene riversata la velocità di trasferimento, in megabit al secondo. Una velocità effettiva momentanea potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.|
|--output-tipo stringa|Formato dell'output del comando. Le scelte includono: text, JSON. Il valore predefinito è "Text".|

## <a name="see-also"></a>Vedi anche

- [azcopy](storage-ref-azcopy.md)
