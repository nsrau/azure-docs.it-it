---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 5bde217601d27129e044b64d90184727ea717950
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66132886"
---
Gestione risorse di Azure permette di definire i parametri per i valori da specificare durante la distribuzione del modello. Il modello include una sezione denominata Parametri che contiene tutti i valori dei parametri.
È necessario definire un parametro per i valori che variano in base al progetto distribuito o all'ambiente in cui viene distribuito il progetto. Non definire i parametri per i valori che rimangono invariati. Ogni valore di parametro nel modello viene usato per definire le risorse distribuite. 

Durante la definizione dei parametri, usare il campo **allowedValues** per specificare i valori che l'utente può fornire durante la distribuzione. Usare il campo **defaultValue** per assegnare un valore al parametro, se non viene specificato alcun valore durante la distribuzione.

Di seguito è fornita la descrizione di ogni parametro del modello.

### <a name="sitename"></a>siteName
Il nome dell'app Web che si desidera creare.

    "siteName":{
      "type":"string"
    }

### <a name="hostingplanname"></a>hostingPlanName
Il nome del piano di servizio app da usare per l'hosting dell'app Web.

    "hostingPlanName":{
      "type":"string"
    }

### <a name="sku"></a>sku
Il piano tariffario del piano di hosting.

    "sku": {
      "type": "string",
      "allowedValues": [
        "F1",
        "D1",
        "B1",
        "B2",
        "B3",
        "S1",
        "S2",
        "S3",
        "P1",
        "P2",
        "P3",
        "P4"
      ],
      "defaultValue": "S1",
      "metadata": {
        "description": "The pricing tier for the hosting plan."
      }
    }

Il modello definisce i valori consentiti per questo parametro e assegna un valore predefinito (S1) se non viene specificato alcun valore.

### <a name="workersize"></a>workerSize
Le dimensioni delle istanze del piano di hosting (piccole, medie o grandi dimensioni).

    "workerSize":{
      "type":"string",
      "allowedValues":[
        "0",
        "1",
        "2"
      ],
      "defaultValue":"0"
    }

Il modello definisce i valori consentiti per questo parametro (0, 1 o 2) e assegna un valore predefinito (0) nel caso in cui non viene specificato alcun valore. I valori corrispondono a piccole, medie e grandi dimensioni.

