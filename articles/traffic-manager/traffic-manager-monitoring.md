<properties 
   pageTitle="Monitoraggio e failover degli endpoint di Gestione traffico | Microsoft Azure"
   description="Questo articolo illustra in che modo Gestione traffico usa il monitoraggio e il failover automatico degli endpoint per consentire ai clienti di Azure di distribuire applicazioni a disponibilità elevata"
   services="traffic-manager"
   documentationCenter=""
   authors="jtuliani"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/04/2016"
   ms.author="jtuliani" />

# Monitoraggio e failover degli endpoint di Gestione traffico

Gestione traffico di Azure include il monitoraggio degli endpoint e il failover automatico degli endpoint. Consente di distribuire applicazioni a disponibilità elevata resilienti agli errori di endpoint, inclusi gli errori di area di Azure.

Ciò è possibile inviando richieste a ogni endpoint e verificando la risposta. Se un endpoint non invia una risposta valida viene contrassegnato come 'Danneggiato' e non è più incluso nelle risposte DNS che restituiscono invece un endpoint alternativo disponibile. Il traffico degli utenti finali viene quindi deviato dagli endpoint con errori per essere inviato agli endpoint disponibili.

I controlli di integrità degli endpoint proseguono anche per gli endpoint 'danneggiati', in modo che possano essere di nuovo inseriti automaticamente nella rotazione quando tornano allo stato di integrità.

## Configurazione del monitoraggio degli endpoint

Per configurare il monitoraggio degli endpoint è necessario specificare le seguenti impostazioni nel profilo di Gestione traffico:

- **Protocollo**: scegliere HTTP o HTTPS. È importante notare che il monitoraggio HTTPS non verifica la validità del certificato SSL, ne verifica solo la presenza.
- **Porta**: scegliere la porta usata per la richiesta. È possibile scegliere tra porte HTTP e HTTPS standard.
- **Percorso **: specificare il percorso relativo e il nome della pagina Web o del file cui il controllo di integrità del sistema di monitoraggio tenterà di accedere. Considerare che una barra "/" è una voce valida per il percorso relativo e implica che il file sia nella directory radice (predefinito).

Per verificare l'integrità di ogni endpoint, Gestione traffico invierà una richiesta GET all'endpoint usando il protocollo, la porta e il percorso relativo specificati.

Una pratica comune consiste nell'implementare una pagina personalizzata all'interno dell'applicazione, ad esempio '/health.aspx', e configurare questa pagina come percorso di monitoraggio degli endpoint di Gestione traffico. All'interno della pagina è possibile eseguire le necessarie verifiche specifiche per l'applicazione, ad esempio la verifica della disponibilità di un database back-end, prima di restituire HTTP 200 (se il servizio è integro) o un codice di stato diverso in caso contrario.

Le impostazioni di monitoraggio degli endpoint sono configurate a livello di profilo di Gestione traffico, non per ogni endpoint. Le stesse impostazioni vengono quindi usate per controllare l'integrità di tutti gli endpoint. Se è necessario usare impostazioni di monitoraggio diverse per i diversi endpoint, usare i [profili nidificati di Gestione traffico](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings).

## Stato di endpoint e profili

Gli endpoint e i profili di Gestione traffico possono essere abilitati e disabilitati dall'utente. Le modifiche di stato possono verificarsi anche in seguito ai controlli di integrità degli endpoint. I parametri seguenti descrivono il comportamento in modo dettagliato.

### Stato endpoint

Stato endpoint è un'impostazione gestita dall'utente che consente di abilitare o disabilitare facilmente un singolo endpoint. Questa operazione non influenza lo stato del servizio sottostante, che potrebbe essere ancora in esecuzione, ma gestisce la disponibilità di questo endpoint dalla prospettiva di Gestione traffico. Quando un endpoint è disabilitato, la sua integrità non verrà controllata e l'endpoint non verrà restituito in una risposta DNS.

### Stato profilo

Stato profilo è un'impostazione gestita dall'utente che consente di abilitare o disabilitare facilmente il profilo. Mentre Stato endpoint interessa un singolo endpoint, Stato profilo interessa l'intero profilo, inclusi tutti gli endpoint. Se il profilo è disabilitato, l'integrità degli endpoint non verrà controllata e nessun endpoint verrà restituito nella risposta DNS; le query DNS riceveranno risposte 'NXDOMAIN'.

