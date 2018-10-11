---
title: File di inclusione
description: File di inclusione
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: fb02496d9c107a2c21acca6c65ef69fdfceb4597
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2018
ms.locfileid: "48904644"
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



