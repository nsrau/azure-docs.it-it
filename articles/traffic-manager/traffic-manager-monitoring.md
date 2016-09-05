<properties
   pageTitle="Monitoraggio e failover degli endpoint di Gestione traffico | Microsoft Azure"
   description="Questo articolo illustra in che modo Gestione traffico usa il monitoraggio e il failover automatico degli endpoint per consentire ai clienti di Azure di distribuire applicazioni a disponibilità elevata"
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/01/2016"
   ms.author="sewhee" />

# Monitoraggio e failover degli endpoint di Gestione traffico

Gestione traffico di Azure include il monitoraggio degli endpoint e il failover automatico degli endpoint. Questa funzionalità consente di distribuire applicazioni a disponibilità elevata resilienti agli errori di endpoint, inclusi gli errori di area di Azure.

Gestione traffico invia richieste a ogni endpoint e quindi ne verifica la risposta. Se un endpoint non fornisce una risposta valida, in Gestione traffico viene visualizzato con lo stato Degraded e non viene incluso nelle risposte DNS, che invece restituiscono un endpoint alternativo disponibile. In tal modo, il traffico degli utenti finali viene deviato dagli endpoint con errori e diretto agli endpoint disponibili.

I controlli di integrità degli endpoint danneggiati continuano finché non vengono reinseriti automaticamente nella rotazione una volta che ne viene ripristinata l'integrità.

## Configurare il monitoraggio degli endpoint

Per configurare il monitoraggio degli endpoint è necessario specificare le seguenti impostazioni nel profilo di Gestione traffico:

- **Protocollo**: scegliere HTTP o HTTPS. È importante notare che il monitoraggio HTTPS verifica solo la presenza del certificato SSL e non la validità.
- **Porta**: scegliere la porta usata per la richiesta. È possibile scegliere tra porte HTTP e HTTPS standard.
- **Percorso**: specificare il percorso relativo e il nome della pagina Web o del file cui il controllo di integrità del sistema di monitoraggio tenterà di accedere. Si noti che la barra (/) è una voce valida per il percorso relativo e implica che il file si trovi nella directory radice (impostazione predefinita).

Per verificare l'integrità di ogni endpoint, Gestione traffico invia una richiesta GET all'endpoint usando il protocollo, la porta e il percorso relativo specificati.

Una procedura comune consiste nell'implementare una pagina personalizzata all'interno dell'applicazione, ad esempio /health.aspx. Configurare questa pagina come percorso di monitoraggio dell'endpoint di Gestione traffico. All'interno della pagina è possibile eseguire le necessarie verifiche specifiche dell'applicazione, ad esempio la verifica della disponibilità di un database, prima di restituire il codice HTTP 200, se il servizio è integro, o un altro codice di stato, in caso contrario.

Le impostazioni di monitoraggio degli endpoint sono configurate a livello di profilo di Gestione traffico, non per ogni endpoint. Le stesse impostazioni vengono quindi usate per controllare l'integrità di tutti gli endpoint. Se è necessario usare impostazioni di monitoraggio diverse per i vari endpoint, usare i [profili nidificati di Gestione traffico](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings).

## Stato di endpoint e profili

Gli endpoint e i profili di Gestione traffico possono essere abilitati e disabilitati. Tuttavia, lo stato degli endpoint può cambiare anche a causa di impostazioni e processi automatici di Gestione traffico. I parametri seguenti descrivono questo comportamento.

### Stato endpoint

L'impostazione relativa allo stato dell'endpoint è controllata dall'utente e consente di abilitare o disabilitare un determinato endpoint. Questa operazione non influisce sullo stato del servizio sottostante (che potrebbe essere ancora in esecuzione). Al contrario, controlla la disponibilità di un determinato endpoint per Gestione traffico. Quando un endpoint è associato allo stato Disabled, Gestione traffico non ne verifica l'integrità e l'endpoint non viene incluso in una risposta DNS.

### Stato profilo

Lo stato del profilo è un'impostazione controllata dall'utente, che può scegliere di abilitare o disabilitare un profilo con facilità. Mentre lo stato dell'endpoint interessa un endpoint solo, lo stato del profilo interessa il profilo intero, che comprende tutti gli endpoint. L'integrità degli endpoint inclusi in un profilo con stato Disabled non viene controllata e nessun endpoint viene incluso in una risposta DNS. Viene invece restituita una risposta NXDOMAIN.