### Endpoint monitor status (Stato monitoraggio endpoint)

Endpoint Monitor Status (Stato monitoraggio endpoint) è un'impostazione generata da Gestione traffico e non può essere definita dall'utente. Mostra lo stato corrente dell'endpoint e indica il monitoraggio dell'endpoint in corso e altre informazioni, ad esempio lo stato dell'endpoint. La tabella seguente indica i valori possibili di Endpoint Monitor Status (Stato monitoraggio endpoint). Per informazioni dettagliate sul calcolo di Endpoint Monitor Status (Stato monitoraggio endpoint) per gli endpoint nidificati, vedere [Profili nidificati di Gestione traffico](traffic-manager-nested-profiles.md).

|Stato profilo|Stato endpoint|Stato monitoraggio endpoint (API e portale)|Note|
|---|---|---|---|
|Disabled|Enabled|Inactive|Il profilo è stato disabilitato dall'utente. Anche se lo stato endpoint può ancora essere abilitato, lo stato profilo ha la precedenza. Gli endpoint nei profili disabilitati non vengono monitorati. Nessun endpoint verrà restituito nelle risposte DNS; verrà invece inviata una risposta 'NXDOMAIN'.|
|&lt;qualsiasi&gt;|Disabled|Disabled|L'endpoint è stato disabilitato dall'utente. Gli endpoint disabilitati non vengono monitorati. Non sono disponibili per l'inclusione nelle risposte DNS e quindi non ricevono traffico.|
|Enabled|Enabled|Online|Endpoint monitorato e integro. È disponibile per l'inclusione nelle risposte DNS e quindi può ricevere traffico.|
|Enabled|Enabled|Degraded|I controlli di integrità del monitoraggio dell'endpoint hanno esito negativo. L'endpoint non è disponibile per l'inclusione nelle risposte DNS e quindi non riceve traffico.|
|Enabled|Enabled|CheckingEndpoint|Endpoint monitorato, ma i risultati del primo test non sono ancora stati ricevuti. Si tratta di uno stato temporaneo subito dopo l'aggiunta di un nuovo endpoint al profilo o subito dopo l'abilitazione di un endpoint o profilo. Gli endpoint in questo stato sono disponibili per l'inclusione nelle risposte DNS e possono quindi ricevere traffico.|
|Enabled|Enabled|Arrestato|Il servizio cloud o l'app Web cui l'endpoint punta non è in esecuzione. Verificare le impostazioni del servizio cloud o dell'app Web. Gli endpoint arrestati non vengono monitorati. Non sono disponibili per l'inclusione nelle risposte DNS e quindi non ricevono traffico.|

### Stato monitoraggio profilo

Stato monitoraggio profilo è una combinazione dello stato di monitoraggio endpoint per tutti gli endpoint nel profilo e dello stato del profilo configurato. I possibili valori sono descritti nella tabella seguente:

|Stato profilo (come configurato)|Stato monitoraggio endpoint|Stato monitoraggio profilo (API e portale)|Note|
|---|---|---|---|
|Disabled|&lt;qualsiasi&gt; o un profilo senza endpoint definiti.|Disabled|Il profilo è stato disabilitato dall'utente.|
|Enabled|Lo stato di almeno un endpoint è "Degraded".|Degraded|Si tratta di un contrassegno per cui è richiesta l'azione del cliente. Esaminare i valori di stato dei singoli endpoint per determinare quali endpoint richiedono attenzione.|
|Enabled|Lo stato di almeno un endpoint è "Online". Nessun endpoint è "Degraded".|Online|Il servizio sta accettando il traffico e non sono richieste azioni da parte del cliente.|
|Enabled|Lo stato di almeno un endpoint è "CheckingEndpoint". Nessun endpoint è "Online" o "Degraded".|CheckingEndpoints|Stato di transizione. In genere si verifica quando un profilo è stato appena creato o abilitato e l'integrità dell'endpoint viene controllata per la prima volta.|
|Enabled|Lo stato di tutti gli endpoint definiti nel profilo è "Disabled" o "Stopped" oppure il profilo non dispone di endpoint definiti.|Inactive|Non ci sono endpoint attivi, ma il profilo è abilitato.|

