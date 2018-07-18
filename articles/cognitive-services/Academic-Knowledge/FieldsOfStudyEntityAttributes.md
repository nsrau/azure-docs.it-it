---
title: Attributi dell'entità Field of Study nell'API Academic Knowledge | Microsoft Docs
description: Informazioni sugli attributi che è possibile usare con l'entità Field of Study nell'API Academic Knowledge in Servizi cognitivi.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: a8176370f5b2f63b7741f7e892ed5a8c775f19c1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372708"
---
# <a name="field-of-study-entity"></a>Entità Field of Study

<sub> *Gli attributi seguenti sono specifici dell'entità Field of Study. (Ty = '6') </sub>

NOME    |DESCRIZIONE                            |type       | Operazioni
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