---
title: Usare il portale di Azure per configurare il caricamento dei file | Documentazione Microsoft
description: Panoramica su come configurare il caricamento dei file tramite il portale di Azure
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
ms.date: 09/30/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e3ac3e8cee2724b76f51423d1a6757382cca04f0


---
# <a name="configure-file-uploads-using-the-azure-portal"></a>Configurare i caricamenti dei file tramite il portale di Azure
## <a name="file-upload"></a>Caricamento di file
Per usare la [funzionalità di caricamento di file nell'hub IoT][lnk-upload], è prima di tutto necessario associare un account di archiviazione di Azure all'hub. Selezionare le impostazioni di **Caricamento del file** per visualizzare un elenco di proprietà di caricamento del file per l'hub IoT da modificare.

![][13]

**Contenitore di archiviazione**: usare il portale di Azure per selezionare il contenitore BLOB in un account di archiviazione di Azure nella sottoscrizione corrente da associare all'hub IoT. Se necessario, è possibile creare un account di archiviazione di Azure nel pannello **Account di archiviazione** e un nuovo contenitore BLOB nel pannello **Contenitori**. L'hub IoT genera automaticamente URI di firma di accesso condiviso con autorizzazioni di scrittura per questo contenitore BLOB che possono essere usati dai dispositivi durante il caricamento di file.

![][14]

**Receive notifications for uploaded files**(Ricezione di notifiche per i file caricati): abilitare o disabilitare le notifiche di caricamento del file mediante l'apposita opzione.

**SAS TTL**(TTL di firma di accesso condiviso): questa impostazione indica la durata degli URI di firma di accesso condiviso restituiti dal dispositivo tramite l’hub IoT. Per impostazione predefinita è impostato su un'ora, ma può essere personalizzato con altri valori tramite il dispositivo di scorrimento.

**File notification settings default TTL**(TTL predefinito per le impostazioni di notifica dei file): durata di una notifica di caricamento del file. Per impostazione predefinita è impostato su un giorno, ma può essere personalizzato con altri valori tramite il dispositivo di scorrimento.

**File notification maximum delivery count**(Numero massimo di recapiti per le notifiche dei file): numero di tentativi che verranno eseguiti dall'hub IoT per distribuire una notifica di caricamento del file. Per impostazione predefinita è impostato su 10, ma può essere personalizzato con altri valori tramite il dispositivo di scorrimento.

![][15]

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle funzionalità di caricamento dei file dell'hub IoT, vedere [Upload files from a device][lnk-upload] (Caricare i file da un dispositivo) nella Guida per gli sviluppatori.

Per ulteriori informazioni sulla gestione dell'hub IoT di Azure, consultare questi collegamenti:

* [Gestire in blocco i dispositivi IoT][lnk-bulk]
* [Metriche di uso][lnk-metrics]
* [Monitoraggio delle operazioni][lnk-monitor]

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Guida per gli sviluppatori][lnk-devguide]
* [Simulazione di un dispositivo con IoT Gateway SDK][lnk-gateway]
* [Proteggere la soluzione IoT sin dall'inizio][lnk-securing]

[13]: ./media/iot-hub-configure-file-upload/file-upload-settings.png
[14]: ./media/iot-hub-configure-file-upload/file-upload-container-selection.png
[15]: ./media/iot-hub-configure-file-upload/file-upload-selected-container.png

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md



<!--HONumber=Nov16_HO3-->


