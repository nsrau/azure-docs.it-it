---
title: ripresa dei processi azcopy | Microsoft Docs
description: Questo articolo contiene informazioni di riferimento per il comando azcopy Jobs Resume.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 3ee7879475801660b5200dddca88a0a81b2b6b9b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034149"
---
# <a name="azcopy-jobs-resume"></a>azcopy jobs resume

Riprende il processo esistente con l'ID del processo specificato.

## <a name="synopsis"></a>Sinossi

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Articoli concettuali correlati

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [Trasferire i dati con AzCopy e l'archiviazione BLOB](storage-use-azcopy-blobs.md)
- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)
- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Opzioni

|Opzione|DESCRIZIONE|
|--|--|
|--Destination-SAS stringa|Firma di accesso condiviso di destinazione della destinazione per il JobId specificato.|
|--Escludi stringa|Filtro: escludere questi trasferimenti non riusciti quando si riprende il processo. I file devono essere separati da';'.|
|-h, --help|Mostra il contenuto della Guida per il comando Resume.|
|--Includi stringa|Filtro: include solo questi trasferimenti non riusciti quando si riprende il processo. I file devono essere separati da';'.|
|--Source-SAS stringa |firma di accesso condiviso dell'origine per il JobId specificato.|

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|DESCRIZIONE|
|---|---|
|--Cap-Mbps UInt32|Viene riversata la velocità di trasferimento, in megabit al secondo. Una velocità effettiva momentanea potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.|
|--output-tipo stringa|Formato dell'output del comando. Le scelte includono: text, JSON. Il valore predefinito è "Text".|

## <a name="see-also"></a>Vedere anche

- [processi azcopy](storage-ref-azcopy-jobs.md)
