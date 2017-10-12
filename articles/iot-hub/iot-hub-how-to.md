---
title: Utilizzo dell'hub IoT di Azure | Documentazione Microsoft
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
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: 786121ae249d69376b4be4c74000868cbb208989
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-azure-iot-hub"></a>Procedure: Usare l'hub IoT di Azure

Sono disponibili varie opzioni per apprendere come sviluppare per il servizio hub IoT:

* Leggere gli articoli concettuali che descrivono le funzionalità dell'hub IoT in dettaglio.
* Seguire una delle esercitazioni che illustrano le varie funzionalità dell'hub IoT.

## <a name="developer-guide"></a>Guida per sviluppatori

Gli sviluppatori possono leggere le linee guida teoriche e dettagliate relative all'hub IoT nella [Guida per gli sviluppatori][lnk-devguide]. Questa guida include:

* Descrizioni dettagliate di tutte le funzionalità dell'hub IoT utili per imparare a usarle.
* Linee guida per la scelta quando sono disponibili più opzioni.

## <a name="tutorials"></a>Esercitazioni

Se si preferisce apprendere le specifiche funzionalità di hub IoT tramite esercizi pratici, sono disponibili numerose esercitazioni tra cui scegliere. Molte esercitazioni sono disponibili anche in più linguaggi di programmazione. Le esercitazioni comprendono:

- [Elaborare messaggi da dispositivo a cloud dell'hub IoT usando i route][lnk-routes-tutorial]. Questa esercitazione illustra come usare le regole di routing di hub IoT per inviare i messaggi da dispositivo a cloud con un semplice metodo basato sulla configurazione.

- [Inviare messaggi da cloud a dispositivo con l'hub IoT][lnk-c2d-tutorial]. Questa esercitazione illustra come inviare messaggi dal cloud al dispositivo tramite l'hub IoT e ricevere messaggi dal cloud al dispositivo su un dispositivo.

- [Eseguire l'upload di file dai dispositivi al cloud con l'hub IoT][lnk-upload-tutorial]. Questa esercitazione illustra come usare le funzionalità di upload di file dell'hub IoT.

- [Introduzione ai dispositivi gemelli][lnk-twin-tutorial]. Questa esercitazione illustra i dispositivi gemelli, le proprietà segnalate, le proprietà desiderate e i tag. I dispositivi gemelli consentono di sincronizzare i valori dei dispositivi dell'utente.

- [Usare metodi diretti][lnk-methods-tutorial]. Questa esercitazione illustra come usare metodi diretti. Aggiungere un gestore per un metodo diretto nel dispositivo simulato e richiamare il metodo diretto dall'hub IoT.

- [Introduzione alla gestione dei dispositivi][lnk-dm-tutorial]. Questa esercitazione illustra come usare le principali funzionalità di gestione dei dispositivi, ad esempio i dispositivi gemelli e i metodi diretti. Queste funzionalità consentono di riavviare in remoto il dispositivo simulato.

- [Usare le proprietà desiderate per configurare i dispositivi][lnk-properties-tutorial]. Questa esercitazione illustra come usare le proprietà desiderate e segnalate del dispositivo gemello per configurare in remoto il dispositivo.

- [Usare i processi del dispositivo per avviare un aggiornamento del firmware del dispositivo][lnk-jobs-tutorial]. Questa esercitazione illustra come usare le principali funzionalità di gestione dei dispositivi, ad esempio i dispositivi gemelli e i metodi diretti. Si impareranno a usare queste funzionalità per aggiornare in remoto il firmware del dispositivo.

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