---
title: Usare il portale di Azure per configurare il caricamento dei file | Microsoft Docs
description: Come usare il portale di Azure per configurare l'hub IoT per abilitare i caricamenti di file da dispositivi connessi. Include informazioni sulla configurazione dell'account di archiviazione di Azure di destinazione.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/03/2017
ms.author: dobett
ms.openlocfilehash: a9f9eeaed2716c5d492099568fd6f90080471af2
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/17/2018
ms.locfileid: "42141377"
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Configurare i caricamenti dei file nell'hub IoT con il portale di Azure

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>Caricamento di file

Per usare la [funzionalità di caricamento di file nell'hub IoT](iot-hub-devguide-file-upload.md), è prima di tutto necessario associare un account di archiviazione di Azure all'hub. Selezionare **Caricamento del file** per visualizzare un elenco di proprietà di caricamento del file per l'hub IoT da modificare.

![Visualizzare le impostazioni di caricamento di file dell'hub IoT nel portale](./media/iot-hub-configure-file-upload/file-upload-settings.png)

* **Contenitore di archiviazione**: usare il portale di Azure per selezionare il contenitore BLOB in un account di archiviazione di Azure nella sottoscrizione corrente da associare all'hub IoT. Se necessario, è possibile creare un account di archiviazione di Azure nel pannello **Account di archiviazione** e un nuovo contenitore BLOB nel pannello **Contenitori**. L'hub IoT genera automaticamente URI di firma di accesso condiviso con autorizzazioni di scrittura per questo contenitore BLOB che possono essere usati dai dispositivi durante il caricamento di file.

   ![Visualizzare i contenitori di archiviazione per il caricamento di file nel portale](./media/iot-hub-configure-file-upload/file-upload-container-selection.png)

* **Receive notifications for uploaded files**(Ricezione di notifiche per i file caricati): abilitare o disabilitare le notifiche di caricamento del file mediante l'apposita opzione.

* **SAS TTL**(TTL di firma di accesso condiviso): questa impostazione indica la durata degli URI di firma di accesso condiviso restituiti dal dispositivo tramite l’hub IoT. Per impostazione predefinita è impostato su un'ora, ma può essere personalizzato con altri valori tramite il dispositivo di scorrimento.

* **File notification settings default TTL**(TTL predefinito per le impostazioni di notifica dei file): durata di una notifica di caricamento del file. Per impostazione predefinita è impostato su un giorno, ma può essere personalizzato con altri valori tramite il dispositivo di scorrimento.

* **File notification maximum delivery count**(Numero massimo di recapiti per le notifiche dei file): numero di tentativi che verranno eseguiti dall'hub IoT per distribuire una notifica di caricamento del file. Per impostazione predefinita è impostato su 10, ma può essere personalizzato con altri valori tramite il dispositivo di scorrimento.

   ![Configurare il caricamento di file dell'hub IoT nel portale](./media/iot-hub-configure-file-upload/file-upload-selected-container.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle funzionalità di caricamento dei file dell'hub IoT, vedere [Caricare file da un dispositivo](iot-hub-devguide-file-upload.md) nella Guida per gli sviluppatori dell'hub IoT.

Per ulteriori informazioni sulla gestione dell'hub IoT di Azure, consultare questi collegamenti:

* [Gestire in blocco i dispositivi IoT](iot-hub-bulk-identity-mgmt.md)
* [Metriche di hub IoT](iot-hub-metrics.md)
* [Monitoraggio delle operazioni](iot-hub-operations-monitoring.md)

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Guida per sviluppatori dell'hub IoT](iot-hub-devguide.md)
* [Distribuzione dell'intelligenza artificiale in dispositivi perimetrali con Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
* [Proteggere la soluzione IoT sin dall'inizio](../iot-fundamentals/iot-security-ground-up.md)
