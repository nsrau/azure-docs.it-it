---
title: Attributi dell'entità Affiliation nell'API Academic Knowledge
titlesuffix: Azure Cognitive Services
description: Informazioni sugli attributi che è possibile usare con l'entità Affiliation nell'API Academic Knowledge.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 177fe9da8bbe821a69eae02d89a225e5d4009331
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900479"
---
# <a name="affiliation-entity"></a>Entità Affiliation

<sub> *Gli attributi seguenti sono specifici dell'entità Affiliation. (Ty = '5') </sub>

Nome    |Descrizione                            |Tipo       | Operazioni
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entità                              |Int64      |Uguale a
AfN     |Nome normalizzato dell'affiliazione        |string     |Uguale a
DAfN    |Nome visualizzato dell'affiliazione       |string     |Nessuno
CC      |Numero totale delle citazioni dell'affiliazione           |Int32      |Nessuno  
ECC     |Numero totale stimato delle citazioni dell'affiliazione |Int32      |Nessuno

## <a name="extended-metadata-attributes"></a>Attributi dei metadati estesi ##

Nome    | Descrizione               
--------|---------------------------    
PC      |Numero dei documenti dell'affiliazione