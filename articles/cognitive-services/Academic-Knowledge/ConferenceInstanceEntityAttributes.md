---
title: Attributi dell'entità Conference Instance - API Academic Knowledge
titlesuffix: Azure Cognitive Services
description: Informazioni sugli attributi che è possibile usare con l'entità Conference Instance nell'API Academic Knowledge.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: c6761206a58724dae96d9dc6f6234658892d4d18
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146572"
---
# <a name="conference-instance-entity"></a>Entità Conference Instance

> [!NOTE]
> Gli attributi seguenti sono specifici dell'entità dell'istanza di conferenza. (Ty =' 4')

Nome | DESCRIZIONE | digitare | Operazioni
--- | --- | --- | ---
CC      |Numero totale delle citazioni dell'istanza di conferenza           |Int32      |nessuno  
CD.D    |Data di un evento dell'istanza di conferenza    |Date       |Equals, tra
CD.T    |Titolo di un evento dell'istanza di conferenza   |Date       |Equals, tra
CIARD   |Scadenza della registrazione astratta dell'istanza di conferenza  |Date       |Equals, tra
CIED    |Data di fine dell'istanza di conferenza    |Date       |Equals, tra
CIFVD   |Scadenza della versione finale dell'istanza di conferenza  |Date       |Equals, tra
CIL     |Posizione dell'istanza di conferenza    |String     |Uguale a, StartsWith
CIN     |Nome normalizzato dell'istanza di conferenza |String        |Uguale a
CINDD   |Data di notifica dell'istanza di conferenza   |Date       |Equals, tra
CISD    |Data di inizio dell'istanza di conferenza  |Date       |Equals, tra
CISDD   |Scadenza dell'invio dell'istanza di conferenza     |Date       |Equals, tra
DCN     |Nome visualizzato dell'istanza di conferenza  |String      |nessuno
E | Metadati estesi</br></br>**Importante**: questo attributo è stato deprecato ed è supportato solo per le applicazioni legacy. La richiesta di questo attributo singolarmente (ad esempio, Attributes = ID, ti, E) comporterà la restituzione di tutti gli attributi di metadati estesi in una *stringa JSON serializzata*</br></br>Tutti gli attributi contenuti nei metadati estesi sono ora disponibili come attributo di primo livello e possono essere richiesti come tali, ad esempio attributi = ID, ti, DOI, IA | [Estesa](#extended) | nessuno
ECC     |Numero totale stimato delle citazioni dell'istanza di conferenza |Int32      |nessuno
FN | Nome completo dell'istanza di conferenza | String | nessuno
id      |ID entità                              |Int64      |Uguale a
PC | Numero totale pubblicazioni istanze conferenza | Int32 | nessuno
PCS.CN  |Nome della serie di conferenze padre dell'istanza |String  |Uguale a
PCS.CId |ID della serie di conferenze padre dell'istanza |Int64     |Uguale a

## <a name="extended"></a>Estesa

> [!IMPORTANT]
> Questo attributo è stato deprecato ed è supportato solo per le applicazioni legacy. La richiesta di questo attributo singolarmente (ad esempio, Attributes = ID, ti, E) comporterà la restituzione di tutti gli attributi di metadati estesi in una *stringa JSON serializzata*</br></br>Tutti gli attributi contenuti nei metadati estesi sono ora disponibili come attributo di primo livello e possono essere richiesti come tali, ad esempio attributi = ID, ti, DOI, IA

> [!IMPORTANT]
> Supporto per la richiesta di singoli attributi estesi utilizzando "E". l'ambito, ovvero "E. DN", verrà deprecato. Sebbene questo sia ancora tecnicamente supportato, richiedendo singoli attributi estesi utilizzando "E". l'ambito comporterà la restituzione del valore dell'attributo in due posizioni nella risposta JSON, come parte dell'oggetto "E" e come attributo di primo livello.

Nome | DESCRIZIONE | digitare | Operazioni
--- | --- | --- | ---
FN | Nome completo dell'istanza di conferenza | String | nessuno
PC | Numero totale pubblicazioni istanze conferenza | Int32 | nessuno
