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
ms.openlocfilehash: 067293f76ac4894ca73f4e74cb01db65ae8d1fba
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876909"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>Confrontare il routing dei messaggi e Griglia di eventi per l'hub IoT

Hub Internet Azure è in grado di trasmettere i dati dai dispositivi connessi e di integrare tali dati nelle applicazioni aziendali. L'hub IoT offre due metodi per integrare gli eventi IoT in altri servizi di Azure o applicazioni aziendali. Questo articolo illustra le due funzionalità che consentono questa integrazione, per poter scegliere l'opzione ideale a seconda dello scenario.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

**[Routing messaggi dell'hub](iot-hub-devguide-messages-d2c.md)** Internet: Questa funzionalità dell'hub degli elementi del tutto consente agli utenti di instradare i messaggi da dispositivo a cloud agli endpoint di servizio come contenitori di archiviazione di Azure, Hub eventi, code del bus di servizio e argomenti del bus di servizio. Il routing fornisce anche una funzionalità di query per filtrare i dati prima di indirizzarli agli endpoint. Oltre ai dati di telemetria dei dispositivi, è anche possibile inviare [gli eventi non di telemetria](iot-hub-devguide-messages-d2c.md#non-telemetry-events) che possono essere usati per attivare le azioni. 

**Integrazione dell'hub Internet con griglia di eventi**: Griglia di eventi di Azure è un servizio di routing di eventi completamente gestito che usa un modello di pubblicazione-sottoscrizione. L'hub IoT e Griglia di eventi interagiscono per [integrare gli eventi dell'hub IoT nei servizi di Azure e non di Azure](iot-hub-event-grid.md), in tempo quasi reale. L'hub Internet delle cose pubblica [gli eventi del dispositivo](iot-hub-event-grid.md#event-types), che sono disponibili a livello generale, e ora pubblica anche gli eventi di telemetria, disponibile in anteprima pubblica.

## <a name="differences"></a>Differenze

Sia il routing dei messaggi che Griglia di eventi consentono la configurazione di avvisi, ma presentano alcune importanti differenze. Consultare la tabella seguente per i dettagli:

| Funzionalità | Routing dei messaggi dell'hub IoT | Integrazione dell'hub IoT con Griglia di eventi |
| ------- | --------------- | ---------- |
| **Messaggi ed eventi del dispositivo** | Sì, il routing dei messaggi può essere usato per i dati di telemetria, per segnalare le modifiche del dispositivo gemello, per gli eventi del ciclo di vita del dispositivo e per gli eventi di modifica del gemello digitale (parte del [plug and Play](../iot-pnp/overview-iot-plug-and-play.md) | Sì, griglia di eventi può essere usata per i dati di telemetria, ma può anche indicare quando i dispositivi vengono creati, eliminati, connessi e disconnessi dall'hub Internet. |
| **Ordinamento** | Sì, viene mantenuto l'ordinamento degli eventi.  | No, l'ordine degli eventi non è garantito. | 
| **Filtri** | Applicazione di filtri avanzati in proprietà di applicazione del messaggio, proprietà di sistema del messaggio, corpo del messaggio, tag del dispositivo gemello e proprietà del dispositivo gemello. Il filtro non viene applicato agli eventi di modifica del dispositivo gemello digitale. Ad esempio, vedere [Sintassi query per il routing dei messaggi di hub IoT](iot-hub-devguide-routing-query-syntax.md). | Filtro basato sul tipo di evento, il tipo di oggetto e gli attributi in ogni evento. Per esempi, vedere [informazioni sugli eventi di filtro nelle sottoscrizioni di griglia di eventi](../event-grid/event-filtering.md). Quando si sottoscrive un evento di telemetria, è possibile applicare filtri aggiuntivi sui dati per filtrare le proprietà del messaggio, il corpo del messaggio e il dispositivo gemello nell'hub Internet, prima della pubblicazione in griglia di eventi. Vedere [come filtrare gli eventi](../iot-hub/iot-hub-event-grid.md#filter-events). |
| **Endpoints** | <ul><li>Hub eventi</li> <li>Archivio BLOB di Azure</li> <li>Coda del bus di servizio</li> <li>Argomenti del bus di servizio</li></ul><br>Gli SKU per hub IoT a pagamento (S1, S2 e S3) sono limitati a 10 endpoint personalizzati. Si possono creare 100 route per ogni hub IoT. | <ul><li>Funzioni di Azure</li> <li>Automazione di Azure</li> <li>Hub eventi</li> <li>App per la logica</li> <li>Archiviazione BLOB</li> <li>Argomenti personalizzati</li> <li>Archiviazione code</li> <li>Microsoft Flow</li> <li>Servizi di terze parti tramite webhook</li></ul><br>sono supportati gli endpoint 500 per l'hub Internet. Per l'elenco più aggiornato di endpoint, vedere [Gestori eventi di Griglia di eventi](../event-grid/overview.md#event-handlers). |
| **Costii** | Non vengono addebitati costi separati per il routing dei messaggi. Vengono addebitati solo i dati di telemetria in ingresso nell'hub IoT. Se ad esempio un messaggio viene instradato a tre endpoint diversi, viene fatturato un solo messaggio. | Non è previsto alcun addebito per l'hub IoT. Griglia di eventi offre le prime 100.000 operazioni al mese gratuite e quindi $0,60 per milione di operazioni in seguito. |

## <a name="similarities"></a>Somiglianze

Il routing dei messaggi dell'hub IoT e Griglia di eventi presentano anche analogie, alcuni delle quali vengono illustrate in dettaglio nella tabella seguente:

| Funzionalità | Routing dei messaggi dell'hub IoT | Integrazione dell'hub IoT con Griglia di eventi |
| ------- | --------------- | ---------- |
| **Dimensioni massime del messaggio** | 256 KB, da dispositivo a cloud | 256 KB, da dispositivo a cloud |
| **Affidabilità** | Alta: Recapita ogni messaggio all'endpoint almeno una volta per ogni route. Imposta come scaduti tutti i messaggi che non vengono recapitati entro un'ora. | Alta: Recapita ogni messaggio al webhook almeno una volta per ogni sottoscrizione. Imposta come scaduti tutti gli eventi che non vengono recapitati entro 24 ore. | 
| **Scalabilità** | Alta: Ottimizzato per supportare milioni di dispositivi connessi simultaneamente che inviano miliardi di messaggi. | Alta: In grado di indirizzare 10 milioni eventi al secondo per area. |
| **Latency** | Bassa: Quasi in tempo reale. | Bassa: Quasi in tempo reale. |
| **Invio a più endpoint** | Sì, invia un singolo messaggio a più endpoint. | Sì, invia un singolo messaggio a più endpoint.  
| **Sicurezza** | L'hub IoT fornisce l'identità per ogni dispositivo e il controllo di accesso revocabile. Per altre informazioni, vedere [Controllo dell'accesso all'hub IoT](iot-hub-devguide-security.md). | Griglia di eventi fornisce la convalida in tre punti: sottoscrizioni di eventi, pubblicazione di eventi e recapito di eventi al webhook. Per altre informazioni, vedere [Event Grid security and authentication](../event-grid/security-authentication.md) (Sicurezza e autenticazione di Griglia di eventi). |

## <a name="how-to-choose"></a>Come scegliere

Il routing dei messaggi dell'hub IoT e l'integrazione dell'hub IoT con Griglia di eventi ottengono risultati simili con azioni diverse. Entrambi acquisiscono le informazioni dalla soluzione hub IoT e le passano in modo che altri servizi possano rispondere. Per decidere quale usare, Prendere in considerazione le domande seguenti per guidare la decisione: 

* **Quale tipo di dati si invierà agli endpoint?**

   Usare il routing dei messaggi dell'hub IoT quando è necessario inviare dati di telemetria ad altri servizi. Il routing dei messaggi consente anche di eseguire query sulle proprietà di applicazione e di sistema del messaggio, sul corpo del messaggio, sui tag del dispositivo gemello e sulle proprietà del dispositivo gemello.

   L'integrazione dell'hub IoT con Griglia di eventi è indicata per gli eventi che si verificano nel servizio hub IoT. Questi eventi dell'hub di interesse includono i dati di telemetria, i dispositivi creati, eliminati, connessi e disconnessi. Quando si sottoscrive un evento di telemetria, è possibile applicare filtri aggiuntivi sui dati per filtrare le proprietà del messaggio, il corpo del messaggio e il dispositivo gemello nell'hub Internet, prima della pubblicazione in griglia di eventi. Vedere [come filtrare gli eventi](../iot-hub/iot-hub-event-grid.md#filter-events).

* **Quali endpoint devono ricevere queste informazioni?**

   Il routing dei messaggi dell'hub Internet supporta un numero limitato di endpoint e tipi di endpoint univoci, ma è possibile creare connettori per reindirizzare i dati ed eventi ad endpoint aggiuntivi. Per un elenco completo degli endpoint supportati, vedere la tabella nella sezione precedente. 

   L'integrazione dell'hub Internet con griglia di eventi supporta gli endpoint 500 per ogni hub e una varietà più ampia di tipi di endpoint. Si integra in modo nativo con funzioni di Azure, app per la logica, code del bus di servizio e di archiviazione e funziona anche con i webhook per estendere l'invio di dati all'esterno dell'ecosistema di servizi di Azure e delle applicazioni aziendali di terze parti.

* **È importante che i dati arrivino in ordine?**

   Il routing dei messaggi dell'hub IoT mantiene l'ordine di invio dei messaggi, in modo che arrivino nello stesso ordine.

   Griglia di eventi non garantisce che gli endpoint ricevano gli eventi nello stesso ordine in cui si sono verificati. Per i casi in cui l'ordine assoluto dei messaggi è significativo e/o in cui un consumer necessita di un identificatore univoco attendibile per i messaggi, è consigliabile usare il routing dei messaggi. 

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sul [routing dei messaggi dell'hub IoT](iot-hub-devguide-messages-d2c.md) e sugli [endpoint dell'hub IoT](iot-hub-devguide-endpoints.md).
* Altre informazioni su [Griglia di eventi di Azure](../event-grid/overview.md).
* Per informazioni su come creare route di messaggi, vedere l'esercitazione [Elaborare messaggi da dispositivo a cloud dell'hub IoT usando route](../iot-hub/tutorial-routing.md).
* Provare l'integrazione di griglia di eventi [inviando notifiche tramite posta elettronica sugli eventi dell'hub Azure Internet tramite app per la logica](../event-grid/publish-iot-hub-events-to-logic-apps.md).
