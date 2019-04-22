---
title: Configurare il caricamento del file nell'hub IoT usando l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Come configurare i caricamenti di file nell'hub IoT usando l'interfaccia della riga di comando multipiattaforma di Azure.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: fe6ce23b9e87235521739b7808712a9d541dabf9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59048963"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Configurare gli upload dei file nell'hub IoT tramite l'interfaccia della riga di comando di Azure

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Per [caricare i file da un dispositivo](iot-hub-devguide-file-upload.md), è prima di tutto necessario associare un account di archiviazione di Azure all'hub IoT. È possibile usare un account di archiviazione esistente o crearne uno nuovo.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

* Un hub IoT di Azure. Se non si dispone di un hub IoT, è possibile usare il comando [ `az iot hub create` ](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create) per crearne uno o [creare un hub IoT usando il portale](iot-hub-create-through-portal.md).

* Un account dell'Archiviazione di Azure. Se non si dispone di un account di archiviazione di Azure, è possibile usare l'[interfaccia della riga di comando di Azure - Gestione degli account di archiviazione](../storage/common/storage-azure-cli.md#manage-storage-accounts) per crearne uno o usare il portale per [creare un account di archiviazione](../storage/common/storage-create-storage-account.md).

## <a name="sign-in-and-set-your-azure-account"></a>Accedere all'account Azure e impostarlo

Accedere al proprio account Azure e selezionare la sottoscrizione.

1. Al prompt dei comandi eseguire il [comando per l'accesso](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest):

    ```azurecli
    az login
    ```

    Seguire le istruzioni per l'autenticazione tramite il codice e accedere all'account Azure con un Web browser.

2. Se si usano più sottoscrizioni di Azure, effettuando l'accesso ad Azure è possibile accedere a tutti gli account Azure associati alle credenziali. Usare il seguente [comando per elencare gli account Azure](https://docs.microsoft.com/cli/azure/account) che è possibile usare:

    ```azurecli
    az account list
    ```

    Usare il comando seguente per selezionare la sottoscrizione che si vuole usare per eseguire i comandi per creare l'hub IoT. È possibile usare il nome o l'ID della sottoscrizione dall'output del comando precedente:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>Recuperare i dettagli dell'account di archiviazione

I passaggi seguenti presuppongono che l'account di archiviazione sia stato creato tramite il modello di distribuzione di **Resource Manager** e non tramite quello **Classico**.

Per configurare i caricamenti dei file dai propri dispositivi, è necessario disporre della stringa di connessione di un account di Archiviazione di Azure. L'account di archiviazione deve trovarsi nella stessa sottoscrizione dell'hub IoT. È inoltre necessario il nome del contenitore BLOB nell'account di archiviazione. Usare il comando seguente per recuperare le chiavi dell'account di archiviazione:

```azurecli
az storage account show-connection-string --name {your storage account name} \
  --resource-group {your storage account resource group}
```

Prendere nota del valore **connectionString**. sarà necessario nei passaggi successivi.

Per i caricamenti dei file, è possibile usare un contenitore BLOB esistente oppure crearne uno nuovo:

* Per elencare i contenitori BLOB esistenti nell'account di archiviazione, usare i comandi seguenti:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Per creare un contenitore BLOB nell'account di archiviazione, usare i comandi seguenti:

    ```azurecli
    az storage container create --name {container name} \
      --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Caricamento di file

È ora possibile configurare l'hub IoT per abilitare il [caricamento dei file nell'hub IoT](iot-hub-devguide-file-upload.md) usando i dettagli dell'account di archiviazione.

La configurazione richiede i valori seguenti:

* **Contenitore di archiviazione**: contenitore BLOB di un account di archiviazione di Azure nella sottoscrizione di Azure corrente da associare all'hub IoT. Le informazioni necessarie sull'account di archiviazione sono state recuperate nella sezione precedente. L'hub IoT genera automaticamente URI di firma di accesso condiviso con autorizzazioni di scrittura per questo contenitore BLOB che possono essere usati dai dispositivi durante il caricamento di file.

* **Ricevi notifiche per i file caricati**: consente di abilitare o disabilitare le notifiche di caricamento dei file.

* **Durata TTL della firma di accesso condiviso**: questa impostazione indica la durata (TTL) degli URI di firma di accesso condiviso restituiti al dispositivo dall'hub IoT. Il valore è un'ora per impostazione predefinita.

* **File notification settings default TTL**(TTL predefinito per le impostazioni di notifica dei file): durata (TTL) di una notifica di caricamento dei file prima della scadenza. Il valore è un giorno per impostazione predefinita.

* **File notification maximum delivery count** (Numero massimo di recapiti per le notifiche dei file): numero di volte in cui l'hub IoT prova a recapitare una notifica di caricamento dei file. Il valore è 10 per impostazione predefinita.

Usare i comandi dell'interfaccia della riga di comando di Azure per configurare le impostazioni di upload dei file nell'hub IoT:

<!--Robinsh this is out of date, add cloud powershell -->

In una shell Bash usare:

```azurecli
az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.connectionString="{your storage account connection string}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.containerName="{your storage container name}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.sasTtlAsIso8601=PT1H0M0S

az iot hub update --name {your iot hub name} \
  --set properties.enableFileUploadNotifications=true

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

È possibile esaminare la configurazione di upload del file sull'hub IoT usando il comando seguente:

```azurecli
az iot hub show --name {your iot hub name}
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle funzionalità di caricamento dei file dell'hub IoT, vedere [Caricare file da un dispositivo](iot-hub-devguide-file-upload.md).

Per ulteriori informazioni sulla gestione dell'hub IoT di Azure, consultare questi collegamenti:

* [Gestire in blocco i dispositivi IoT](iot-hub-bulk-identity-mgmt.md)
* [Metriche di hub IoT](iot-hub-metrics.md)
* [Monitoraggio delle operazioni](iot-hub-operations-monitoring.md)

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Guida per sviluppatori dell'hub IoT](iot-hub-devguide.md)
* [Distribuzione dell'intelligenza artificiale in dispositivi perimetrali con Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
* [Proteggere la soluzione IoT sin dall'inizio](../iot-fundamentals/iot-security-ground-up.md)
