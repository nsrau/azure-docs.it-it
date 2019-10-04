---
title: Attributi dell'entità Conference Instance - API Academic Knowledge
titlesuffix: Azure Cognitive Services
description: Informazioni sugli attributi che è possibile usare con l'entità Conference Instance nell'API Academic Knowledge.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 37a353fbb86ca199b2316dcfba5904f4b46b0276
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705067"
---
# <a name="conference-instance-entity"></a>Entità Conference Instance

<sub> *Gli attributi seguenti sono specifici dell'entità Conference Instance. (Ty = '4') </sub>

NOME    |DESCRIZIONE                            |Type       | Operazioni
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entità                              |Int64      |Equals
CIN     |Nome normalizzato dell'istanza di conferenza ({ConferenceSeriesNormalizedName} {ConferenceInstanceYear})        |String     |Equals
DCN     |Nome visualizzato dell'istanza di conferenza ({ConferenceSeriesNormalizedName} {ConferenceInstanceYear})       |String     |none
CIL     |Posizione dell'istanza di conferenza    |String     |Uguale a<br/>StartsWith
CISD    |Data di inizio dell'istanza di conferenza  |Date       |Uguale a<br/>IsBetween
CIED    |Data di fine dell'istanza di conferenza    |Date       |Uguale a<br/>IsBetween
CIARD   |Scadenza della registrazione astratta dell'istanza di conferenza  |Date       |Uguale a<br/>IsBetween
CISDD   |Scadenza dell'invio dell'istanza di conferenza     |Date       |Uguale a<br/>IsBetween
CIFVD   |Scadenza della versione finale dell'istanza di conferenza  |Date       |Uguale a<br/>IsBetween
CINDD   |Data di notifica dell'istanza di conferenza   |Date       |Uguale a<br/>IsBetween
CD.T    |Titolo di un evento dell'istanza di conferenza   |Date       |Uguale a<br/>IsBetween
CD.D    |Data di un evento dell'istanza di conferenza    |Date       |Uguale a<br/>IsBetween
PCS.CN  |Nome della serie di conferenze dell'istanza |String     |Equals
PCS.CId |ID della serie di conferenze dell'istanza |Int64    |Equals
Cc      |Numero totale delle citazioni dell'istanza di conferenza           |Int32      |none  
ECC     |Numero totale stimato delle citazioni dell'istanza di conferenza |Int32      |none


## <a name="extended-metadata-attributes"></a>Attributi dei metadati estesi ##

NOME    | Descrizione               
--------|---------------------------    
FN      | Nome completo dell'istanza di conferenza
