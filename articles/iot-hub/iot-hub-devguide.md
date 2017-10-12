---
title: Guida per gli sviluppatori dell'hub IoT di Azure | Documentazione Microsoft
description: "La Guida per gli sviluppatori dell'hub IoT di Azure include informazioni su endpoint, sicurezza, registro delle identità, gestione dei dispositivi, metodi diretti, dispositivi gemelli, caricamenti di file, processi, linguaggio di query dell'hub IoT e messaggistica."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: d534ff9d-2de5-4995-bb2d-84a02693cb2e
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: dobett
ms.openlocfilehash: 07497231e2cfab86082c5a0d1f4a4ec8ab31f6a3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-iot-hub-developer-guide"></a>Guida per gli sviluppatori dell'hub IoT di Azure

L'hub IoT di Azure è un servizio completamente gestito che consente di abilitare comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi e un back-end della soluzione.

L'hub IoT di Azure offre:

* Comunicazioni protette con credenziali di sicurezza per singoli dispositivi e controllo di accesso.
* Più opzioni di comunicazione da dispositivo a cloud e da cloud a dispositivo su vasta scala.
* Archivio disponibile per query con informazioni di stato e metadati per ogni dispositivo.
* Connettività semplice dei dispositivi con librerie di dispositivi per i linguaggi e le piattaforme più diffusi.

Questa Guida per gli sviluppatori dell'hub IoT include gli articoli seguenti:

