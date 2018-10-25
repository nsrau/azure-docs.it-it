---
title: Attributi dell'entità Author - API Academic Knowledge
titlesuffix: Azure Cognitive Services
description: Informazioni sugli attributi che è possibile usare con l'entità Author nell'API Academic Knowledge.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 62e6da6d558a0494fb83115b1e307081099399d4
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900690"
---
# <a name="author-entity"></a>Entità Author
<sub> *Gli attributi seguenti sono specifici dell'entità Author. (Ty = '1') </sub>

Nome    |Descrizione                            |Tipo       | Operazioni
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entità                              |Int64      |Uguale a
AuN     |Nome normalizzato dell'autore                 |string     |Uguale a
DAuN    |Nome visualizzato dell'autore                    |string     |Nessuno
CC      |Numero totale delle citazioni dell'autore            |Int32      |Nessuno  
ECC     |Numero totale stimato delle citazioni dell'autore  |Int32      |Nessuno
E       |Metadati estesi (vedere la tabella "Attributi dei metadati estesi")  |string     |Nessuno  


## <a name="extended-metadata-attributes"></a>Attributi dei metadati estesi ##

Nome    | Descrizione               
--------|---------------------------    
LKA.Afn     | Nome visualizzato dell'affiliazione associato all'autore  
LKA.AfId        | ID entità dell'affiliazione associato all'autore