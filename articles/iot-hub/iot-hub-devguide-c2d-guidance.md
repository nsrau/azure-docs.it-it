---
title: Opzioni da cloud a dispositivo dell&quot;hub IoT di Azure | Documentazione Microsoft
description: "Guida per gli sviluppatori: indicazioni su quando usare i metodi diretti, le proprietà desiderate del dispositivo gemello o i messaggi da cloud a dispositivo per le comunicazioni da cloud a dispositivo."
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: 1ac90923-1edf-4134-bbd4-77fee9b68d24
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/25/2017
ms.author: elioda
ms.translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 65a9815525c612bdfac569e5ec56a1db53d82c78
ms.contentlocale: it-it
ms.lasthandoff: 03/10/2017


---
# <a name="cloud-to-device-communications-guidance"></a>Indicazioni sulle comunicazioni da cloud a dispositivo
L'hub IoT fornisce tre opzioni per le app per dispositivo che consentono di esporre le funzionalità a un'app back-end:

* [Metodi diretti][lnk-methods] per le comunicazioni che richiedono la conferma immediata del risultato. I metodi diretti vengono spesso usati per il controllo interattivo dei dispositivi, ad esempio l'accensione di una ventola.
* [Proprietà desiderate del dispositivo gemello][lnk-twins], per comandi a esecuzione prolungata che consentono di impostare uno stato specifico per il dispositivo, ad esempio per impostare l'intervallo di invio dei dati di telemetria su 30 minuti.
* [Messaggi da cloud a dispositivo][lnk-c2d], per le notifiche unidirezionali verso l'app per dispositivo.

Ecco un confronto dettagliato delle diverse opzioni di comunicazione da cloud a dispositivo.

|  | Metodi diretti | Proprietà desiderate del dispositivo gemello | Messaggi da cloud a dispositivo |
| ---- | ------- | ---------- | ---- |
| Scenario | Comandi che richiedono una conferma immediata, ad esempio l'accensione di una ventola. | Comandi a esecuzione prolungata che consentono di impostare uno stato specifico per il dispositivo, ad esempio per impostare l'intervallo di invio dei dati di telemetria su 30 minuti. | Notifiche unidirezionali verso l'app per dispositivo. |
| Flusso di dati | Bidirezionale. L'app per dispositivo può rispondere immediatamente al metodo. Il back-end della soluzione riceve il risultato insieme alla richiesta. | Unidirezionale. L'app per dispositivo riceve una notifica quando la proprietà viene modificata. | Unidirezionale. L'app per dispositivo riceve il messaggio.
| Durabilità | I dispositivi disconnessi non vengono contattati. Il back-end della soluzione riceve una notifica che indica che il dispositivo non è connesso. | I valori delle proprietà vengono conservati nel dispositivo gemello. Il dispositivo li leggerà alla riconnessione successiva. I valori delle proprietà possono essere recuperati con il [linguaggio di query dell'hub IoT][lnk-query]. | I messaggi possono essere conservati dall'hub IoT per un massimo di 48 ore. |
| Destinazioni | Singolo dispositivo che usa **deviceId** o più dispositivi che usano [processi][lnk-jobs]. | Singolo dispositivo che usa **deviceId** o più dispositivi che usano [processi][lnk-jobs]. | Singolo dispositivo in base a **deviceId**. |
| Dimensione | Fino a 8 KB di richieste e 8 KB di risposte. | Le dimensioni massime per le proprietà desiderate sono 8 KB. | Messaggi di un massimo di 64 KB. |
| Frequenza | Elevata. Per altre informazioni, vedere i [limiti dell'hub IoT][lnk-quotas]. | Media. Per altre informazioni, vedere i [limiti dell'hub IoT][lnk-quotas]. | Bassa. Per altre informazioni, vedere i [limiti dell'hub IoT][lnk-quotas]. |
| Protocol | Attualmente disponibile solo quando si usa MQTT. | Attualmente disponibile solo quando si usa MQTT. | Disponibile in tutti i protocolli. Il dispositivo deve eseguire il polling quando usa HTTP. |

Informazioni su come usare i metodi diretti, le proprietà desiderate e messaggi da cloud a dispositivo nelle esercitazioni seguenti:

* [Usare i metodi diretti ][lnk-methods-tutorial], per i metodi diretti.
* [Usare le proprietà desiderate per configurare i dispositivi][lnk-twin-properties], per le proprietà desiderate del dispositivo gemello. 
* [Inviare messaggi da cloud a dispositivo][lnk-c2d-tutorial], per messaggi da cloud a dispositivo.

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-tutorial]: iot-hub-node-node-c2d.md

