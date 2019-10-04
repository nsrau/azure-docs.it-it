---
title: Mostra i processi di azcopy | Microsoft Docs
description: Questo articolo contiene informazioni di riferimento per il comando azcopy Jobs show.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d4369bd0c986ee20a0796436fea47509a711de4f
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195958"
---
# <a name="azcopy-jobs-show"></a>Mostra i processi di azcopy

Mostra informazioni dettagliate per l'ID del processo specificato.

## <a name="synopsis"></a>Sinossi

Se viene fornito solo l'ID processo senza flag, viene restituito il riepilogo dello stato di avanzamento del processo.

Se il `with-status` flag è impostato, verrà visualizzato l'elenco di trasferimenti nel processo con il valore specificato.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="options"></a>Opzioni

|Opzione|DESCRIZIONE|
|--|--|
|-h, --help|Visualizza il contenuto della Guida per il comando Mostra.|
|--with-status stringa|Elencare solo i trasferimenti del processo con questo stato, i valori disponibili: Avviato, esito positivo, non riuscito|

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|Descrizione|
|---|---|
|--Cap-Mbps UInt32|Viene riversata la velocità di trasferimento, in megabit al secondo. Una velocità effettiva momentanea potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.|
|--output-tipo stringa|Formato dell'output del comando. Le scelte includono: text, JSON. Il valore predefinito è "Text".|

## <a name="see-also"></a>Vedere anche

- [processi azcopy](storage-ref-azcopy-jobs.md)
