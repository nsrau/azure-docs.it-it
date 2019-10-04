---
title: Attributi dell'entità Author - API Academic Knowledge
titlesuffix: Azure Cognitive Services
description: Informazioni sugli attributi che è possibile usare con l'entità Author nell'API Academic Knowledge.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: e63e9d3f8f17a2473caf3d31b83e318ddb132b43
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705098"
---
# <a name="author-entity"></a>Entità Author
<sub> *Gli attributi seguenti sono specifici dell'entità Author. (Ty = '1') </sub>

NOME    |DESCRIZIONE                            |Type       | Operazioni
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entità                              |Int64      |Equals
AuN     |Nome normalizzato dell'autore                 |String     |Equals
DAuN    |Nome visualizzato dell'autore                    |String     |none
Cc      |Numero totale delle citazioni dell'autore            |Int32      |none  
ECC     |Numero totale stimato delle citazioni dell'autore  |Int32      |none
E       |Metadati estesi (vedere la tabella "Attributi dei metadati estesi")  |String     |none  


## <a name="extended-metadata-attributes"></a>Attributi dei metadati estesi ##

NOME    | DESCRIZIONE               
--------|---------------------------    
LKA.Afn     | Nome visualizzato dell'affiliazione associato all'autore  
LKA.AfId        | ID entità dell'affiliazione associato all'autore
