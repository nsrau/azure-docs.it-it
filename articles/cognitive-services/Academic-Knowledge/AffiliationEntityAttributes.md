---
title: Attributi dell'entità Affiliation nell'API Academic Knowledge
titlesuffix: Azure Cognitive Services
description: Informazioni sugli attributi che è possibile usare con l'entità Affiliation nell'API Academic Knowledge.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 344b26b16f74cd44982e3c93fa69295792daa9a0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55190634"
---
# <a name="affiliation-entity"></a>Entità Affiliation

<sub> *Gli attributi seguenti sono specifici dell'entità Affiliation. (Ty = '5') </sub>

NOME    |DESCRIZIONE                            |Type       | Operazioni
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entità                              |Int64      |Uguale a
AfN     |Nome normalizzato dell'affiliazione        |string     |Uguale a
DAfN    |Nome visualizzato dell'affiliazione       |string     |Nessuno
CC      |Numero totale delle citazioni dell'affiliazione           |Int32      |Nessuno  
ECC     |Numero totale stimato delle citazioni dell'affiliazione |Int32      |Nessuno

## <a name="extended-metadata-attributes"></a>Attributi dei metadati estesi ##

NOME    | DESCRIZIONE               
--------|---------------------------    
PC      |Numero dei documenti dell'affiliazione
