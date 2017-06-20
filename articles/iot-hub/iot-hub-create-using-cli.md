---
title: Creare un hub IoT usando l'interfaccia della riga di comando di Azure (az.py) | Documentazione Microsoft
description: Come creare un hub IoT di Azure usando l'interfaccia della riga di comando di Azure 2.0 multipiattaforma (az.py).
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-hub
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 159aed19b4c5e381ef15c40c15cf6bd7694d2fa3
ms.contentlocale: it-it
ms.lasthandoff: 03/24/2017


---
# <a name="create-an-iot-hub-using-the-azure-cli-20"></a>Creare un hub IoT usando l'interfaccia della riga di comando di Azure 2.0

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduzione

È possibile usare l'interfaccia della riga di comando di Azure 2.0 (az.py) per creare e gestire hub IoT di Azure a livello di codice. Questo articolo illustra come usare l'interfaccia della riga di comando di Azure 2.0 (az.py) per creare un hub IoT.

È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando:

* [Interfaccia della riga di comando di Azure (azure.js)](iot-hub-create-using-cli-nodejs.md): l'interfaccia della riga di comando per i modelli di distribuzione classica e di gestione delle risorse.
* Interfaccia della riga di comando di Azure 2.0 (az.py): interfaccia avanzata per il modello di distribuzione di gestione delle risorse, come descritto in questo articolo.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non si ha un account, è possibile crearne uno [gratuito][lnk-free-trial] in pochi minuti.
* [Interfaccia della riga di comando di Azure 2.0][lnk-CLI-install].

## <a name="sign-in-and-set-your-azure-account"></a>Accedere all'account Azure e impostarlo

Accedere al proprio account Azure e selezionare la sottoscrizione.

1. Al prompt dei comandi eseguire il [comando per l'accesso][lnk-login-command]:
    
    ```azurecli
    az login
    ```

    Seguire le istruzioni per l'autenticazione tramite il codice e accedere all'account Azure con un Web browser.

2. Se si usano più sottoscrizioni di Azure, effettuando l'accesso ad Azure è possibile accedere a tutti gli account Azure associati alle credenziali. Usare il seguente [comando per elencare gli account Azure][lnk-az-account-command] che è possibile usare:
    
    ```azurecli
    az account list 
    ```

    Usare il comando seguente per selezionare la sottoscrizione che si vuole usare per eseguire i comandi per creare l'hub IoT. È possibile usare il nome o l'ID della sottoscrizione dall'output del comando precedente:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="create-an-iot-hub"></a>Creare un hub IoT

Usare l'interfaccia della riga di comando di Azure per creare un gruppo di risorse e quindi aggiungere un hub IoT.

1. Quando si crea un hub IoT, è necessario crearlo in un gruppo di risorse. Usare un gruppo di risorse esistente o eseguire questo [comando per creare un gruppo di risorse][lnk-az-resource-command]:
    
    ```azurecli
     az group create --name {your resource group name} --location westus
    ```

    > [!TIP]
    > L'esempio precedente crea il gruppo di risorse nella località Stati Uniti occidentali. È possibile visualizzare un elenco di località disponibili eseguendo il comando `az account list-locations -o table`.
    >
    >

2. Eseguire questo [comando per creare un hub IoT][lnk-az-iot-command] nel gruppo di risorse:
    
    ```azurecli
    az iot hub create --name {your iot hub name} --resource-group {your resource group name} --sku S1
    ```

> [!NOTE]
> Il nome dell'hub IoT deve essere globalmente univoco. Il comando precedente crea un hub IoT nel piano tariffario S1 che viene fatturato. Per altre informazioni, vedere [Azure IoT Hub Prezzi][lnk-iot-pricing].
>
>

## <a name="remove-an-iot-hub"></a>Rimuovere un hub IoT

È possibile usare l'interfaccia della riga di comando di Azure per [eliminare una singola risorsa][lnk-az-resource-command], ad esempio un hub IoT, o eliminare un gruppo di risorse e tutte le risorse, inclusi gli hub IoT.

Per eliminare un hub IoT, eseguire questo comando:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

Per eliminare un gruppo di risorse e tutte le risorse, eseguire questo comando:

```azurecli
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle attività di sviluppo per l'hub IoT, vedere gli articoli seguenti:

* [Guida per gli sviluppatori dell'hub IoT][lnk-devguide]

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Uso del portale di Azure per gestire l'hub IoT][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-devguide]: iot-hub-devguide.md
[lnk-portal]: iot-hub-create-through-portal.md 

