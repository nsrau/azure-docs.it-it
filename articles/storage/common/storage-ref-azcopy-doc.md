---
title: doc azcopy | Microsoft Docs
description: Questo articolo contiene informazioni di riferimento per il comando azcopy doc.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d1f57ae40b47c1d910ba20ae8a8f19cdc6908d6b
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70196036"
---
# <a name="azcopy-doc"></a>documento azcopy

Genera la documentazione per lo strumento nel formato Markdown.

## <a name="synopsis"></a>Sinossi

Genera la documentazione per lo strumento in formato Markdown e la archivia nel percorso designato.

Per impostazione predefinita, i file vengono archiviati in una cartella denominata "doc" nella directory corrente.

```azcopy
azcopy doc [flags]
```

## <a name="options"></a>Opzioni

|Opzione|Descrizione|
|--|--|
|-h, --help|Visualizza il contenuto della Guida per il comando doc.|

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|Descrizione|
|---|---|
|--Cap-Mbps UInt32|Viene riversata la velocità di trasferimento, in megabit al secondo. Una velocità effettiva momentanea potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.|
|--output-tipo stringa|Formato dell'output del comando. Le scelte includono: text, JSON. Il valore predefinito è "Text".|

## <a name="see-also"></a>Vedere anche

- [azcopy](storage-ref-azcopy.md)