### Endpoint monitor status (Stato monitoraggio endpoint)

L'impostazione Endpoint monitor status (Stato monitoraggio endpoint) viene generata da Gestione traffico per mostrare lo stato corrente dell'endpoint. Questa impostazione non può essere modificata manualmente. Stato monitoraggio endpoint rispecchia costantemente il monitoraggio degli endpoint, oltre a fornire altre informazioni, ad esempio sullo stato degli endpoint. La tabella seguente indica i valori possibili dello stato di monitoraggio degli endpoint. Per informazioni dettagliate su come viene calcolato lo stato del monitoraggio degli endpoint nidificati, vedere [Profili nidificati di Gestione traffico](traffic-manager-nested-profiles.md).

|Stato profilo|Stato endpoint|Stato monitoraggio endpoint (API e portale)|Note|
|---|---|---|---|
|Disabled|Enabled|Inactive|Il profilo è stato disabilitato dall'utente. Anche se agli endpoint è associato lo stato Enabled, se lo stato del profilo è Disabled, quest'ultimo avrà la precedenza. Gli endpoint nei profili disabilitati non vengono monitorati e nessuno viene incluso nelle risposte DNS. Viene invece restituita una risposta NXDOMAIN.|
|&lt;qualsiasi&gt;|Disabled|Disabled|L'endpoint è stato disabilitato dall'utente. Gli endpoint con stato Disabled non vengono monitorati. Non possono essere inclusi nelle risposte DNS e pertanto non ricevono traffico.|
|Enabled|Enabled|Online|L'endpoint è monitorato e integro. Può essere incluso nelle risposte DNS e pertanto può ricevere traffico.|
|Enabled|Enabled|Degraded|I controlli di integrità del monitoraggio dell'endpoint hanno esito negativo. L'endpoint non può essere incluso nelle risposte DNS e pertanto non riceve traffico.|
|Enabled|Enabled|CheckingEndpoint|L'endpoint è monitorato, ma i risultati del primo test non sono ancora pervenuti. Si tratta di uno stato temporaneo che in genere si verifica subito dopo aver aggiunto un nuovo endpoint al profilo o abilitato un profilo o un endpoint. Un endpoint con questo stato può essere incluso nelle risposte DNS e pertanto può ricevere traffico.|
|Enabled|Enabled|Arrestato|Il servizio cloud o l'app Web cui punta l'endpoint non è in esecuzione. Verificare le impostazioni del servizio cloud o dell'app Web. Gli endpoint con stato Interrotto non vengono monitorati. Non possono essere inclusi nelle risposte DNS e pertanto non ricevono traffico.|

### Stato monitoraggio profilo

L'impostazione Stato monitoraggio profilo è una combinazione dei valori relativi allo stato di monitoraggio di tutti gli endpoint inclusi nel profilo e dello stato del profilo configurato. Nella tabella seguente sono descritti i valori possibili.

|Stato profilo (come configurato)|Endpoint monitor status (Stato monitoraggio endpoint)|Stato monitoraggio profilo (API e portale)|Note|
|---|---|---|---|
|Disabled|&lt;qualsiasi&gt; o un profilo senza endpoint definiti.|Disabled|Il profilo è stato disabilitato dall'utente.|
|Enabled|Almeno un endpoint è associato allo stato Degraded.|Degraded|Si tratta di un contrassegno per cui è richiesta l'azione del cliente. Esaminare i valori di stato dei singoli endpoint per determinare quali endpoint richiedono attenzione.|
|Enabled|Almeno un endpoint è associato allo stato Online. Nessun endpoint presenta lo stato Degraded.|Online|Il servizio sta accettando il traffico e non sono richieste azioni da parte del cliente.|
|Enabled|Almeno un endpoint è associato allo stato CheckingEndpoint. Nessun endpoint presenta lo stato Online o Degraded.|CheckingEndpoints|Stato di transizione. In genere si verifica quando un profilo è stato appena creato o abilitato e l'integrità dell'endpoint viene controllata per la prima volta.|
|Enabled|Tutti gli endpoint definiti nel profilo presentano lo stato Disabled o Stopped oppure nel profilo non sono definiti endpoint.|Inactive|Non ci sono endpoint attivi, ma il profilo presenta lo stato Enabled.|

