---
title: Creare un hub IoT usando l&quot;interfaccia della riga di comando di Azure (azure.js) | Documentazione Microsoft
description: Come creare un hub IoT di Azure usando l&quot;interfaccia della riga di comando di Azure multipiattaforma (azure.js).
services: iot-hub
documentationcenter: .net
author: BeatriceOltean
manager: timlt
editor: 
ms.assetid: 46a17831-650c-41d9-b228-445c5bb423d3
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2017
ms.author: boltean
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 8ac82da36b2edb71fcd0599dac12a3ed18e33b6f
ms.lasthandoff: 03/24/2017


---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Creare un hub IoT usando l'interfaccia della riga di comando di Azure

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduzione

È possibile usare l'interfaccia della riga di comando di Azure (azure.js) per creare e gestire hub IoT di Azure a livello di codice. Questo articolo illustra come usare l'interfaccia della riga di comando di Azure (azure.js) per creare un hub IoT.

È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando:

* Interfaccia della riga di comando di Azure (azure.js): interfaccia della riga di comando per i modelli di distribuzione classica e Resource Manager.
* [Interfaccia della riga di comando di Azure 2.0 (az.py)](iot-hub-create-using-cli.md): interfaccia della riga di comando di nuova generazione per il modello di distribuzione Resource Manager.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non si ha un account, è possibile crearne uno [gratuito][lnk-free-trial] in pochi minuti.
* [Interfaccia della riga di comando di Azure 0.10.4][lnk-CLI-install] o versione successiva. Se l'interfaccia della riga di comando di Azure è installata, è possibile convalidare la versione corrente al prompt dei comandi inserendo il comando seguente:

```azurecli
azure --version
```

> [!NOTE]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [modello di distribuzione classica e Azure Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md). L'interfaccia della riga di comando di Azure deve essere impostata obbligatoriamente sulla modalità Azure Resource Manager:
>
> ```azurecli
> azure config mode arm
> ```

## <a name="set-your-azure-account-and-subscription"></a>Impostare l'account e la sottoscrizione di Azure

1. Per accedere, digitare il comando seguente al prompt dei comandi:

   ```azurecli
    azure login
   ```

   Per l'autenticazione, usare il browser e il codice suggeriti.
1. Se si usano più sottoscrizioni Azure e si esegue l'accesso ad Azure, è possibile accedere a tutte le sottoscrizioni di Azure associate alle credenziali. Per visualizzare le sottoscrizioni di Azure e identificare quella predefinita, usare il comando:

   ```azurecli
    azure account list
   ```

   Per impostare il contesto della sottoscrizione in cui eseguire il resto dei comandi usare:

   ```azurecli
    azure account set <subscription name>
   ```

1. Se non si dispone di un gruppo di risorse, è possibile crearne uno denominato **exampleResourceGroup**:

   ```azurecli
    azure group create -n exampleResourceGroup -l westus
   ```

> [!TIP]
> L'articolo [Usare l'interfaccia della riga di comando di Azure per gestire risorse e gruppi di risorse][lnk-CLI-arm] contiene altre informazioni su come usare l'interfaccia della riga di comando di Azure per gestire risorse di Azure.

## <a name="create-an-iot-hub"></a>Creare un hub IoT

Parametri obbligatori:

```azurecli
azure iothub create -g <resource-group> -n <name> -l <location> -s <sku-name> -u <units>
```

* **resource-group**. Il nome del gruppo di risorse. Il formato non distingue tra maiuscole e minuscole, usa caratteri alfanumerici, caratteri di sottolineatura e trattini e ammette una lunghezza da 1 a 64.
* **name**. Il nome dell'hub IoT da creare. Il formato non distingue tra maiuscole e minuscole, usa caratteri alfanumerici, caratteri di sottolineatura e trattini e ammette una lunghezza da 3 a 50.
* **location**. Il percorso (area/data center di Azure) per eseguire il provisioning dell'hub IoT.
* **sku-name**. Il nome dello SKU, uno fra: [F1, S1, S2, S3]. Per l'elenco completo più recente, vedere la pagina relativa ai prezzi di IoT Hub.
* **units**. Il numero di unità fornite. Intervallo: F1 [1-1] : S1, S2 [1-200] : S3 [1-10]. Le unità IoT Hub sono basate sul numero totale di messaggi e il numero di dispositivi a cui si vuole connettersi.

Per visualizzare tutti i parametri disponibili per la creazione, è possibile usare il comando help nel prompt dei comandi:

```azurecli
azure iothub create -h
```

Esempio rapido: per creare un hub IoT denominato **exampleIoTHubName** nel gruppo di risorse **exampleResourceGroup**, eseguire questo comando:

```azurecli
azure iothub create -g exampleResourceGroup -n exampleIoTHubName -l westus -k s1 -u 1
```

> [!NOTE]
> Attraverso questo comando dell'interfaccia della riga di comando di Azure viene creato un hub IoT S1 Standard che viene addebitato. Per eliminare l'hub IoT **exampleIoTHubName** eseguire questo comando:
>
> ```azurecli
> azure iothub delete -g exampleResourceGroup -n exampleIoTHubName
> ```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle attività di sviluppo per l'hub IoT, vedere l'articolo seguente:

* [IoT SDKs][lnk-sdks] (SDK di IoT)

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Uso del portale di Azure per gestire l'hub IoT][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-CLI-install]:../cli-install-nodejs.md
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-CLI-arm]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md

[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-portal]: iot-hub-create-through-portal.md 

