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
ms.date: 03/16/2017
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: cec4f541ebac6202a3880ec7338a9f0a0ac645b5
ms.lasthandoff: 03/18/2017

---

# <a name="traffic-manager-endpoint-monitoring"></a>Monitoraggio degli endpoint di Gestione traffico

Gestione traffico di Azure include il monitoraggio degli endpoint e il failover automatico degli endpoint. Questa funzionalità consente di distribuire applicazioni a disponibilità elevata resilienti agli errori di endpoint, inclusi gli errori di area di Azure.

## <a name="configure-endpoint-monitoring"></a>Configurare il monitoraggio degli endpoint

Per configurare il monitoraggio degli endpoint è necessario specificare le seguenti impostazioni nel profilo di Gestione traffico:

* **Protocollo**. scegliere HTTP o HTTPS. È importante notare che il monitoraggio HTTPS verifica solo la presenza del certificato SSL e non la validità.
* **Porta**. scegliere la porta usata per la richiesta.
* **Percorso**. Specificare il percorso relativo e il nome della pagina Web o del file a cui il sistema di monitoraggio tenterà di accedere. Una barra (/) è una voce valida per il percorso relativo. Questo valore implica che il file sia nella directory radice (impostazione predefinita).

Per verificare l'integrità di ogni endpoint, Gestione traffico invia una richiesta GET all'endpoint usando il protocollo, la porta e il percorso relativo specificati.

Una procedura comune consiste nell'implementare una pagina personalizzata all'interno dell'applicazione, ad esempio /health.aspx. Usando questo percorso per il monitoraggio, è possibile eseguire controlli specifici dell'applicazione, ad esempio il controllo dei contatori delle prestazioni o la verifica della disponibilità del database. In base a questi controlli personalizzati, la pagina restituisce un codice di stato HTTP appropriato.

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
| Enabled |Enabled |Degraded |I controlli di integrità del monitoraggio dell'endpoint hanno esito negativo. L'endpoint non è incluso nelle risposte DNS e non riceve traffico. |
| Enabled |Enabled |CheckingEndpoint |L'endpoint è monitorato, ma i risultati del primo test non sono ancora pervenuti. CheckingEndpoint è un stato temporaneo che in genere si verifica immediatamente dopo l'aggiunta o l'abilitazione di un endpoint nel profilo. Un endpoint con questo stato viene incluso nelle risposte DNS e può ricevere traffico. |
| Enabled |Enabled |Arrestato |Il servizio cloud o l'app Web cui punta l'endpoint non è in esecuzione. Verificare le impostazioni del servizio cloud o dell'app Web. Gli endpoint con stato Interrotto non vengono monitorati. Non è incluso nelle risposte DNS e non riceve traffico. |

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

> [!NOTE]
> Gestione traffico considera l'endpoint come online solo se viene restituito il messaggio 200 OK. Un endpoint non è integro quando si verifica uno degli eventi seguenti:
>
> * Viene ricevuta una risposta non 200, incluso un codice 2xx diverso o un reindirizzamento 301/302
> * Richiesta di autenticazione client
> * Timeout; la soglia di timeout è 10 secondi
> * Non è possibile connettersi
>
> Per ulteriori informazioni su come risolvere i problemi relativi ai controlli non riusciti, vedere [Risoluzione dei problemi relativi allo stato Danneggiato di Gestione traffico](traffic-manager-troubleshooting-degraded.md).

La sequenza temporale seguente è una descrizione dettagliata del processo di monitoraggio.

![Sequenza di failover e failback degli endpoint di Gestione traffico](./media/traffic-manager-monitoring/timeline.png)

1. **GET**. Per ogni endpoint il sistema di monitoraggio di Gestione traffico esegue una richiesta GET sul percorso e sul file specificati nelle impostazioni di monitoraggio.
2. **200 OK**. il sistema di monitoraggio prevede la restituzione di un messaggio HTTP 200 OK entro 10 secondi. Alla ricezione della risposta, il sistema riconosce la disponibilità del servizio.
3. **30 secondi tra i controlli**. Il controllo di integrità dell'endpoint viene ripetuto ogni 30 secondi.
4. **Servizio non disponibile**. il servizio diventa non disponibile. Gestione traffico non avrà informazioni fino al successivo controllo di integrità.
5. **Tentativi di accesso al file di monitoraggio (quattro tentativi)**. il sistema di monitoraggio esegue una richiesta GET, ma non riceve risposte entro il periodo di timeout di 10 secondi oppure riceve una risposta diversa da 200. Il sistema esegue quindi altri tre tentativi a intervalli di 30 secondi. Se uno dei tentativi ha esito positivo, il conteggio viene azzerato.
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

Per altre informazioni, vedere [Metodi di routing del traffico di Gestione traffico](traffic-manager-routing-methods.md).

> [!NOTE]
> Un'eccezione al normale comportamento del routing del traffico si verifica quando lo stato di tutti gli endpoint idonei risulta Degraded. Gestione traffico effettua un tentativo e *risponde come se tutti gli endpoint con stato Degraded fossero in realtà Online*, una condizione preferibile all'alternativa di non restituire endpoint nella risposta DNS. Gli endpoint disabilitati o arrestati non vengono monitorati, di conseguenza non sono considerati idonei per il traffico.
>
> Questa condizione è in genere causata da una configurazione non corretta del servizio, ad esempio:
>
> * Un elenco di controllo di accesso [ACL] che blocca i controlli di integrità di Gestione traffico
> * Una configurazione non corretta del percorso di monitoraggio nel profilo di Gestione traffico
>
> La conseguenza di questo comportamento è che se i controlli di integrità di Gestione traffico non sono configurati in modo appropriato, dal routing del traffico potrebbe sembrare che Gestione traffico *funzioni* correttamente. In questo caso il failover degli endpoint non viene tuttavia eseguito, con ripercussioni sulla disponibilità complessiva dell'applicazione. È importante verificare che il profilo indichi lo stato Online e non Degraded. Lo stato Online indica che i controlli di integrità di Gestione traffico funzionano come previsto.

Per altre informazioni su come risolvere i problemi relativi ai controlli di integrità non riusciti, vedere [Risoluzione dei problemi relativi allo stato danneggiato di Gestione traffico](traffic-manager-troubleshooting-degraded.md).



## <a name="next-steps"></a>Passaggi successivi

Informazioni sul [funzionamento di Gestione traffico](traffic-manager-how-traffic-manager-works.md)

Ulteriori informazioni sui [metodi di routing del traffico](traffic-manager-routing-methods.md) supportati da Gestione traffico

Informazioni su come [creare un profilo di Gestione traffico](traffic-manager-manage-profiles.md)

[Risoluzione dei problemi relativi allo stato Degraded](traffic-manager-troubleshooting-degraded.md) di un endpoint di Gestione traffico