## Failover e failback degli endpoint

Si consideri uno scenario in cui compare un errore in un endpoint precedentemente integro. Gestione traffico rileva l'errore e l'endpoint viene escluso dalla rotazione. Successivamente, l'endpoint viene ripristinato. Gestione traffico rileva il ripristino e l'endpoint viene rimesso in rotazione.

>[AZURE.NOTE] Gestione traffico considera l'endpoint come online solo se viene restituito il messaggio 200 OK. Se si presenta una delle situazioni seguenti, per Gestione traffico il controllo di integrità degli endpoint non sarà riuscito:

>- Viene ricevuta una risposta non 200, incluso un codice 2xx diverso o un reindirizzamento 301/302
>- Richiesta di autenticazione client
>- Timeout; la soglia di timeout è 10 secondi
>- Non è possibile connettersi

>Per ulteriori informazioni su come risolvere i problemi relativi ai controlli non riusciti, vedere [Risoluzione dei problemi relativi allo stato Danneggiato di Gestione traffico](traffic-manager-troubleshooting-degraded.md).

La sequenza temporale seguente descrive i passaggi in dettaglio.

![Sequenza di failover e failback degli endpoint di Gestione traffico](./media/traffic-manager-monitoring/timeline.png)

1. **GET**: il sistema di monitoraggio di Gestione traffico esegue una richiesta GET sul percorso e sul file specificati nelle impostazioni di monitoraggio. Questa operazione viene ripetuta per ogni endpoint.
2. **200 OK**: il sistema di monitoraggio prevede la restituzione di un messaggio HTTP 200 OK entro 10 secondi. Alla ricezione della risposta, il sistema riconosce la disponibilità del servizio.
3. **30 secondi tra i controlli**: il controllo di integrità dell'endpoint viene ripetuto ogni 30 secondi.
4. **Servizio non disponibile**: il servizio diventa non disponibile. Gestione traffico non avrà informazioni fino al successivo controllo di integrità.
5. **Tentativi di accesso al file di monitoraggio (quattro tentativi)**: il sistema di monitoraggio esegue una richiesta GET, ma non riceve risposte entro il periodo di timeout di 10 secondi oppure riceve una risposta diversa da 200. Il sistema esegue quindi altri tre tentativi a intervalli di 30 secondi. Se uno dei tentativi ha esito positivo, il conteggio viene azzerato.
6. **Stato impostato su Degraded**: al quarto errore consecutivo, il sistema di monitoraggio contrassegna lo stato dell'endpoint non disponibile come Degraded.
7. **Traffico deviato ad altri endpoint**: vengono aggiornati i server dei nomi DNS di Gestione traffico, che non restituisce più l'endpoint in risposta alle query DNS. Le nuove connessioni vengono indirizzate ad altri endpoint disponibili. Tuttavia, le risposte DNS precedenti che includono questo endpoint possono essere ancora memorizzate nella cache da server DNS e client DNS ricorsivi. Alcuni client potrebbero quindi tentare di connettersi a questo endpoint. Quando queste cache scadono, i client eseguono nuove query DNS, indirizzate a endpoint diversi. La durata della cache è determinata dall'impostazione TTL definita nel profilo di Gestione traffico, ad esempio 30 secondi.
8. **I controlli di integrità proseguono**. Gestione traffico continua a controllare l'integrità dell'endpoint finché si trova nello stato Degraded. In questo modo può rilevare quando l'endpoint torna integro.
9. **Il servizio ritorna online**: il servizio diventa disponibile. L'endpoint mantiene lo stato Degraded in Gestione traffico finché il sistema di monitoraggio non esegue il controllo di integrità successivo.
10. **Ripresa del traffico al servizio**: Gestione traffico invia una richiesta GET e riceve una risposta di stato 200 OK, la quale indica che il servizio ha restituito uno stato integro. I server dei nomi di Gestione traffico vengono aggiornati di nuovo e iniziano a distribuire il nome DNS del servizio nelle risposte DNS. Il traffico torna all'endpoint non appena scadono le risposte DNS memorizzate nella cache che restituiscono altri endpoint e le connessioni esistenti verso altri endpoint vengono interrotte.

