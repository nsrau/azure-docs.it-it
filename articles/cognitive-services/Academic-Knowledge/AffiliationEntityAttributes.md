---
title: Attributi dell'entità Affiliation nell'API Academic Knowledge | Microsoft Docs
description: Informazioni sugli attributi che è possibile usare con l'entità Affiliation nell'API Academic Knowledge in Servizi cognitivi.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: a0ec67cb811ca207b3d038028491da2516028f0b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372697"
---
# <a name="affiliation-entity"></a>Entità Affiliation

<sub> *Gli attributi seguenti sono specifici dell'entità Affiliation. (Ty = '5') </sub>

NOME    |DESCRIZIONE                            |type       | Operazioni
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