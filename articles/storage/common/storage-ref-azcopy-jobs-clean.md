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
ms.openlocfilehash: 31529155ee44b2bcfad90e8634053403dfe8fc8c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518512"
---
# <a name="azcopy-jobs-clean"></a>processi azcopy puliti

Rimuovi tutti i file di registro e di piano per tutti i processi

```
azcopy jobs clean [flags]
```

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

## <a name="see-also"></a>Vedi anche

- [processi azcopy](storage-ref-azcopy-jobs.md)
