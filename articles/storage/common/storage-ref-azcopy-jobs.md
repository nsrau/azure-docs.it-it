---
title: processi azcopy | Microsoft Docs
description: Questo articolo contiene informazioni di riferimento per il comando azcopy Jobs.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 2422b16eb89ef6f1a6a1eb703d88f0ff2b76422e
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514724"
---
# <a name="azcopy-jobs"></a>azcopy jobs

Sottocomandi correlati alla gestione dei processi.

## <a name="examples"></a>esempi

```azcopy
azcopy jobs show [jobID]
```

## <a name="options"></a>Opzioni

|Opzione|Description|
|--|--|
|-h, --help|Mostra il contenuto della Guida per il comando processi.|

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|Description|
|---|---|
|--Cap-Mbps UInt32|Viene riversata la velocità di trasferimento, in megabit al secondo. Una velocità effettiva momentanea potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.|
|--output-tipo stringa|Formato dell'output del comando. Le scelte includono: text, JSON. Il valore predefinito è "Text".|

## <a name="see-also"></a>Vedi anche

- [azcopy](storage-ref-azcopy.md)
- [Elenco processi azcopy](storage-ref-azcopy-jobs-list.md)
- [Riprendi processi azcopy](storage-ref-azcopy-jobs-resume.md)
- [Mostra i processi di azcopy](storage-ref-azcopy-jobs-show.md)
