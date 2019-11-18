---
title: Attributi dell'entità Field of Study - API Academic Knowledge
titlesuffix: Azure Cognitive Services
description: Informazioni sugli attributi che è possibile usare con l'entità Field of Study nell'API Academic Knowledge.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: b6bb5e0fd822541a0a4c10ef5f275d46664f8e10
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146484"
---
# <a name="field-of-study-entity"></a>Entità Field of Study

> [!NOTE]
> Gli attributi seguenti sono specifici del campo dell'entità Study. (Ty =' 6')

Nome | DESCRIZIONE | digitare | Operazioni
--- | --- | --- | ---
CC      |Numero totale delle citazioni del campo di studi    |Int32      |nessuno  
DFN     |Nome visualizzato del campo di studi            |String     |nessuno
ECC     |Numero totale stimato delle citazioni del campo di studi|Int32      |nessuno
FL      |Livello nella gerarchia del campo di studi     |Int32      |Equals, tra
FN      |Nome normalizzato del campo di studi         |String     |Uguale a
FC.FId  |ID del campo di studi figlio                |Int64      |Uguale a
FC.FN   |Nome del campo di studi figlio              |String     |Uguale a
FP.FId  |ID del campo di studi padre               |Int64      |Uguale a
FP.FN   |Nome del campo di studi padre             |String     |Uguale a
id      |ID entità                              |Int64      |Uguale a
PC    | Campo di studio numero totale pubblicazioni | Int32 | nessuno
