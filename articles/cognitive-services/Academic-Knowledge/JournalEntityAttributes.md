---
title: Attributi dell'entità Journal - API Academic Knowledge
titlesuffix: Azure Cognitive Services
description: Informazioni sugli attributi che è possibile usare con l'entità Journal nell'API Academic Knowledge in Servizi cognitivi.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 93868bb014809693e7614e74bde7db864de95c04
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704954"
---
# <a name="journal-entity"></a>Entità Journal

<sub> *Gli attributi seguenti sono specifici dell'entità Journal. (Ty = '2') </sub>

NOME    |Descrizione                            |Type       | Operazioni
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entità                              |Int64      |Equals
DJN     |Nome normalizzato del journal                |String     |none
JN      |Nome visualizzato del journal                   |String     |Equals
Cc      |Numero totale delle citazioni del journal           |Int32      |none  
ECC     |Numero totale stimato delle citazioni del journal |Int32      |none
