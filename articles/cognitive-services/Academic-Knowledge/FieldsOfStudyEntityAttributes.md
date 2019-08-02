---
title: Attributi dell'entità Field of Study - API Academic Knowledge
titlesuffix: Azure Cognitive Services
description: Informazioni sugli attributi che è possibile usare con l'entità Field of Study nell'API Academic Knowledge.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: d54f3c72462e6702b09068092b7c18ea50f12048
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704986"
---
# <a name="field-of-study-entity"></a>Entità Field of Study

<sub> *Gli attributi seguenti sono specifici dell'entità Field of Study. (Ty = '6') </sub>

NOME    |Descrizione                            |Type       | Operazioni
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entità                              |Int64      |Equals
FN      |Nome normalizzato del campo di studi         |String     |Equals
DFN     |Nome visualizzato del campo di studi            |String     |none
Cc      |Numero totale delle citazioni del campo di studi    |Int32      |none  
ECC     |Numero totale stimato delle citazioni del campo di studi|Int32      |none
FL      |Livello nella gerarchia del campo di studi     |Int32      |Uguale a <br/>IsBetween
FP.FN   |Nome del campo di studi padre             |String     |Equals
FP.FId  |ID del campo di studi padre               |Int64      |Equals
FC.FN   |Nome del campo di studi figlio              |String     |Equals
FC.FId  |ID del campo di studi figlio                |Int64      |Equals
