---
title: File di inclusione
description: File di inclusione
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: 80503ad154a9fc4d01614ffd2816f9d5fd497fdb
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228890"
---
<a name="paths"></a>
## <a name="paths"></a>Percorsi

<a name="anomalydetection-post"></a>
### <a name="detect-anomaly-points-for-the-time-series-data-points-requested"></a>Rilevare i punti di anomalia per i punti dati delle serie temporali richiesti
```
POST /anomalydetection
```


#### <a name="parameters"></a>Parametri

|Type|NOME|DESCRIZIONE|SCHEMA|
|---|---|---|---|
|**Corpo**|**body**  <br>*obbligatorio*|I punti dati delle serie temporali e il periodo, se necessario.|[request](#request)|


#### <a name="responses"></a>Risposte

|Codice HTTP|DESCRIZIONE|SCHEMA|
|---|---|---|
|**200**|Operazione riuscita.|< [risposta](#response) > matrice|
|**400**|Impossibile analizzare la richiesta JSON.|Nessun contenuto|
|**403**|Il certificato fornito non è accettato dal server.|Nessun contenuto|
|**405**|Metodo non consentito|Nessun contenuto|
|**500**|Errore interno del server.|Nessun contenuto|


#### <a name="consumes"></a>Utilizza

* `application/json`


#### <a name="produces"></a>Genera

* `application/json`


#### <a name="tags"></a>Tag

* anomalydetection



