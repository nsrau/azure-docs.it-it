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
ms.openlocfilehash: 183a307159adb5dfdb248eb0cf4862462a626db6
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55879191"
---
# <a name="conference-instance-entity"></a>Entità Conference Instance

<sub> *Gli attributi seguenti sono specifici dell'entità Conference Instance. (Ty = '4') </sub>

NOME    |DESCRIZIONE                            |Type       | Operazioni
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entità                              |Int64      |Uguale a
CIN     |Nome normalizzato dell'istanza di conferenza ({ConferenceSeriesNormalizedName} {ConferenceInstanceYear})        |string     |Uguale a
DCN     |Nome visualizzato dell'istanza di conferenza ({ConferenceSeriesNormalizedName} {ConferenceInstanceYear})       |string     |Nessuno
CIL     |Posizione dell'istanza di conferenza    |string     |Uguale a<br/>StartsWith
CISD    |Data di inizio dell'istanza di conferenza  |Data       |Uguale a<br/>IsBetween
CIED    |Data di fine dell'istanza di conferenza    |Data       |Uguale a<br/>IsBetween
CIARD   |Scadenza della registrazione astratta dell'istanza di conferenza  |Data       |Uguale a<br/>IsBetween
CISDD   |Scadenza dell'invio dell'istanza di conferenza     |Data       |Uguale a<br/>IsBetween
CIFVD   |Scadenza della versione finale dell'istanza di conferenza  |Data       |Uguale a<br/>IsBetween
CINDD   |Data di notifica dell'istanza di conferenza   |Data       |Uguale a<br/>IsBetween
CD.T    |Titolo di un evento dell'istanza di conferenza   |Data       |Uguale a<br/>IsBetween
CD.D    |Data di un evento dell'istanza di conferenza    |Data       |Uguale a<br/>IsBetween
PCS.CN  |Nome della serie di conferenze dell'istanza |string     |Uguale a
PCS.CId |ID della serie di conferenze dell'istanza |Int64    |Uguale a
CC      |Numero totale delle citazioni dell'istanza di conferenza           |Int32      |Nessuno  
ECC     |Numero totale stimato delle citazioni dell'istanza di conferenza |Int32      |Nessuno


## <a name="extended-metadata-attributes"></a>Attributi dei metadati estesi ##

NOME    | DESCRIZIONE               
--------|---------------------------    
FN      | Nome completo dell'istanza di conferenza
