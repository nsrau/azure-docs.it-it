---
title: Confrontare Griglia di eventi di Azure e il routing per l'hub IoT | Microsoft Docs
description: L'hub IoT offre il proprio servizio di routing dei messaggi, ma si integra anche con Griglia di eventi per la pubblicazione di eventi. Confrontare le due funzionalità.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 0b17a87fa02c382ae19cca6e4abcfff2ec475450
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66252683"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>Confrontare il routing dei messaggi e Griglia di eventi per l'hub IoT

IoT Hub di Azure offre la possibilità di trasmettere i dati da dispositivi connessi e integrare i dati nelle applicazioni aziendali. L'hub IoT offre due metodi per integrare gli eventi IoT in altri servizi di Azure o applicazioni aziendali. Questo articolo illustra le due funzionalità che consentono questa integrazione, per poter scegliere l'opzione ideale a seconda dello scenario.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

**[Routing dei messaggi dell'IoT Hub](iot-hub-devguide-messages-d2c.md)** : Questa funzionalità dell'IoT Hub consente agli utenti di indirizzare i messaggi da dispositivo a cloud a endpoint di servizio, ad esempio i contenitori di archiviazione di Azure, hub eventi, le code del Bus di servizio e gli argomenti del Bus di servizio. Il routing fornisce anche una funzionalità di query per filtrare i dati prima di indirizzarli agli endpoint. Oltre ai dati di telemetria dei dispositivi, è anche possibile inviare [gli eventi non di telemetria](iot-hub-devguide-messages-d2c.md#non-telemetry-events) che possono essere usati per attivare le azioni. 

**Integrazione dell'IoT Hub con griglia di eventi**: Griglia di eventi di Azure è un servizio di routing di eventi completamente gestito che usa un modello di pubblicazione-sottoscrizione. L'hub IoT e Griglia di eventi interagiscono per [integrare gli eventi dell'hub IoT nei servizi di Azure e non di Azure](iot-hub-event-grid.md), in tempo quasi reale. L'IoT Hub pubblica [gli eventi del dispositivo](iot-hub-event-grid.md#event-types), disponibili a livello generale che ora pubblica anche gli eventi di telemetria, che è disponibile in anteprima pubblica.

## <a name="differences"></a>Differenze

Sia il routing dei messaggi che Griglia di eventi consentono la configurazione di avvisi, ma presentano alcune importanti differenze. Consultare la tabella seguente per i dettagli:

| Funzionalità | Routing dei messaggi dell'hub IoT | Integrazione dell'hub IoT con Griglia di eventi |
| ------- | --------------- | ---------- |
| **Gli eventi e messaggi da dispositivo** | Sì, il routing dei messaggi è possibile usare per i dati di telemetria e le modifiche al dispositivo gemello di report e gli eventi del ciclo di vita del dispositivo. | Sì, griglia di eventi può essere usata per i dati di telemetria, ma possono anche segnalare quando i dispositivi vengono creati, eliminati, connessi, disconnesso dall'IoT Hub |
| **Ordinamento** | Sì, viene mantenuto l'ordinamento degli eventi.  | No, l'ordine degli eventi non è garantito. | 
| **Filtri** | Applicazione di filtri avanzati in proprietà di applicazione del messaggio, proprietà di sistema del messaggio, corpo del messaggio, tag del dispositivo gemello e proprietà del dispositivo gemello. Ad esempio, vedere [Sintassi query per il routing dei messaggi di hub IoT](iot-hub-devguide-routing-query-syntax.md). | Filtro basato sul tipo di evento, tipo di oggetto e gli attributi in ogni evento. Per esempi, vedere [comprendere gli eventi di filtro nelle sottoscrizioni di griglia di eventi](../event-grid/event-filtering.md). Quando si sottoscrive gli eventi di telemetria, è possibile applicare filtri aggiuntivi sui dati per il filtro sulle proprietà del messaggio, corpo e il dispositivo gemello di messaggio nell'IoT Hub, prima della pubblicazione a griglia di eventi. Visualizzare [come filtrare gli eventi](../iot-hub/iot-hub-event-grid.md#filter-events). |
| **Endpoints** | <ul><li>Hub eventi</li> <li>Archivio BLOB di Azure</li> <li>Coda del bus di servizio</li> <li>Argomenti del bus di servizio</li></ul><br>Gli SKU per hub IoT a pagamento (S1, S2 e S3) sono limitati a 10 endpoint personalizzati. Si possono creare 100 route per ogni hub IoT. | <ul><li>Funzioni di Azure</li> <li>Automazione di Azure</li> <li>Hub eventi</li> <li>App per la logica</li> <li>Archiviazione BLOB</li> <li>Argomenti personalizzati</li> <li>Archiviazione code</li> <li>Microsoft Flow</li> <li>Servizi di terze parti tramite webhook</li></ul><br>sono supportati gli endpoint di 500 per ogni IoT Hub. Per l'elenco più aggiornato di endpoint, vedere [Gestori eventi di Griglia di eventi](../event-grid/overview.md#event-handlers). |
| **Costii** | Non vengono addebitati costi separati per il routing dei messaggi. Vengono addebitati solo i dati di telemetria in ingresso nell'hub IoT. Se ad esempio un messaggio viene instradato a tre endpoint diversi, viene fatturato un solo messaggio. | Non è previsto alcun addebito per l'hub IoT. Griglia di eventi offre gratuitamente le prime 100.000 operazioni al mese e quindi $0,60 per milione di operazioni in un secondo momento. |

## <a name="similarities"></a>Analogie

Il routing dei messaggi dell'hub IoT e Griglia di eventi presentano anche analogie, alcuni delle quali vengono illustrate in dettaglio nella tabella seguente:

| Funzionalità | Routing dei messaggi dell'hub IoT | Integrazione dell'hub IoT con Griglia di eventi |
| ------- | --------------- | ---------- |
| **Dimensioni massime del messaggio** | 256 KB, da dispositivo a cloud | 256 KB, da dispositivo a cloud |
| **Affidabilità** | Alta: Ogni messaggio viene recapitato all'endpoint almeno una volta per ogni route. Imposta come scaduti tutti i messaggi che non vengono recapitati entro un'ora. | Alta: Ogni messaggio viene recapitato al webhook almeno una volta per ogni sottoscrizione. Imposta come scaduti tutti gli eventi che non vengono recapitati entro 24 ore. | 
| **Scalabilità** | Alta: Ottimizzato per supportare milioni di dispositivi connessi simultaneamente che inviano miliardi di messaggi. | Alta: È in grado di routing 10.000.000 eventi al secondo per ogni area. |
| **Latency** | Bassa: Quasi in tempo reale. | Bassa: Quasi in tempo reale. |
| **Invio a più endpoint** | Sì, invia un singolo messaggio a più endpoint. | Sì, invia un singolo messaggio a più endpoint.  
| **Sicurezza** | L'hub IoT fornisce l'identità per ogni dispositivo e il controllo di accesso revocabile. Per altre informazioni, vedere [Controllo dell'accesso all'hub IoT](iot-hub-devguide-security.md). | Griglia di eventi fornisce la convalida in tre punti: sottoscrizioni di eventi, pubblicazione di eventi e recapito di eventi al webhook. Per altre informazioni, vedere [Event Grid security and authentication](../event-grid/security-authentication.md) (Sicurezza e autenticazione di Griglia di eventi). |

## <a name="how-to-choose"></a>Come scegliere

Il routing dei messaggi dell'hub IoT e l'integrazione dell'hub IoT con Griglia di eventi ottengono risultati simili con azioni diverse. Entrambi acquisiscono le informazioni dalla soluzione hub IoT e le passano in modo che altri servizi possano rispondere. Per decidere quale usare, Prendere in considerazione le domande seguenti come guida per la decisione: 

* **Quale tipo di dati si invierà agli endpoint?**

   Usare il routing dei messaggi dell'hub IoT quando è necessario inviare dati di telemetria ad altri servizi. Il routing dei messaggi consente anche di eseguire query sulle proprietà di applicazione e di sistema del messaggio, sul corpo del messaggio, sui tag del dispositivo gemello e sulle proprietà del dispositivo gemello.

   L'integrazione dell'hub IoT con Griglia di eventi è indicata per gli eventi che si verificano nel servizio hub IoT. Questi eventi dell'IoT Hub includono i dati di telemetria, dispositivo creato, eliminato, connesse e disconnesse. Quando si sottoscrive gli eventi di telemetria, è possibile applicare filtri aggiuntivi sui dati per il filtro sulle proprietà del messaggio, corpo e il dispositivo gemello di messaggio nell'IoT Hub, prima della pubblicazione a griglia di eventi. Visualizzare [come filtrare gli eventi](../iot-hub/iot-hub-event-grid.md#filter-events).

* **Quali endpoint devono ricevere queste informazioni?**

   Routing dei messaggi dell'IoT Hub supporta un numero limitato di un endpoint univoco e tipi di endpoint, ma è possibile compilare connettori per reindirizzare i dati e gli eventi a endpoint aggiuntivi. Per un elenco completo degli endpoint supportati, vedere la tabella nella sezione precedente. 

   L'integrazione dell'IoT Hub con griglia di eventi supporta 500 endpoint per l'IoT Hub e una più ampia varietà di tipi di endpoint. Gestire si integra con funzioni di Azure, App per la logica, le code di archiviazione e Bus di servizio e si integra inoltre con i webhook per estendere l'invio dei dati all'esterno dell'ecosistema di servizi di Azure e nelle applicazioni aziendali di terze parti.

* **È importante che i dati arrivino in ordine?**

   Il routing dei messaggi dell'hub IoT mantiene l'ordine di invio dei messaggi, in modo che arrivino nello stesso ordine.

   Griglia di eventi non garantisce che gli endpoint ricevano gli eventi nello stesso ordine in cui si sono verificati. Per i casi in cui ordine assoluto dei messaggi è significativo e/o in cui un consumer necessita di un identificatore univoco attendibile per i messaggi, è consigliabile usare il routing dei messaggi. 

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sul [routing dei messaggi dell'hub IoT](iot-hub-devguide-messages-d2c.md) e sugli [endpoint dell'hub IoT](iot-hub-devguide-endpoints.md).
* Altre informazioni su [Griglia di eventi di Azure](../event-grid/overview.md).
* Per informazioni su come creare route di messaggi, vedere l'esercitazione [Elaborare messaggi da dispositivo a cloud dell'hub IoT usando route](../iot-hub/tutorial-routing.md).
* Provare l'integrazione di griglia di eventi [invio di notifiche di posta elettronica sugli eventi di IoT Hub di Azure usando App per la logica](../event-grid/publish-iot-hub-events-to-logic-apps.md).