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
ROBOTS: NOINDEX
ms.openlocfilehash: 333875472d9b859196c4d828061301b9d32c4d5a
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705107"
---
# <a name="affiliation-entity"></a>Entità Affiliation

<sub> *Gli attributi seguenti sono specifici dell'entità Affiliation. (Ty = '5') </sub>

NOME    |Descrizione                            |Type       | Operazioni
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entità                              |Int64      |Equals
AfN     |Nome normalizzato dell'affiliazione        |String     |Equals
DAfN    |Nome visualizzato dell'affiliazione       |String     |none
Cc      |Numero totale delle citazioni dell'affiliazione           |Int32      |none  
ECC     |Numero totale stimato delle citazioni dell'affiliazione |Int32      |none

## <a name="extended-metadata-attributes"></a>Attributi dei metadati estesi ##

Name    | Descrizione               
--------|---------------------------    
PC      |Numero dei documenti dell'affiliazione
