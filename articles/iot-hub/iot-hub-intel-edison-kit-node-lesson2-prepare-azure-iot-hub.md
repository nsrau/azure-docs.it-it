---
title: 'Connettere Intel Edison (Node) ad Azure IoT: lezione 2: Registrare il dispositivo | Documentazione Microsoft'
description: Creare un gruppo di risorse, creare un hub IoT di Azure e registrare Edison nell&quot;hub IoT di Azure tramite l&quot;interfaccia della riga di comando di Azure.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: 
ms.assetid: c1465cc2-d0d9-4326-967c-64d95b61dd54
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/8/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: 384724ccf78dc08b215ef602ff4366dd54801ab5


---
# <a name="create-your-iot-hub-and-register-intel-edison"></a>Creare l'hub IoT e registrare Intel Edison
## <a name="what-you-will-do"></a>Contenuto dell'esercitazione
* Creare un gruppo di risorse.
* Creare l'hub IoT di Azure nel gruppo di risorse.
* Aggiungere Intel Edison all'hub IoT di Azure usando l'interfaccia della riga di comando di Azure.

Quando si usa l'interfaccia della riga di comando di Azure per aggiungere Edison all'hub IoT, il servizio genera una chiave per l'autenticazione di Edison con il servizio. In caso di problemi, cercare le soluzioni nella [pagina sulla risoluzione dei problemi][troubleshooting].

## <a name="what-you-will-learn"></a>Contenuto dell'esercitazione
Contenuto dell'articolo:
* Come usare l'interfaccia della riga di comando di Azure per creare un hub IoT.
* Come creare un'identità del dispositivo per Edison nell'hub IoT.

## <a name="what-you-need"></a>Elementi necessari
* Un account Azure. Se non si ha un account Azure, creare un [account Azure gratuito](http://azure.microsoft.com/pricing/free-trial/) in pochi minuti.
* È necessario che sia installata l'interfaccia della riga di comando di Azure.

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


## <a name="register-edison-in-your-iot-hub"></a>Registrare Edison nell'hub IoT
Ogni dispositivo che invia o riceve messaggi da e verso l'hub IoT deve essere registrato con un ID univoco.

Registrare Edison nell'hub IoT eseguendo il comando seguente:

```bash
az iot device create --device-id myinteledison --hub-name {my hub name}
```

## <a name="summary"></a>Riepilogo
È stato creato un hub IoT ed Edison è stato registrato con un'identità del dispositivo nell'hub IoT. È ora possibile apprendere come inviare messaggi da Edison all'hub IoT.

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per le funzioni di Azure e un account di archiviazione di Azure per elaborare e archiviare i messaggi dell'hub IoT][process-and-store-iot-hub-messages].


<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[process-and-store-iot-hub-messages]: iot-hub-intel-edison-kit-node-lesson3-deploy-resource-manager-template.md


<!--HONumber=Jan17_HO4-->


