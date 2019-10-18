---
title: azcopy processi rimossi | Microsoft Docs
description: Questo articolo contiene informazioni di riferimento per il comando di rimozione dei processi azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 82c399580322334e67c0c9c2b88d1edf6f175e0c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518291"
---
# <a name="azcopy-jobs-remove"></a>rimozione processi azcopy

Rimuovere tutti i file associati all'ID processo specificato.

> [!NOTE] 
> È possibile personalizzare il percorso in cui vengono salvati i file di log e di piano. Per altre informazioni, vedere il comando [azcopy ENV](storage-ref-azcopy-env.md) .

```
azcopy jobs remove [jobID] [flags]
```

## <a name="examples"></a>esempi

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>Opzioni

**-h,--Help**                Guida per Remove.

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

**--Cap-Mbps UInt32**      Viene riversata la velocità di trasferimento, in megabit al secondo. Una velocità effettiva momentanea potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.

**--output-** formato stringa di tipo dell'output del comando. Le scelte includono: text, JSON. Il valore predefinito è "Text". (impostazione predefinita "testo")

## <a name="see-also"></a>Vedi anche

- [processi azcopy](storage-ref-azcopy-jobs.md)
