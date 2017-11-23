---
title: Simulare Azure IoT Edge in Windows | Microsoft Docs
description: Installare il runtime di Azure IoT Edge su un dispositivo simulato in Windows e distribuire il primo modulo
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 08c501b9132bb21f47f099725d1fad5556befb4c
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-windows----preview"></a>Distribuire Azure IoT Edge su un dispositivo simulato in Windows: anteprima

Azure IoT Edge sposta la potenza del cloud sui dispositivi di Internet delle cose. Questa esercitazione illustrata la creazione di un dispositivo IoT Edge simulato che genera i dati del sensore. Si apprenderà come:

![Architettura dell'esercitazione][2]

Il dispositivo simulato creato in questa esercitazione è un monitor in turbina eolica che genera dati di temperatura, umidità e pressione. L'utente è interessato a questo tipo di dati poiché le turbine funzionano a diversi livelli di efficienza, a seconda delle condizioni meteorologiche. Le altre esercitazioni di Azure IoT Edge si basano sulle operazioni eseguite qui tramite la distribuzione di moduli che consentono di analizzare i dati per ottenere informazioni aziendali accurate. 

## <a name="prerequisites"></a>Prerequisiti

Questa esercitazione presuppone che si usi un computer o una macchina virtuale che esegue Windows per simulare un dispositivo di Internet delle cose. 

>[!TIP]
>Se si esegue Windows in una macchina virtuale, abilitare la [virtualizzazione annidata][lnk-nested] e allocare almeno 2 GB di memoria. 

1. Assicurarsi di usare una versione di Windows supportata:
   * Windows 10 
   * Windows Server
2. Installare [Docker per Windows][lnk-docker] e assicurarsi che sia in esecuzione.
3. Installare [Python 2.7 su Windows][lnk-python] e assicurarsi di usare il comando pip.
4. Eseguire il comando seguente per scaricare lo script di controllo di IoT Edge.

   ```
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> Azure IoT Edge può eseguire contenitori Windows o contenitori Linux. Per usare contenitori Windows è necessario eseguire:
>    * Windows 10 Fall Creators Update o
>    * Windows Server 1709 (Build 16299) oppure
>    * Windows IoT Core (Build 16299) su un dispositivo basato su x64
>
> Per Windows IoT Core, seguire le istruzioni disponibili in [Installare il runtime di IoT Edge su Windows IoT Core][lnk-install-iotcore]. In caso contrario, è sufficiente [configurare Docker per l'uso dei contenitori Windows][lnk-docker-containers] e facoltativamente convalidare i prerequisiti con il comando di PowerShell seguente:
>    ```
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```


## <a name="create-an-iot-hub"></a>Creare un hub IoT

Avviare l'esercitazione creando l'hub IoT.
![Creare l'hub IoT][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Registrare un dispositivo IoT Edge

Registrare un dispositivo IoT Edge con l'hub IoT appena creato.
![Registrare un dispositivo][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>Configurare il runtime di IoT Edge

Installare e avviare il runtime di Azure IoT Edge sul dispositivo. 
![Registrare un dispositivo][5]

Il runtime di IoT Edge viene distribuito in tutti i dispositivi IoT Edge. È costituito da due moduli. L'agente di IoT Edge prima di tutto semplifica la distribuzione e il monitoraggio dei moduli nel dispositivo IoT Edge. L'hub IoT Edge gestisce quindi le comunicazioni tra i moduli nel dispositivo IoT Edge e tra il dispositivo e l'hub IoT. 


Usare la procedura seguente per installare e avviare il runtime di IoT Edge:

1. Configurare il runtime con la stringa di connessione al dispositivo IoT Edge dalla sezione precedente.

   ```
   iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
   ```

1. Avviare il runtime.

   ```
   iotedgectl start
   ```

1. Controllare Docker per verificare che l'agente di IoT Edge sia in esecuzione come modulo.

   ```
   docker ps
   ```

## <a name="deploy-a-module"></a>Distribuire un modulo

Gestire il dispositivo di Azure IoT Edge dal cloud per la distribuzione di un modulo che invierà i dati di telemetria all'hub IoT.
![Registrare un dispositivo][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]


## <a name="view-generated-data"></a>Visualizzare i dati generati

Nella guida introduttiva è stato creato un nuovo dispositivo IoT Edge e in tale dispositivo è stato installato il runtime di IoT Edge. È stato quindi usato il portale di Azure per eseguire il push di un modulo di IoT Edge per l'esecuzione nel dispositivo senza dovere apportare modifiche al dispositivo stesso. In questo caso il modulo di cui è stato eseguito il push crea dati ambientali che è possibile usare per le esercitazioni. 

Visualizzare i messaggi inviati dal modulo tempSensor:

```cmd/sh
sudo docker logs -f tempSensor
```

È anche possibile visualizzare i dati di telemetria inviati dal dispositivo tramite lo [strumento di esplorazione dell'hub IoT][lnk-iothub-explorer]. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è creato un nuovo dispositivo IoT Edge ed è stata usata l'interfaccia cloud di Azure IoT Edge per distribuire il codice nel dispositivo. A questo punto, si dispone di un dispositivo simulato che genera dati non elaborati sul proprio ambiente. 

Questa esercitazione è necessaria per tutte le altre esercitazioni di IoT Edge. È possibile continuare con una delle altre esercitazioni per ottenere informazioni sui modi in cui Azure IoT Edge può contribuire alla trasformazione dei dati in informazioni aziendali dettagliate nei dispositivi perimetrali.

> [!div class="nextstepaction"]
> [Sviluppare e distribuire il codice C# come modulo](tutorial-csharp-module.md)

<!-- Images -->
[2]: ./media/tutorial-install-iot-edge/install-edge-full.png
[3]: ./media/tutorial-install-iot-edge/create-iot-hub.png
[4]: ./media/tutorial-install-iot-edge/register-device.png
[5]: ./media/tutorial-install-iot-edge/start-runtime.png
[6]: ./media/tutorial-install-iot-edge/deploy-module.png

<!-- Links -->
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-python]: https://www.python.org/downloads/
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-install-iotcore]: how-to-install-iot-core.md