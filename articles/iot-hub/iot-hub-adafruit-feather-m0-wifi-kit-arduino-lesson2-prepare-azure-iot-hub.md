---
title: 'Connettere Arduino ad Azure IoT: lezione 2: Registrare il dispositivo | Documentazione Microsoft'
description: Creare un gruppo di risorse, creare un hub IoT di Azure e registrare Adafruit Feather M0 WiFi nell&quot;hub IoT di Azure tramite l&quot;interfaccia della riga di comando di Azure.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: connessione di arduino al cloud, hub iot di azure, cloud per internet delle cose, dispositivo di creazione hub iot di azure, cloud per arduino
ms.assetid: 5edc690b-7a1d-4ebc-b011-ff27bfffe6e8
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: ac9421be9211d53f8b5239c356201ee3efd27999
ms.lasthandoff: 03/10/2017


---
# <a name="create-your-iot-hub-and-register-your-adafruit-feather-m0-wifi-arduino-board"></a>Creare l'hub IoT e registrare la scheda Adafruit Feather M0 WiFi Arduino

## <a name="what-you-will-do"></a>Contenuto dell'esercitazione
* Creare un gruppo di risorse.
* Creare l'hub IoT di Azure nel gruppo di risorse.
* Aggiungere la scheda Arduino all'hub IoT di Azure tramite l'interfaccia della riga di comando di Azure.

Quando si usa l'interfaccia della riga di comando di Azure per aggiungere la scheda Arduino all'hub IoT, il servizio genera una chiave per autenticare la scheda. In caso di problemi, cercare le soluzioni nella [pagina sulla risoluzione dei problemi][troubleshoot].

## <a name="what-you-will-learn"></a>Contenuto dell'esercitazione
Contenuto dell'articolo:
* Come usare l'interfaccia della riga di comando di Azure per creare un hub IoT.
* Come creare un'identità di dispositivo per la scheda Arduino nell'hub IoT.

## <a name="what-you-need"></a>Elementi necessari
* Un account Azure
* Un computer con l'interfaccia della riga di comando di Azure installata

## <a name="create-your-iot-hub"></a>Creare l'hub IoT
L'hub IoT di Azure consente di connettersi a milioni di asset IoT, monitorarli e gestirli. Per creare l'hub IoT, seguire questa procedura:

1. Accedere al proprio account di Azure usando il comando seguente:

   ```bash
   az login
   ```

   Dopo l'accesso vengono elencate tutte le sottoscrizioni disponibili.

2. Impostare la sottoscrizione predefinita che si vuole usare tramite il comando seguente:

   ```bash
   az account set --subscription {subscription id or name}
   ```

   `subscription ID or name` si trova nell'output del comando `az login` o `az account list`.

3. Registrare il provider usando il comando seguente. I provider di risorse sono servizi che forniscono risorse per l'applicazione. Prima di poter distribuire la risorsa di Azure fornita dal provider, è necessario registrare il provider.

   ```bash
   az provider register -n "Microsoft.Devices"
   ```
4. Creare un gruppo di risorse denominato iot-sample nell'area Stati Uniti occidentali usando il comando seguente:

   ```bash
   az group create --name iot-sample --location westus
   ```

   `westus` è la località in cui si crea il gruppo di risorse. Per usare un'altra località è possibile eseguire `az account list-locations -o table` per visualizzare tutte le località supportate da Azure.

5. Creare un hub IoT nel gruppo di risorse iot-sample usando il comando seguente:

   ```bash
   az iot hub create --name {my hub name} --resource-group iot-sample
   ```

Per impostazione predefinita, lo strumento crea un hub IoT nel piano tariffario gratuito. Per altre informazioni, vedere [Prezzi dell'hub Iot di Azure](https://azure.microsoft.com/pricing/details/iot-hub/).

> [!NOTE]
> Il nome dell'hub IoT deve essere globalmente univoco.
> È possibile creare una sola edizione F1 dell'hub IoT di Azure nella sottoscrizione di Azure.

## <a name="register-your-arduino-board-in-your-iot-hub"></a>Registrare la scheda Arduino nell'hub IoT
Ogni dispositivo che invia o riceve messaggi da e verso l'hub IoT deve essere registrato con un ID univoco.

Registrare la scheda Arduino nell'hub IoT con il comando seguente:

```bash
az iot device create --device-id mym0wifi --hub-name {my hub name}
```

## <a name="summary"></a>Riepilogo
È stato creato un hub IoT ed è stata registrata una scheda Arduino con un'identità di dispositivo nell'hub IoT. A questo punto è possibile inviare messaggi dalla scheda Arduino all'hub IoT.

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per le funzioni di Azure e un account di archiviazione di Azure per elaborare e archiviare i messaggi dell'hub IoT][process-and-store-iot-hub-messages].


<!-- Images and links -->

[troubleshoot]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[process-and-store-iot-hub-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-deploy-resource-manager-template.md
