---
title: Distribuire un modello di Azure Machine Learning in un dispositivo Azure IoT Edge | Microsoft Docs
description: Questo documento descrive come i modelli di Azure Machine Learning possono essere distribuiti nei dispositivi Azure IoT Edge.
services: machine-learning
author: tedway
ms.author: tedway
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 08/24/2018
ms.openlocfilehash: 792ac3f26bdea6c6ccb084d893925d60e6333edb
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2018
ms.locfileid: "51852454"
---
# <a name="deploy-an-azure-machine-learning-model-to-an-azure-iot-edge-device"></a>Distribuire un modello di Azure Machine Learning in un dispositivo Azure IoT Edge

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


I modelli di Azure Machine Learning possono essere nei contenitori come i servizi Web basati su Docker. Azure IoT Edge consente di distribuire contenitori in modalità remota nei dispositivi. Usare questi servizi insieme per eseguire i modelli nei dispositivi perimetrali per tempi di risposta più rapidi e per ridurre il trasferimento dei dati. 

Le istruzioni e gli script aggiuntivi sono riportati in [AI Toolkit for Azure IoT Edge](https://aka.ms/AI-toolkit).

## <a name="operationalize-the-model"></a>Operazionalizzare il modello

I moduli di Azure IoT Edge sono basati su immagini del contenitore. Per distribuire il modello di Machine Learning in un dispositivo IoT Edge, è necessario creare un'immagine Docker.

Rendere operativo il modello seguendo le istruzioni in [Distribuzione di un modello di Machine Learning come un servizio Web](model-management-service-deploy.md) per creare un'immagine Docker con il modello.

## <a name="deploy-to-azure-iot-edge"></a>Distribuire su Azure IoT Edge

Dopo aver creato l'immagine del modello, è possibile distribuirlo a tutti i dispositivi Azure IoT Edge. Tutti i modelli di Machine Learning possono essere eseguiti sui dispositivi IoT Edge. 

### <a name="set-up-an-iot-edge-device"></a>Configurare un dispositivo IoT Edge

Usare la documentazione di Azure IoT Edge per preparare un dispositivo. 

1. [Registrare un dispositivo con l'hub IoT di Azure](../../iot-edge/how-to-register-device-portal.md). L'output di questo processo è una stringa di connessione che è possibile usare per configurare il dispositivo fisico. 
2. Installare il runtime IoT Edge nel dispositivo fisico e configurarlo con una stringa di connessione. È possibile installare il runtime nei dispositivi [Windows](../../iot-edge/how-to-install-iot-edge-windows-with-windows.md) oppure [Linux](../../iot-edge/how-to-install-iot-edge-linux.md).  


### <a name="find-the-machine-learning-container-image-location"></a>Trovare il percorso dell'immagine del contenitore di Machine Learning
È necessario il percorso dell'immagine del contenitore di Machine Learning. Per trovare il percorso dell'immagine del contenitore:

1. Accedere al [portale di Azure](http://portal.azure.com/).
2. Nel pannello **Registro contenitori di Azure** selezionare il registro che si desidera esaminare.
3. Nel registro fare clic su **Repository** per vedere un elenco di tutti i repository e le relative immagini.

Mentre si esamina il registro contenitori nel portale di Azure, recuperare le credenziali del registro contenitori. Queste credenziali devono essere inviate al dispositivo IoT Edge in modo che sia possibile caricare l'immagine dal registro privato. 

1. Nel registro contenitori, fare clic su **Chiavi di accesso**. 
2. **Abilitare** l'utente amministratore, se non lo è già. 
3. Salvare i valori nei campi **Server di accesso**, **Nome utente** e **Password**. 

### <a name="deploy-the-container-image-to-your-device"></a>Distribuire il contenitore dell'immagine nel dispositivo

Con l'immagine del contenitore e le credenziali del registro contenitori, si è pronti per distribuire il modello di machine learning nel dispositivo IoT Edge. 

Seguire le istruzioni in [moduli di distribuzione di IoT Edge dal portale di Azure](../../iot-edge/how-to-deploy-modules-portal.md) per avviare il modello nel dispositivo IoT Edge. 











