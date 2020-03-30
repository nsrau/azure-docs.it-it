---
title: ripresa dei processi di azcopy Documenti Microsoft
description: In questo articolo vengono fornite informazioni di riferimento per il comando azcopy jobs resume.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 3ee7879475801660b5200dddca88a0a81b2b6b9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034149"
---
# <a name="azcopy-jobs-resume"></a>azcopy jobs resume

Riprende il processo esistente con l'ID processo specificato.

## <a name="synopsis"></a>Riepilogo

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Articoli concettuali correlati

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [Trasferire dati con AzCopy e l'archiviazione BLOBTransfer data with AzCopy and Blob storage](storage-use-azcopy-blobs.md)
- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)
- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Opzioni

|Opzione|Descrizione|
|--|--|
|--destinazione-stringa sas|Creazione di una chiave di distribuzione di destinazione per JobId specificato.|
|--exclude stringa|Filtro: escludere questi trasferimenti non riusciti quando si riprende il processo. I file devono essere separati da ';'.|
|-h, --help|Visualizzare il contenuto della Guida per il comando resume.|
|--include stringa|Filtro: includere solo questi trasferimenti non riusciti quando si riprende il processo. I file devono essere separati da ';'.|
|--stringa sas di origine |sAS di origine dell'origine per un JobId specificato.|

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|Descrizione|
|---|---|
|--cap-mbps uint32|Limita la velocità di trasferimento, in megabit al secondo. La velocità effettiva momento per momento potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.|
|--output-tipo stringa|Formato dell'output del comando. Le opzioni disponibili includono: text, json. Il valore predefinito è "text".|

## <a name="see-also"></a>Vedere anche

- [azcopy jobs](storage-ref-azcopy-jobs.md)
