---
title: disconnessione azcopy | Microsoft Docs
description: Questo articolo contiene informazioni di riferimento per il comando di disconnessione di azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: f42e2517f8c40855e56bd062fe8bc9b22634d4dc
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195919"
---
# <a name="azcopy-logout"></a>disconnessione azcopy

Disconnette l'utente e termina l'accesso alle risorse di archiviazione di Azure.

## <a name="synopsis"></a>Sinossi

Tramite questo comando vengono rimosse tutte le informazioni di accesso memorizzate nella cache per l'utente corrente.

```azcopy
azcopy logout [flags]
```

## <a name="options"></a>Opzioni

|Opzione|Descrizione|
|--|--|
|-h, --help|Mostra il contenuto della Guida per il comando Disconnetti.|

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|DESCRIZIONE|
|---|---|
|--Cap-Mbps UInt32|Viene riversata la velocità di trasferimento, in megabit al secondo. Una velocità effettiva momentanea potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.|
|--output-tipo stringa|Formato dell'output del comando. Le scelte includono: text, JSON. Il valore predefinito è "Text".|

## <a name="see-also"></a>Vedere anche

- [azcopy](storage-ref-azcopy.md)
