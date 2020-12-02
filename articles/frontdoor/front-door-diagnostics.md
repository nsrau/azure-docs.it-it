---
title: Monitoraggio di metriche e log nella porta anteriore di Azure | Microsoft Docs
description: Questo articolo descrive le diverse metriche e i log di accesso supportati da Azure front door
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: yuajia
ms.openlocfilehash: cd99be40700ab1c34176f2bf7497e4debf5cd424
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483798"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>Monitoraggio di metriche e log in front-end di Azure

Con l'uso di Azure front door è possibile monitorare le risorse nei modi seguenti:

- **Metrics** (Metriche). Il front-end di Azure dispone attualmente di otto metriche per visualizzare i contatori delle prestazioni.
- **Log**. I log attività e di diagnostica consentono di salvare o utilizzare le prestazioni, l'accesso e altri dati da una risorsa a scopo di monitoraggio.

### <a name="metrics"></a>Metriche

Le metriche sono una funzionalità per alcune risorse di Azure che consentono di visualizzare i contatori delle prestazioni nel portale. Di seguito sono riportate le metriche front-end disponibili:

| Metrica | Nome visualizzato per la metrica | Unità | Dimensioni | Descrizione |
| --- | --- | --- | --- | --- |
| RequestCount | Conteggio richieste | Conteggio | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Numero di richieste client gestite da Frontdoor.  |
| RequestSize | Dimensioni della richiesta | Byte | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Numero di byte inviati come richieste dai client a Frontdoor. |
| ResponseSize | Dimensioni della risposta | Byte | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Numero di byte inviati come risposte da Frontdoor ai client. |
| TotalLatency | Latenza totale | Millisecondi | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Tempo totale dalla richiesta client ricevuta dalla porta anteriore fino all'ultimo byte di risposta inviato da AFD al client. |
| BackendRequestCount | Conteggio delle richieste del back-end | Conteggio | HttpStatus</br>HttpStatusGroup</br>Back-end | Numero di richieste inviate da Frontdoor ai back-end. |
| BackendRequestLatency | Latenza della richiesta del back-end | Millisecondi | Back-end | Tempo calcolato dal momento dell'invio della richiesta al back-end da parte di Frontdoor al momento della ricezione da parte di Frontdoor dell'ultimo byte della risposta inviata dal back-end. |
| BackendHealthPercentage | Percentuale di integrità del back-end | Percentuale | Back-end</br>BackendPool | Percentuale di probe di integrità con esito positivo da Frontdoor ai back-end. |
| WebApplicationFirewallRequestCount | Conteggio delle richieste web application firewall | Conteggio | PolicyName</br>RuleName</br>Azione | Numero di richieste client elaborate dalla sicurezza del livello dell'applicazione di Frontdoor. |

## <a name="activity-logs"></a><a name="activity-log"></a>Log attività

I log attività forniscono informazioni sulle operazioni eseguite sull'anta anteriore. Determinano anche gli elementi What, who e when per qualsiasi operazione di scrittura (Put, post o DELETE) eseguita sulla porta anteriore.

>[!NOTE]
>I log attività non includono le operazioni di lettura (Get). Non includono inoltre le operazioni eseguite tramite l'portale di Azure o l'API di gestione originale.

Accedere ai log attività nel front-end o a tutti i log delle risorse di Azure in monitoraggio di Azure. Per visualizzare i log di attività:

1. Selezionare l'istanza della porta anteriore.
2. Selezionare **Log attività**.

    :::image type="content" source="./media/front-door-diagnostics/activity-log.png" alt-text="Log attività":::

3. Scegliere un ambito di filtro e quindi fare clic su **applica**.

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>Log di diagnostica
I log di diagnostica forniscono informazioni dettagliate sulle operazioni e sugli errori importanti per il controllo e la risoluzione dei problemi. I log di diagnostica sono diversi dai log attività.

I log attività forniscono informazioni approfondite sulle operazioni eseguite sulle risorse di Azure. I log di diagnostica forniscono informazioni dettagliate sulle operazioni eseguite dalla risorsa. Per altre informazioni, vedere [log di diagnostica di monitoraggio di Azure](../azure-monitor/platform/platform-logs-overview.md).

:::image type="content" source="./media/front-door-diagnostics/diagnostic-log.png" alt-text="Log di diagnostica":::

Per configurare i log di diagnostica per la porta anteriore:

1. Selezionare la porta di ingresso di Azure.

2. Scegliere **impostazioni di diagnostica**.

3. Selezionare **Attiva diagnostica**. Archiviare i log di diagnostica insieme alle metriche in un account di archiviazione, trasmetterli a un hub eventi o inviarli ai log di monitoraggio di Azure.

La porta anteriore fornisce attualmente log di diagnostica (in batch ogni ora). I log di diagnostica forniscono singole richieste API a ogni voce con lo schema seguente:

