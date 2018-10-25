---
title: Confrontare Griglia di eventi di Azure e il routing per l'hub IoT | Microsoft Docs
description: L'hub IoT offre il proprio servizio di routing dei messaggi, ma si integra anche con Griglia di eventi per la pubblicazione di eventi. Confrontare le due funzionalità.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2018
ms.author: kgremban
ms.openlocfilehash: 3d52ca0c7022e08655ece8775b5855f3ae985aca
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2018
ms.locfileid: "48247453"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>Confrontare il routing dei messaggi e Griglia di eventi per l'hub IoT

L'hub IoT di Azure consente di trasmettere i dati dai dispositivi connessi e di integrare tali dati nelle applicazioni aziendali. L'hub IoT offre due metodi per integrare gli eventi IoT in altri servizi di Azure o applicazioni aziendali. Questo articolo illustra le due funzionalità che consentono questa integrazione, per poter scegliere l'opzione ideale a seconda dello scenario.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

* **[Routing dei messaggi dell'hub IoT](iot-hub-devguide-messages-d2c.md)**: questa funzionalità dell'hub IoT consente agli utenti di instradare i messaggi da dispositivo a cloud agli endpoint di servizio, ad esempio i contenitori di archiviazione di Azure, Hub eventi, le code del bus di servizio e gli argomenti del bus di servizio. Il routing fornisce anche una funzionalità di query per filtrare i dati prima di indirizzarli agli endpoint. Oltre ai dati di telemetria dei dispositivi, è anche possibile inviare [gli eventi non di telemetria](iot-hub-devguide-messages-d2c.md#non-telemetry-events) che possono essere usati per attivare le azioni. 

* **Integrazione dell'hub IoT con Griglia di eventi**: Griglia di eventi di Azure è un servizio di routing di eventi completamente gestito che usa un modello di pubblicazione-sottoscrizione. L'hub IoT e Griglia di eventi interagiscono per [integrare gli eventi dell'hub IoT nei servizi di Azure e non di Azure](iot-hub-event-grid.md), in tempo quasi reale. 

## <a name="similarities-and-differences"></a>Analogie e differenze

Sia il routing dei messaggi che Griglia di eventi consentono la configurazione di avvisi, ma presentano alcune importanti differenze. Consultare la tabella seguente per i dettagli:

| Funzionalità | Routing dei messaggi dell'hub IoT | Integrazione dell'hub IoT con Griglia di eventi |
| ------- | --------------- | ---------- |
| **Messaggi del dispositivo** | Sì, il routing dei messaggi può essere usato per i dati di telemetria. | No, Griglia di eventi può essere usata solo per gli eventi dell'hub IoT non di telemetria. |
| **Tipo di evento** | Sì, il routing dei messaggi può segnalare le modifiche del dispositivo gemello e gli eventi del ciclo di vita del dispositivo. | Sì, Griglia di eventi può segnalare quando i dispositivi vengono creati, eliminati, connessi e disconnessi dall'hub IoT |
| **Ordinamento** | Sì, viene mantenuto l'ordinamento degli eventi.  | No, l'ordine degli eventi non è garantito. | 
| **Dimensioni massime del messaggio** | 256 KB, da dispositivo a cloud | 64 KB |
| **Filtri** | Applicazione di filtri avanzati in proprietà di applicazione del messaggio, proprietà di sistema del messaggio, corpo del messaggio, tag del dispositivo gemello e proprietà del dispositivo gemello. Ad esempio, vedere [Sintassi query per il routing dei messaggi di hub IoT](iot-hub-devguide-routing-query-syntax.md). | Filtri basati sul suffisso/prefisso degli ID dei dispositivi, indicati per i servizi gerarchici, ad esempio l'archiviazione. |
| **Endpoints** | <ul><li>Hub eventi</li> <li>Archiviazione BLOB di Azure</li> <li>Coda del bus di servizio</li> <li>Argomenti del bus di servizio</li></ul><br>Gli SKU per hub IoT a pagamento (S1, S2 e S3) sono limitati a 10 endpoint personalizzati. Si possono creare 100 route per ogni hub IoT. | <ul><li>Funzioni di Azure</li> <li>Automazione di Azure</li> <li>Hub eventi</li> <li>App per la logica</li> <li>Archiviazione BLOB</li> <li>Argomenti personalizzati</li> <li>Servizi di terze parti tramite webhook</li></ul><br>Per l'elenco più aggiornato di endpoint, vedere [Gestori eventi di Griglia di eventi](../event-grid/overview.md#event-handlers). |
| **Costii** | Non vengono addebitati costi separati per il routing dei messaggi. Vengono addebitati solo i dati di telemetria in ingresso nell'hub IoT. Se ad esempio un messaggio viene instradato a tre endpoint diversi, viene fatturato un solo messaggio. | Non è previsto alcun addebito per l'hub IoT. Griglia di eventi offre gratuitamente le prime 100.000 operazioni al mese e le successive al costo di 0,60 dollari per milione di operazioni. |

Il routing dei messaggi dell'hub IoT e Griglia di eventi presentano anche analogie, alcuni delle quali vengono illustrate in dettaglio nella tabella seguente:

| Funzionalità | Routing dei messaggi dell'hub IoT | Integrazione dell'hub IoT con Griglia di eventi |
| ------- | --------------- | ---------- |
| **Affidabilità** | Elevata: ogni messaggio viene recapitato all'endpoint almeno una volta per ogni route. Imposta come scaduti tutti i messaggi che non vengono recapitati entro un'ora. | Elevata: ogni messaggio viene recapitato al webhook almeno una volta per ogni sottoscrizione. Imposta come scaduti tutti gli eventi che non vengono recapitati entro 24 ore. | 
| **Scalabilità** | Elevata: è ottimizzato per supportare milioni di dispositivi connessi simultaneamente che inviano miliardi di messaggi. | Elevata: consente il routing di 10.000.000 di eventi al secondo per ogni area. |
| **Latency** | Bassa: in tempo quasi reale. | Bassa: in tempo quasi reale. |
| **Invio a più endpoint** | Sì, invia un singolo messaggio a più endpoint. | Sì, invia un singolo messaggio a più endpoint.  | 
| **Sicurezza** | L'hub IoT fornisce l'identità per ogni dispositivo e il controllo di accesso revocabile. Per altre informazioni, vedere [Controllo dell'accesso all'hub IoT](iot-hub-devguide-security.md). | Griglia di eventi fornisce la convalida in tre punti: sottoscrizioni di eventi, pubblicazione di eventi e recapito di eventi al webhook. Per altre informazioni, vedere [Event Grid security and authentication](../event-grid/security-authentication.md) (Sicurezza e autenticazione di Griglia di eventi). |

## <a name="how-to-choose"></a>Come scegliere

Il routing dei messaggi dell'hub IoT e l'integrazione dell'hub IoT con Griglia di eventi ottengono risultati simili con azioni diverse. Entrambi acquisiscono le informazioni dalla soluzione hub IoT e le passano in modo che altri servizi possano rispondere. Per decidere quale usare, oltre ai dati della sezione precedente, vedere le domande seguenti per poter scegliere più facilmente: 

* **Quale tipo di dati si invierà agli endpoint?**

   Usare il routing dei messaggi dell'hub IoT quando è necessario inviare dati di telemetria ad altri servizi. Il routing dei messaggi consente anche di eseguire query sulle proprietà di applicazione e di sistema del messaggio, sul corpo del messaggio, sui tag del dispositivo gemello e sulle proprietà del dispositivo gemello.

   L'integrazione dell'hub IoT con Griglia di eventi è indicata per gli eventi che si verificano nel servizio hub IoT. Questi eventi dell'hub IoT includono la creazione, l'eliminazione, la connessione e la disconnessione dei dispositivi. 

* **Quali endpoint devono ricevere queste informazioni?**

   Il routing dei messaggi dell'hub IoT supporta un numero limitato di endpoint, ma è possibile compilare connettori per reindirizzare i dati e gli eventi a endpoint aggiuntivi. Per un elenco completo degli endpoint supportati, vedere la tabella nella sezione precedente. 

   L'integrazione dell'hub IoT con Griglia di eventi supporta più endpoint. Interagisce anche con i webhook per estendere il routing all'esterno dell'ecosistema di servizi di Azure e alle applicazioni aziendali di terze parti. 

* **È importante che i dati arrivino in ordine?**

   Il routing dei messaggi dell'hub IoT mantiene l'ordine di invio dei messaggi, in modo che arrivino nello stesso ordine.

   Griglia di eventi non garantisce che gli endpoint ricevano gli eventi nello stesso ordine in cui si sono verificati. Lo schema degli eventi include tuttavia un timestamp che può essere usato per identificare l'ordine dopo che gli eventi raggiungono l'endpoint. 

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sul [routing dei messaggi dell'hub IoT](iot-hub-devguide-messages-d2c.md) e sugli [endpoint dell'hub IoT](iot-hub-devguide-endpoints.md).
* Altre informazioni su [Griglia di eventi di Azure](../event-grid/overview.md)
* Per informazioni su come creare route di messaggi, vedere l'esercitazione [Elaborare messaggi da dispositivo a cloud dell'hub IoT usando route](../iot-hub/tutorial-routing.md).
* Provare l'integrazione di Griglia di eventi [inviando notifiche tramite posta elettronica sugli eventi dell'hub IoT di Azure con App per la logica](../event-grid/publish-iot-hub-events-to-logic-apps.md)