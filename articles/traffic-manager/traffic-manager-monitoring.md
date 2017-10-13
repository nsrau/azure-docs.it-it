---
title: Monitoraggio degli endpoint di Gestione traffico di Azure | Microsoft Docs
description: "Questo articolo illustra in che modo Gestione traffico usa il monitoraggio e il failover automatico degli endpoint per consentire ai clienti di Azure di distribuire applicazioni a disponibilità elevata"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: fff25ac3-d13a-4af9-8916-7c72e3d64bc7
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/22/2017
ms.author: kumud
ms.openlocfilehash: 5ce000814f2f5899a7338fdefb39c4873c006b1d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="traffic-manager-endpoint-monitoring"></a>Monitoraggio degli endpoint di Gestione traffico

Gestione traffico di Azure include il monitoraggio degli endpoint e il failover automatico degli endpoint. Questa funzionalità consente di distribuire applicazioni a disponibilità elevata resilienti agli errori di endpoint, inclusi gli errori di area di Azure.

## <a name="configure-endpoint-monitoring"></a>Configurare il monitoraggio degli endpoint

Per configurare il monitoraggio degli endpoint è necessario specificare le seguenti impostazioni nel profilo di Gestione traffico:

* **Protocollo**. Scegliere HTTP, HTTPS o TCP come protocollo che Gestione traffico usa quando esegue il sondaggio dell'endpoint per verificarne l'integrità. Il monitoraggio HTTPS non verifica la validità del certificato SSL, ma solo la presenza.
* **Porta**. scegliere la porta usata per la richiesta.
* **Percorso**. Questa impostazione di configurazione è valida solo per i protocolli HTTP e HTTPS, per i quali è necessario specificare l'impostazione del percorso. Se si specifica questa impostazione per il protocollo di monitoraggio TCP, viene generato un errore. Per il protocollo TCP specificare il percorso relativo e il nome della pagina Web o il file a cui accede il monitoraggio. Una barra (/) è una voce valida per il percorso relativo. Questo valore implica che il file sia nella directory radice (impostazione predefinita).
* **Intervallo sondaggio**. Questo valore specifica la frequenza con cui viene controllata l'integrità di un endpoint dall'agente di sondaggio di Gestione traffico. È possibile specificare due valori qui: 30 secondi (sondaggio normale) e 10 secondi (sondaggio veloce). Se non viene specificato alcun valore, il profilo imposta un valore predefinito di 30 secondi. Per altre informazioni sui prezzi per il sondaggio rapido, visitare la pagina dei [prezzi per Gestione traffico](https://azure.microsoft.com/pricing/details/traffic-manager).
* **Numero di errori tollerati**. Questo valore specifica il numero di errori tollerati da un agente di sondaggio di Gestione traffico prima di contrassegnare l'endpoint come non integro. Il valore può essere compreso tra 0 e 9. Un valore pari a 0 indica che un singolo errore di monitoraggio può far sì che l'endpoint venga contrassegnato come non integro. Se non si specifica alcun valore, viene usato il valore predefinito di 3.
* **Timeout di monitoraggio**. Questa proprietà specifica la quantità di tempo che l'agente di sondaggio di Gestione traffico deve attendere prima di considerare il controllo come un errore quando un sondaggio di controllo di integrità viene inviato all'endpoint. Se l'intervallo sondaggio è impostato su 30 secondi, è possibile impostare il valore di timeout tra 5 e 10 secondi. Se non si specifica alcun valore, viene usato il valore predefinito di 10 secondi. Se l'intervallo sondaggio è impostato su 10 secondi, è possibile impostare il valore di timeout tra 5 e 9 secondi. Se non si specifica alcun valore di timeout, viene usato il valore predefinito di 9 secondi.

![Monitoraggio degli endpoint di Gestione traffico](./media/traffic-manager-monitoring/endpoint-monitoring-settings.png)

**Figura 1: Monitoraggio degli endpoint di Gestione traffico**

## <a name="how-endpoint-monitoring-works"></a>Funzionamento del monitoraggio degli endpoint

Se il protocollo di monitoraggio è impostato come HTTP o HTTPS, l'agente di sondaggio di Gestione traffico esegue una richiesta GET all'endpoint usando il protocollo, la porta e il percorso relativo specificati. Se ottiene una risposta 200-OK, l'endpoint viene considerato integro. Se la risposta è un valore diverso, oppure se non viene ricevuta alcuna risposta entro il periodo di timeout specificato, l'agente di sondaggio di Gestione traffico esegue un nuovo tentativo in base all'impostazione Numero di tentativi tollerati. Se quindi questa impostazione è pari a 0, non viene eseguito alcun altro tentativo. Se il numero di tentativi consecutivi non riusciti è superiore all'impostazione di Numero di tentativi tollerati, l'endpoint viene contrassegnato come non integro. 

Se il protocollo di monitoraggio è TCP, l'agente di sondaggio di Gestione traffico avvia una richiesta di connessione TCP usando la porta specificata. Se l'endpoint risponde alla richiesta con una risposta per stabilire la connessione, il controllo integrità viene contrassegnato come esito positivo e l'agente di sondaggio di Gestione traffico reimposta la connessione TCP. Se la risposta è un valore diverso, oppure se non viene ricevuta alcuna risposta entro il periodo di timeout specificato, l'agente di sondaggio di Gestione traffico esegue un nuovo tentativo in base all'impostazione Numero di tentativi tollerati. Se quindi questa impostazione è pari a 0, non viene eseguito alcun altro tentativo. Se il numero di tentativi consecutivi non riusciti è superiore all'impostazione di Numero di errori tollerati, l'endpoint viene contrassegnato come non integro.

In tutti i casi, i sondaggi di Gestione traffico da più posizioni e la determinazione di errori consecutivi avvengono all'interno di ogni area. Questo significa anche che gli endpoint ricevono sondaggi di integrità da Gestione traffico con una frequenza maggiore rispetto all'impostazione usata per l'intervallo di sondaggio.

>[!NOTE]
>Per il protocollo di monitoraggio HTTP o HTTPS, una pratica comune sul lato endpoint consiste nell'implementare una pagina personalizzata all'interno dell'applicazione, ad esempio /health.aspx. Usando questo percorso per il monitoraggio, è possibile eseguire controlli specifici dell'applicazione, ad esempio il controllo dei contatori delle prestazioni o la verifica della disponibilità del database. In base a questi controlli personalizzati, la pagina restituisce un codice di stato HTTP appropriato.

Tutti gli endpoint in un profilo di Gestione traffico condividono le impostazioni di monitoraggio. Se è necessario usare impostazioni di monitoraggio diverse per i vari endpoint, è possibile creare i [profili nidificati di Gestione traffico](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings).

## <a name="endpoint-and-profile-status"></a>Stato di endpoint e profili

Gli endpoint e i profili di Gestione traffico possono essere abilitati e disabilitati. Tuttavia, lo stato degli endpoint può cambiare anche a causa di impostazioni e processi automatici di Gestione traffico.

### <a name="endpoint-status"></a>Stato endpoint

È possibile abilitare o disabilitare un endpoint specifico. Il servizio sottostante, che potrebbe essere ancora integro, non è interessato. La modifica dello stato dell'endpoint controlla la disponibilità dell'endpoint nel profilo di Gestione traffico. Quando un endpoint è associato allo stato Disabled, Gestione traffico non ne verifica l'integrità e l'endpoint non viene incluso in una risposta DNS.

### <a name="profile-status"></a>Stato profilo

Usando l'impostazione dello stato del profilo è possibile abilitare o disabilitare un profilo specifico. Mentre lo stato dell'endpoint interessa un solo endpoint, lo stato del profilo interessa l'intero profilo, che include tutti gli endpoint. Quando si disabilita un profilo, l'integrità degli endpoint non viene verificata e nessun endpoint viene incluso in una risposta DNS. Per la query DNS viene restituito un codice di risposta [NXDOMAIN](https://tools.ietf.org/html/rfc2308).

### <a name="endpoint-monitor-status"></a>Endpoint monitor status (Stato monitoraggio endpoint)

Il valore relativo allo stato di monitoraggio dell'endpoint viene generato da Gestione traffico per indicare lo stato corrente dell'endpoint. Questa impostazione non può essere modificata manualmente. Lo stato del monitoraggio dell'endpoint è una combinazione dei risultati del monitoraggio dell'endpoint e dello stato dell'endpoint configurato. La tabella seguente indica i valori possibili dello stato di monitoraggio degli endpoint:

| Stato profilo | Stato endpoint | Endpoint monitor status (Stato monitoraggio endpoint) | Note |
| --- | --- | --- | --- |
| Disabled |Enabled |Inactive |Il profilo è stato disabilitato. Anche se lo stato dell'endpoint è Enabled, se lo stato del profilo è Disabled, quest'ultimo avrà la precedenza. Gli endpoint nei profili disabilitati non vengono monitorati. Per la query DNS viene restituito un codice di risposta NXDOMAIN. |
| &lt;qualsiasi&gt; |Disabled |Disabled |L'endpoint è stato disabilitato. Gli endpoint disabilitati non vengono monitorati. L'endpoint non è incluso nelle risposte DNS e pertanto non riceve traffico. |
| Enabled |Enabled |Online |L'endpoint è monitorato e integro. È incluso nelle risposte DNS ed è in grado di ricevere traffico. |
| Enabled |Enabled |Degraded |I controlli di integrità del monitoraggio dell'endpoint hanno esito negativo. L'endpoint non è incluso nelle risposte DNS e non riceve traffico. <br>Un'eccezione a questa situazione è quando tutti gli endpoint sono danneggiati. In questo caso tutti gli elementi vengono considerati da restituire nella risposta alla query.</br>|
| Enabled |Enabled |CheckingEndpoint |L'endpoint è monitorato, ma i risultati del primo test non sono ancora pervenuti. CheckingEndpoint è un stato temporaneo che in genere si verifica immediatamente dopo l'aggiunta o l'abilitazione di un endpoint nel profilo. Un endpoint con questo stato viene incluso nelle risposte DNS e può ricevere traffico. |
| Enabled |Enabled |Arrestato |Il servizio cloud o l'app Web cui punta l'endpoint non è in esecuzione. Verificare le impostazioni del servizio cloud o dell'app Web. Questa situazione può verificarsi anche se l'endpoint è di tipo annidato e il profilo figlio è disabilitato o non è attivo. <br>Gli endpoint con stato Interrotto non vengono monitorati. Non è incluso nelle risposte DNS e non riceve traffico. Un'eccezione a questa situazione è quando tutti gli endpoint sono danneggiati. In questo caso tutti gli elementi vengono considerati da restituire nella risposta alla query.</br>|

Per informazioni dettagliate su come viene calcolato lo stato del monitoraggio degli endpoint nidificati, vedere [Profili nidificati di Gestione traffico](traffic-manager-nested-profiles.md).

### <a name="profile-monitor-status"></a>Stato monitoraggio profilo

Lo stato di monitoraggio del profilo è una combinazione dei valori relativi allo stato del profilo configurato e allo stato di monitoraggio di tutti gli endpoint. I possibili valori sono descritti nella tabella seguente:

| Stato profilo (come configurato) | Endpoint monitor status (Stato monitoraggio endpoint) | Stato monitoraggio profilo | Note |
| --- | --- | --- | --- |
| Disabled |&lt;qualsiasi&gt; o un profilo senza endpoint definiti. |Disabled |Il profilo è stato disabilitato. |
| Enabled |Almeno un endpoint è associato allo stato Degraded. |Degraded |Esaminare i valori di stato dei singoli endpoint per determinare quali endpoint richiedono attenzione. |
| Enabled |Almeno un endpoint è associato allo stato Online. Nessun endpoint presenta lo stato Degraded. |Online |Il servizio sta accettando il traffico. Non è necessaria alcuna azione. |
| Enabled |Almeno un endpoint è associato allo stato CheckingEndpoint. Nessun endpoint presenta lo stato Online o Degraded. |CheckingEndpoints |Questo stato di transizione si verifica quando un profilo viene creato o abilitato. Viene verificata l'integrità dell'endpoint per la prima volta. |
| Enabled |Tutti gli endpoint del profilo presentano lo stato Disabled o Stopped oppure nel profilo non sono definiti endpoint. |Inactive |Non ci sono endpoint attivi, ma il profilo presenta lo stato Enabled. |

## <a name="endpoint-failover-and-recovery"></a>Failover e ripristino degli endpoint

Gestione traffico verifica periodicamente l'integrità di ogni endpoint, inclusi gli endpoint non integri. Rileva quando un endpoint diventa integro e lo reinserisce nella rotazione.

Un endpoint non è integro quando si verifica uno degli eventi seguenti:
- Se il protocollo di monitoraggio è HTTP o HTTPS:
    - Viene ricevuta una risposta non 200, incluso un codice 2xx diverso o un reindirizzamento 301/302.
- Se il protocollo di monitoraggio è TCP: 
    - Viene ricevuta una risposta diversa da ACK o SYN-ACK in risposta alla richiesta di sincronizzazione inviata da Gestione traffico per tentare di stabilire una connessione.
- Timeout. 
- Qualsiasi altro problema di connessione che determina l'irraggiungibilità dell'endpoint.

Per ulteriori informazioni su come risolvere i problemi relativi ai controlli non riusciti, vedere [Risoluzione dei problemi relativi allo stato Danneggiato di Gestione traffico](traffic-manager-troubleshooting-degraded.md). 

La sequenza temporale seguente nella Figura 2 è una descrizione dettagliata del processo di monitoraggio dell'endpoint di Gestione traffico con le seguenti impostazioni: il protocollo di monitoraggio è HTTP, l'intervallo di sondaggio è 30 secondi, il numero di errori tollerati è 3, il valore di timeout è 10 secondi e il TTL del DNS è 30 secondi.

![Sequenza di failover e failback degli endpoint di Gestione traffico](./media/traffic-manager-monitoring/timeline.png)

**Figura 2: Sequenza di failover e ripristino degli endpoint di Gestione traffico**

1. **GET**. Per ogni endpoint, il sistema di monitoraggio di Gestione traffico esegue una richiesta GET sul percorso specificato nelle impostazioni di monitoraggio.
2. **200 OK**. il sistema di monitoraggio prevede la restituzione di un messaggio HTTP 200 OK entro 10 secondi. Alla ricezione della risposta, il sistema riconosce la disponibilità del servizio.
3. **30 secondi tra i controlli**. Il controllo di integrità dell'endpoint viene ripetuto ogni 30 secondi.
4. **Servizio non disponibile**. il servizio diventa non disponibile. Gestione traffico non avrà informazioni fino al successivo controllo di integrità.
5. **Tenta di accedere al percorso di monitoraggio**. il sistema di monitoraggio esegue una richiesta GET, ma non riceve risposte entro il periodo di timeout di 10 secondi oppure riceve una risposta diversa da 200. Il sistema esegue quindi altri tre tentativi a intervalli di 30 secondi. Se uno dei tentativi ha esito positivo, il conteggio viene azzerato.
6. **Stato impostato su Degraded**. al quarto errore consecutivo, il sistema di monitoraggio contrassegna lo stato dell'endpoint non disponibile come Degraded.
7. **Traffico deviato ad altri endpoint**. vengono aggiornati i server dei nomi DNS di Gestione traffico, che non restituisce più l'endpoint in risposta alle query DNS. Le nuove connessioni vengono indirizzate ad altri endpoint disponibili. Tuttavia, le risposte DNS precedenti che includono questo endpoint possono essere ancora memorizzate nella cache da server DNS e client DNS ricorsivi. I client continuano a usare l'endpoint fino alla scadenza della cache DNS. Quando la cache DNS scade i client eseguono nuove query DNS, indirizzate a endpoint diversi. La durata della cache è determinata dall'impostazione TTL definita nel profilo di Gestione traffico, ad esempio 30 secondi.
8. **I controlli di integrità proseguono**. Gestione traffico continua a controllare l'integrità dell'endpoint finché si trova nello stato Degraded. Gestione traffico rileva quando l'endpoint ritorna integro.
9. **Il servizio ritorna online**. il servizio diventa disponibile. L'endpoint mantiene lo stato Degraded in Gestione traffico finché il sistema di monitoraggio non esegue il controllo di integrità successivo.
10. **Ripresa del traffico al servizio**. Gestione traffico invia una richiesta GET e riceve una risposta di stato 200 OK, Il servizio è tornato a uno stato integro. I server dei nomi di Gestione traffico vengono aggiornati di nuovo e iniziano a distribuire il nome DNS del servizio nelle risposte DNS. Il traffico torna all'endpoint non appena scadono le risposte DNS memorizzate nella cache che restituiscono altri endpoint e le connessioni esistenti verso altri endpoint vengono interrotte.

    > [!NOTE]
    > Poiché lavora a livello di DNS, Gestione traffico non può influenzare le connessioni esistenti verso qualsiasi endpoint. Quando indirizza il traffico tra gli endpoint, modificando le impostazioni del profilo oppure durante il failover o il failback, Gestione traffico indirizza le nuove connessioni agli endpoint disponibili. Tuttavia, altri endpoint possono continuare a ricevere il traffico tramite le connessioni esistenti finché tali sessioni non vengono terminate. Per consentire lo smaltimento del traffico dalle connessioni esistenti, le applicazioni devono limitare la durata della sessione usata con ogni endpoint.

## <a name="traffic-routing-methods"></a>Metodi di routing del traffico

Un endpoint il cui stato è Degraded non viene restituito nella risposta alle query DNS. Viene invece scelto e restituito un endpoint alternativo. Il metodo di routing del traffico configurato nel profilo determina il modo in cui viene scelto l'endpoint alternativo.

* **Priorità**. gli endpoint formano un elenco con priorità. Viene sempre restituito il primo endpoint disponibile nell'elenco. Se lo stato di un endpoint è Degraded, viene restituito il successivo endpoint disponibile.
* **Ponderato**. Viene scelto in modo casuale uno degli endpoint disponibili, in base ai pesi assegnati e ai pesi degli altri endpoint disponibili.
* **Prestazioni**. Viene restituito l'endpoint più vicino all'utente finale. Se tale endpoint non è disponibile, viene scelto in modo casuale un endpoint tra tutti gli altri endpoint disponibili. La scelta casuale di un endpoint evita un errore a catena che può verificarsi in caso di overload del successivo endpoint più vicino. È possibile configurare piani di failover alternativi per il routing del traffico con il metodo Prestazioni usando [profili di Gestione traffico nidificati](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region).
* **Geografico**. Viene restituito l'endpoint mappato per fornire la posizione geografica in base all'IP della richiesta di query. Se tale endpoint non è disponibile, non verrà selezionato un altro endpoint per eseguire il failover, poiché un'area geografica può essere mappata solo a un endpoint in un profilo. Per altre informazioni, vedere le [domande frequenti](traffic-manager-FAQs.md#traffic-manager-geographic-traffic-routing-method). Come procedura consigliata, quando si usa il routing geografico, si consiglia ai clienti di usare profili di Gestione traffico annidati con più di un endpoint come endpoint del profilo.

Per altre informazioni, vedere [Metodi di routing del traffico di Gestione traffico](traffic-manager-routing-methods.md).

> [!NOTE]
> Un'eccezione al normale comportamento del routing del traffico si verifica quando lo stato di tutti gli endpoint idonei risulta Degraded. Gestione traffico effettua un tentativo e *risponde come se tutti gli endpoint con stato Degraded fossero in realtà Online*, una condizione preferibile all'alternativa di non restituire endpoint nella risposta DNS. Gli endpoint disabilitati o arrestati non vengono monitorati, di conseguenza non sono considerati idonei per il traffico.
>
> Questa condizione è in genere causata da una configurazione non corretta del servizio, ad esempio:
>
> * Un elenco di controllo di accesso [ACL] che blocca i controlli di integrità di Gestione traffico.
> * Una configurazione non corretta del protocollo o della porta di monitoraggio nel profilo di Gestione traffico.
>
> La conseguenza di questo comportamento è che se i controlli di integrità di Gestione traffico non sono configurati in modo appropriato, dal routing del traffico potrebbe sembrare che Gestione traffico *funzioni* correttamente. In questo caso il failover degli endpoint non viene tuttavia eseguito, con ripercussioni sulla disponibilità complessiva dell'applicazione. È importante verificare che il profilo indichi lo stato Online e non Degraded. Lo stato Online indica che i controlli di integrità di Gestione traffico funzionano come previsto.

Per altre informazioni su come risolvere i problemi relativi ai controlli di integrità non riusciti, vedere [Risoluzione dei problemi relativi allo stato danneggiato di Gestione traffico](traffic-manager-troubleshooting-degraded.md).



## <a name="next-steps"></a>Passaggi successivi

Informazioni sul [funzionamento di Gestione traffico](traffic-manager-how-traffic-manager-works.md)

Ulteriori informazioni sui [metodi di routing del traffico](traffic-manager-routing-methods.md) supportati da Gestione traffico

Informazioni su come [creare un profilo di Gestione traffico](traffic-manager-manage-profiles.md)

[Risoluzione dei problemi relativi allo stato Degraded](traffic-manager-troubleshooting-degraded.md) di un endpoint di Gestione traffico
