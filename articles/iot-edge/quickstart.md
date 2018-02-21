---
title: Guida introduttiva per Azure IoT Edge e Windows | Microsoft Docs
description: "È possibile provare Azure IoT Edge tramite l'esecuzione di analisi in un dispositivo perimetrale simulato"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 17675f870a015e86f98bf286a9b1c2bbc05c16cd
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2017
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>Guida introduttiva: Distribuire il primo modulo di IoT Edge dal portale di Azure in un dispositivo Windows - Anteprima

In questa guida introduttiva viene usata l'interfaccia cloud di Azure IoT Edge per distribuire in modalità remota codice predefinito in un dispositivo IoT Edge. Per completare questa attività, usare prima di tutto il dispositivo Windows per simulare un dispositivo IoT Edge, quindi distribuire un modulo in tale dispositivo.

Se non si ha una sottoscrizione di Azure attiva, creare un [account gratuito][lnk-account] prima di iniziare.

## <a name="prerequisites"></a>prerequisiti

Questa esercitazione presuppone che si usi un computer o una macchina virtuale che esegue Windows per simulare un dispositivo di Internet delle cose. Se si esegue Windows in una macchina virtuale, abilitare la [virtualizzazione annidata][lnk-nested] e allocare almeno 2 GB di memoria. 

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

## <a name="create-an-iot-hub-with-azure-cli"></a>Creare un hub IoT con l'interfaccia della riga di comando di Azure

Creare un hub IoT nella sottoscrizione di Azure. Per questa guida introduttiva è possibile usare il livello gratuito dell'hub IoT. Se è già stato usato l'hub IoT ed è già stato creato un hub gratuito, è possibile saltare questa sezione e passare a [Registrare un dispositivo IoT Edge][anchor-register]. Ogni sottoscrizione può avere un solo hub IoT gratuito. 

1. Accedere al [portale di Azure][lnk-portal]. 
1. Selezionare il pulsante **Cloud Shell**. 

   ![Pulsante Cloud Shell][1]

1. Creare un gruppo di risorse. Il codice seguente crea un gruppo di risorse denominato **IoTEdge** nell'area **Stati Uniti occidentali**:

   ```azurecli
   az group create --name IoTEdge --location westus
   ```

1. Creare un hub IoT nel nuovo gruppo di risorse. Il codice seguente crea un hub **F1** gratuito denominato **MyIotHub** nel gruppo di risorse **IoTEdge**:

   ```azurecli
   az iot hub create --resource-group IoTEdge --name MyIotHub --sku F1 
   ```

## <a name="register-an-iot-edge-device"></a>Registrare un dispositivo IoT Edge

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>Configurare il runtime di IoT Edge

Il runtime di IoT Edge viene distribuito in tutti i dispositivi IoT Edge. È costituito da due moduli. Il primo, l'agente di IoT Edge, semplifica la distribuzione e il monitoraggio dei moduli nel dispositivo IoT Edge. Il secondo, l'hub IoT Edge, gestisce le comunicazioni tra moduli nel dispositivo IoT Edge e tra il dispositivo e l'hub IoT. 

Configurare il runtime con la stringa di connessione del dispositivo IoT Edge definita nella sezione precedente.

```
iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
```

Avviare il runtime.

```
iotedgectl start
```

Controllare Docker per verificare che l'agente di IoT Edge sia in esecuzione come modulo.

```
docker ps
```

![Vedere edgeAgent in Docker](./media/tutorial-simulate-device-windows/docker-ps.png)

## <a name="deploy-a-module"></a>Distribuire un modulo

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Visualizzare i dati generati

In questa guida introduttiva è stato creato un nuovo dispositivo IoT Edge, nel quale è stato installato il runtime di IoT Edge. È stato quindi usato il portale di Azure per eseguire il push di un modulo di IoT Edge da eseguire nel dispositivo senza dovere apportare modifiche al dispositivo stesso. In questo caso il modulo di cui è stato eseguito il push crea dati ambientali che è possibile usare per le esercitazioni. 

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
## <a name="clean-up-resources"></a>Pulire le risorse

Quando l'hub IoT creato non è più necessario, è possibile usare il comando [az iot hub delete][lnk-delete] per rimuovere la risorsa ed eventuali dispositivi associati ad essa:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

## <a name="next-steps"></a>Passaggi successivi

È stato illustrato come distribuire un modulo di IoT Edge in un dispositivo IoT Edge. È ora possibile provare a distribuire tipi diversi di servizi di Azure come moduli, in modo che sia possibile analizzare i dati nei dispositivi perimetrali. 

* [Distribuire Funzione di Azure come modulo](tutorial-deploy-function.md)
* [Distribuire Analisi di flusso di Azure come modulo](tutorial-deploy-stream-analytics.md)
* [Distribuire il codice personalizzato come modulo](tutorial-csharp-module.md)


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png

<!-- Links -->
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-python]: https://www.python.org/downloads/
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete
[lnk-install-iotcore]: how-to-install-iot-core.md

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
