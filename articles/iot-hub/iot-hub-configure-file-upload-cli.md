---
title: Configurare l'upload del file nell'hub IoT usando l'interfaccia della riga di comando di Azure (az.py) | Microsoft Docs
description: Come configurare gli upload di file nell'hub IoT usando l'interfaccia della riga di comando della multipiattaforma di Azure 2.0 (az.py).
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 915f1597-272d-4fd4-8c5b-a0ccb1df0d91
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: e2cd7eae50006717dfc0da358436ae3553a81d00
ms.contentlocale: it-it
ms.lasthandoff: 08/09/2017

---

# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Configurare gli upload dei file nell'hub IoT tramite l'interfaccia della riga di comando di Azure

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Per usare la [funzionalità di caricamento di file nell'hub IoT][lnk-upload], è prima di tutto necessario associare un account di archiviazione di Azure all'hub IoT. È possibile usare un account di archiviazione esistente o crearne uno nuovo.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non si ha un account, è possibile crearne uno [gratuito][lnk-free-trial] in pochi minuti.
* [Interfaccia della riga di comando di Azure 2.0][lnk-CLI-install].
* Un hub IoT di Azure. Se non si dispone di un hub IoT, è possibile usare il `az iot hub create` [comando][lnk-cli-create-iothub] per crearne uno o il portale per [Creare un hub IoT][lnk-portal-hub].
* Un account dell'Archiviazione di Azure. Se non si dispone di un account di Archiviazione di Azure, è possibile usare l'[interfaccia della riga di comando di Azure 2.0 - Gestione degli account di archiviazione][lnk-manage-storage] per crearne uno o usare il portale per [Creare un account di archiviazione][lnk-portal-storage].

## <a name="sign-in-and-set-your-azure-account"></a>Accedere all'account Azure e impostarlo

Accedere al proprio account Azure e selezionare la sottoscrizione.

1. Al prompt dei comandi eseguire il [comando per l'accesso][lnk-login-command]:

    ```azurecli
    az login
    ```

    Seguire le istruzioni per l'autenticazione tramite il codice e accedere all'account Azure con un Web browser.

1. Se si usano più sottoscrizioni di Azure, effettuando l'accesso ad Azure è possibile accedere a tutti gli account Azure associati alle credenziali. Usare il seguente [comando per elencare gli account Azure][lnk-az-account-command] che è possibile usare:

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
az storage account show-connection-string --name {your storage account name} --resource-group {your storage account resource group}
```

Prendere nota del valore **connectionString**. sarà necessario nei passaggi successivi.

Per i caricamenti dei file, è possibile usare un contenitore BLOB esistente oppure crearne uno nuovo:

* Per elencare i contenitori BLOB esistenti nell'account di archiviazione, usare i comandi seguenti:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Per creare un contenitore BLOB nell'account di archiviazione, usare i comandi seguenti:

    ```azurecli
    az storage container create --name {container name} --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Caricamento di file

È ora possibile configurare l'hub IoT per abilitare la [funzionalità di caricamento dei file][lnk-upload] usando i dettagli dell'account di archiviazione.

La configurazione richiede i valori seguenti:

**Contenitore di archiviazione**: un contenitore BLOB in un account di archiviazione di Azure nella sottoscrizione corrente da associare all'hub IoT. Le informazioni necessarie sull'account di archiviazione sono state recuperate nella sezione precedente. L'hub IoT genera automaticamente URI di firma di accesso condiviso con autorizzazioni di scrittura per questo contenitore BLOB che possono essere usati dai dispositivi durante il caricamento di file.

**Receive notifications for uploaded files** (Ricezione di notifiche per i file caricati): abilitare o disabilitare le notifiche di caricamento del file.

**SAS TTL**(TTL di firma di accesso condiviso): questa impostazione indica la durata degli URI di firma di accesso condiviso restituiti dal dispositivo tramite l’hub IoT. Il valore è un'ora per impostazione predefinita.

**File notification settings default TTL**(TTL predefinito per le impostazioni di notifica dei file): durata di una notifica di caricamento del file. Il valore è un giorno per impostazione predefinita.

**File notification maximum delivery count**(Numero massimo di recapiti per le notifiche dei file): numero di tentativi che verranno eseguiti dall'hub IoT per distribuire una notifica di caricamento del file. Il valore è 10 per impostazione predefinita.

Usare i comandi dell'interfaccia della riga di comando di Azure per configurare le impostazioni di upload dei file nell'hub IoT:

In un uso di shell bash:

```azurecli
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.connectionString="{your storage account connection string}"
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.containerName="{your storage container name}"
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.sasTtlAsIso8601=PT1H0M0S

az iot hub update --name {your iot hub name} --set properties.enableFileUploadNotifications=true
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

Nell'uso di un prompt dei comandi di Windows:

```azurecli
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.connectionString="{your storage account connection string}""
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.containerName="{your storage container name}""
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.sasTtlAsIso8601=PT1H0M0S"

az iot hub update --name {your iot hub name} --set properties.enableFileUploadNotifications=true
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

È possibile esaminare la configurazione di upload del file sull'hub IoT usando il comando seguente:

```azurecli
az iot hub show --name {your iot hub name}
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle funzionalità di caricamento dei file dell'hub IoT, vedere [Caricare file da un dispositivo][lnk-upload].

Per ulteriori informazioni sulla gestione dell'hub IoT di Azure, consultare questi collegamenti:

* [Gestire in blocco i dispositivi IoT][lnk-bulk]
* [Metriche di Hub IoT][lnk-metrics]
* [Monitoraggio delle operazioni][lnk-monitor]

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Guida per gli sviluppatori dell'hub IoT][lnk-devguide]
* [Simulazione di un dispositivo con IoT Edge][lnk-iotedge]
* [Proteggere la soluzione IoT sin dall'inizio][lnk-securing]

[13]: ./media/iot-hub-configure-file-upload/file-upload-settings.png
[14]: ./media/iot-hub-configure-file-upload/file-upload-container-selection.png
[15]: ./media/iot-hub-configure-file-upload/file-upload-selected-container.png

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md


[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-manage-storage]:../storage/common/storage-azure-cli.md#manage-storage-accounts
[lnk-portal-storage]:../storage/common/storage-create-storage-account.md
[lnk-cli-create-iothub]: https://docs.microsoft.com/cli/azure/iot/hub#az_iot_hub_create
