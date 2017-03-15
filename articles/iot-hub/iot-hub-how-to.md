---
title: Utilizzo dell&quot;hub IoT di Azure | Documentazione Microsoft
description: "In che modo gli sviluppatori usano le varie funzionalità di hub IoT?"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 6fbdebd316cd00e7dd762487848e616fdd2317e8
ms.openlocfilehash: 358fd0888049f97e5fde6e2a6303ea6c4cf65da9
ms.lasthandoff: 03/01/2017


---
# <a name="how-to-use-azure-iot-hub"></a>Procedure: Usare l'hub IoT di Azure

Sono disponibili varie opzioni per apprendere come sviluppare per il servizio hub IoT. È possibile leggere gli articoli teorici che descrivono le funzionalità di hub IoT in dettaglio o mettere in pratica una delle esercitazioni che illustrano le diverse funzionalità di hub IoT.

## <a name="the-developer-guide"></a>Guida per sviluppatori

Gli sviluppatori possono leggere le linee guida teoriche e dettagliate relative all'hub IoT nella [Guida per gli sviluppatori][lnk-devguide]. Questa guida include descrizioni dettagliate di tutte le funzionalità dell'hub IoT, che aiutano a comprenderne l'uso e a scegliere tra le varie opzioni disponibili.


## <a name="the-tutorials"></a>Esercitazioni

Se si preferisce apprendere le specifiche funzionalità di hub IoT tramite esercizi pratici, sono disponibili numerose esercitazioni tra cui scegliere. Molte esercitazioni sono disponibili anche in più linguaggi di programmazione. Le esercitazioni comprendono:

- [Elaborare messaggi da dispositivo a cloud dell'hub IoT usando i route][lnk-routes-tutorial]. Questa esercitazione illustra come usare le regole di routing di hub IoT per inviare i messaggi da dispositivo a cloud con un semplice metodo basato sulla configurazione.

- [Inviare messaggi da cloud a dispositivo con l'hub IoT][lnk-c2d-tutorial]. Questa esercitazione illustra come inviare messaggi dal cloud al dispositivo tramite l'hub IoT e ricevere messaggi dal cloud al dispositivo su un dispositivo.

- [Eseguire l'upload di file dai dispositivi al cloud con l'hub IoT][lnk-upload-tutorial]. Questa esercitazione illustra come usare le funzionalità di upload di file dell'hub IoT.

- [Introduzione ai dispositivi gemelli][lnk-twin-tutorial]. Questa esercitazione illustra i dispositivi gemelli, le proprietà segnalate, le proprietà desiderate e i tag. I dispositivi gemelli consentono di sincronizzare i valori dei dispositivi dell'utente.

- [Usare metodi diretti][lnk-methods-tutorial]. Questa esercitazione illustra come usare metodi diretti. Aggiungere un gestore per un metodo diretto nel dispositivo simulato e richiamare il metodo diretto dall'hub IoT.

- [Introduzione alla gestione dei dispositivi][lnk-dm-tutorial]. Questa esercitazione illustra come usare le principali funzionalità di gestione dei dispositivi, ad esempio i dispositivi gemelli e i metodi diretti per riavviare in remoto il dispositivo simulato.

- [Usare le proprietà desiderate per configurare i dispositivi][lnk-properties-tutorial]. Questa esercitazione illustra come usare le proprietà desiderate del dispositivo gemello in combinazione con le proprietà segnalate per configurare in remoto il dispositivo.

- [Usare i processi del dispositivo per avviare un aggiornamento del firmware del dispositivo][lnk-jobs-tutorial]. Questa esercitazione illustra come usare le principali funzionalità di gestione dei dispositivi, ad esempio i dispositivi gemelli e i metodi diretti per aggiornare in remoto il firmware del dispositivo.

- [Pianificare e trasmettere processi][lnk-schedule-tutorial]. Questa esercitazione illustra come usare le proprietà desiderate e i metodi diretti per interagire con più dispositivi a un orario pianificato.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul servizio hub IoT, vedere la [Guida per gli sviluppatori][lnk-devguide].

[lnk-devguide]: ./iot-hub-devguide.md
[lnk-routes-tutorial]: ./iot-hub-csharp-csharp-process-d2c.md
[lnk-c2d-tutorial]: ./iot-hub-csharp-csharp-c2d.md
[lnk-upload-tutorial]: ./iot-hub-csharp-csharp-file-upload.md
[lnk-twin-tutorial]: ./iot-hub-node-node-twin-getstarted.md
[lnk-methods-tutorial]: ./iot-hub-node-node-direct-methods.md
[lnk-dm-tutorial]: ./iot-hub-node-node-device-management-get-started.md
[lnk-properties-tutorial]: ./iot-hub-node-node-twin-how-to-configure.md
[lnk-jobs-tutorial]: ./iot-hub-node-node-firmware-update.md
[lnk-schedule-tutorial]: ./iot-hub-node-node-schedule-jobs.md
