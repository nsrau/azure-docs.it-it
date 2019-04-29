---
title: Distribuire moduli da Visual Studio Code - Azure IoT Edge | Microsoft Docs
description: Usare Visual Studio Code per distribuire i moduli in un dispositivo IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 91a074cf98291b105864a69730314efff3482254
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126410"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Distribuire i moduli di Azure IoT Edge da Visual Studio Code

Dopo aver creato i moduli di IoT Edge in base alla propria logica di business, si distribuiscono i moduli nei dispositivi per consentirne l'uso a livello perimetrale. Se si hanno più moduli che interagiscono per raccogliere ed elaborare dati, è possibile distribuirli contemporaneamente e dichiarare le regole di routing che li connettono. 

Questo articolo illustra come creare un manifesto della distribuzione JSON e quindi usare tale file per eseguire il push della distribuzione in un dispositivo IoT Edge. Per informazioni sulla creazione di una distribuzione da assegnare a più dispositivi in base ai relativi tag condivisi, vedere [Distribuire e monitorare i moduli di IoT Edge su larga scala](how-to-deploy-monitor.md).

## <a name="prerequisites"></a>Prerequisiti

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) nella sottoscrizione di Azure. 
* Un [dispositivo IoT Edge](how-to-register-device-portal.md) con il runtime di IoT Edge installato. 
* [Visual Studio Code](https://code.visualstudio.com/).
* [Strumenti di Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) per Visual Studio Code.

## <a name="configure-a-deployment-manifest"></a>Configurare un manifesto della distribuzione

Un manifesto della distribuzione è un documento JSON contenente la descrizione dei moduli da distribuire, dei flussi di dati esistenti tra i moduli e delle proprietà desiderate dei moduli gemelli. Per altre informazioni sul funzionamento e sulla modalità di creazione dei manifesti della distribuzione, vedere [Informazioni su come usare, configurare e riusare i moduli IoT Edge](module-composition.md).

Per distribuire i moduli tramite Visual Studio Code, salvare il manifesto della distribuzione a livello locale come file con estensione JSON. Il percorso del file verrà usato nella sezione successiva quando si eseguirà il comando per applicare la configurazione al dispositivo.

Di seguito è riportato un esempio di manifesto della distribuzione di base con un solo modulo:

   ```json
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {}
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                 "createOptions": "{}"
               }
             }
           },
           "modules": {
             "tempSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
               "route": "FROM /* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "tempSensor": {
         "properties.desired": {}
       }
     }
   }
   ```
   
## <a name="sign-in-to-access-your-iot-hub"></a>Eseguire l'accesso all'hub IoT

Per eseguire operazioni con l'hub IoT, è possibile usare le estensioni Azure IoT per Visual Studio Code. Per il corretto funzionamento di queste operazioni è necessario accedere all'account Azure e selezionare l'hub IoT in cui si sta lavorando.

1. In Visual Studio Code aprire la **finestra di esplorazione**.

2. Nella parte inferiore della finestra di esplorazione espandere la sezione **Azure IoT Hub Devices** (Dispositivi hub IoT di Azure). 

   ![Espandere la sezione Azure IoT Hub Devices (Dispositivi dell'Hub IoT di Azure).](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

3. Fare clic sui puntini di sospensione (**...**) nell'intestazione della sezione **Azure IoT Hub Devices** (Dispositivi hub IoT di Azure). Se i puntini di sospensione non sono visibili, passare il puntatore sull'intestazione. 

4. Scegliere **Select IoT Hub** (Seleziona l'hub IoT).

5. Se ancora non lo si è fatto, eseguire l'accesso all'account di Azure seguendo le istruzioni. 

6. Selezionare la sottoscrizione di Azure. 

7. Selezionare l'hub IoT. 


## <a name="deploy-to-your-device"></a>Eseguire la distribuzione nel dispositivo

I moduli vengono distribuiti nel dispositivo applicando il manifesto della distribuzione configurato con le informazioni relative ai moduli. 

1. Nella visualizzazione di esplorazione di Visual Studio Code espandere la sezione **Azure IoT Hub Devices** (Dispositivi dell'Hub IoT di Azure). 

1. Fare clic con il pulsante destro del mouse sul dispositivo IoT Edge che si vuole configurare con il manifesto di distribuzione.

    > [!TIP]
    > Per controllare di aver scelto un dispositivo IoT Edge, selezionarlo per espandere l'elenco dei moduli e verificare la presenza di **$edgeHub** e **$edgeAgent**. Ogni dispositivo IoT Edge include questi due moduli.

1. Selezionare **Create Deployment for Single Device** (Crea la distribuzione per un unico dispositivo).

4. Passare al file JSON del manifesto di distribuzione che si vuole usare e fare clic su **Select Edge deployment manifest** (Seleziona il manifesto della distribuzione di Edge). 

   ![Selezionare il manifesto della distribuzione di Edge](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)


I risultati della distribuzione vengono stampati nell'output di VS Code. Le distribuzioni corrette vengono applicate entro pochi minuti se il dispositivo di destinazione è in esecuzione e connesso a Internet. 

## <a name="view-modules-on-your-device"></a>Visualizzare i moduli nel dispositivo

Dopo aver distribuito i moduli nel dispositivo, è possibile visualizzarli tutti nella sezione **Azure IoT Hub Devices** (Dispositivi dell'Hub IoT di Azure). Selezionare la freccia accanto al dispositivo IoT Edge per espanderlo. Vengono visualizzati tutti i moduli attualmente in esecuzione. 

Se sono stati distribuiti di recente nuovi moduli in un dispositivo, passare il mouse sull'intestazione della sezione **Azure IoT Hub Devices** (Dispositivi dell'Hub IoT di Azure) e selezionare l'icona di aggiornamento per aggiornare la visualizzazione. 

Fare clic con il pulsante destro del mouse sul nome di un modulo per visualizzare e modificare il modulo gemello. 

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [distribuire e monitorare i moduli di IoT Edge su larga scala](how-to-deploy-monitor.md)