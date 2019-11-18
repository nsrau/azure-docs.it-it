---
title: Attributi dell'entità Author - API Academic Knowledge
titlesuffix: Azure Cognitive Services
description: Informazioni sugli attributi che è possibile usare con l'entità Author nell'API Academic Knowledge.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: d5fc770c380397f605f8810fa41d3a8907f2358e
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146501"
---
# <a name="author-entity"></a>Entità Author

> [!NOTE]
> Gli attributi seguenti sono specifici dell'entità Author. (Ty = "1")

Nome | DESCRIZIONE | digitare | Operazioni
--- | --- | --- | ---
id      | ID entità                             |Int64      |Uguale a
AuN     | Nome normalizzato dell'autore                    |String     |Uguale a
CC      | Numero totale delle citazioni dell'autore           |Int32      |nessuno  
DAuN    | Nome visualizzato dell'autore                   |String     |nessuno
E | Metadati estesi</br></br>**Importante**: questo attributo è stato deprecato ed è supportato solo per le applicazioni legacy. La richiesta di questo attributo singolarmente (ad esempio, Attributes = ID, ti, E) comporterà la restituzione di tutti gli attributi di metadati estesi in una *stringa JSON serializzata*</br></br>Tutti gli attributi contenuti nei metadati estesi sono ora disponibili come attributo di primo livello e possono essere richiesti come tali, ad esempio attributi = ID, ti, DOI, IA | [Estesa](#extended) | nessuno
ECC     | Numero totale stimato delle citazioni dell'autore |Int32      |nessuno
LKA.AfId | ID entità dell'ultima affiliazione nota trovata per l'autore | Int64 | nessuno
LKA. AfN | Nome normalizzato dell'ultima affiliazione nota trovata per l'autore | String | nessuno
PC | Numero totale pubblicazioni autore | Int32 | nessuno

## <a name="extended"></a>Estesa

> [!IMPORTANT]
> Questo attributo è stato deprecato ed è supportato solo per le applicazioni legacy. La richiesta di questo attributo singolarmente (ad esempio, Attributes = ID, ti, E) comporterà la restituzione di tutti gli attributi di metadati estesi in una *stringa JSON serializzata*</br></br>Tutti gli attributi contenuti nei metadati estesi sono ora disponibili come attributo di primo livello e possono essere richiesti come tali, ad esempio attributi = ID, ti, DOI, IA

> [!IMPORTANT]
> Supporto per la richiesta di singoli attributi estesi utilizzando "E". l'ambito, ovvero "E. DN", verrà deprecato. Sebbene questo sia ancora tecnicamente supportato, richiedendo singoli attributi estesi utilizzando "E". l'ambito comporterà la restituzione del valore dell'attributo in due posizioni nella risposta JSON, come parte dell'oggetto "E" e come attributo di primo livello.

Nome | DESCRIZIONE | digitare | Operazioni
--- | --- | --- | ---
LKA.AfId | ID entità dell'ultima affiliazione nota trovata per l'autore | Int64 | nessuno
LKA. AfN | Nome normalizzato dell'ultima affiliazione nota trovata per l'autore | String | nessuno
PC | Numero totale pubblicazioni autore | Int32 | nessuno