>[AZURE.NOTE] Poiché lavora a livello di DNS, Gestione traffico non può influenzare le connessioni esistenti verso qualsiasi endpoint. Quando indirizza il traffico tra gli endpoint, modificando le impostazioni del profilo oppure durante il failover o il failback, Gestione traffico indirizza le nuove connessioni agli endpoint disponibili. Tuttavia, altri endpoint possono continuare a ricevere il traffico tramite le connessioni esistenti finché tali sessioni non vengono terminate. Per consentire lo smaltimento del traffico dalle connessioni esistenti, le applicazioni devono limitare la durata della sessione usata con ogni endpoint.

## Endpoint danneggiati

Quando un endpoint è associato allo stato Degraded, non viene più restituito in risposta alle query DNS. Per conoscere un'eccezione a questa regola, vedere la nota in fondo alla sezione. Viene invece scelto e restituito un endpoint alternativo. La scelta dell'endpoint alternativo dipende dal metodo di routing del traffico configurato:

- **Priorità**: gli endpoint formano un elenco con priorità. Viene sempre restituito il primo endpoint disponibile nell'elenco. Se lo stato di un endpoint è Degraded, viene restituito il successivo endpoint disponibile.
- **Ponderato**: può essere restituito uno degli endpoint disponibili, scelto in modo casuale in base ai pesi assegnati e ai pesi degli altri endpoint disponibili. Se lo stato di un endpoint è Degraded, ne viene scelto un altro dal pool di endpoint disponibili rimanenti.
- **Prestazioni**: viene restituito l'endpoint più vicino all'utente finale, a esclusione degli endpoint con stato Disabled o Stopped. Se quell'endpoint non è disponibile, l'endpoint restituito viene scelto in modo casuale tra tutti gli altri endpoint disponibili. In questo modo è possibile evitare un errore a catena che potrebbe verificarsi in caso di overload del successivo endpoint più vicino. È possibile configurare piani di failover alternativi per il routing del traffico con il metodo Prestazioni usando [profili di gestione traffico nidificati](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region).

Per altre informazioni, vedere [Metodi di routing del traffico di Gestione traffico](traffic-manager-routing-methods.md).

>[AZURE.NOTE] Cosa succede se, escludendo gli endpoint Disabled o Stopped, nessun endpoint di Gestione traffico supera i controlli di integrità e tutti vengono contrassegnati come Degraded?

>Questa situazione è in genere dovuta a un errore nella configurazione del servizio, ad esempio un elenco ACL che blocca i controlli di integrità di Gestione traffico, oppure a un errore nella configurazione del profilo di Gestione traffico, ad esempio un percorso di monitoraggio errato.

>In questo caso, Gestione traffico effettua un tentativo e *risponde come se tutti gli endpoint con stato Degraded fossero in realtà Online*, una condizione preferibile all'alternativa di non restituire endpoint nella risposta DNS.

>Una conseguenza di questo comportamento è che se i controlli di integrità di Gestione traffico non sono configurati correttamente, dal routing del traffico potrebbe sembrare che Gestione traffico *funzioni* a dovere. In questo caso, se si verifica un errore in un endpoint, il failover non viene tuttavia eseguito, con ripercussioni sulla disponibilità complessiva dell'applicazione. Per evitarlo, è importante verificare che il profilo indichi lo stato Online, anziché Degraded. Lo stato Online indica che i controlli di integrità di Gestione traffico funzionano come previsto.

Per ulteriori dettagli sulla risoluzione dei problemi relativi ai controlli non riusciti, vedere [Risoluzione dei problemi relativi allo stato Danneggiato di Gestione traffico](traffic-manager-troubleshooting-degraded.md).

## Domande frequenti

### Gestione traffico è in sé resistente agli errori di area di Azure?