| Proprietà  | Descrizione |
| ------------- | ------------- |
| BackendHostname | Se la richiesta è stata trasmessa a un back-end, questo campo rappresenta il nome host del back-end. Questo campo sarà vuoto se la richiesta viene reindirizzata o inoltrata a una cache a livello di area (quando la memorizzazione nella cache viene abilitata per la regola di routing). |
| CacheStatus | Per gli scenari di memorizzazione nella cache, questo campo definisce l'hit/miss della cache al POP |
| ClientIp | Indirizzo IP del client che ha eseguito la richiesta. Se nella richiesta è presente un'intestazione X-Inoltred-for, l'indirizzo IP del client viene selezionato dallo stesso. |
| ClientPort | Porta IP del client che ha effettuato la richiesta. |
| HttpMethod | Metodo HTTP usato dalla richiesta. |
| HttpStatusCode | Codice di stato HTTP restituito dal proxy. |
| HttpStatusDetails | Stato risultante nella richiesta. Il significato di questo valore stringa è disponibile in una tabella di riferimento sullo stato. |
| HttpVersion | Tipo della richiesta o della connessione. |
| POP | Nome breve del perimetro in cui è stata sbarcata la richiesta. |
| RequestBytes | Dimensioni del messaggio di richiesta HTTP in byte, inclusi intestazioni e corpo della richiesta. |
| RequestUri | URI della richiesta ricevuta. |
| ResponseBytes | Byte inviati dal server back-end come risposta.  |
| RoutingRuleName | Nome della regola di routing a cui corrisponde la richiesta. |
| RulesEngineMatchNames | Nomi delle regole corrispondenti alla richiesta. |
| SecurityProtocol | Versione del protocollo TLS/SSL usata dalla richiesta o Null se non è stato usato alcun tipo di crittografia. |
| SentToOriginShield </br> (obsoleto) * **vedere le note sulla deprecazione nella sezione seguente.**| Se true, la risposta alla richiesta proviene dalla cache dello shield di origine anziché dal POP perimetrale. Lo shield di origine è una cache padre usata per migliorare la percentuale di riscontri nella cache. |
| isReceivedFromClient | Se true, significa che la richiesta proviene dal client. Se false, la richiesta non è presente nel perimetro (POP figlio) e viene risposta da Shield di origine (POP padre). |
| TimeTaken | Intervallo di tempo dal primo byte della richiesta alla porta anteriore all'ultimo byte di risposta, in secondi. |
| TrackingReference | Stringa di riferimento univoca che identifica una richiesta fornita da Frontdoor, inviata anche come intestazione X-Azure-Ref al client. Obbligatoria per la ricerca di dettagli nei log di accesso per una richiesta specifica. |
| UserAgent | Tipo di browser usato dal client. |
| ErrorInfo | Questo campo contiene il tipo di errore specifico per ulteriori operazioni di risoluzione dei problemi. </br> I valori possibili sono: </br> **NOERROR**: indica che non è stato trovato alcun errore. </br> **CertificateError**: errore di certificato SSL generico.</br> **CertificateNameCheckFailed**: il nome host nel certificato SSL non è valido o non corrisponde. </br> **ClientDisconnected**: errore della richiesta a causa della connessione di rete del client. </br> **UnspecifiedClientError**: errore del client generico. </br> **InvalidRequest**: richiesta non valida. Potrebbe verificarsi a causa di un'intestazione, un corpo e un URL non validi. </br> **DNSFailure**: errore DNS. </br> **DNSNameNotResolved**: non è stato possibile risolvere il nome o l'indirizzo del server. </br> **OriginConnectionAborted**: la connessione con l'origine è stata interrotta bruscamente. </br> **OriginConnectionError**: errore di connessione all'origine generica. </br> **OriginConnectionRefused**: non è stato possibile stabilire la connessione con l'origine. </br> **OriginError**: errore di origine generico. </br> **OriginInvalidResponse**: Origin ha restituito una risposta non valida o non riconosciuta. </br> **OriginTimeout**: il periodo di timeout per la richiesta di origine è scaduto. </br> **ResponseHeaderTooBig**: l'origine ha restituito troppo grande di un'intestazione di risposta. </br> **RestrictedIP**: la richiesta è stata bloccata a causa dell'IP limitato. </br> **SSLHandshakeError**: Impossibile stabilire una connessione con l'origine a causa di un errore di scuotimento della mano SSL. </br> **UnspecifiedError**: si è verificato un errore che non rientrava in nessuno degli errori della tabella. |

### <a name="sent-to-origin-shield-deprecation"></a>Deprecazione inviata a schermata di origine
La proprietà del log non elaborato **isSentToOriginShield** è stata deprecata e sostituita da un nuovo campo **isReceivedFromClient**. Utilizzare il nuovo campo se si sta già utilizzando il campo deprecato. 

I log non elaborati includono i log generati dal perimetro della rete CDN (POP figlio) e dallo scudo di origine. Shield di origine si riferisce ai nodi padre posizionati in modo strategico in tutto il mondo. Questi nodi comunicano con i server di origine e riducono il carico di traffico all'origine. 

Per ogni richiesta che passa a Shield di origine, sono presenti due voci di log:

* Uno per i nodi perimetrali
* Uno per Shield di origine. 

