---
title: Attributi dell'entità Author - API Academic Knowledge
titlesuffix: Azure Cognitive Services
description: Informazioni sugli attributi che è possibile usare con l'entità Author nell'API Academic Knowledge.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 48758ac9ec8c993bbdb490229ae20fcce1fb0a49
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55175180"
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