Implementando controlli di integrità predefiniti e il failover automatico tra le aree, Gestione traffico è un componente chiave per la distribuzione di applicazioni a disponibilità elevata in Azure, incluse le applicazioni resilienti a un'interruzione di Azure a livello di area.

Per questo motivo, Gestione traffico in sé deve offrire un livello di disponibilità estremamente elevato, inclusa la resilienza in caso di errore a livello di area.

Anche se alcune parti di Gestione traffico vengono eseguite in Azure, questi componenti sono distribuiti tra le aree e sono progettati per essere resilienti a un guasto completo di qualsiasi area di Azure. Questa resilienza si applica a tutti i componenti di Gestione traffico: server dei nomi DNS, API, livello di archiviazione e servizio di monitoraggio degli endpoint.

Anche nell'improbabile caso di un'interruzione totale di un'intera area di Azure, Gestione traffico continua a funzionare come di consueto e i clienti con applicazioni distribuite in più aree di Azure possono fare affidamento su Gestione traffico per indirizzare il traffico a un'istanza disponibile della propria applicazione.

### In che modo la scelta della posizione del gruppo di risorse si ripercuote su Gestione traffico?

Gestione traffico è un singolo servizio globale. Non è a livello di area. La scelta della posizione del gruppo di risorse non è rilevante per i profili di Gestione traffico distribuiti in quel gruppo di risorse.

Azure Resource Manager richiede che tutti i gruppi di risorse specifichino una posizione che determina il percorso predefinito delle risorse distribuite nel gruppo di risorse in questione. Per questa posizione verrà chiesto se si crea un nuovo gruppo di risorse durante la creazione di un profilo di Gestione traffico con il portale di Azure.

Tutti i profili di Gestione traffico usano **globale** come posizione. Essendo l'unico valore accettato, questa impostazione non è accessibile nel portale di Azure, in PowerShell o nell'interfaccia della riga di comando di Azure. Sostituisce il valore predefinito del gruppo di risorse.

### Come si determina lo stato di integrità corrente di ogni endpoint?

Lo stato di monitoraggio corrente di ogni endpoint viene visualizzato nel portale di Azure, insieme al profilo complessivo. Queste informazioni sono anche disponibili tramite l'[API REST](https://msdn.microsoft.com/library/azure/mt163667.aspx) di Gestione traffico, i [cmdlet PowerShell](https://msdn.microsoft.com/library/mt125941.aspx) e l'[interfaccia della riga di comando multipiattaforma di Azure](../xplat-cli-install.md).

Non è possibile visualizzare informazioni cronologiche sullo stato di integrità degli endpoint precedent, né è possibile configurare avvisi per le variazioni dello stato di integrità degli endpoint.

### È possibile monitorare gli endpoint HTTPS?

Sì. Gestione traffico supporta il probing su HTTPS. È sufficiente definire **HTTPS** come protocollo nella configurazione del monitoraggio. Si noti che:

- Il certificato sul lato server non è convalidato
- I certificati SNI sul lato server non sono supportati
- I certificati client non sono supportati

### Quali intestazione host viene usata per i controlli di integrità degli endpoint?

L'intestazione host usata nei controlli di integrità HTTP e HTTPS è il nome DNS associato a ogni endpoint. È esposto come target dell'endpoint nel portale di Azure, nei [cmdlet PowerShell](https://msdn.microsoft.com/library/mt125941.aspx), [nell'interfaccia della riga di comando multipiattaforma di Azure](../xplat-cli-install.md) e nell'[API REST](https://msdn.microsoft.com/library/azure/mt163667.aspx).

Questo valore fa parte della configurazione dell'endpoint. Il valore usato nell'intestazione host non può essere specificato separatamente dalla proprietà target.


## Passaggi successivi

Informazioni sul [funzionamento di Gestione traffico](traffic-manager-how-traffic-manager-works.md)

Ulteriori informazioni sui [metodi di routing del traffico](traffic-manager-routing-methods.md) supportati da Gestione traffico

Informazioni su come [creare un profilo di Gestione traffico](traffic-manager-manage-profiles.md)

[Risoluzione dei problemi relativi allo stato Degraded](traffic-manager-troubleshooting-degraded.md) di un endpoint di Gestione traffico

<!---HONumber=AcomDC_0824_2016-->