---
title: Azcopy lavori puliti Documenti Microsoft
description: In questo articolo vengono fornite informazioni di riferimento per il comando azcopy jobs clean.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7ae14c3606dfe6bffa8481682843f3f2e85c2131
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033712"
---
# <a name="azcopy-jobs-clean"></a>azcopy jobs clean

Rimuovere tutti i file di registro e di piano per tutti i processi

```
azcopy jobs clean [flags]
```

## <a name="related-conceptual-articles"></a>Articoli concettuali correlati

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [Trasferire dati con AzCopy e l'archiviazione BLOBTransfer data with AzCopy and Blob storage](storage-use-azcopy-blobs.md)
- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)
- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Esempi

```
  azcopy jobs clean --with-status=completed
```

## <a name="options"></a>Opzioni

**-h, --help**                Aiuto per pulito.

**--with-status** stringa Rimuovi solo i processi con questo stato, valori disponibili: Annullato, Completato, Non riuscito, InProgress, Tutti (impostazione predefinita "Tutti")

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

**--cap-mbps uint32**      Limita la velocità di trasferimento, in megabit al secondo. La velocità effettiva momento per momento potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.

**--output-type** string Format dell'output del comando. Le opzioni disponibili includono: text, json. Il valore predefinito è 'text'. (predefinito "testo")

## <a name="see-also"></a>Vedere anche

- [azcopy jobs](storage-ref-azcopy-jobs.md)
