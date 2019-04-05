---
title: Usare il portale di Azure per configurare il caricamento dei file | Microsoft Docs
description: Come usare il portale di Azure per configurare l'hub IoT per abilitare i caricamenti di file da dispositivi connessi. Include informazioni sulla configurazione dell'account di archiviazione di Azure di destinazione.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/03/2017
ms.author: robinsh
ms.openlocfilehash: bd7cc37b8fc81fc9d4109826743f2243913d0604
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59047280"
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Configurare i caricamenti dei file nell'hub IoT con il portale di Azure

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>Caricamento di file

Per usare la [funzionalità di caricamento di file nell'hub IoT](iot-hub-devguide-file-upload.md), è prima di tutto necessario associare un account di archiviazione di Azure all'hub. Selezionare **Caricamento del file** per visualizzare un elenco di proprietà di caricamento del file per l'hub IoT da modificare.

![Visualizzare le impostazioni di caricamento di file dell'hub IoT nel portale](./media/iot-hub-configure-file-upload/file-upload-settings.png)

* **Contenitore di archiviazione**: Usare il portale di Azure per selezionare un contenitore blob in un account di archiviazione di Azure nella sottoscrizione corrente da associare all'IoT Hub. Se necessario, è possibile creare un account di archiviazione di Azure nel pannello **Account di archiviazione** e un nuovo contenitore BLOB nel pannello **Contenitori**. L'hub IoT genera automaticamente URI di firma di accesso condiviso con autorizzazioni di scrittura per questo contenitore BLOB che possono essere usati dai dispositivi durante il caricamento di file.

   ![Visualizzare i contenitori di archiviazione per il caricamento di file nel portale](./media/iot-hub-configure-file-upload/file-upload-container-selection.png)

* **Ricevi notifiche per i file caricati**: Abilitare o disabilitare le notifiche di caricamento di file tramite l'elemento toggle.

* **Durata TTL della firma di accesso condiviso**: questa impostazione indica la durata (TTL) degli URI di firma di accesso condiviso restituiti al dispositivo dall'hub IoT. Per impostazione predefinita è impostato su un'ora, ma può essere personalizzato con altri valori tramite il dispositivo di scorrimento.

* **File notification settings default TTL**(TTL predefinito per le impostazioni di notifica dei file): durata (TTL) di una notifica di caricamento dei file prima della scadenza. Per impostazione predefinita è impostato su un giorno, ma può essere personalizzato con altri valori tramite il dispositivo di scorrimento.

* **File notification maximum delivery count** (Numero massimo di recapiti per le notifiche dei file): numero di volte in cui l'hub IoT prova a recapitare una notifica di caricamento dei file. Per impostazione predefinita è impostato su 10, ma può essere personalizzato con altri valori tramite il dispositivo di scorrimento.

   ![Configurare il caricamento di file dell'hub IoT nel portale](./media/iot-hub-configure-file-upload/file-upload-selected-container.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle funzionalità di caricamento dei file dell'hub IoT, vedere [Caricare file da un dispositivo](iot-hub-devguide-file-upload.md) nella Guida per gli sviluppatori dell'hub IoT.

Per ulteriori informazioni sulla gestione dell'hub IoT di Azure, consultare questi collegamenti:

* [Gestire in blocco i dispositivi IoT](iot-hub-bulk-identity-mgmt.md)
* [Metriche di Hub IoT](iot-hub-metrics.md)
* [Monitoraggio delle operazioni](iot-hub-operations-monitoring.md)

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Guida per gli sviluppatori dell'IoT Hub](iot-hub-devguide.md)
* [Distribuzione dell'intelligenza artificiale in dispositivi perimetrali con Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
* [Proteggere la soluzione IoT sin dall'inizio backup](../iot-fundamentals/iot-security-ground-up.md)
