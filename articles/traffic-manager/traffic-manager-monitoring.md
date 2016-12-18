---
title: Monitoraggio e failover degli endpoint di Gestione traffico | Documentazione Microsoft
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
ms.date: 10/11/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 69b94c93ad3e9c9745af8485766b4237cac0062c
ms.openlocfilehash: 4df9f744c7dde9224157eca1f869c0c420036d76

---

# <a name="traffic-manager-endpoint-monitoring-and-failover"></a>Monitoraggio e failover degli endpoint di Gestione traffico

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

## <a name="faq"></a>Domande frequenti

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>Gestione traffico è resiliente rispetto agli errori di area di Azure?

Gestione traffico è un componente fondamentale del recapito di applicazioni a disponibilità elevata in Azure.
Per assicurare una disponibilità elevata, Gestione traffico deve garantire un livello estremamente elevato di disponibilità, nonché la resilienza rispetto agli errori di area.

Per impostazione predefinita, i componenti di Gestione traffico resistono sono resilienti agli errori di qualsiasi area di Azure a livello globale. Questa resilienza si applica a tutti i componenti di Gestione traffico: server dei nomi DNS, API, livello di archiviazione e servizio di monitoraggio degli endpoint.

Nel caso improbabile in cui si verifichi l'interruzione di un'intera area di Azure, Gestione traffico deve continuare a funzionare normalmente. Per le applicazioni distribuite in più aree di Azure Gestione traffico consente di indirizzare il traffico alle istanze disponibili delle applicazioni.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>In che modo la scelta della posizione del gruppo di risorse si ripercuote su Gestione traffico?

Gestione traffico è un singolo servizio globale. Non è a livello di area. La scelta della posizione del gruppo di risorse non è rilevante per i profili di Gestione traffico distribuiti in quel gruppo di risorse.

Azure Resource Manager richiede che tutti i gruppi di risorse specifichino una posizione che determina il percorso predefinito delle risorse distribuite nel gruppo di risorse in questione. Quando si crea un profilo di Gestione traffico, viene creato in un gruppo di risorse. Tutti i profili di Gestione traffico usano **globale** come posizione, ignorando l'impostazione predefinita del gruppo di risorse.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Come si determina lo stato di integrità corrente di ogni endpoint?

Lo stato di monitoraggio corrente di ogni endpoint viene visualizzato nel portale di Azure, insieme al profilo complessivo. Queste informazioni sono anche disponibili con l'[API REST](https://msdn.microsoft.com/library/azure/mt163667.aspx) di Gestione traffico, i [cmdlet PowerShell](https://msdn.microsoft.com/library/mt125941.aspx) e l'[interfaccia della riga di comando multipiattaforma di Azure](../xplat-cli-install.md).

In Azure non vengono visualizzate informazioni cronologiche sull'integrità precedente degli endpoint e non è possibile generare avvisi sulle modifiche dell'integrità degli endpoint.

### <a name="can-i-monitor-https-endpoints"></a>È possibile monitorare gli endpoint HTTPS?

Sì. Gestione traffico supporta il probing su HTTPS. Definire **HTTPS** come protocollo nella configurazione del monitoraggio.

Gestione traffico non prevede alcuna convalida di certificati, tra cui:

* I certificati sul lato server non vengono convalidati
* I certificati SNI sul lato server non sono supportati
* I certificati client non sono supportati

### <a name="what-host-header-do-endpoint-health-checks-use"></a>Quali intestazione host viene usata per i controlli di integrità degli endpoint?

Gestione traffico usa le intestazioni host nei controlli di integrità HTTP e HTTPS. L'intestazione host usata da Gestione traffico è il nome dell'endpoint di destinazione configurato nel profilo. Il valore usato nell'intestazione host non può essere specificato separatamente dalla proprietà target.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Quali sono gli indirizzi IP da cui si originano i controlli di integrità?

L'elenco seguente contiene gli indirizzi IP da cui possono provenire i controlli di integrità di Gestione traffico. È possibile usare questo elenco per assicurarsi che le connessioni in ingresso da questi indirizzi IP siano consentite agli endpoint per controllarne lo stato di integrità.

* 40.68.30.66
* 40.68.31.178
* 137.135.80.149
* 137.135.82.249
* 23.96.236.252
* 65.52.217.19
* 40.87.147.10
* 40.87.151.34
* 13.75.124.254
* 13.75.127.63
* 52.172.155.168
* 52.172.158.37
* 104.215.91.84
* 13.75.153.124
* 13.84.222.37
* 23.101.191.199
* 23.96.213.12
* 137.135.46.163
* 137.135.47.215
* 191.232.208.52
* 191.232.214.62
* 13.75.152.253
* 104.41.187.209
* 104.41.190.203

## <a name="next-steps"></a>Passaggi successivi

Informazioni sul [funzionamento di Gestione traffico](traffic-manager-how-traffic-manager-works.md)

Ulteriori informazioni sui [metodi di routing del traffico](traffic-manager-routing-methods.md) supportati da Gestione traffico

Informazioni su come [creare un profilo di Gestione traffico](traffic-manager-manage-profiles.md)

[Risoluzione dei problemi relativi allo stato Degraded](traffic-manager-troubleshooting-degraded.md) di un endpoint di Gestione traffico



<!--HONumber=Nov16_HO3-->