## Failover e failback degli endpoint

Si consideri lo scenario in cui un endpoint di Gestione traffico precedentemente integro ha un errore, questo errore viene rilevato da Gestione traffico e l'endpoint viene escluso dalla rotazione. L'endpoint successivamente torna integro; anche questo evento viene rilevato da Gestione traffico e l'endpoint viene di nuovo incluso nella rotazione.

>[AZURE.NOTE] Gestione traffico considera che l'endpoint sarà online solo se il messaggio restituito è un 200 OK. Se si verifica uno degli eventi seguenti, il controllo sarà considerato non riuscito:

>- Viene ricevuta una risposta non 200, incluso un codice 2xx diverso o un reindirizzamento 301/302
>- Richiesta di autenticazione client
>- Timeout; la soglia di timeout è 10 secondi
>- Non è possibile connettersi

>Per informazioni più dettagliate sulla risoluzione dei problemi relativi ai controlli non riusciti, vedere [Risoluzione dei problemi relativi allo stato Danneggiato di Gestione traffico](traffic-manager-troubleshooting-degraded.md).

La sequenza temporale seguente descrive i passaggi in dettaglio.

![Sequenza di failover e failback degli endpoint di Gestione traffico](./media/traffic-manager-monitoring/timeline.png)

1. **GET**: il sistema di monitoraggio di Gestione traffico esegue un GET sul percorso e sul file specificati nelle impostazioni di monitoraggio. Questa operazione viene ripetuta per ogni endpoint.
2. **200 OK**: il sistema di monitoraggio prevede un messaggio HTTP 200 OK restituito entro 10 secondi. Alla ricezione della risposta presuppone che il servizio sia disponibile.
3. **30 secondi tra i controlli**: questo controllo di integrità dell'endpoint viene eseguito ogni 30 secondi.
4. **Servizio non disponibile**: il servizio non è disponibile. Gestione traffico non avrà informazioni fino al successivo controllo di integrità.
5. **Tentativi di accesso al file di monitoraggio (4 tentativi)**: il sistema di monitoraggio esegue un GET, ma non riceve risposte entro un periodo di timeout di 10 secondi o in alternativa riceve una risposta diversa da 200. Esegue poi altri 3 tentativi a intervalli di 30 secondi. Se uno dei tentativi ha esito positivo, il conteggio viene azzerato.
6. **Contrassegnato come danneggiato**: dopo il quarto tentativo non riuscito in una riga, il sistema di monitoraggio contrassegna l'endpoint non disponibile come danneggiato. 
7. **Il traffico viene deviato su altri endpoint**: vengono aggiornati i server dei nomi DNS di Gestione traffico e l'endpoint non verrà più restituito da Gestione traffico in risposta alle query DNS. Le nuove connessioni verranno quindi indirizzate ad altri endpoint disponibili. Le risposte DNS precedenti che includono questo endpoint possono essere tuttavia ancora memorizzate nella cache da server DNS e client DNS ricorsivi. Alcuni client potrebbero quindi tentare di connettersi a questo endpoint. Quando queste cache scadono, i client invieranno le nuove query DNS a endpoint diversi. La durata della cache è determinata dall'impostazione TTL definita nel profilo di Gestione traffico, ad esempio 30 secondi. 
8. **I controlli di integrità proseguono**: Gestione traffico continua a controllare l'integrità dell'endpoint mentre si trova nello stato danneggiato. In questo modo può rilevare quando l'endpoint torna integro.
9. **Il servizio ritorna online**: il servizio torna disponibile. L'endpoint rimane danneggiato in Gestione traffico fino a quando il sistema di monitoraggio non esegue il controllo di integrità successivo.
10. **Ripresa del traffico al servizio**: Gestione traffico invia un GET e riceve un messaggio 200 OK a indicare che il servizio è di nuovo integro. I server dei nomi di Gestione traffico vengono aggiornati di nuovo e iniziano a distribuire il nome DNS del servizio nelle risposte DNS. Il traffico tornerà all'endpoint ancora una volta non appena scadono le risposte DNS memorizzate nella cache che restituiscono altri endpoint e le connessioni esistenti verso altri endpoint vengono interrotte.

