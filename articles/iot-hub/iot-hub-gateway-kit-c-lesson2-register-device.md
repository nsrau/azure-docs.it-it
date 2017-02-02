---
title: Creare un hub IoT di Azure e registrare il dispositivo | Documentazione Microsoft
description: 
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: hub IoT di azure, cloud Internet delle cose, creare dispositivo di hub IoT di azure, ti sensortag, ti ble
ms.assetid: 2c18f5ae-e39a-48ae-a9fe-04bb595740a0
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 7765f122476c51011ab12613e615e5fea0bc58f4
ms.openlocfilehash: 4b878ebe3222c4ab1c41051c659ba8333bf94c32


---

# <a name="create-your-azure-iot-hub-and-register-your-device"></a>Creare l'hub IoT di Azure e registrare il dispositivo

## <a name="what-you-will-do"></a>Contenuto dell'esercitazione

- Creare un gruppo di risorse
- Creare il primo hub IoT
- Registrare il dispositivo nell'hub IoT usando l'interfaccia della riga di comando di Azure. 

Quando si registra il dispositivo nell'hub IoT, il servizio hub IoT di Azure genera una chiave che il dispositivo deve usare per l'autenticazione con il servizio. 

In caso di problemi, cercare le soluzioni nella pagina sulla [risoluzione dei problemi](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenuto dell'esercitazione

In questa lezione si apprenderà:

- Come usare l'interfaccia della riga di comando di Azure per creare un hub IoT.
- Come registrare un dispositivo in un hub IoT.

## <a name="what-you-need"></a>Elementi necessari

- Una sottoscrizione di Azure attiva. Se non si ha un account Azure, è possibile [creare un account Azure gratuito](http://azure.microsoft.com/pricing/free-trial/) in pochi minuti.
- È necessario che sia installata l'interfaccia della riga di comando di Azure.

## <a name="create-an-iot-hub"></a>Creare un hub IoT

Per creare un hub IoT, seguire questa procedura:

1. Accedere al proprio account di Azure usando il comando seguente:

   ```bash
   az login
   ```

   Dopo l'accesso verranno elencate tutte le sottoscrizioni disponibili.

2. Impostare la sottoscrizione di Azure predefinita che si vuole usare mediante il comando seguente:

   ```bash
   az account set --subscription {subscription id or name}
   ```

   `subscription ID or name` si trova nell'output del comando `az login` o `az account list`.

3. Registrare il provider usando il comando seguente. I provider di risorse sono servizi che forniscono risorse per l'applicazione. Prima di poter distribuire la risorsa di Azure fornita dal provider, è necessario registrare il provider.

   ```bash
   az provider register -n "Microsoft.Devices"
   ```

4. Creare un gruppo di risorse denominato `iot-gateway` nell'area Stati Uniti occidentali usando il comando seguente:

   ```bash
   az group create --name iot-gateway --location westus
   ```
   
   `westus` è la località in cui si crea il gruppo di risorse. Per usare un'altra località è possibile eseguire `az account list-locations -o table` per visualizzare tutte le località supportate da Azure.

5. Creare un hub IoT nel gruppo di risorse `iot-gateway` usando il comando seguente:

   ```bash
   az iot hub create --name {my hub name} --resource-group iot-gateway
   ```

Per impostazione predefinita, lo strumento crea un hub IoT nel piano tariffario gratuito. Per altre informazioni, vedere [Prezzi dell'hub Iot di Azure](https://azure.microsoft.com/pricing/details/iot-hub/).

> [!NOTE]
> Il nome dell'hub IoT deve essere globalmente univoco. È possibile creare una sola edizione F1 dell'hub IoT di Azure nella sottoscrizione di Azure.

## <a name="register-your-device-in-your-iot-hub"></a>Registrare il dispositivo nell'hub IoT

Ogni dispositivo che invia o riceve messaggi da e verso l'hub IoT deve essere registrato con un ID univoco.
Registrare il dispositivo nell'hub IoT usando il comando seguente:

```bash
az iot device create --device-id mydevice --hub-name {my hub name} --resource-group iot-gateway
```

## <a name="summary"></a>Riepilogo

È stato creato un hub IoT e il dispositivo logico è stato registrato con un'identità del dispositivo nell'hub IoT. Si è già appreso come configurare ed eseguire un'applicazione gateway di esempio per inviare dati dal dispositivo fisico all'hub IoT sul cloud.

## <a name="next-steps"></a>Passaggi successivi
[Configurare ed eseguire un'app di esempio BLE](iot-hub-gateway-kit-c-lesson3-configure-ble-app.md)


<!--HONumber=Dec16_HO3-->