Per distinguere l'uscita o le risposte dai nodi perimetrali rispetto a Shield di origine, è possibile usare il campo **isReceivedFromClient** per ottenere i dati corretti. 

Se il valore è false, significa che la richiesta è stata risposta dalla schermatura di origine ai nodi perimetrali. Questo approccio è efficace per confrontare i log non elaborati con i dati di fatturazione. Gli addebiti non vengono addebitati per l'uscita dalla schermatura di origine ai nodi perimetrali. Vengono addebitati i costi per l'uscita dai nodi perimetrali ai client. 

**Esempio di query kusto per escludere i log generati sullo scudo di origine in Log Analytics.**

`AzureDiagnostics 
| where Category == "FrontdoorAccessLog" and isReceivedFromClient_b == true`

> [!NOTE]
> Per diverse configurazioni di routing e comportamenti del traffico, alcuni dei campi come backendHostname, cacheStatus, isReceivedFromClient e POP Field possono rispondere con valori diversi. La tabella seguente illustra i diversi valori che questi campi avranno per diversi scenari:

| Scenari | Numero di voci di log | POP | BackendHostname | isReceivedFromClient | CacheStatus |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| Regola di routing senza caching abilitato | 1 | Codice POP perimetrale | Back-end in cui la richiesta è stata trasmessa | Vero | CONFIG_NOCACHE |
| Regola di routing con memorizzazione nella cache abilitata. Riscontro nella cache al POP perimetrale | 1 | Codice POP perimetrale | Empty | Vero | COLPITO |
| Regola di routing con memorizzazione nella cache abilitata. Mancati riscontri nella cache al pop perimetrale, ma riscontro nella cache padre | 2 | 1. codice POP perimetrale</br>2. codice POP della cache padre | 1. nome host POP della cache padre</br>2. vuoto | 1. true</br>2. false | 1. MISS</br>2. HIT |
| Regola di routing con memorizzazione nella cache abilitata. Mancata memorizzazione nella cache al POP perimetrale ma riscontro nella cache parziale nel POP della cache padre | 2 | 1. codice POP perimetrale</br>2. codice POP della cache padre | 1. nome host POP della cache padre</br>2. back-end che consente di popolare la cache | 1. true</br>2. false | 1. MISS</br>2. PARTIAL_HIT |
| Regola di routing con memorizzazione nella cache abilitata. PARTIAL_HIT della cache al POP perimetrale ma riscontri nella cache nel POP della cache padre | 2 | 1. codice POP perimetrale</br>2. codice POP della cache padre | 1. codice POP perimetrale</br>2. codice POP della cache padre | 1. true</br>2. false | 1. PARTIAL_HIT</br>2. HIT |
| Regola di routing con memorizzazione nella cache abilitata. Mancati riscontri nella cache in corrispondenza del blocco e della cache padre | 2 | 1. codice POP perimetrale</br>2. codice POP della cache padre | 1. codice POP perimetrale</br>2. codice POP della cache padre | 1. true</br>2. false | 1. MISS</br>2. MISS |

> [!NOTE]
> Per gli scenari di memorizzazione nella cache, il valore per lo stato della cache verrà partial_hit quando alcuni byte per una richiesta vengono serviti dal bordo anteriore o dalla cache dello scudo di origine, mentre alcuni byte vengono serviti dall'origine per oggetti di grandi dimensioni.

Frontdoor di Azure usa una tecnica chiamata suddivisione degli oggetti in blocchi. Quando viene richiesto un file di grandi dimensioni, la porta anteriore recupera parti più piccole del file dall'origine. Quando il server POP porta anteriore riceve un intervallo completo o di byte del file richiesto, il server perimetrale della porta anteriore richiede il file dall'origine in blocchi di 8 MB.

Quando il blocco arriva al bordo anteriore, viene memorizzato nella cache e servito immediatamente all'utente. La porta anteriore, quindi, preleva il blocco successivo in parallelo. Questa prelettura garantisce che il contenuto rimanga un blocco davanti all'utente, riducendo la latenza. Questo processo continua fino a quando non viene scaricato l'intero file (se richiesto), sono disponibili tutti gli intervalli di byte (se richiesti) o il client chiude la connessione. Per altre informazioni sulla richiesta di intervalli di byte, vedere RFC 7233. La porta anteriore memorizza nella cache tutti i blocchi non appena vengono ricevuti. Non è necessario memorizzare l'intero file nella cache della porta anteriore. Le richieste successive per il file o gli intervalli di byte vengono gestite dalla cache della porta anteriore. Se non tutti i blocchi vengono memorizzati nella cache nella porta anteriore, viene usata la prelettura per richiedere i blocchi dall'origine. Questa ottimizzazione presuppone il fatto che il server di origine supporti le richieste di intervalli di byte. Se il server di origine non supporta le richieste di intervalli di byte, questa ottimizzazione non è efficace.

## <a name="next-steps"></a>Passaggi successivi

- [Creare un profilo Frontdoor](quickstart-create-front-door.md)
- [Funzionamento della porta anteriore](front-door-routing-architecture.md)
