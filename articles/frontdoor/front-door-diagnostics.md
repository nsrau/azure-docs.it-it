---
title: Servizio Frontdoor di Azure - Metriche e registrazione | Microsoft Docs
description: Questo articolo consente di comprendere le diverse metriche e gli svariati log di accesso supportati dal servizio Frontdoor di Azure
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sharadag
ms.openlocfilehash: 3097f4a1716718df5d67769e234562a234623cfe
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407029"
---
# <a name="monitoring-metrics-and-logs-for-front-door"></a>Monitoraggio delle metriche e log di ingresso principale

Il servizio di ingresso principale di Azure consente di monitorare le risorse nei modi seguenti:

* [Metriche](#metrics): il gateway applicazione dispone attualmente di sette metriche per visualizzare i contatori delle prestazioni.
* [Log](#diagnostic-logging): i log consentono di salvare o usare i dati delle prestazioni, di accesso e di altro tipo relativi a una risorsa per scopi di monitoraggio.

## <a name="metrics"></a>Metriche

Le metriche sono una funzionalità di alcune risorse di Azure che consente di visualizzare i contatori delle prestazioni nel portale. Per Frontdoor sono disponibili le metriche seguenti:

| Metrica | Nome visualizzato per la metrica | Unità | Dimensioni | DESCRIZIONE |
| --- | --- | --- | --- | --- |
| RequestCount | Conteggio richieste | Conteggio | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Numero di richieste client gestite da Frontdoor.  |
| RequestSize | Dimensioni della richiesta | Byte | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Numero di byte inviati come richieste dai client a Frontdoor. |
| ResponseSize | Dimensioni della risposta | Byte | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Numero di byte inviati come risposte da Frontdoor ai client. |
| TotalLatency | Latenza totale | Millisecondi | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Tempo calcolato dal momento della ricezione della richiesta client da parte di Frontdoor al momento della conferma da parte del client della ricezione dell'ultimo byte della risposta di Frontdoor. |
| BackendRequestCount | Conteggio delle richieste del back-end | Conteggio | HttpStatus</br>HttpStatusGroup</br>Back-end | Numero di richieste inviate da Frontdoor ai back-end. |
| BackendRequestLatency | Latenza della richiesta del back-end | Millisecondi | Back-end | Tempo calcolato dal momento dell'invio della richiesta al back-end da parte di Frontdoor al momento della ricezione da parte di Frontdoor dell'ultimo byte della risposta inviata dal back-end. |
| BackendHealthPercentage | Percentuale di integrità del back-end | Percentuale | Back-end</br>BackendPool | Percentuale di probe di integrità con esito positivo da Frontdoor ai back-end. |
| WebApplicationFirewallRequestCount | Conteggio delle richieste web application firewall | Conteggio | PolicyName</br>RuleName</br>Azione | Numero di richieste client elaborate dalla sicurezza del livello dell'applicazione di Frontdoor. |

## <a name="activity-log"></a>Log attività

I log attività offrono informazioni dettagliate sulle operazioni eseguite sull'ingresso principale. Uso del log attività, è possibile determinare la "cosa, chi e quando" per qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita sull'ingresso principale.

> [!NOTE]
> I log attività non includono le operazioni di lettura (GET) né le operazioni eseguite nel portale di Azure o usando le API di gestione originali.

È possibile accedere ai log attività di ingresso principale o accedere ai log di tutte le risorse di Azure in Monitoraggio di Azure. 

Per visualizzare i log di attività:

1. Selezionare l'istanza di ingresso principale.
2. Fare clic su **Log attività**.

    ![Log attività](./media/front-door-diagnostics/activity-log.png)

3. Selezionare l'ambito di filtro desiderato e fare clic su **Applica**.

## <a name="diagnostic-logging"></a>Registri di diagnostica
I log di diagnostica offrono informazioni dettagliate sulle operazioni e gli errori importanti per il controllo e per la risoluzione dei problemi. I log di diagnostica differiscono dai log attività. I log attività offrono informazioni approfondite sulle operazioni eseguite nelle risorse di Azure. I log di diagnostica forniscono informazioni dettagliate sulle operazioni eseguite dalla risorsa. Altre informazioni sulle [log di diagnostica di monitoraggio di Azure](../azure-monitor/platform/diagnostic-logs-overview.md). 

Per configurare i log di diagnostica per l'ingresso principale:

1. Selezionare l'istanza del servizio Gestione API.
2. Fare clic su **Impostazioni di diagnostica**.

    ![Log di diagnostica](./media/front-door-diagnostics/diagnostic-log.png)

3. Fare clic su **Attiva diagnostica**. I log di diagnostica possono essere archiviati con le metriche in un account di archiviazione, trasmessi a un hub eventi o inviati ai log di Monitoraggio di Azure. 

Servizio di ingresso principale di Azure offre attualmente funzionalità di diagnostica log (in batch orari) sulle singole API richiesta con ogni voce con lo schema seguente:

| Proprietà  | DESCRIZIONE |
| ------------- | ------------- |
| ClientIp | Indirizzo IP del client che ha eseguito la richiesta. |
| ClientPort | La porta IP del client che ha effettuato la richiesta. |
| HttpMethod | Metodo HTTP usato dalla richiesta. |
| HttpStatusCode | Il codice di stato HTTP restituito dal proxy. |
| HttpStatusDetails | Stato risultante nella richiesta. Significato del valore stringa è reperibile in una tabella di riferimento sullo stato. |
| HttpVersion | Tipo della richiesta o della connessione. |
| RequestBytes | Le dimensioni del messaggio di richiesta HTTP in byte, comprese le intestazioni della richiesta e il corpo della richiesta. |
| RequestUri | URI della richiesta ricevuta. |
| ResponseBytes | Byte inviati dal server back-end come risposta.  |
| RoutingRuleName | Il nome della regola di routing che soddisfano la richiesta. |
| SecurityProtocol | La versione del protocollo TLS/SSL non usato per la richiesta o null se nessuna crittografia. |
| TimeTaken | Il periodo di tempo impiegato dall'azione, in millisecondi. |
| UserAgent | Il tipo di browser usato dal client |
| TrackingReference | La stringa di riferimento univoco che identifica una richiesta servita dal porta d'ingresso, inviati anche come intestazione X-Azure-Ref al client. Obbligatorio per la ricerca di dettagli nei log di accesso per una richiesta specifica. |

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).
