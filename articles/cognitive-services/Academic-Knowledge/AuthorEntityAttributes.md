---
title: Attributi dell'entità Author nell'API Academic Knowledge | Microsoft Docs
description: Informazioni sugli attributi che è possibile usare con l'entità Author nell'API Academic Knowledge in Servizi cognitivi.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: afcc3257c49522a4631c4aae0e0c2b88373f9af1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372700"
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