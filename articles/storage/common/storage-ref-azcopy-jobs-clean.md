---
title: processi azcopy puliti | Microsoft Docs
description: Questo articolo contiene informazioni di riferimento per il comando azcopy Jobs clean.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7ae14c3606dfe6bffa8481682843f3f2e85c2131
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033712"
---
# <a name="azcopy-jobs-clean"></a>azcopy jobs clean

Rimuovi tutti i file di registro e di piano per tutti i processi

```
azcopy jobs clean [flags]
```

## <a name="related-conceptual-articles"></a>Articoli concettuali correlati

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [Trasferire i dati con AzCopy e l'archiviazione BLOB](storage-use-azcopy-blobs.md)
- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)
- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>esempi

```
  azcopy jobs clean --with-status=completed
```

## <a name="options"></a>Opzioni

**-h,--Help**                Guida per pulire.

**--with-status** stringa rimuove solo i processi con questo stato, i valori disponibili: annullato, completato, non riuscito, in corso, tutti (predefinito "tutti")

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

**--Cap-Mbps UInt32**      Viene riversata la velocità di trasferimento, in megabit al secondo. Una velocità effettiva momentanea potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.

**--output-** formato stringa di tipo dell'output del comando. Le scelte includono: text, JSON. Il valore predefinito è "Text". (impostazione predefinita "testo")

## <a name="see-also"></a>Vedere anche

- [processi azcopy](storage-ref-azcopy-jobs.md)
