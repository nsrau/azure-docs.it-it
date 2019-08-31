---
title: elenco azcopy | Microsoft Docs
description: Questo articolo contiene informazioni di riferimento per il comando azcopy List.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8c40241c49917c6db6663f346aed0ec5d3b96be7
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195945"
---
# <a name="azcopy-list"></a>elenco azcopy

Elenca le entità in una determinata risorsa.

## <a name="synopsis"></a>Sinossi

Nella versione corrente sono supportati solo i contenitori BLOB.

```azcopy
azcopy list [containerURL] [flags]
```

## <a name="examples"></a>Esempi

```azcopy
azcopy list [containerURL]
```

## <a name="options"></a>Opzioni

|Opzione|Descrizione|
|--|--|
|-h, --help|Mostra il contenuto della Guida per il comando list.|
|--Machine-Readable|Elenca le dimensioni dei file in byte.|
|--unità mega|Visualizza le unità in ordine 1000, non 1024.|
|--Running-Tally|Conta il numero totale di file e le relative dimensioni.|

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|Descrizione|
|---|---|
|--Cap-Mbps UInt32|Viene riversata la velocità di trasferimento, in megabit al secondo. Una velocità effettiva momentanea potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.|
|--output-tipo stringa|Formato dell'output del comando. Le scelte includono: text, JSON. Il valore predefinito è "Text".|

## <a name="see-also"></a>Vedere anche

- [azcopy](storage-ref-azcopy.md)
