---
title: Attributi dell'entità Field of Study - API Academic Knowledge
titlesuffix: Azure Cognitive Services
description: Informazioni sugli attributi che è possibile usare con l'entità Field of Study nell'API Academic Knowledge.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: 793b35d9c6412c40a87f3f91fcd772476d57584f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55154568"
---
# <a name="field-of-study-entity"></a>Entità Field of Study

<sub> *Gli attributi seguenti sono specifici dell'entità Field of Study. (Ty = '6') </sub>

NOME    |DESCRIZIONE                            |Type       | Operazioni
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entità                              |Int64      |Uguale a
FN      |Nome normalizzato del campo di studi         |string     |Uguale a
DFN     |Nome visualizzato del campo di studi            |string     |Nessuno
CC      |Numero totale delle citazioni del campo di studi    |Int32      |Nessuno  
ECC     |Numero totale stimato delle citazioni del campo di studi|Int32      |Nessuno
FL      |Livello nella gerarchia del campo di studi     |Int32      |Uguale a <br/>IsBetween
FP.FN   |Nome del campo di studi padre             |string     |Uguale a
FP.FId  |ID del campo di studi padre               |Int64      |Uguale a
FC.FN   |Nome del campo di studi figlio              |string     |Uguale a
FC.FId  |ID del campo di studi figlio                |Int64      |Uguale a
