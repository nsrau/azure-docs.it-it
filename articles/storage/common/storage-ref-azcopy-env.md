---
title: azcopy ENV | Microsoft Docs
description: Questo articolo contiene informazioni di riferimento per il comando azcopy ENV.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 15e72493190e1bc56e779c22695bc51bd05da940
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195776"
---
# <a name="azcopy-env"></a>azcopy ENV

Mostra le variabili di ambiente che consentono di configurare il comportamento di AzCopy.

## <a name="synopsis"></a>Sinossi

```azcopy
azcopy env [flags]
```

> [!IMPORTANT]
> Se si imposta una variabile di ambiente tramite la riga di comando, tale variabile sarà leggibile nella cronologia della riga di comando. Si consiglia di cancellare le variabili che contengono credenziali dalla cronologia della riga di comando. Per evitare che le variabili vengano visualizzate nella cronologia, è possibile utilizzare uno script per richiedere le credenziali dell'utente e per impostare la variabile di ambiente.

## <a name="options"></a>Opzioni

|Opzione|DESCRIZIONE|
|--|--|
|-h, --help|Visualizza il contenuto della Guida per il comando env. |
|--Show-sensitive|Mostra le variabili di ambiente sensibili/segrete.|

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|Descrizione|
|---|---|
|--Cap-Mbps UInt32|Viene riversata la velocità di trasferimento, in megabit al secondo. Una velocità effettiva momentanea potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.|
|--output-tipo stringa|Formato dell'output del comando. Le scelte includono: text, JSON. Il valore predefinito è "Text".|

## <a name="see-also"></a>Vedere anche

- [azcopy](storage-ref-azcopy.md)
