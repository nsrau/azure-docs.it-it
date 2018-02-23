---
title: Distribuire un modello di Azure Machine Learning in un dispositivo Azure IoT Edge | Microsoft Docs
description: Questo documento descrive come i modelli di Azure Machine Learning possono essere distribuiti nei dispositivi Azure IoT Edge.
services: machine-learning
author: tedway
ms.author: tedway
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 2/1/2018
ms.openlocfilehash: e33ff3fdc38b5aca6bbaef2c1ac6888217797541
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2018
---
# <a name="deploy-an-azure-machine-learning-model-to-an-azure-iot-edge-device"></a>Distribuire un modello di Azure Machine Learning in un dispositivo Azure IoT Edge

Tutti i modelli di Azure Machine Learning nei contenitori come i servizi Web basati su Docker possono essere eseguiti anche sui dispositivi Azure IoT Edge. Le istruzioni e gli script aggiuntivi sono riportati in [AI Toolkit for Azure IoT Edge](http://aka.ms/AI-toolkit).

## <a name="operationalize-the-model"></a>Operazionalizzare il modello
Rendere operativo il modello seguendo le istruzioni in [Distribuzione di un modello di Machine Learning come un servizio Web](model-management-service-deploy.md) per creare un'immagine Docker con il modello.

## <a name="deploy-to-azure-iot-edge"></a>Distribuire su Azure IoT Edge
Azure IoT Edge sposta nei dispositivi l'analisi cloud e la logica di business personalizzata. Tutti i modelli di Machine Learning possono essere eseguiti sui dispositivi IoT Edge. La documentazione per la configurazione di un dispositivo IoT Edge e per la creazione di una distribuzione è reperibile in [aka.ms/azure-iot-bordo-doc](https://aka.ms/azure-iot-edge-doc).

Di seguito sono indicati gli altri aspetti da osservare.

### <a name="add-registry-credentials-to-the-edge-runtime-on-your-edge-device"></a>Aggiungere le credenziali del registro al runtime di Edge nel dispositivo Edge
Nel computer in cui si sta eseguendo IoT Edge, aggiungere le credenziali del registro in modo che il runtime possa avere accesso per effettuare il pull del contenitore.

Per Windows, eseguire il comando seguente:
```cmd/sh
iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password>
```
Per Linux, eseguire il comando seguente:
```cmd/sh
sudo iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password>
```

### <a name="find-the-machine-learning-container-image-location"></a>Trovare il percorso dell'immagine del contenitore di Machine Learning
È necessario il percorso dell'immagine del contenitore di Machine Learning. Per trovare il percorso dell'immagine del contenitore:

1. Accedere al [portale di Azure](http://portal.azure.com/).
2. Nel pannello **Registro contenitori di Azure** selezionare il registro che si desidera esaminare.
3. Nel registro fare clic su **Repository** per vedere un elenco di tutti i repository e le relative immagini.













