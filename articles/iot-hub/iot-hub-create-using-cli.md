---
title: Creare un hub IoT mediante l'interfaccia della riga di comando | Microsoft Docs
description: Questo articolo illustra come creare un hub ioT tramite l'interfaccia della riga di comando di Azure.
services: iot-hub
documentationcenter: .net
author: BeatriceOltean
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2016
ms.author: boltean

---
# <a name="create-an-iot-hub-using-cli"></a>Creare un hub IoT tramite l'interfaccia della riga di comando
[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduzione
È possibile utilizzare l'interfaccia della riga di comando di Azure per creare e gestire hub IoT di Azure a livello di codice. Questo articolo illustra come usare l'interfaccia della riga di comando di Azure per creare un hub IoT.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. È possibile creare un [Account di valutazione gratuita][lnk-free-trial] in pochi minuti.
* [Azure CLI 0.10.4][lnk-CLI-install] o versione successiva. Se si dispone già dell'interfaccia della riga di comando di Azure è possibile convalidare la versione corrente al prompt dei comandi inserendo il comando seguente:
  ```
    azure --version
  ```

> [!NOTE]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../resource-manager-deployment-model.md). L'interfaccia della riga di comando di Azure deve essere impostata obbligatoriamente sulla modalità Azure Resource Manager:
> 
> ```
> azure config mode arm
> ```
> 
> 

## <a name="set-your-azure-account-and-subscription"></a>Impostare l'account e la sottoscrizione di Azure
1. Accedere digitando il comando seguente al prompt dei comandi
   
   ```
    azure login
   ```
   Per l'autenticazione, usare il browser e il codice suggeriti.
2. Se si dispone di più sottoscrizioni Azure, effettuando l'accesso ad Azure sarà possibile accedere a tutte le sottoscrizioni associate alle credenziali. Per visualizzare le sottoscrizioni, inclusa quella predefinita, utilizzare il comando
   
   ```
    azure account list 
   ```

Per impostare il contesto della sottoscrizione in cui eseguire il resto dei comandi usare

```
    azure account set <subscription name>
```

1. Se non si dispone di un gruppo di risorse è possibile crearne uno denominato **exampleResourceGroup** 
   ```
    azure group create -n exampleResourceGroup -l westus
   ```

> [!TIP]
> L'articolo [Usare l'interfaccia della riga di comando di Azure per gestire risorse e gruppi di risorse][lnk-CLI-arm] offre altre informazioni su come usare l'interfaccia della riga di comando di Azure per gestire le risorse di Azure. 
> 
> 

## <a name="create-and-iot-hub"></a>Creare un hub IoT
Parametri obbligatori:

```
 azure iothub create -g <resource-group> -n <name> -l <location> -s <sku-name> -u <units>  
    - <resourceGroup> The resource group name (case insensitive alphanumeric, underscore and hyphen, 1-64 length)
    - <name> (The name of the IoT hub to be created. The format is case insensitive alphanumeric, underscore and hyphen, 3-50 length )
    - <location> (The location (azure region/datacenter) where the IoT hub will be provisioned.
    - <sku-name> (The name of the sku, one of: [F1, S1, S2, S3] etc. For the latest full list refer to the pricing page for IoT Hub.
    - <units> (The number of provisioned units. Range : F1 [1-1] : S1, S2 [1-200] : S3 [1-10]. IoT Hub units are based on your total message count and the number of devices you want to connect.)
```
Per visualizzare tutti i parametri disponibili per la creazione è possibile usare il comando help nel prompt dei comandi

```
    azure iothub create -h 
```
Esempio:

 Per creare un hub IoT denominato **exampleIoTHubName** nel gruppo di risorse **exampleResourceGroup** eseguire il comando seguente

```
    azure iothub create -g exampleResourceGroup -n exampleIoTHubName -l westus -k s1 -u 1
```

> [!NOTE]
> Questo comando crea un hub IoT S1 Standard che viene addebitato. Per eliminare l'hub IoT **exampleIoTHubName** eseguire il comando seguente 
> 
> ```
> azure iothub delete -g exampleResourceGroup -n exampleIoTHubName
> ```
> 
> 
> 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle attività di sviluppo per l'hub IoT, vedere quanto segue:

* [SDK Hub IoT][lnk-sdks]

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Gestire hub IoT tramite il portale di Azure][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-CLI-install]: ../xplat-cli-install.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../resource-group-overview.md
[lnk-CLI-arm]: ../xplat-cli-azure-resource-manager.md

[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-portal]: iot-hub-create-through-portal.md 



<!--HONumber=Oct16_HO2-->


