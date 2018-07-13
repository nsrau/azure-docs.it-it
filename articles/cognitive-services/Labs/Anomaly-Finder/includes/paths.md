---
title: File di inclusione
description: File di inclusione
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: a806cac410eb57e59dacb42da9be954b2f962956
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35375337"
---
<a name="paths"></a>
## <a name="paths"></a>Percorsi

<a name="anomalydetection-post"></a>
### <a name="detect-anomaly-points-for-the-time-series-data-points-requested"></a>Rilevare i punti di anomalia per i punti dati delle serie temporali richiesti
```
POST /anomalydetection
```


#### <a name="parameters"></a>Parametri

|type|NOME|DESCRIZIONE|SCHEMA|
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



