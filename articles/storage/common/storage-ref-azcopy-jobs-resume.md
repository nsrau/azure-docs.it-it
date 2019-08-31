---
title: ripresa dei processi azcopy | Microsoft Docs
description: Questo articolo contiene informazioni di riferimento per il comando azcopy Jobs Resume.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8786da42f000a2f13279499159a7af424aa10748
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195789"
---
# <a name="azcopy-jobs-resume"></a>Riprendi processi azcopy

Riprende il processo esistente con l'ID del processo specificato.

## <a name="synopsis"></a>Sinossi

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="options"></a>Opzioni

|Opzione|DESCRIZIONE|
|--|--|
|--Destination-SAS stringa|Firma di accesso condiviso di destinazione della destinazione per il JobId specificato.|
|--Escludi stringa|Filtro Escludere questi trasferimenti non riusciti quando si riprende il processo. I file devono essere separati da';'.|
|-h, --help|Mostra il contenuto della Guida per il comando Resume.|
|--Includi stringa|Filtro: include solo questi trasferimenti non riusciti quando si riprende il processo. I file devono essere separati da';'.|
|--Source-SAS stringa |firma di accesso condiviso dell'origine per il JobId specificato.|

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|Descrizione|
|---|---|
|--Cap-Mbps UInt32|Viene riversata la velocità di trasferimento, in megabit al secondo. Una velocità effettiva momentanea potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.|
|--output-tipo stringa|Formato dell'output del comando. Le scelte includono: text, JSON. Il valore predefinito è "Text".|

## <a name="see-also"></a>Vedere anche

- [processi azcopy](storage-ref-azcopy-jobs.md)
