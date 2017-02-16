---
title: Creare l&quot;hub IoT e registrare il dispositivo Raspberry Pi 3 | Documentazione Microsoft
description: Creare un gruppo di risorse, creare un hub IoT di Azure e registrare il dispositivo Pi nell&quot;hub IoT di Azure tramite l&quot;interfaccia della riga di comando di Azure.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: cloud raspberry pi, connessione cloud pi
ms.assetid: 4bcfb071-b3ae-48cc-8ea5-7e7434732287
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 155e5d6280d86b06b1718fc3032c2c224539183d
ms.openlocfilehash: 4fb7280cbf9221d1fe8b9fae24356e367969d32a


---
# <a name="create-your-iot-hub-and-register-raspberry-pi-3"></a>Creare l'hub IoT e registrare Raspberry Pi 3
## <a name="what-you-will-do"></a>Contenuto dell'esercitazione
* Creare un gruppo di risorse.
* Creare l'hub IoT di Azure nel gruppo di risorse.
* Aggiungere Raspberry Pi 3 all'hub IoT di Azure usando l'interfaccia della riga di comando di Azure.

Quando si usa l'interfaccia della riga di comando di Azure per aggiungere il dispositivo Pi all'hub IoT, il servizio genera una chiave per l'autenticazione del dispositivo Pi con il servizio. In caso di problemi, cercare le soluzioni nella pagina sulla [risoluzione dei problemi](iot-hub-raspberry-pi-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenuto dell'esercitazione
Contenuto dell'articolo:
* Come usare l'interfaccia della riga di comando di Azure per creare un hub IoT.
* Come creare un'identità per il dispositivo Pi nell'hub IoT.

## <a name="what-you-need"></a>Elementi necessari
* Un account Azure
* Un computer Mac o Windows con l'interfaccia della riga di comando di Azure installata

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
> Il nome dell'hub IoT deve essere globalmente univoco. È possibile creare una sola edizione F1 dell'hub IoT di Azure nella sottoscrizione di Azure.

## <a name="register-pi-in-your-iot-hub"></a>Registrare il dispositivo Pi nell'hub IoT
Ogni dispositivo che invia o riceve messaggi da e verso l'hub IoT deve essere registrato con un ID univoco.

Registrare il dispositivo Pi nell'hub usando il comando seguente:

```bash
az iot device create --device-id myraspberrypi --hub {my hub name} --resource-group iot-sample
```

## <a name="summary"></a>Riepilogo
È stato creato un hub IoT e il dispositivo Pi è stato registrato con un'identità del dispositivo nell'hub IoT. È ora possibile apprendere come inviare messaggi dal dispositivo Pi all'hub IoT.

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per le funzioni di Azure e un account di archiviazione di Azure per elaborare e archiviare i messaggi dell'hub IoT](iot-hub-raspberry-pi-kit-c-lesson3-deploy-resource-manager-template.md).




<!--HONumber=Dec16_HO1-->


