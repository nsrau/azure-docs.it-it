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
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878936"
---
# <a name="author-entity"></a>Entità Author
<sub> *Gli attributi seguenti sono specifici dell'entità Author. (Ty = '1') </sub>

NOME    |DESCRIZIONE                            |Type       | Operazioni
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entità                              |Int64      |Uguale a
AuN     |Nome normalizzato dell'autore                 |string     |Uguale a
DAuN    |Nome visualizzato dell'autore                    |string     |Nessuno
CC      |Numero totale delle citazioni dell'autore            |Int32      |Nessuno  
ECC     |Numero totale stimato delle citazioni dell'autore  |Int32      |Nessuno
E       |Metadati estesi (vedere la tabella "Attributi dei metadati estesi")  |string     |Nessuno  


## <a name="extended-metadata-attributes"></a>Attributi dei metadati estesi ##

NOME    | DESCRIZIONE               
--------|---------------------------    
LKA.Afn     | Nome visualizzato dell'affiliazione associato all'autore  
LKA.AfId        | ID entità dell'affiliazione associato all'autore