* [Device-to-cloud communications guidance][lnk-d2c-guidance] (Indicazioni sulla comunicazione da dispositivo a cloud), utile nella scelta tra messaggi da dispositivo a cloud, proprietà segnalate del dispositivo gemello e caricamento di file.
* [Cloud-to-device communication guidance][lnk-c2d-guidance] (Indicazioni sulla comunicazione da cloud a dispositivo), utile nella scelta tra metodi diretti, proprietà desiderate del dispositivo gemello e messaggi da cloud a dispositivo.
* [Inviare e ricevere messaggi con l'hub IoT][devguide-messaging] descrive le funzionalità di messaggistica da dispositivo a cloud e da cloud a dispositivo esposte dall'hub IoT.
  * [Inviare messaggi da dispositivo a cloud all'hub IoT][devguide-messages-d2c].
  * [Leggere messaggi da dispositivo a cloud dall'endpoint predefinito][devguide-builtin].
  * [Usare endpoint personalizzati e regole di routing per i messaggi da dispositivo a cloud][devguide-custom].
  * [Inviare messaggi da cloud a dispositivo dall'hub IoT][devguide-messages-c2d].
  * [Creare e leggere messaggi dell'hub IoT][devguide-format].
* [Upload files from a device][devguide-upload] (Caricare file da un dispositivo), che descrive come caricare file da un dispositivo. Questo articolo contiene anche informazioni sul modo in cui le notifiche possono essere inviate dal processo di aggiornamento.
* [Manage device identities in IoT Hub][devguide-identities] (Gestire le identità dei dispositivi nell'hub IoT), che descrive le informazioni archiviate nel registro delle identità di ogni hub IoT e spiega come accedere alle informazioni e modificarle.
* [Controllare l'accesso all'hub IoT][devguide-security], che descrive il modello di sicurezza usato per concedere l'accesso alla funzionalità hub IoT per i dispositivi e i componenti cloud. In questo articolo sono incluse informazioni sull'uso di token e certificati X.509, oltre ai dettagli delle autorizzazioni che è possibile concedere.
* [Usare dispositivi gemelli per sincronizzare stato e configurazioni][devguide-device-twins], che descrive il concetto di *dispositivi gemelli* e la funzionalità esposta, come la sincronizzazione di un dispositivo con il suo gemello. In questo articolo sono incluse informazioni sui dati archiviati in un dispositivo gemello.
* [Richiamare un metodo diretto in un dispositivo][devguide-directmethods], che descrive il ciclo di vita di un metodo diretto, informazioni su come richiamare i metodi in un dispositivo dall'app back-end e gestire il metodo diretto nel dispositivo.
* [Pianificare processi in più dispositivi][devguide-jobs], che descrive come pianificare processi in più dispositivi. Questo articolo descrive come inviare processi che eseguono attività come un metodo diretto, aggiornando un dispositivo tramite un dispositivo gemello. Viene descritto anche come eseguire una query sullo stato di un processo.
* [Reference - choose a communication protocol][devguide-protocol] (Informazioni di riferimento: scegliere un protocollo di comunicazione) descrive i protocolli di comunicazione supportati dall'hub IoT per la comunicazione tra dispositivi ed elenca le porte che devono essere aperte.
* [Reference - IoT Hub endpoints][devguide-endpoints] (Informazioni di riferimento: Endpoint dell'hub IoT) illustra i diversi endpoint esposti da ogni hub IoT per operazioni della fase di esecuzione e di gestione. L'articolo descrive anche come è possibile creare endpoint aggiuntivi nell'hub IoT e come usare un gateway di campo per abilitare la connettività di dispositivi per gli endpoint dell'hub IoT in scenari non standard.
* [Informazioni di riferimento: linguaggio di query dell'hub IoT per dispositivi gemelli e processi][devguide-query] illustra il linguaggio di query dell'hub IoT che consente di recuperare informazioni dall'hub sui dispositivi gemelli e sui processi.
* [Reference - quotas and throttling][devguide-quotas] (Informazioni di riferimento: Quote e limitazioni), che riepiloga le quote impostate nel servizio dell'hub IoT e il comportamento previsto quando viene superata una quota.
* [Informazioni di riferimento - prezzi][devguide-pricing] contiene informazioni generali sui diversi SKU e sui prezzi dell'hub IoT e informazioni su come le diverse funzionalità dell'hub IoT vengono misurate come messaggi dall'hub IoT stesso.
* [Informazioni di riferimento - SDK per dispositivi e servizi][devguide-sdks] elenca gli SDK di Azure IoT da usare per sviluppare app per dispositivi e servizi che interagiscono con l'hub IoT. In questo articolo sono inclusi i collegamenti alla documentazione API online.
* [Informazioni di riferimento - Supporto di MQTT nell'hub IoT][devguide-mqtt], che fornisce informazioni dettagliate sul supporto dell'hub IoT per il protocollo MQTT. Questo articolo descrive il supporto del protocollo MQTT incorporato negli Azure IoT SDK e offre informazioni sull'uso diretto del protocollo MQTT.
* Il [glossario][devguide-glossary], che include un elenco dei termini più comuni correlati all'hub IoT.

[devguide-messaging]: iot-hub-devguide-messaging.md
[devguide-upload]: iot-hub-devguide-file-upload.md
[devguide-identities]: iot-hub-devguide-identity-registry.md
[devguide-security]: iot-hub-devguide-security.md
[devguide-device-twins]: iot-hub-devguide-device-twins.md
[devguide-directmethods]: iot-hub-devguide-direct-methods.md
[devguide-jobs]: iot-hub-devguide-jobs.md
[devguide-endpoints]: iot-hub-devguide-endpoints.md
[devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[devguide-query]: iot-hub-devguide-query-language.md
[devguide-sdks]: iot-hub-devguide-sdks.md
[devguide-mqtt]: iot-hub-mqtt-support.md
[devguide-glossary]: iot-hub-devguide-glossary.md
[devguide-pricing]: iot-hub-devguide-pricing.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[devguide-messages-d2c]: iot-hub-devguide-messages-d2c.md
[devguide-builtin]: iot-hub-devguide-messages-read-builtin.md
[devguide-custom]: iot-hub-devguide-messages-read-custom.md
[devguide-messages-c2d]: iot-hub-devguide-messages-c2d.md
[devguide-format]: iot-hub-devguide-messages-construct.md
[devguide-protocol]: iot-hub-devguide-protocols.md
