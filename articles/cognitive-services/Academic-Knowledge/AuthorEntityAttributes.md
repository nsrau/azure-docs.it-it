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
ms.openlocfilehash: d4b33c06ab023023aadf403cf0ef0b08c2bafc5f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60609751"
---
# <a name="author-entity"></a>Entità Author
<sub> *Gli attributi seguenti sono specifici dell'entità Author. (Ty = '1') </sub>

Name    |Descrizione                            |Type       | Operazioni
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entità                              |Int64      |Uguale a
AuN     |Nome normalizzato dell'autore                 |String     |Uguale a
DAuN    |Nome visualizzato dell'autore                    |String     |Nessuno
CC      |Numero totale delle citazioni dell'autore            |Int32      |Nessuno  
ECC     |Numero totale stimato delle citazioni dell'autore  |Int32      |Nessuno
E       |Metadati estesi (vedere la tabella "Attributi dei metadati estesi")  |String     |Nessuno  


## <a name="extended-metadata-attributes"></a>Attributi dei metadati estesi ##

Name    | Descrizione               
--------|---------------------------    
LKA.Afn     | Nome visualizzato dell'affiliazione associato all'autore  
LKA.AfId        | ID entità dell'affiliazione associato all'autore
