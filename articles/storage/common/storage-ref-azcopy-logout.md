---
title: disconnessione azcopy | Microsoft Docs
description: Questo articolo contiene informazioni di riferimento per il comando di disconnessione di azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 84b65470b12212eb1038e18bd442ff07511a5c1a
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513509"
---
# <a name="azcopy-logout"></a>azcopy logout

Disconnette l'utente e termina l'accesso alle risorse di archiviazione di Azure.

## <a name="synopsis"></a>Sinossi

Tramite questo comando vengono rimosse tutte le informazioni di accesso memorizzate nella cache per l'utente corrente.

```azcopy
azcopy logout [flags]
```

## <a name="options"></a>Opzioni

|Opzione|Description|
|--|--|
|-h, --help|Mostra il contenuto della Guida per il comando Disconnetti.|

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|Description|
|---|---|
|--Cap-Mbps UInt32|Viene riversata la velocità di trasferimento, in megabit al secondo. Una velocità effettiva momentanea potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.|
|--output-tipo stringa|Formato dell'output del comando. Le scelte includono: text, JSON. Il valore predefinito è "Text".|

## <a name="see-also"></a>Vedi anche

- [azcopy](storage-ref-azcopy.md)
