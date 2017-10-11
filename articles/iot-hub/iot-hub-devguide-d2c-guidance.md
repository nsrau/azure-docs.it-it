---
title: Opzioni da dispositivo a cloud dell'hub IoT di Azure | Documentazione Microsoft
description: "Guida per gli sviluppatori: indicazioni su quando usare i messaggi da dispositivo a cloud, le proprietà segnalate o il caricamento di file per le comunicazioni da cloud a dispositivo."
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 979136db-c92d-4288-870c-f305e8777bdd
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/25/2017
ms.author: elioda
ms.translationtype: HT
ms.sourcegitcommit: 9b7316a5bffbd689bdb26e9524129ceed06606d5
ms.openlocfilehash: 146a2e16399a3eeed6b178d72234584c76cc8f51
ms.contentlocale: it-it
ms.lasthandoff: 09/08/2017

---
# <a name="device-to-cloud-communications-guidance"></a>Indicazioni sulle comunicazioni da dispositivo a cloud
Durante l'invio di informazioni dall'app per dispositivo al back-end della soluzione, l'hub IoT espone tre opzioni:

* [Messaggi da dispositivo a cloud][lnk-d2c] per dati di telemetria e avvisi relativi alle serie temporali.
* [Proprietà segnalate][lnk-twins] per segnalare informazioni sullo stato del dispositivo, ad esempio le funzionalità disponibili, le condizioni e lo stato dei flussi di lavoro a esecuzione prolungata. come aggiornamenti della configurazione e del software.
* [Caricamenti di file][lnk-fileupload], per i file multimediali e batch di telemetria di grandi dimensioni caricati da dispositivi con connessione intermittente o compressi per risparmiare la larghezza di banda.

Ecco un confronto dettagliato delle diverse opzioni di comunicazione da dispositivo a cloud.

|  | Messaggi da dispositivo a cloud | Proprietà segnalate | Caricamenti di file |
| ---- | ------- | ---------- | ---- |
| Scenario | Serie temporale di telemetria e avvisi. Ad esempio, batch di dati di sensori di 256 KB inviati ogni 5 minuti. | Funzionalità disponibili e condizioni. Ad esempio, la modalità di connessione del dispositivo corrente, ad esempio cellulare o Wi-Fi. Sincronizzazione di flussi di lavoro a esecuzione prolungata, ad esempio aggiornamenti della configurazione e del software. | File multimediali. Batch di telemetria di grandi dimensioni, in genere compressi. |
| Archiviazione e recupero | Archiviati temporaneamente dall'hub IoT, per un massimo di 7 giorni. Solo lettura sequenziale. | Archiviate dall'hub IoT nel dispositivo gemello. Recuperabili mediante il [linguaggio di query dell'hub IoT][lnk-query]. | Archiviati nell'account di Archiviazione di Azure specificato dall'utente. |
| Dimensione | Fino a messaggi di 256 KB. | Le dimensioni massime per le proprietà segnalate sono 8 KB. | Dimensioni di file massime supportate dall'Archiviazione BLOB di Azure. |
| Frequenza | Elevata. Per altre informazioni, vedere i [limiti dell'hub IoT][lnk-quotas]. | Media. Per altre informazioni, vedere i [limiti dell'hub IoT][lnk-quotas]. | Bassa. Per altre informazioni, vedere i [limiti dell'hub IoT][lnk-quotas]. |
| Protocol | Disponibile in tutti i protocolli. | Disponibile tramite MQTT o AMQP. | Disponibile quando si usa qualsiasi protocollo, ma richiede HTTP nel dispositivo. |

È possibile che un'applicazione richieda di inviare informazioni sotto forma di serie temporali di telemetria o avvisi e di renderle disponibili nel dispositivo gemello. In questo scenario è possibile scegliere una delle opzioni seguenti:

* L'app per dispositivo invia un messaggio da dispositivo a cloud e segnala una modifica della proprietà.
* La soluzione back-end può archiviare le informazioni nei tag del dispositivo gemello quando riceve il messaggio.

Poiché i messaggi da dispositivo a cloud consentono una velocità effettiva notevolmente superiore rispetto agli aggiornamenti del dispositivo gemello, è a volte consigliabile evitare di aggiornare il dispositivo gemello per ogni messaggio da dispositivo a cloud.


[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-fileupload]: iot-hub-devguide-file-upload.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md

