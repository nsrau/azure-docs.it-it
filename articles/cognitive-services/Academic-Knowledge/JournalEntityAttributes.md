---
title: Attributi dell'entità Journal nell'API Academic Knowledge | Microsoft Docs
description: Informazioni sugli attributi che è possibile usare con l'entità Journal nell'API Academic Knowledge in Servizi cognitivi.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: e782c57b8ac57028e070c16382c53ec76666e94d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372689"
---
# <a name="journal-entity"></a>Entità Journal

<sub> *Gli attributi seguenti sono specifici dell'entità Journal. (Ty = '2') </sub>

NOME    |DESCRIZIONE                            |type       | Operazioni
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entità                              |Int64      |Uguale a
DJN     |Nome normalizzato del journal                |string     |Nessuno
JN      |Nome visualizzato del journal                   |string     |Uguale a
CC      |Numero totale delle citazioni del journal           |Int32      |Nessuno  
ECC     |Numero totale stimato delle citazioni del journal |Int32      |Nessuno