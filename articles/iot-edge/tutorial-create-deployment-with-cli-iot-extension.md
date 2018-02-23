---
title: Distribuire moduli nei dispositivi IoT Edge usando l'estensione IoT per l'interfaccia della riga di comando di Azure 2.0 | Microsoft Docs
description: Distribuire moduli nei dispositivi IoT Edge usando l'estensione IoT per l'interfaccia della riga di comando di Azure 2.0
services: iot-edge
keywords: 
author: chrissie926
manager: timlt
ms.author: menchi
ms.date: 02/12/2018
ms.topic: article
ms.service: iot-edge
ms.custom: mvc
ms.reviewer: kgremban
ms.openlocfilehash: ce3e979428233af578d71dee5ed10103e105f4f4
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2018
---
# <a name="deploy-modules-to-an-iot-edge-device-using-iot-extension-for-azure-cli-20"></a>Distribuire moduli nei dispositivi IoT Edge usando l'estensione IoT per l'interfaccia della riga di comando di Azure 2.0

L'[interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/overview?view=azure-cli-latest) è uno strumento da riga di comando multipiattaforma e open source per la gestione di risorse di Azure come IoT Edge. L'interfaccia della riga di comando di Azure 2.0 è disponibile su Windows, Linux e MacOS.

L'interfaccia della riga di comando di Azure 2.0 consente di gestire le risorse dell'hub IoT di Azure, le istanze del servizio Device Provisioning e gli hub collegati predefiniti. La nuova estensione IoT arricchisce l'interfaccia della riga di comando di Azure 2.0 con funzionalità quali la gestione dei dispositivi e le funzionalità complete di IoT Edge.

In questa esercitazione viene completata prima di tutto la procedura per la configurazione dell'interfaccia della riga di comando di Azure 2.0 e dell'estensione IoT. Vengono quindi fornite informazioni su come distribuire moduli in un dispositivo IoT Edge usando i comandi disponibili dell'interfaccia della riga di comando.

## <a name="prerequisites"></a>prerequisiti

* Un account Azure. Se non si ha un account, è possibile [crearne uno gratuito](https://azure.microsoft.com/free/?v=17.39a). 

* [Python 2.7x o Python 3.x](https://www.python.org/downloads/).

* [Interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) nell'ambiente. La versione dell'interfaccia della riga di comando di Azure 2.0 deve essere 2.0.24 o successiva. Usare `az –-version` per la convalida. Questa versione supporta i comandi dell'estensione az e introduce il framework dei comandi Knack. Un modo semplice per eseguire l'installazione in Windows è quello di scaricare e installare il file [MSI](https://aka.ms/InstallAzureCliWindows).

* [Estensione IoT per l'interfaccia della riga di comando di Azure 2.0](https://github.com/Azure/azure-iot-cli-extension):
   1. Eseguire `az extension add --name azure-cli-iot-ext`. 
   2. Dopo l'installazione, usare `az extension list` per convalidare le estensioni attualmente installate o `az extension show --name azure-cli-iot-ext` per visualizzare informazioni dettagliate sull'estensione IoT.
   3. Per rimuovere l'estensione, usare `az extension remove --name azure-cli-iot-ext`.


## <a name="create-an-iot-edge-device"></a>Creare un dispositivo IoT Edge
Questo articolo contiene le istruzioni per creare una distribuzione di IoT Edge. Questo esempio mostra come accedere all'account Azure, creare un gruppo di risorse di Azure (un contenitore che include le risorse correlate per una soluzione di Azure), creare un hub IoT, creare tre identità di dispositivi IoT Edge, impostare tag e quindi creare una distribuzione di IoT Edge destinata a questi dispositivi. 

Accedere all'account Azure. Dopo aver immesso il comando seguente di account di accesso, viene chiesto di usare un Web browser per accedere con un codice monouso: 

   ```cli
   az login
   ```

Creare un nuovo gruppo di risorse denominato **IoTHubCLI** nell'area Stati Uniti orientali: 

   ```cli
   az group create -l eastus -n IoTHubCLI
   ```

   ![Creare un gruppo di risorse][2]

Creare un hub IoT denominato **CLIDemoHub** nel gruppo di risorse appena creato:

   ```cli
   az iot hub create --name CLIDemoHub --resource-group IoTHubCLI --sku S1
   ```

   >[!TIP]
   >A ogni sottoscrizione è associato un hub IoT gratuito. Per creare un hub gratuito con il comando dell'interfaccia della riga di comando, sostituire il valore SKU con `--sku F1`. Se si ha già un hub gratuito nella sottoscrizione, quando si tenta di crearne un secondo verrà visualizzato un messaggio di errore. 

Creare un dispositivo IoT Edge:

   ```cli
   az iot hub device-identity create --device-id edge001 -hub-name CLIDemoHub --edge-enabled
   ```

   ![Creare un dispositivo IoT Edge][4]

## <a name="configure-the-iot-edge-device"></a>Configurare il dispositivo IoT Edge

Creare un modello JSON di distribuzione e salvarlo localmente come file TXT. Sarà necessario il percorso per il file quando si esegue il comando apply-configuration.

I modelli di distribuzione JSON devono sempre includere sempre i due moduli di sistema, edgeAgent ed edgeHub. Oltre a questi due, è possibile usare il file per distribuire moduli aggiuntivi per il dispositivo IoT Edge. Usare l'esempio seguente per configurare il dispositivo IoT Edge con un modulo tempSensor:

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

Applicare la configurazione al dispositivo IoT Edge:

   ```cli
   az iot hub apply-configuration --device-id edge001 --hub-name CLIDemoHub --content C:\<configuration.txt file path>
   ```

Visualizzare i moduli nel dispositivo IoT Edge:
    
   ```cli
   az iot hub module-identity list --device-id edge001 --hub-name CLIDemoHub
   ```

   ![Elencare i moduli][6]

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [usare un dispositivo IoT Edge come gateway](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[2]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-resource-group.png
[4]: ./media/tutorial-create-deployment-with-cli-iot-extension/Create-edge-device.png
[6]: ./media/tutorial-create-deployment-with-cli-iot-extension/list-modules.png

