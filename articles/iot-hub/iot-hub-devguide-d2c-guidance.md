---
title: Opzioni da dispositivo a cloud dell'hub IoT di Azure | Documentazione Microsoft
description: 'Guida per gli sviluppatori: indicazioni su quando usare i messaggi da dispositivo a cloud, le proprietà segnalate o il caricamento di file per le comunicazioni da cloud a dispositivo.'
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: fffa064b912a96b05feb901d1d2d44533c4681b7
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57007982"
---
# <a name="device-to-cloud-communications-guidance"></a>Indicazioni sulle comunicazioni da dispositivo a cloud

Durante l'invio di informazioni dall'app per dispositivo al back-end della soluzione, l'hub IoT espone tre opzioni:

* [Messaggi da dispositivo a cloud](iot-hub-devguide-messages-d2c.md) per dati di telemetria e avvisi relativi alle serie temporali.

* [Proprietà segnalate del dispositivo gemello](iot-hub-devguide-device-twins.md) per segnalare informazioni sullo stato del dispositivo, ad esempio le funzionalità disponibili, le condizioni e lo stato dei flussi di lavoro a esecuzione prolungata. come aggiornamenti della configurazione e del software.

* [Caricamenti di file](iot-hub-devguide-file-upload.md), per i file multimediali e batch di telemetria di grandi dimensioni caricati da dispositivi con connessione intermittente o compressi per risparmiare la larghezza di banda.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Ecco un confronto dettagliato delle diverse opzioni di comunicazione da dispositivo a cloud.

|  | Messaggi da dispositivo a cloud | Proprietà segnalate del dispositivo gemello | Caricamenti di file |
| ---- | ------- | ---------- | ---- |
| Scenario | Serie temporale di telemetria e avvisi. Ad esempio, batch di dati di sensori di 256 KB inviati ogni 5 minuti. | Funzionalità disponibili e condizioni. Ad esempio, la modalità di connessione del dispositivo corrente, ad esempio cellulare o Wi-Fi. Sincronizzazione di flussi di lavoro a esecuzione prolungata, ad esempio aggiornamenti della configurazione e del software. | File multimediali. Batch di telemetria di grandi dimensioni, in genere compressi. |
| Archiviazione e recupero | Archiviati temporaneamente dall'hub IoT, per un massimo di 7 giorni. Solo lettura sequenziale. | Archiviate dall'hub IoT nel dispositivo gemello. Recuperabili mediante il [linguaggio di query dell'hub IoT](iot-hub-devguide-query-language.md). | Archiviati nell'account di Archiviazione di Azure specificato dall'utente. |
| Dimensione | Fino a messaggi di 256 KB. | Le dimensioni massime per le proprietà segnalate sono 8 KB. | Dimensioni di file massime supportate dall'Archiviazione BLOB di Azure. |
| Frequenza | Elevata. Per altre informazioni, vedere i [limiti dell'hub IoT](iot-hub-devguide-quotas-throttling.md). | Media. Per altre informazioni, vedere i [limiti dell'hub IoT](iot-hub-devguide-quotas-throttling.md). | Bassa. Per altre informazioni, vedere i [limiti dell'hub IoT](iot-hub-devguide-quotas-throttling.md). |
| Protocollo | Disponibile in tutti i protocolli. | Disponibile tramite MQTT o AMQP. | Disponibile quando si usa qualsiasi protocollo, ma richiede HTTPS nel dispositivo. |

Un'applicazione può avere la necessità di inviare informazioni sotto forma di serie temporali di telemetria o di avvisi e di renderle disponibili nel dispositivo gemello. In questo scenario è possibile scegliere una delle opzioni seguenti:

* L'app per dispositivo invia un messaggio da dispositivo a cloud e segnala una modifica della proprietà.
* La soluzione back-end può archiviare le informazioni nei tag del dispositivo gemello quando riceve il messaggio.

Poiché i messaggi da dispositivo a cloud consentono una velocità effettiva notevolmente superiore rispetto agli aggiornamenti del dispositivo gemello, è a volte consigliabile evitare di aggiornare il dispositivo gemello per ogni messaggio da dispositivo a cloud.