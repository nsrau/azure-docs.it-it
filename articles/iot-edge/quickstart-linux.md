---
title: Guida introduttiva per Azure IoT Edge e Linux | Microsoft Docs
description: "È possibile provare Azure IoT Edge tramite l'esecuzione di analisi in un dispositivo perimetrale simulato"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/16/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: bfa6652eac34f88baf09f55353cf58227a20e4cf
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2017
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-linux-device---preview"></a>Guida introduttiva: distribuire il primo modulo di IoT Edge dal portale di Azure in un dispositivo Linux - anteprima

Azure IoT Edge sposta la potenza del cloud sui dispositivi di Internet delle cose. In questo argomento si apprende come usare l'interfaccia cloud per distribuire in modalità remota il codice predefinito in un dispositivo IoT Edge.

Se non si ha una sottoscrizione di Azure attiva, creare un [account gratuito][lnk-account] prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire questa operazione, usare il computer o una macchina virtuale per simulare un dispositivo di Internet delle cose. I servizi seguenti sono necessari per distribuire correttamente un dispositivo di IoT Edge:

- [Installare Docker su Linux][lnk-docker-ubuntu] e assicurarsi che sia in esecuzione. 
- Nella maggior parte delle distribuzioni di Linux, inclusa Ubuntu, Python 2.7 è già installato. Usare il comando seguente per verificare che pip sia installato: `sudo apt-get install python-pip`.

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

Creare un'identità del dispositivo per il dispositivo simulato in modo che possa comunicare con l'hub IoT. Poiché i dispositivi IoT Edge si comportano e possono essere gestiti in modo diverso dai dispositivi IoT tipici, dichiarare fin dall'inizio che si tratta di un dispositivo IoT Edge. 

1. Nel portale di Azure passare all'hub IoT.
1. Selezionare **IoT Edge (preview)** (IoT Edge - anteprima).
1. Selezionare **Add IoT Edge device** (Aggiungi il dispositivo di IoT Edge).
1. Assegnare al dispositivo simulato un ID univoco.
1. Selezionare **Salva** per aggiungere il dispositivo.
1. Selezionare il nuovo dispositivo dall'elenco dei dispositivi. 
1. Copiare il valore per **Stringa di connessione - chiave primaria** e salvarlo. Usare questo valore per configurare il runtime IoT Edge nella sezione successiva. 

## <a name="install-and-start-the-iot-edge-runtime"></a>Installare e avviare il runtime IoT Edge

Il runtime di IoT Edge viene distribuito in tutti i dispositivi IoT Edge. È costituito da due moduli. Il primo, l'agente di IoT Edge, semplifica la distribuzione e il monitoraggio dei moduli nel dispositivo IoT Edge. L'hub IoT Edge gestisce quindi le comunicazioni tra i moduli nel dispositivo IoT Edge e tra il dispositivo e l'hub IoT. 

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

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Visualizzare i dati generati

In questa guida introduttiva è stato creato un nuovo dispositivo IoT Edge, nel quale è stato installato il runtime di IoT Edge. È stato quindi usato il portale di Azure per eseguire il push di un modulo di IoT Edge da eseguire nel dispositivo senza dovere apportare modifiche al dispositivo stesso. In questo caso il modulo di cui è stato eseguito il push crea dati ambientali che è possibile usare per le esercitazioni. 

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

## <a name="clean-up-resources"></a>Pulire le risorse

Quando l'hub IoT creato non è più necessario, è possibile usare il comando [az iot hub delete][lnk-delete] per rimuovere la risorsa ed eventuali dispositivi associati ad essa:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

## <a name="next-steps"></a>Passaggi successivi

È stato illustrato come distribuire un modulo di IoT Edge in un dispositivo IoT Edge. È ora possibile provare a distribuire tipi diversi di servizi di Azure come moduli, in modo che sia possibile analizzare i dati nei dispositivi perimetrali. 

* [Distribuire il codice personalizzato come modulo](tutorial-csharp-module.md)
* [Distribuire Funzione di Azure come modulo](tutorial-deploy-function.md)
* [Distribuire Analisi di flusso di Azure come modulo](tutorial-deploy-stream-analytics.md)


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png

<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
