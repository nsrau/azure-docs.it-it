---
title: Monitoraggio di metriche e log nel servizio front door di Azure | Microsoft Docs
description: Questo articolo descrive le diverse metriche e i registri di accesso supportati dal servizio front door di Azure
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
ms.openlocfilehash: 5f76df0045fc3939392759ed0edd266380295a85
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260165"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door-service"></a>Monitoraggio di metriche e log nel servizio front-end di Azure

Con il servizio front door di Azure è possibile monitorare le risorse nei modi seguenti:

- **Metrics** (Metriche). il gateway applicazione dispone attualmente di sette metriche per visualizzare i contatori delle prestazioni.
- **Log**. I log attività e di diagnostica consentono di salvare o utilizzare le prestazioni, l'accesso e altri dati da una risorsa a scopo di monitoraggio.

### <a name="metrics"></a>metrics

Le metriche sono una funzionalità per alcune risorse di Azure che consentono di visualizzare i contatori delle prestazioni nel portale. Di seguito sono riportate le metriche front-end disponibili:

| Metrica | Nome visualizzato per la metrica | Unità | Dimensioni | Descrizione |
| --- | --- | --- | --- | --- |
| RequestCount | Conteggio richieste | Count | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Numero di richieste client gestite da Frontdoor.  |
| RequestSize | Dimensioni della richiesta | Byte | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Numero di byte inviati come richieste dai client a Frontdoor. |
| ResponseSize | Dimensioni della risposta | Byte | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Numero di byte inviati come risposte da Frontdoor ai client. |
| TotalLatency | Latenza totale | Millisecondi | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Tempo calcolato dalla richiesta client ricevuta dalla porta anteriore finché il client non ha riconosciuto l'ultimo byte di risposta da sportello anteriore. |
| BackendRequestCount | Conteggio delle richieste del back-end | Count | HttpStatus</br>HttpStatusGroup</br>Back-end | Numero di richieste inviate da Frontdoor ai back-end. |
| BackendRequestLatency | Latenza della richiesta del back-end | Millisecondi | Back-end | Tempo calcolato dal momento dell'invio della richiesta al back-end da parte di Frontdoor al momento della ricezione da parte di Frontdoor dell'ultimo byte della risposta inviata dal back-end. |
| BackendHealthPercentage | Percentuale di integrità del back-end | Percent | Back-end</br>BackendPool | Percentuale di probe di integrità con esito positivo da Frontdoor ai back-end. |
| WebApplicationFirewallRequestCount | Conteggio delle richieste web application firewall | Count | PolicyName</br>RuleName</br>Azione | Numero di richieste client elaborate dalla sicurezza del livello dell'applicazione di Frontdoor. |

## <a name="activity-log"></a>Log attività

I log attività forniscono informazioni sulle operazioni eseguite sul servizio front door. Determinano anche gli elementi What, who e when per qualsiasi operazione di scrittura (Put, post o DELETE) eseguita sul servizio front door.

>[!NOTE]
>I log attività non includono le operazioni di lettura (Get). Non includono inoltre le operazioni eseguite tramite l'portale di Azure o l'API di gestione originale.

Accedere ai log attività nel servizio front door o a tutti i log delle risorse di Azure in monitoraggio di Azure. Per visualizzare i log di attività:

1. Selezionare l'istanza della porta anteriore.
2. Selezionare **log attività**.

    ![Log attività](./media/front-door-diagnostics/activity-log.png)

3. Scegliere un ambito di filtro e quindi fare clic su **applica**.

## <a name="diagnostic-logging"></a>Log di diagnostica
I log di diagnostica forniscono informazioni dettagliate sulle operazioni e sugli errori importanti per il controllo e la risoluzione dei problemi. I log di diagnostica sono diversi dai log attività.

I log attività forniscono informazioni approfondite sulle operazioni eseguite sulle risorse di Azure. I log di diagnostica forniscono informazioni dettagliate sulle operazioni eseguite dalla risorsa. Per altre informazioni, vedere [log di diagnostica di monitoraggio di Azure](../azure-monitor/platform/resource-logs-overview.md).

![Log di diagnostica](./media/front-door-diagnostics/diagnostic-log.png)

Per configurare i log di diagnostica per il servizio front-door:

1. Selezionare il servizio front door di Azure.

2. Scegliere **impostazioni di diagnostica**.

3. Selezionare **Attiva diagnostica**. Archiviare i log di diagnostica insieme alle metriche in un account di archiviazione, trasmetterli a un hub eventi o inviarli ai log di monitoraggio di Azure.

Il servizio front door fornisce attualmente log di diagnostica (in batch ogni ora). I log di diagnostica forniscono singole richieste API a ogni voce con lo schema seguente:

| Proprietà  | Descrizione |
| ------------- | ------------- |
| ClientIp | Indirizzo IP del client che ha eseguito la richiesta. |
| ClientPort | Porta IP del client che ha effettuato la richiesta. |
| HttpMethod | Metodo HTTP usato dalla richiesta. |
| HttpStatusCode | Codice di stato HTTP restituito dal proxy. |
| HttpStatusDetails | Stato risultante della richiesta. Il significato di questo valore stringa è reperibile in una tabella di riferimento sullo stato. |
| HttpVersion | Tipo della richiesta o della connessione. |
| RequestBytes | Dimensioni del messaggio di richiesta HTTP in byte, incluse le intestazioni della richiesta e il corpo della richiesta. |
| RequestUri | URI della richiesta ricevuta. |
| ResponseBytes | Byte inviati dal server back-end come risposta.  |
| RoutingRuleName | Nome della regola di routing a cui corrisponde la richiesta. |
| SecurityProtocol | Versione del protocollo TLS/SSL utilizzata dalla richiesta o null se non è presente alcuna crittografia. |
| TimeTaken | Periodo di tempo impiegato dall'azione, in millisecondi. |
| UserAgent | Tipo di browser utilizzato dal client |
| TrackingReference | Stringa di riferimento univoca che identifica una richiesta servita dalla porta anteriore, inviata anche come intestazione X-Azure-Ref al client. Obbligatorio per la ricerca dei dettagli nei log di accesso per una richiesta specifica. |

## <a name="next-steps"></a>Passaggi successivi

- [Creare un profilo di porta anteriore](quickstart-create-front-door.md)
- [Funzionamento della porta anteriore](front-door-routing-architecture.md)
