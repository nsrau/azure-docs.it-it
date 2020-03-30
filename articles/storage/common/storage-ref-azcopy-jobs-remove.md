---
title: processi di azcopy rimuovere Documenti Microsoft
description: In questo articolo vengono fornite informazioni di riferimento per il comando azcopy jobs remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ba403c8d823b7ead0414521ebd51dc6f6601ccd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034170"
---
# <a name="azcopy-jobs-remove"></a>azcopy jobs remove

Rimuovere tutti i file associati all'ID processo specificato.

> [!NOTE] 
> È possibile personalizzare il percorso in cui vengono salvati i file di log e di piano. Vedere il comando [azcopy env](storage-ref-azcopy-env.md) per ulteriori informazioni.

```
azcopy jobs remove [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Articoli concettuali correlati

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [Trasferire dati con AzCopy e l'archiviazione BLOBTransfer data with AzCopy and Blob storage](storage-use-azcopy-blobs.md)
- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)
- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Esempi

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>Opzioni

**-h, --help**                Aiuto per rimuovere.

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

**--cap-mbps uint32**      Limita la velocità di trasferimento, in megabit al secondo. La velocità effettiva momento per momento potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.

**--output-type** string Format dell'output del comando. Le opzioni disponibili includono: text, json. Il valore predefinito è 'text'. (predefinito "testo")

## <a name="see-also"></a>Vedere anche

- [azcopy jobs](storage-ref-azcopy-jobs.md)
