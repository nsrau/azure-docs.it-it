---
title: Simulare Azure IoT Edge in Linux | Microsoft Docs
description: Installare il runtime di Azure IoT Edge in un dispositivo simulato in Linux e distribuire il primo modulo
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 01/11/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 55770c92f5d5959e83066b425bc6ccf2b9dcc62e
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2018
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-linux-or-macos---preview"></a>Distribuire Azure IoT Edge in un dispositivo simulato in Linux o macOS: anteprima

Azure IoT Edge consente di eseguire operazioni di analisi ed elaborazione dei dati direttamente nei dispositivi, invece di dover eseguire il push di tutti i dati nel cloud. Le esercitazioni di IoT Edge spiegano come distribuire i diversi tipi di moduli, compilati da servizi di Azure o da codice personalizzato. Per eseguire i test, è però necessario un dispositivo. 

In questa esercitazione si apprenderà come:

1. Creare un hub IoT
2. Registrare un dispositivo IoT Edge
3. Avviare il runtime di IoT Edge
4. Distribuire un modulo

![Architettura dell'esercitazione][2]

Il dispositivo simulato creato in questa esercitazione è un monitor che genera i dati relativi a temperatura, umidità e pressione. Le altre esercitazioni di Azure IoT Edge si basano sulle operazioni eseguite qui tramite la distribuzione di moduli che consentono di analizzare i dati per ottenere informazioni aziendali accurate. 

## <a name="prerequisites"></a>Prerequisiti

In questa esercitazione viene usato un computer o una macchina virtuale come un dispositivo IoT. I servizi seguenti sono necessari per convertire il computer in uso in un dispositivo IoT Edge:

* Pip di Python per installare il runtime di IoT Edge.
   * Linux: `sudo apt-get install python-pip`.
   * MacOS: `sudo easy_install pip`.
* Docker, per eseguire i moduli IoT Edge
   * [Installare Docker per Linux][lnk-docker-ubuntu] e assicurarsi che sia in esecuzione. 
   * [Installare Docker per Mac][lnk-docker-mac] e assicurarsi che sia in esecuzione. 

## <a name="create-an-iot-hub"></a>Creare un hub IoT

Avviare l'esercitazione creando l'hub IoT.
![Creare l'hub IoT][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Registrare un dispositivo IoT Edge

Registrare un dispositivo IoT Edge con l'hub IoT appena creato.
![Registrare un dispositivo][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="install-and-start-the-iot-edge-runtime"></a>Installare e avviare il runtime di IoT Edge

Installare e avviare il runtime di Azure IoT Edge nel dispositivo. 
![Registrare un dispositivo][5]

Il runtime di IoT Edge viene distribuito in tutti i dispositivi IoT Edge. È costituito da due moduli. L'**agente di IoT Edge** semplifica la distribuzione e il monitoraggio dei moduli nel dispositivo IoT Edge. L'**hub IoT Edge** gestisce le comunicazioni tra moduli nel dispositivo IoT Edge e tra il dispositivo e l'hub IoT. Quando si configura il runtime nel nuovo dispositivo, inizialmente viene avviato solo l'agente di IoT Edge. Quando si distribuisce un modulo, l'hub IoT Edge viene avviato dopo. 

Nel computer in cui verrà eseguito il dispositivo IoT Edge, scaricare lo script di controllo di IoT Edge:
```cmd
sudo pip install -U azure-iot-edge-runtime-ctl
```

Configurare il runtime con la stringa di connessione al dispositivo IoT Edge dalla sezione precedente:
```cmd
sudo iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
```

Avviare il runtime:
```cmd
sudo iotedgectl start
```

Controllare Docker per verificare che l'agente di IoT Edge sia in esecuzione come modulo:
```cmd
sudo docker ps
```

![Vedere edgeAgent in Docker](./media/tutorial-simulate-device-linux/docker-ps.png)

## <a name="deploy-a-module"></a>Distribuire un modulo

Gestire il dispositivo Azure IoT Edge dal cloud per distribuire un modulo che invierà i dati di telemetria all'hub IoT.
![Registrare un dispositivo][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Visualizzare i dati generati

In questa esercitazione è stato creato un nuovo dispositivo IoT Edge, nel quale è stato installato il runtime di IoT Edge. È stato quindi usato il portale di Azure per eseguire il push di un modulo di IoT Edge da eseguire nel dispositivo senza dovere apportare modifiche al dispositivo stesso. In questo caso il modulo di cui è stato eseguito il push crea dati ambientali che è possibile usare per le esercitazioni. 

Aprire il prompt dei comandi nel computer eseguendo ancora il dispositivo simulato. Verificare che il modulo distribuito dal cloud sia in esecuzione nel dispositivo IoT Edge:

```cmd
sudo docker ps
```

![Visualizzare tre moduli nel dispositivo](./media/tutorial-simulate-device-linux/docker-ps2.png)

Visualizzare i messaggi inviati dal modulo tempSensor al cloud:

```cmd
sudo docker logs -f tempSensor
```

![Visualizzare i dati dal modulo](./media/tutorial-simulate-device-linux/docker-logs.png)

È anche possibile visualizzare i dati di telemetria inviati dal dispositivo tramite lo [strumento di esplorazione dell'hub IoT][lnk-iothub-explorer]. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un nuovo dispositivo IoT Edge ed è stata usata l'interfaccia cloud di Azure IoT Edge per distribuire il codice nel dispositivo. A questo punto è disponibile un dispositivo simulato che genera dati non elaborati sul proprio ambiente. 

Questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni su IoT Edge. È possibile continuare con una delle altre esercitazioni per ottenere informazioni sui modi in cui Azure IoT Edge può contribuire alla trasformazione dei dati in informazioni aziendali dettagliate nei dispositivi perimetrali.

> [!div class="nextstepaction"]
> [Sviluppare e distribuire il codice C# come modulo](tutorial-csharp-module.md)


<!-- Images -->
[1]: ./media/tutorial-install-iot-edge/view-module.png
[2]: ./media/tutorial-install-iot-edge/install-edge-full.png
[3]: ./media/tutorial-install-iot-edge/create-iot-hub.png
[4]: ./media/tutorial-install-iot-edge/register-device.png
[5]: ./media/tutorial-install-iot-edge/start-runtime.png
[6]: ./media/tutorial-install-iot-edge/deploy-module.png

<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-docker-mac]: https://docs.docker.com/docker-for-mac/install/
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
