---
title: Usare Azure PowerShell per configurare il caricamento dei file | Microsoft Docs
description: Come usare i cmdlet di Azure PowerShell per configurare l'hub IoT per abilitare i caricamenti di file da dispositivi connessi. Include informazioni sulla configurazione dell'account di archiviazione di Azure di destinazione.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robin.shahan
ms.openlocfilehash: 9754fe2bedae9c1eaf6b18614014485dbe8051f2
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010478"
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>Configurare i caricamenti dei file nell'hub IoT con PowerShell

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Per usare la [funzionalità di caricamento di file nell'hub IoT](iot-hub-devguide-file-upload.md), è prima di tutto necessario associare un account di archiviazione di Azure all'hub IoT. È possibile usare un account di archiviazione esistente o crearne uno nuovo.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

* [cmdlet di Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

* Un hub IoT di Azure. Se non si dispone di un hub IoT, è possibile usare la [cmdlet New-AzIoTHub](https://docs.microsoft.com/powershell/module/az.iothub/new-aziothub) per crearne uno o usare il portale per [creare un hub IoT](iot-hub-create-through-portal.md).

* Un account di archiviazione di Azure. Se non si dispone di un account di archiviazione di Azure, è possibile usare i [cmdlet PowerShell di Archiviazione di Azure](https://docs.microsoft.com/powershell/module/az.storage/) per crearne uno o usare il portale per [creare un account di archiviazione](../storage/common/storage-create-storage-account.md)

## <a name="sign-in-and-set-your-azure-account"></a>Accedere all'account Azure e impostarlo

Accedere al proprio account Azure e selezionare la sottoscrizione.

1. Al prompt di PowerShell, eseguire la **Connect-AzAccount** cmdlet:

    ```powershell
    Connect-AzAccount
    ```

2. Se si usano più sottoscrizioni Azure e si esegue l'accesso ad Azure, è possibile accedere a tutte le sottoscrizioni di Azure associate alle credenziali. Usare il comando seguente per elencare gli account Azure che è possibile usare:

    ```powershell
    Get-AzSubscription
    ```

    Usare il comando seguente per selezionare la sottoscrizione che si vuole usare per eseguire i comandi per gestire l'hub IoT. È possibile usare il nome o l'ID della sottoscrizione dall'output del comando precedente:

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

## <a name="retrieve-your-storage-account-details"></a>Recuperare i dettagli dell'account di archiviazione

I passaggi seguenti presuppongono che l'account di archiviazione sia stato creato tramite il modello di distribuzione di **Resource Manager** e non tramite quello **Classico**.

Per configurare i caricamenti dei file dai propri dispositivi, è necessario disporre della stringa di connessione di un account di Archiviazione di Azure. L'account di archiviazione deve trovarsi nella stessa sottoscrizione dell'hub IoT. È inoltre necessario il nome del contenitore BLOB nell'account di archiviazione. Usare il comando seguente per recuperare le chiavi dell'account di archiviazione:

```powershell
Get-AzStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

Prendere nota del valore della chiave dell'account di archiviazione **key1**. sarà necessario nei passaggi successivi.

Per i caricamenti dei file, è possibile usare un contenitore BLOB esistente oppure crearne uno nuovo:

* Per elencare i contenitori BLOB esistente nell'account di archiviazione, usare i comandi seguenti:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzStorageContainer -Context $ctx
    ```

* Per creare un contenitore BLOB nell'account di archiviazione, usare i comandi seguenti:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>Configurare l'hub IoT

È ora possibile configurare l'hub IoT per [caricare i file nell'hub IoT](iot-hub-devguide-file-upload.md) usando i dettagli dell'account di archiviazione.

La configurazione richiede i valori seguenti:

* **Contenitore di archiviazione**: contenitore BLOB di un account di archiviazione di Azure nella sottoscrizione di Azure corrente da associare all'hub IoT. Le informazioni necessarie sull'account di archiviazione sono state recuperate nella sezione precedente. L'hub IoT genera automaticamente URI di firma di accesso condiviso con autorizzazioni di scrittura per questo contenitore BLOB che possono essere usati dai dispositivi durante il caricamento di file.

* **Ricevi notifiche per i file caricati**: consente di abilitare o disabilitare le notifiche di caricamento dei file.

* **Durata TTL della firma di accesso condiviso**: questa impostazione indica la durata (TTL) degli URI di firma di accesso condiviso restituiti al dispositivo dall'hub IoT. Il valore è un'ora per impostazione predefinita.

* **File notification settings default TTL**(TTL predefinito per le impostazioni di notifica dei file): durata (TTL) di una notifica di caricamento dei file prima della scadenza. Il valore è un giorno per impostazione predefinita.

* **File notification maximum delivery count** (Numero massimo di recapiti per le notifiche dei file): numero di volte in cui l'hub IoT prova a recapitare una notifica di caricamento dei file. Il valore è 10 per impostazione predefinita.

Usare il cmdlet PowerShell seguente per configurare le impostazioni di caricamento dei file nell'hub IoT:

```powershell
Set-AzIotHub `
    -ResourceGroupName "{your iot hub resource group}" `
    -Name "{your iot hub name}" `
    -FileUploadNotificationTtl "01:00:00" `
    -FileUploadSasUriTtl "01:00:00" `
    -EnableFileUploadNotifications $true `
    -FileUploadStorageConnectionString "DefaultEndpointsProtocol=https;AccountName={your storage account name};AccountKey={your storage account key};EndpointSuffix=core.windows.net" `
    -FileUploadContainerName "{your blob container name}" `
    -FileUploadNotificationMaxDeliveryCount 10
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
