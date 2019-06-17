---
title: Attributi dell'entità Affiliation nell'API Academic Knowledge
titlesuffix: Azure Cognitive Services
description: Informazioni sugli attributi che è possibile usare con l'entità Affiliation nell'API Academic Knowledge.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 82e6a5b66342e58e62da029d617cbd1d74c28149
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61340520"
---
# <a name="affiliation-entity"></a>Entità Affiliation

<sub> *Gli attributi seguenti sono specifici dell'entità Affiliation. (Ty = '5') </sub>

NOME    |Descrizione                            |Type       | Operazioni
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entità                              |Int64      |Uguale a
AfN     |Nome normalizzato dell'affiliazione        |String     |Uguale a
DAfN    |Nome visualizzato dell'affiliazione       |String     |Nessuno
CC      |Numero totale delle citazioni dell'affiliazione           |Int32      |Nessuno  
ECC     |Numero totale stimato delle citazioni dell'affiliazione |Int32      |Nessuno

## <a name="extended-metadata-attributes"></a>Attributi dei metadati estesi ##

Name    | Descrizione               
--------|---------------------------    
PC      |Numero dei documenti dell'affiliazione
