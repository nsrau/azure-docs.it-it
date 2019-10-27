---
title: Distribuire moduli dalla riga di comando - Azure IoT Edge | Microsoft Docs
description: Usare l'estensione IoT per l'interfaccia della riga di comando di Azure per distribuire i moduli in un dispositivo IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/16/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: c8cd6e2d13e252f9a7560b55eca58341e791db5a
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72964932"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli"></a>Distribuire i moduli di Azure IoT Edge con l'interfaccia della riga di comando di Azure

Dopo aver creato i moduli di IoT Edge in base alla propria logica di business, si distribuiscono i moduli nei dispositivi per consentirne l'uso a livello perimetrale. Se si hanno più moduli che interagiscono per raccogliere ed elaborare dati, è possibile distribuirli contemporaneamente e dichiarare le regole di routing che li connettono.

L'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) è uno strumento da riga di comando multipiattaforma e open source per la gestione di risorse di Azure come IoT Edge. Consente di gestire le risorse dell'hub IoT di Azure, le istanze del servizio Device Provisioning e gli hub collegati predefiniti. La nuova estensione IoT arricchisce l'interfaccia della riga di comando di Azure con funzionalità quali la gestione dei dispositivi e le funzionalità complete di IoT Edge.

Questo articolo illustra come creare un manifesto della distribuzione JSON e quindi usare tale file per eseguire il push della distribuzione in un dispositivo IoT Edge. Per informazioni sulla creazione di una distribuzione da assegnare a più dispositivi in base ai relativi tag condivisi, vedere [Distribuire e monitorare i moduli di IoT Edge su larga scala](how-to-deploy-monitor-cli.md).

## <a name="prerequisites"></a>Prerequisiti

* Un [hub IoT](../iot-hub/iot-hub-create-using-cli.md) nella sottoscrizione di Azure.
* Un [dispositivo IoT Edge](how-to-register-device.md#register-with-the-azure-cli) con il runtime di IoT Edge installato.
* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) nell'ambiente in uso. La versione dell'interfaccia della riga di comando di Azure deve essere 2.0.24 o successiva. Usare `az --version` per la convalida. Questa versione supporta i comandi dell'estensione az e introduce il framework dei comandi Knack.
* [Estensione IoT per l'interfaccia della riga di comando di Azure](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Configurare un manifesto della distribuzione

Un manifesto della distribuzione è un documento JSON contenente la descrizione dei moduli da distribuire, dei flussi di dati esistenti tra i moduli e delle proprietà desiderate dei moduli gemelli. Per altre informazioni sul funzionamento e sulla modalità di creazione dei manifesti della distribuzione, vedere [Informazioni su come usare, configurare e riusare i moduli IoT Edge](module-composition.md).

Per distribuire i moduli tramite l'interfaccia della riga di comando di Azure, salvare il manifesto della distribuzione in locale come file con estensione .json. Il percorso del file verrà usato nella sezione successiva quando si eseguirà il comando per applicare la configurazione al dispositivo.

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
             "SimulatedTemperatureSensor": {
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
       "SimulatedTemperatureSensor": {
         "properties.desired": {}
       }
     }
   }
   ```

## <a name="deploy-to-your-device"></a>Eseguire la distribuzione nel dispositivo

I moduli vengono distribuiti nel dispositivo applicando il manifesto della distribuzione configurato con le informazioni relative ai moduli.

Passare alle directory della cartella in cui è stato salvato il manifesto della distribuzione. Se è stato usato uno dei modelli IoT Edge di Visual Studio Code, usare il file `deployment.json` nella cartella **config** della directory della soluzione e non il file `deployment.template.json`.

Per applicare la configurazione a un dispositivo IoT Edge usare il comando seguente:

   ```cli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content [file path]
   ```

Il parametro ID dispositivo distingue tra maiuscole e minuscole. Il parametro content punta al file del manifesto della distribuzione salvato.

   ![az iot edge set-modules output](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>Visualizzare i moduli nel dispositivo

Dopo aver distribuito i moduli nel dispositivo, è possibile visualizzarli tutti con il comando seguente:

Visualizzare i moduli nel dispositivo IoT Edge:

   ```cli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

Il parametro ID dispositivo distingue tra maiuscole e minuscole.

   ![az iot hub module-identity list output](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [distribuire e monitorare i moduli di IoT Edge su larga scala](how-to-deploy-monitor.md)
