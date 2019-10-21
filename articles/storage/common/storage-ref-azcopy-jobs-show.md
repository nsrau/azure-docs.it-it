---
title: Mostra i processi di azcopy | Microsoft Docs
description: Questo articolo contiene informazioni di riferimento per il comando azcopy Jobs show.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 42b8bde9adb6980ff2c7004d43b02fc1fdc38363
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513460"
---
# <a name="azcopy-jobs-show"></a>azcopy jobs show

Mostra informazioni dettagliate per l'ID del processo specificato.

## <a name="synopsis"></a>Sinossi

Se viene fornito solo l'ID processo senza flag, viene restituito il riepilogo dello stato di avanzamento del processo.

I conteggi dei byte e la percentuale di completamento visualizzati quando si esegue questo comando riflettono solo i file completati nel processo. Non riflettono i file parzialmente completati.

Se viene impostato il flag di `with-status`, viene visualizzato l'elenco di trasferimenti nel processo con il valore specificato.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="options"></a>Opzioni

|Opzione|Description|
|--|--|
|-h, --help|Visualizza il contenuto della Guida per il comando Mostra.|
|--with-status stringa|Elencare solo i trasferimenti del processo con questo stato, i valori disponibili: Started, Success, failed|

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|Description|
|---|---|
|--Cap-Mbps UInt32|Viene riversata la velocità di trasferimento, in megabit al secondo. Una velocità effettiva momentanea potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.|
|--output-tipo stringa|Formato dell'output del comando. Le scelte includono: text, JSON. Il valore predefinito è "Text".|

## <a name="see-also"></a>Vedi anche

- [processi azcopy](storage-ref-azcopy-jobs.md)
