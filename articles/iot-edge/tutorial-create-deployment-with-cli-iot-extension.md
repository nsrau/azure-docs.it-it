---
title: Distribuire moduli nei dispositivi IoT Edge usando l'estensione IoT per l'interfaccia della riga di comando di Azure 2.0 | Microsoft Docs
description: Distribuire moduli nei dispositivi IoT Edge usando l'estensione IoT per l'interfaccia della riga di comando di Azure 2.0
services: iot-edge
keywords: 
author: chrissie926
manager: timlt
ms.author: menchi
ms.date: 01/11/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 26067187864f9a2a4c85c953ae8aca888458d245
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2018
---
# <a name="deploy-modules-to-an-iot-edge-device-using-iot-extension-for-azure-cli-20"></a>Distribuire moduli nei dispositivi IoT Edge usando l'estensione IoT per l'interfaccia della riga di comando di Azure 2.0

L'[interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/overview?view=azure-cli-latest) è uno strumento da riga di comando multipiattaforma e open source per la gestione di risorse di Azure come IoT Edge. L'interfaccia della riga di comando di Azure 2.0 è disponibile su Windows, Linux e MacOS.

L'interfaccia della riga di comando di Azure 2.0 consente di gestire le risorse dell'hub IoT di Azure, le istanze del servizio Device Provisioning e gli hub collegati predefiniti. La nuova estensione IoT arricchisce l'interfaccia della riga di comando di Azure 2.0 con funzionalità quali la gestione dei dispositivi e le funzionalità complete di IoT Edge.

In questa esercitazione viene completata prima di tutto la procedura per la configurazione dell'interfaccia della riga di comando di Azure 2.0 e dell'estensione IoT. Vengono quindi fornite informazioni su come distribuire moduli in un dispositivo IoT Edge usando i comandi disponibili dell'interfaccia della riga di comando.

## <a name="installation"></a>Installazione 

### <a name="step-1---install-python"></a>Passaggio 1 - Installare Python

È necessario [Python 2.7x o Python 3.x](https://www.python.org/downloads/).

### <a name="step-2---install-azure-cli-20"></a>Passaggio 2 - Installare l'interfaccia della riga di comando di Azure 2.0

Seguire le [istruzioni di installazione](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) per configurare l'interfaccia della riga di comando di Azure 2.0 nell'ambiente. La versione l'interfaccia della riga di comando di Azure 2.0 deve essere almeno 2.0.24 o versione successiva. Usare `az –version` per la convalida. Questa versione supporta i comandi dell'estensione az e introduce il framework dei comandi Knack. Un approccio semplice per l'installazione su Windows consiste nel scaricare e installare [MSI](https://aka.ms/InstallAzureCliWindows).

### <a name="step-3---install-iot-extension"></a>Passaggio 3 - Installare l'estensione IoT

Il [file Leggimi dell'estensione IoT](https://github.com/Azure/azure-iot-cli-extension) illustra molti modi per installare l'estensione. L'approccio più semplice consiste nell'eseguire `az extension add --name azure-cli-iot-ext`. Dopo l'installazione, è possibile usare `az extension list` per convalidare le estensioni attualmente installate o `az extension show --name azure-cli-iot-ext` per visualizzare informazioni dettagliate sull'estensione IoT. Per rimuovere l'estensione, è possibile usare `az extension remove --name azure-cli-iot-ext`.


## <a name="deploy-modules-to-an-iot-edge-device"></a>Distribuire moduli in un dispositivo IoT Edge
In questa esercitazione viene illustrato come creare una distribuzione IoT Edge. Questo esempio mostra come accedere all'account Azure, creare un gruppo di risorse di Azure (un contenitore che include le risorse correlate per una soluzione di Azure), creare un hub IoT, creare tre identità di dispositivi IoT Edge, impostare tag e quindi creare una distribuzione IoT Edge che fa riferimento a questi dispositivi. Prima di iniziare, completare la procedura di installazione illustrata in precedenza. Se non si ha un account Azure, è possibile [creare un account gratuito](https://azure.microsoft.com/free/?v=17.39a) subito. 


### <a name="1-login-to-the-azure-account"></a>1. Accedere all'account Azure.
  
    az login

![Accesso][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Creare un gruppo di risorse IoTHubBlogDemo in eastus

    az group create -l eastus -n IoTHubBlogDemo

![Creare un gruppo di risorse][2]


### <a name="3-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>3. Creare un hub IoT denominato blogDemoHub nel gruppo di risorse appena creato

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![Creare un hub IoT][3]


### <a name="4-create-an-iot-edge-device"></a>4. Creare un dispositivo IoT Edge

    az iot hub device-identity create -d edge001 -n blogDemoHub --edge-enabled

![Creare un dispositivo IoT Edge][4]

### <a name="5-apply-configuration-to-the-iot-edge-device"></a>5. Applicare la configurazione al dispositivo IoT Edge

Salvare il modello JSON della distribuzione localmente come file TXT. Sarà necessario il percorso per il file quando si esegue il comando apply-configuration.

Di seguito è riportato un modello JSON di distribuzione di esempio che include un modulo tempSensor:

```json
{
  "moduleContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": ""
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "edgepreview.azurecr.io/azureiotedge/edge-agent:1.0-preview",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "edgepreview.azurecr.io/azureiotedge/edge-hub:1.0-preview",
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
              "image": "edgepreview.azurecr.io/azureiotedge/simulated-temperature-sensor:1.0-preview",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
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

    az iot hub apply-configuration --device-id edge001 --hub-name blogDemoHub --content C:\<yourLocation>\edgeconfig.txt

![Applicare una configurazione][5]

### <a name="6-list-modules"></a>6. Elencare i moduli
    
    az iot hub module-identity list --device-id edge001 --hub-name blogDemoHub

![Elencare i moduli][6]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato Funzioni di Azure contenente il codice per filtrare i dati non elaborati generati dal dispositivo di IoT Edge. Per continuare a esplorare Azure IoT Edge, imparare a usare un dispositivo IoT come gateway. 

> [!div class="nextstepaction"]
> [Creare un dispositivo gateway di IoT Edge](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-create-deployment-with-cli-iot-extension/login.jpg
[2]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-resource-group.jpg
[3]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-hub.jpg
[4]: ./media/tutorial-create-deployment-with-cli-iot-extension/Create-edge-device.png
[5]: ./media/tutorial-create-deployment-with-cli-iot-extension/apply-configuration.PNG
[6]: ./media/tutorial-create-deployment-with-cli-iot-extension/list-modules.PNG