>[AZURE.NOTE] Dal momento che Gestione traffico lavora a livello di DNS, non è in grado di influenzare le connessioni esistenti verso qualsiasi endpoint. Quando si indirizza il traffico tra gli endpoint, modificando le impostazioni del profilo oppure durante il failover o il failback, Gestione traffico indirizza le nuove connessioni agli endpoint disponibili. Altri endpoint possono tuttavia continuare a ricevere il traffico tramite le connessioni esistenti fino a quando quelle sessioni non vengono terminate. Per consentire lo smaltimento del traffico dalle connessioni esistenti, le applicazioni devono limitare la durata della sessione usata con ogni endpoint.

## Endpoint danneggiati

Quando un endpoint è danneggiato non viene più restituito in risposta alle query DNS. Per un'eccezione a questa regola vedere la nota seguente. Viene invece scelto e restituito un endpoint alternativo. La scelta dell'endpoint alternativo dipende dal metodo di routing del traffico configurato:

- **Priorità**: gli endpoint formano un elenco con priorità. Viene sempre restituito il primo endpoint disponibile nell'elenco. Se diventa danneggiato viene restituito l'endpoint disponibile successivo.
- **Ponderato**: può essere restituito uno degli endpoint disponibili, scelto in modo casuale in base ai pesi assegnati e ai pesi degli altri endpoint disponibili. Se un endpoint diventa danneggiato, ne viene scelto un altro dal pool di endpoint disponibili rimanenti.
- **Prestazioni**: viene restituito l'endpoint 'più vicino' all'utente finale, escludendo gli endpoint disabilitati o arrestati. Se quell'endpoint non è disponibile, l'endpoint restituito viene scelto in modo casuale tra tutti gli altri endpoint disponibili. In questo modo è possibile evitare un errore a catena che potrebbe verificarsi in caso di overload del successivo endpoint più vicino. È possibile configurare piani di failover alternativi per il routing del traffico con il metodo Prestazioni usando [profili di gestione traffico nidificati](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region).

Per altre informazioni, vedere [Metodi di routing del traffico di Gestione traffico](traffic-manager-routing-methods.md).

>[AZURE.NOTE] Cosa accade se tutti gli endpoint di Gestione traffico, escludendo gli endpoint disabilitati o arrestati, non superano i controlli di integrità e vengono contrassegnati come 'danneggiati'?

>Questa situazione si verifica in genere a causa di un errore nella configurazione del servizio, ad esempio un ACL che blocca i controlli di integrità di Gestione traffico, oppure un errore nella configurazione del profilo di Gestione traffico, ad esempio un percorso di monitoraggio errato.

>Gestione traffico effettua in questo caso un tentativo e *risponde come se tutti gli endpoint danneggiati fossero effettivamente 'Online'*. Ciò è preferibile all'alternativa, che consisterebbe nel non restituire endpoint nella risposta DNS.

>Una conseguenza di questo comportamento è che quando i controlli di integrità di Gestione traffico non sono configurati correttamente, dal routing del traffico può sembrare che Gestione traffico funzioni correttamente. Il failover non viene tuttavia eseguito in caso di errore di un endpoint, con ripercussioni sulla disponibilità complessiva dell'applicazione. Per garantire che questo non accada è importante verificare che il profilo indichi uno stato 'Online' anziché 'Danneggiato'. Uno stato 'Online' indica che i controlli di integrità di Gestione traffico funzionano come previsto.

Per altri dettagli sulla risoluzione dei problemi relativi ai controlli non riusciti, vedere [Risoluzione dei problemi relativi allo stato Danneggiato di Gestione traffico](traffic-manager-troubleshooting-degraded.md)
 
## Domande frequenti

### Gestione traffico è in sé resistente agli errori di area di Azure?

Implementando controlli di integrità predefiniti e il failover automatico tra le aree, Gestione traffico di Azure è un componente chiave per la distribuzione di applicazioni a disponibilità elevata in Azure, incluse le applicazioni resilienti a un'interruzione di Azure a livello di area.

