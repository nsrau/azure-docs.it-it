---
title: azcopy make | Microsoft Docs
description: Questo articolo contiene informazioni di riferimento per il comando azcopy make.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 9afcd8de1af42424649dd8e44fc07f7bfd881257
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195711"
---
# <a name="azcopy-make"></a>azcopy make

Crea un contenitore o una condivisione file.

## <a name="synopsis"></a>Sinossi

Creare un contenitore o una condivisione file rappresentata dall'URL della risorsa specificato.

```azcopy
azcopy make [resourceURL] [flags]
```

## <a name="examples"></a>Esempi

```azcopy
azcopy make "https://[account-name].[blob,file,dfs].core.windows.net/[top-level-resource-name]"
```

## <a name="options"></a>Opzioni

|Opzione|DESCRIZIONE|
|--|--|
|-h, --help|Mostra il contenuto della Guida per il comando make. |
|--quota-GB UInt32|Specifica la dimensione massima della condivisione in gigabyte (GiB), 0 indica che si accetta la quota predefinita del servizio file.|

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|Descrizione|
|---|---|
|--Cap-Mbps UInt32|Viene riversata la velocità di trasferimento, in megabit al secondo. Una velocità effettiva momentanea potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.|
|--output-tipo stringa|Formato dell'output del comando. Le scelte includono: text, JSON. Il valore predefinito è "Text".|

## <a name="see-also"></a>Vedere anche

- [azcopy](storage-ref-azcopy.md)
