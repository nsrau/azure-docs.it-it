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
ms.openlocfilehash: e9d6badf76efd03c0520a728af7b3e47b25f200a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61339586"
---
# <a name="field-of-study-entity"></a>Entità Field of Study

<sub> *Gli attributi seguenti sono specifici dell'entità Field of Study. (Ty = '6') </sub>

Name    |Descrizione                            |Type       | Operazioni
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entità                              |Int64      |Uguale a
FN      |Nome normalizzato del campo di studi         |String     |Uguale a
DFN     |Nome visualizzato del campo di studi            |String     |Nessuno
CC      |Numero totale delle citazioni del campo di studi    |Int32      |Nessuno  
ECC     |Numero totale stimato delle citazioni del campo di studi|Int32      |Nessuno
FL      |Livello nella gerarchia del campo di studi     |Int32      |Uguale a <br/>IsBetween
FP.FN   |Nome del campo di studi padre             |String     |Uguale a
FP.FId  |ID del campo di studi padre               |Int64      |Uguale a
FC.FN   |Nome del campo di studi figlio              |String     |Uguale a
FC.FId  |ID del campo di studi figlio                |Int64      |Uguale a