È quindi opportuno che Gestione traffico in sé offra un livello di disponibilità estremamente elevato, inclusa la resilienza in caso di errore a livello di area.

Anche se alcune parti di Gestione traffico vengono eseguite in Azure, queste parti sono distribuite tra le aree e sono state progettate per essere resilienti a un guasto completo di qualsiasi area di Azure. Questa resilienza si applica a tutti i componenti di Gestione traffico: server dei nomi DNS, API, livello di archiviazione e servizio di monitoraggio degli endpoint.

Anche nell'improbabile caso di un'interruzione totale di un'intera area di Azure, Gestione traffico continua quindi a funzionare come di consueto e i clienti con applicazioni distribuite in più aree di Azure possono fare affidamento su Gestione traffico per indirizzare il traffico a un'istanza disponibile della propria applicazione.

### In che modo la scelta della posizione del gruppo di risorse si ripercuote su Gestione traffico?

Gestione traffico è un singolo servizio globale. Non è a livello di area. La scelta della posizione del gruppo di risorse non è rilevante per i profili di Gestione traffico distribuiti in quel gruppo di risorse.

Azure Resource Manager richiede che tutti i gruppi di risorse specifichino una 'posizione' che determina il percorso predefinito delle risorse distribuite in quel gruppo di risorse. Per questa posizione verrà chiesto se si crea un nuovo gruppo di risorse durante la creazione di un profilo di Gestione traffico con il portale di Azure.

Tutti i profili di Gestione traffico usano "globale" come posizione. Trattandosi dell'unico valore accettato, non viene esposto durante l'uso del portale di Azure, di PowerShell o dell'interfaccia della riga di comando. Sostituisce il valore predefinito del gruppo di risorse.

### Come si determina lo stato di integrità corrente di ogni endpoint?

Lo stato di monitoraggio corrente di ogni endpoint e il profilo complessivo vengono visualizzati nel portale di gestione di Azure. Queste informazioni sono anche disponibili tramite [API REST](https://msdn.microsoft.com/library/azure/mt163667.aspx) di Gestione traffico, [cmdlet PowerShell](https://msdn.microsoft.com/library/mt125941.aspx) e [interfaccia della riga di comando multipiattaforma di Azure](../xplat-cli-install.md).

Non è attualmente possibile visualizzare informazioni cronologiche sullo stato di integrità precedente degli endpoint, né è possibile configurare avvisi per le variazioni dello stato di integrità degli endpoint.

### È possibile monitorare gli endpoint HTTPS?

Sì. Gestione traffico supporta il probing su HTTPS. È sufficiente definire 'HTTPS' come protocollo nella configurazione del monitoraggio. Si noti che:

- Il certificato sul lato server non è convalidato
- I certificati SNI sul lato server non sono supportati
- I certificati client non sono supportati

### Quali intestazione 'host' viene usata per i controlli di integrità dell'endpoint?

L'intestazione 'host' usata nei controlli di integrità HTTP/S è il nome DNS associato a ogni endpoint. È esposto come 'target' dell'endpoint nel portale di Azure, [nei cmdlet PowerShell](https://msdn.microsoft.com/library/mt125941.aspx), [nell'interfaccia della riga di comando multipiattaforma di Azure](../xplat-cli-install.md) e nell'[API REST](https://msdn.microsoft.com/library/azure/mt163667.aspx).

Questo valore fa parte della configurazione dell'endpoint. Il valore usato nell'intestazione Host non può essere specificato separatamente dalla proprietà 'target'.


## Passaggi successivi

Informazioni sul [funzionamento di Gestione traffico](traffic-manager-how-traffic-manager-works.md).

Altre informazioni sui [metodi di routing del traffico](traffic-manager-routing-methods.md) supportati da Gestione traffico.

Informazioni su come [creare un profilo di Gestione traffico](traffic-manager-manage-profiles.md).

[Risoluzione dei problemi relativi allo stato 'Danneggiato'](traffic-manager-troubleshooting-degraded.md) di un endpoint di Gestione traffico.
 

<!---HONumber=AcomDC_0608_2016-->