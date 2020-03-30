---
title: i lavori di azcopy mostrano Documenti Microsoft
description: In questo articolo vengono fornite informazioni di riferimento per il comando azcopy jobs show.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7b5f566757dd77a61f252b123d0c9c1b74303fbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034132"
---
# <a name="azcopy-jobs-show"></a>azcopy jobs show

Visualizza informazioni dettagliate per l'ID processo specificato.

## <a name="synopsis"></a>Riepilogo

Se viene fornito solo l'ID processo senza flag, viene restituito il riepilogo dell'avanzamento del processo.

I conteggi dei byte e la percentuale di completamento visualizzati quando si esegue questo comando riflettono solo i file completati nel processo. Non riflettono i file parzialmente completati.

Se `with-status` il flag è impostato, verrà visualizzato l'elenco dei trasferimenti nel processo con il valore specificato.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Articoli concettuali correlati

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [Trasferire dati con AzCopy e l'archiviazione BLOBTransfer data with AzCopy and Blob storage](storage-use-azcopy-blobs.md)
- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)
- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Opzioni

|Opzione|Descrizione|
|--|--|
|-h, --help|Mostra il contenuto della Guida per il comando show.|
|--with-status (stringa)|Elencare solo i trasferimenti dei processi con questo stato, valori disponibili: Iniziato, Operazione riuscita, Non riuscita|

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|Descrizione|
|---|---|
|--cap-mbps uint32|Limita la velocità di trasferimento, in megabit al secondo. La velocità effettiva momento per momento potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.|
|--output-tipo stringa|Formato dell'output del comando. Le opzioni disponibili includono: text, json. Il valore predefinito è "text".|

## <a name="see-also"></a>Vedere anche

- [azcopy jobs](storage-ref-azcopy-jobs.md)
