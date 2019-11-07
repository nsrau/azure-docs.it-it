---
title: Gestione della versione dei modelli
titleSuffix: Azure Cognitive Services
description: Specificare le versioni del modello negli endpoint V3
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/25/2019
ms.author: aahi
ms.openlocfilehash: 5a06e26e5f1640024e343c714db3df134422115c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488641"
---
La versione 3 dell'API Analisi del testo consente di scegliere il modello di Analisi del testo usato per i dati. Usare il parametro facoltativo `model-version` per selezionare una versione del modello nelle richieste. Se questo parametro non è specificato, l'API verrà impostato in modo predefinito su `latest`, ovvero la versione stabile più recente del modello.

Versioni del modello disponibili:
* `2019-10-01` (`latest`)

Ogni risposta dagli endpoint v3 include un campo `model-version` che specifica la versione del modello utilizzata.

```json
{
    “documents”: […]
    “errors”: []
    “model-version”: “2019-10-01”
}
```
