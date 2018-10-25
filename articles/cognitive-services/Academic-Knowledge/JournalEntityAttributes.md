---
title: Attributi dell'entità Journal - API Academic Knowledge
titlesuffix: Azure Cognitive Services
description: Informazioni sugli attributi che è possibile usare con l'entità Journal nell'API Academic Knowledge in Servizi cognitivi.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 45b2860b56e79c197d96998350131a142326157a
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902808"
---
# <a name="journal-entity"></a>Entità Journal

<sub> *Gli attributi seguenti sono specifici dell'entità Journal. (Ty = '2') </sub>

Nome    |Descrizione                            |Tipo       | Operazioni
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entità                              |Int64      |Uguale a
DJN     |Nome normalizzato del journal                |string     |Nessuno
JN      |Nome visualizzato del journal                   |string     |Uguale a
CC      |Numero totale delle citazioni del journal           |Int32      |Nessuno  
ECC     |Numero totale stimato delle citazioni del journal |Int32      |Nessuno