---
title: Monitoraggio delle metriche e log nel servizio di ingresso principale di Azure | Microsoft Docs
description: Questo articolo descrive le diverse metriche e i log di accesso supportati dal servizio di ingresso principale di Azure
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
ms.openlocfilehash: 16770ea0a320b3d9f081cc21a102ab050a6467f6
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60009753"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door-service"></a>Monitoraggio delle metriche e log nel servizio di ingresso principale di Azure

Il servizio di ingresso principale di Azure consente di monitorare le risorse nei modi seguenti:

- **Metrics** (Metriche). il gateway applicazione dispone attualmente di sette metriche per visualizzare i contatori delle prestazioni.
- **I log**. Attività e i log di diagnostica consentono prestazioni, accesso e altri dati per essere salvati o utilizzati da una risorsa a scopo di monitoraggio.

### <a name="metrics"></a>Metriche

Le metriche sono una funzionalità per determinate risorse di Azure che consentono di visualizzare i contatori delle prestazioni nel portale. Di seguito sono disponibili metriche di ingresso principale:

| Metrica | Nome visualizzato per la metrica | Unità | Dimensioni | DESCRIZIONE |
| --- | --- | --- | --- | --- |
| RequestCount | Conteggio richieste | Conteggio | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Numero di richieste client gestite da Frontdoor.  |
| RequestSize | Dimensioni della richiesta | Byte | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Numero di byte inviati come richieste dai client a Frontdoor. |
| ResponseSize | Dimensioni della risposta | Byte | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Numero di byte inviati come risposte da Frontdoor ai client. |
| TotalLatency | Latenza totale | Millisecondi | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | L'ora calcolata della richiesta del client ha ricevuta da porta principale fino al client di riconoscimento dell'ultimo byte di risposta dalla porta principale. |
| BackendRequestCount | Conteggio delle richieste del back-end | Conteggio | HttpStatus</br>HttpStatusGroup</br>Back-end | Numero di richieste inviate da Frontdoor ai back-end. |
| BackendRequestLatency | Latenza della richiesta del back-end | Millisecondi | Back-end | Tempo calcolato dal momento dell'invio della richiesta al back-end da parte di Frontdoor al momento della ricezione da parte di Frontdoor dell'ultimo byte della risposta inviata dal back-end. |
| BackendHealthPercentage | Percentuale di integrità del back-end | Percentuale | Back-end</br>BackendPool | Percentuale di probe di integrità con esito positivo da Frontdoor ai back-end. |
| WebApplicationFirewallRequestCount | Conteggio delle richieste web application firewall | Conteggio | PolicyName</br>RuleName</br>Azione | Numero di richieste client elaborate dalla sicurezza del livello dell'applicazione di Frontdoor. |

## <a name="activity-log"></a>Log attività

I log attività offrono informazioni sulle operazioni eseguite nel servizio di ingresso principale. È inoltre possibile determinare cosa, chi e quando per qualsiasi operazione di scrittura (put, post o delete) eseguita sul servizio di ingresso principale.

>[!NOTE]
>I log attività non includono le operazioni di lettura (get). Non includono anche le operazioni eseguite tramite il portale di Azure o l'API di gestione originale.

Attività di accesso effettua l'accesso al servizio di ingresso principale o tutti i log delle risorse di Azure in Monitoraggio di Azure. Per visualizzare i log di attività:

1. Selezionare l'istanza di ingresso principale.
2. Selezionare **log attività**.

    ![Log attività](./media/front-door-diagnostics/activity-log.png)

3. Scegliere un ambito di filtro e quindi selezionare **applica**.

## <a name="diagnostic-logging"></a>Log di diagnostica
I log di diagnostica offrono informazioni dettagliate sulle operazioni e gli errori che sono importanti per il controllo e la risoluzione dei problemi. I log di diagnostica differiscono dai log attività.

I log attività offrono informazioni dettagliate sulle operazioni eseguite sulle risorse di Azure. I log di diagnostica forniscono informazioni dettagliate sulle operazioni eseguite dalla risorsa. Per altre informazioni, vedere [log di diagnostica di monitoraggio di Azure](../azure-monitor/platform/diagnostic-logs-overview.md).

![Log di diagnostica](./media/front-door-diagnostics/diagnostic-log.png)

Per configurare i log di diagnostica per il servizio di ingresso principale:

1. Selezionare il servizio di ingresso principale di Azure.

2. Scegli **le impostazioni di diagnostica**.

3. Selezionare **Attiva diagnostica**. Archiviare i log di diagnostica con le metriche in un account di archiviazione, trasmessi a un hub eventi o inviarli a log di monitoraggio di Azure.

Il servizio di ingresso principale offre attualmente i log di diagnostica (in batch su base oraria). I log di diagnostica forniscono le singole richieste di API con ogni voce con lo schema seguente:

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

- [Creare un profilo di porta principale](quickstart-create-front-door.md)
- [Funzionamento di ingresso principale](front-door-routing-architecture.md)
