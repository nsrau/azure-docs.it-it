---
title: Simulare Azure IoT Edge in Windows | Microsoft Docs
description: Installare il runtime di Azure IoT Edge su un dispositivo simulato in Windows e distribuire il primo modulo
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 11/16/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 0207418cf71902ce9bc9d2911124d1d46889d893
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2017
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-windows----preview"></a>Distribuire Azure IoT Edge su un dispositivo simulato in Windows: anteprima

Azure IoT Edge consente di eseguire operazioni di analisi ed elaborazione dei dati direttamente nei dispositivi, invece di dover eseguire il push di tutti i dati nel cloud. Le esercitazioni di IoT Edge spiegano come distribuire i diversi tipi di moduli, compilati da servizi di Azure o da codice personalizzato. Per eseguire i test, è però necessario un dispositivo. 

In questa esercitazione si apprenderà come:

1. Creare un hub IoT
2. Registrare un dispositivo IoT Edge
3. Avviare il runtime di IoT Edge
4. Distribuire un modulo

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

   ```cmd
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> Azure IoT Edge può eseguire contenitori Windows o contenitori Linux. Se si esegue una delle seguenti versioni di Windows, è possibile usare i contenitori di Windows:
>    * Windows 10 Fall Creators Update
>    * Windows Server 1709 (Build 16299)
>    * Windows IoT Core (Build 16299) su un dispositivo basato su x64
>
> Per Windows IoT Core, seguire le istruzioni disponibili in [Installare il runtime di IoT Edge su Windows IoT Core][lnk-install-iotcore]. In caso contrario, semplicemente [configurare Docker per l'uso dei contenitori di Windows][lnk-docker-containers]. Usare il comando seguente per convalidare i prerequisiti:
>    ```powershell
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

Il runtime di IoT Edge viene distribuito in tutti i dispositivi IoT Edge. È costituito da due moduli. L'**agente di IoT Edge** semplifica la distribuzione e il monitoraggio dei moduli nel dispositivo IoT Edge. L'**hub IoT Edge** gestisce le comunicazioni tra moduli nel dispositivo IoT Edge e tra il dispositivo e l'hub IoT. Quando si configura il runtime nel nuovo dispositivo, inizialmente viene avviato solo l'agente di IoT Edge. Quando si distribuisce un modulo, l'hub IoT Edge viene avviato dopo. 


Configurare il runtime con la stringa di connessione del dispositivo IoT Edge definita nella sezione precedente.

```cmd
iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
```

Avviare il runtime.

```cmd
iotedgectl start
```

Controllare Docker per verificare che l'agente di IoT Edge sia in esecuzione come modulo.

```cmd
docker ps
```

![Vedere edgeAgent in Docker](./media/tutorial-simulate-device-windows/docker-ps.png)

## <a name="deploy-a-module"></a>Distribuire un modulo

Gestire il dispositivo Azure IoT Edge dal cloud per distribuire un modulo che invierà i dati di telemetria all'hub IoT.
![Registrare un dispositivo][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]


## <a name="view-generated-data"></a>Visualizzare i dati generati

In questa esercitazione è stato creato un nuovo dispositivo IoT Edge, nel quale è stato installato il runtime di IoT Edge. È stato quindi usato il portale di Azure per eseguire il push di un modulo di IoT Edge da eseguire nel dispositivo senza dovere apportare modifiche al dispositivo stesso. In questo caso il modulo di cui è stato eseguito il push crea dati ambientali che è possibile usare per le esercitazioni. 

Aprire il prompt dei comandi nel computer eseguendo ancora il dispositivo simulato. Verificare che il modulo distribuito dal cloud sia in esecuzione nel dispositivo IoT Edge. 

```cmd
docker ps
```

![Visualizzare tre moduli nel dispositivo](./media/tutorial-simulate-device-windows/docker-ps2.png)

Visualizzare i messaggi inviati dal modulo tempSensor al cloud. 

```cmd
docker logs -f tempSensor
```

![Visualizzare i dati dal modulo](./media/tutorial-simulate-device-windows/docker-logs.png)

È anche possibile visualizzare i dati di telemetria inviati dal dispositivo tramite lo [strumento di esplorazione dell'hub IoT][lnk-iothub-explorer]. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un nuovo dispositivo IoT Edge ed è stata usata l'interfaccia cloud di Azure IoT Edge per distribuire il codice nel dispositivo. A questo punto è disponibile un dispositivo simulato che genera dati non elaborati sul proprio ambiente. 

Questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni su IoT Edge. È possibile continuare con una delle altre esercitazioni per ottenere informazioni sui modi in cui Azure IoT Edge può contribuire alla trasformazione dei dati in informazioni aziendali dettagliate nei dispositivi perimetrali.

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