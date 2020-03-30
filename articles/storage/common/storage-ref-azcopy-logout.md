---
title: azcopy logout Documenti Microsoft
description: In questo articolo vengono fornite informazioni di riferimento per il comando azcopy logout.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 538c54c338540059baa17b9818d444cc863b7309
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034062"
---
# <a name="azcopy-logout"></a>azcopy logout

Disconnette l'utente e termina l'accesso alle risorse di Archiviazione di Azure.Logs the user out and terminates access to Azure Storage resources.

## <a name="synopsis"></a>Riepilogo

Questo comando rimuoverà tutte le informazioni di accesso memorizzate nella cache per l'utente corrente.

```azcopy
azcopy logout [flags]
```

## <a name="related-conceptual-articles"></a>Articoli concettuali correlati

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [Trasferire dati con AzCopy e l'archiviazione BLOBTransfer data with AzCopy and Blob storage](storage-use-azcopy-blobs.md)
- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)
- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Opzioni

|Opzione|Descrizione|
|--|--|
|-h, --help|Visualizzare il contenuto della Guida per il comando di disconnessione.|

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|Descrizione|
|---|---|
|--cap-mbps uint32|Limita la velocità di trasferimento, in megabit al secondo. La velocità effettiva momento per momento potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.|
|--output-tipo stringa|Formato dell'output del comando. Le opzioni disponibili includono: text, json. Il valore predefinito è "text".|

## <a name="see-also"></a>Vedere anche

- [azcopy](storage-ref-azcopy.md)
