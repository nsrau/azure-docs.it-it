---
title: Diagnosticare e risolvere i problemi quando si usa .NET SDK di Azure Cosmos DB
description: Usare funzionalità come la registrazione lato client e altri strumenti di terze parti per identificare, diagnosticare e risolvere i problemi di Azure Cosmos DB quando si usa .NET SDK.
author: j82w
ms.service: cosmos-db
ms.date: 03/11/2020
ms.author: jawilley
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 5f92d98630c6fb875babeb907f92732b0c24bb52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137955"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Diagnosticare e risolvere i problemi quando si usa .NET SDK di Azure Cosmos DB
Questo articolo illustra problemi comuni, soluzioni alternative, passaggi di diagnostica e strumenti quando si usa .NET SDK con account API SQL del database di Azure.This article covers common issues, workarounds, diagnostic steps, and tools when you use the [.NET SDK](sql-api-sdk-dotnet.md) with Azure Cosmos DB SQL API accounts.
The .NET SDK provides client-side logical representation to access the Azure Cosmos DB SQL API. Questo articolo descrive strumenti e approcci utili ad affrontare eventuali problemi.

## <a name="checklist-for-troubleshooting-issues"></a>Elenco di controllo per la risoluzione dei problemi:
Prima di spostare l'applicazione nell'ambiente di produzione, prendere in considerazione l'elenco di controllo seguente. L'utilizzo dell'elenco di controllo impedirà diversi problemi comuni che potrebbero verificarsi. È inoltre possibile diagnosticare rapidamente quando si verifica un problema:You can also quickly diagnose when an issue occurs:

*    Utilizzare [l'SDK](sql-api-sdk-dotnet-standard.md)più recente . Gli SDK di anteprima non devono essere utilizzati per la produzione. In questo modo si eviteranno di colpire i problemi noti che sono già stati risolti.
*    Esaminare i [suggerimenti sulle prestazioni](performance-tips.md) e seguire le procedure consigliate. In questo modo sarà possibile evitare problemi di scalabilità, latenza e altri problemi di prestazioni.
*    Abilitare la registrazione SDK per risolvere un problema. L'abilitazione della registrazione può influire sulle prestazioni, pertanto è consigliabile abilitarla solo durante la risoluzione dei problemi. È possibile abilitare i seguenti registri:
    *    [Registrare le metriche](monitor-accounts.md) tramite il portale di Azure.Log metrics by using the Azure portal. Le metriche del portale mostrano i dati di telemetria del database Cosmos di Azure, che è utile per determinare se il problema corrisponde a Azure Cosmos DB o se proviene dal lato client.
    *    Registrare la stringa di [diagnostica](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring) nell'SDK V2 o nella [diagnostica](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics) in V3 SDK dalle risposte dell'operazione punto.
    *    Registrare le [metriche](sql-api-query-metrics.md) di query SQL da tutte le risposte alle queryLog the SQL Query Metrics from all the query responses 
    *    Seguire l'installazione per la [registrazione SDK]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)

Diamo un'occhiata alla sezione [Problemi e soluzioni alternative comuni](#common-issues-workarounds) in questo articolo.

Controllare la sezione Problemi di [GitHub](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) che viene monitorata attivamente. Verificare se è già stato pubblicato un problema simile con una soluzione alternativa. Se non hai trovato una soluzione, invia un problema a GitHub. È possibile aprire un segno di spunta di supporto per problemi urgenti.


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Problemi comuni e soluzioni alternative

### <a name="general-suggestions"></a>Suggerimenti generici
* Eseguire l'app nella stessa area di Azure dell'account del database Cosmos di Azure, quando possibile. 
* È possibile che si verifichino problemi di connettività/disponibilità a causa della mancanza di risorse nel computer client. È consigliabile monitorare l'utilizzo della CPU nei nodi che eseguono il client di database Cosmos di Azure e aumentare/disattivare se vengono eseguiti con un carico elevato.

### <a name="check-the-portal-metrics"></a>Controllare le metriche del portale
Il controllo delle [metriche](monitor-accounts.md) del portale consente di determinare se si tratta di un problema sul lato client o se si è verificato un problema con il servizio. Ad esempio, se le metriche contengono una frequenza elevata di richieste limitate (codice di stato HTTP 429), ovvero la richiesta viene limitata, controllare la sezione Frequenza richieste [troppo grande.] 

### <a name="requests-timeouts"></a><a name="request-timeouts"></a>Timeout delle richieste
RequestTimeout in genere si verifica quando si utilizza Direct/TCP, ma può verificarsi in modalità Gateway.RequestTimeout usually happens when using Direct/TCP, but can happen in Gateway mode. Questi errori sono le cause note comuni e suggerimenti su come risolvere il problema.

* L'utilizzo della CPU è elevato, il che causerà timeout di latenza e/o richiesta. Il cliente può aumentare la scalare la macchina host per fornire più risorse oppure il carico può essere distribuito su più macchine.
* La disponibilità di socket/porta potrebbe essere insufficiente. Quando vengono eseguiti in Azure, i client che usano .NET SDK possono colpire l'esaurimento delle porte sNAT (PAT) di Azure.When running in Azure, clients using the .NET SDK can hit Azure SNAT (PAT) port exhaustion. Per ridurre la possibilità di risolvere questo problema, utilizzare la versione 2.x o 3.x più recente di .NET SDK. Questo è un esempio del motivo per cui si consiglia di eseguire sempre la versione SDK più recente.
* La creazione di più istanze Di DocumentClient potrebbe causare problemi di connessione e timeout. Seguire i [suggerimenti sulle prestazioni](performance-tips.md)e utilizzare una singola istanza DocumentClient in un intero processo.
* A volte gli utenti vedono latenza elevata o timeout delle richieste perché il provisioning delle raccolte viene eseguito in modo insufficiente, le richieste di limitazione back-end e il client riprova internamente. Controllare le [metriche](monitor-accounts.md)del portale .
* Azure Cosmos DB distribuisce la velocità effettiva complessiva di cui è stato eseguito il provisioning in modo uniforme tra le partizioni fisiche. Controllare le metriche del portale per verificare se il carico di lavoro rileva un [tasto di partizione](partition-data.md)attiva. In questo modo la velocità effettiva utilizzata aggregata (RU/s) verrà visualizzata nelle RU di cui è stato eseguito il provisioning, ma una singola partizione usata (RU/s) supererà la velocità effettiva di cui è stato eseguito il provisioning. 
* Inoltre, 2.0 SDK aggiunge la semantica di canale alle connessioni direct/TCP. Una connessione TCP viene utilizzata per più richieste contemporaneamente. Questo può portare a due problemi in casi specifici:
    * Un elevato grado di concorrenza può portare a conflitti sul canale.
    * Richieste o risposte di grandi dimensioni possono portare al blocco head-of-line sul canale ed esacerbare la contesa, anche con un livello relativamente basso di concorrenza.
    * Se il caso rientra in una di queste due categorie (o se si sospetta un utilizzo elevato della CPU), si tratta di possibili attenuazioni:
        * Provare ad aumentare/ridurre l'applicazione.
        * Inoltre, i log SDK possono essere acquisiti tramite [il listener](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md) di traccia per ottenere ulteriori dettagli.

### <a name="high-network-latency"></a><a name="high-network-latency"></a>Elevata latenza di rete
È possibile identificare un'elevata latenza di rete usando la stringa di [diagnostica](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) in V2 SDK o [la diagnostica](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?view=azure-dotnet#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics) in V3 SDK.

Se non sono presenti [timeout](#request-timeouts) e la diagnostica mostra singole richieste in `ResponseTime` `RequestStartTime`cui l'alta latenza è evidente sulla differenza tra e , in questo modo (>300 millisecondi in questo esempio):

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```

Questa latenza può avere più cause:This latency can have multiple causes:

* L'applicazione non è in esecuzione nella stessa area dell'account del database Cosmos di Azure.Your application is not running in the same region as your Azure Cosmos DB account.
* La configurazione [di PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) o [ApplicationRegion](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) non è corretta e sta tentando di connettersi a un'area diversa in cui l'applicazione è attualmente in esecuzione.
* Potrebbe esserci un collo di bottiglia nell'interfaccia di rete a causa di traffico elevato. Se l'applicazione è in esecuzione in Macchine virtuali di Azure, sono disponibili possibili soluzioni alternative:If the application is running on Azure Virtual Machines, there are possible workarounds:
    * Prendere in considerazione l'utilizzo di una [macchina virtuale con la rete accelerata abilitata](../virtual-network/create-vm-accelerated-networking-powershell.md).
    * Abilitare [la rete accelerata in una macchina virtuale esistente.](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms)
    * Prendere in considerazione l'utilizzo di una [macchina virtuale di fascia alta](../virtual-machines/windows/sizes.md).

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Esaurimento delle porte SNAT (PAT) di Azure

Se l'app viene distribuita in Macchine virtuali di [Azure senza un indirizzo IP pubblico,](../load-balancer/load-balancer-outbound-connections.md#defaultsnat)per impostazione predefinita le porte [SNAT](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) di Azure stabiliscono connessioni a qualsiasi endpoint esterno alla macchina virtuale. Il numero di connessioni consentite dalla macchina virtuale all'endpoint di Azure Cosmos DB è limitato dalla [configurazione SNAT di Azure](../load-balancer/load-balancer-outbound-connections.md#preallocatedports). Questa situazione può causare la limitazione delle connessioni, la chiusura della connessione o i timeout delle richieste sopra [indicati.](#request-timeouts)

 Le porte SNAT di Azure vengono usate solo quando la macchina virtuale dispone di un indirizzo IP privato si connette a un indirizzo IP pubblico. Esistono due soluzioni alternative per evitare la limitazione di Azure SNAT (a condizione che si stia già usando una singola istanza client nell'intera applicazione):There are two workarounds to avoid Azure SNAT limitation (provided you already using a single client instance across the entire application):

* Aggiungere l'endpoint del servizio Azure Cosmos DB alla subnet della rete virtuale di macchine virtuali di Azure. Per altre informazioni, vedere [Endpoint servizio di rete virtuale di Azure](../virtual-network/virtual-network-service-endpoints-overview.md). 

    Quando l'endpoint del servizio è abilitato, le richieste non vengono più inviate da un indirizzo IP pubblico ad Azure Cosmos DB. Vengono invece inviate le identità di rete virtuale e subnet. Questa modifica può comportare blocchi del firewall se sono consentiti solo indirizzi IP pubblici. Se si usa un firewall, quando si abilita l'endpoint del servizio, aggiungere una subnet al firewall tramite [ACL di rete virtuale](../virtual-network/virtual-networks-acl.md).
* Assegnare un indirizzo IP pubblico alla macchina virtuale di [Azure.Assign a public IP to your Azure VM](../load-balancer/load-balancer-outbound-connections.md#assignilpip).

### <a name="http-proxy"></a>Proxy HTTP
Se si usa un proxy HTTP, assicurarsi che possa supportare il numero di connessioni configurate in `ConnectionPolicy` dell'SDK.
In caso contrario, verranno riscontrati problemi di connessione.

### <a name="request-rate-too-large"></a><a name="request-rate-too-large"></a>La frequenza delle richieste è troppo elevata
'Frequenza richieste troppo grande' o il codice di errore 429 indica che le richieste vengono limitate, perché la velocità effettiva utilizzata (RU/s) ha superato la velocità effettiva di cui è [stato eseguito](set-throughput.md)il provisioning. L'SDK ritenterà automaticamente le richieste in base ai criteri di [ripetizione dei tentativi](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet)specificati. Se si verifica spesso questo errore, è consigliabile aumentare la velocità effettiva nella raccolta. Controlla le [metriche del portale](use-metrics.md) per vedere se stai ricevendo 429 errori. Esaminare la [chiave di partizione](partitioning-overview.md#choose-partitionkey) per assicurarsi che si traduca in una distribuzione uniforme dell'archiviazione e del volume delle richieste. 

### <a name="slow-query-performance"></a>Rallentamento delle prestazioni delle query
Le [metriche](sql-api-query-metrics.md) delle query consentono di determinare dove la query sta spendendo la maggior parte del tempo. Dalle metriche di query, è possibile vedere quanto viene speso sul back-end rispetto al client.
* Se la query back-end viene restituita rapidamente e dedica un tempo elevato al client per controllare il carico nel computer. È probabile che non vi sia risorse sufficienti e che l'SDK sia in attesa che le risorse siano disponibili per gestire la risposta.
* Se la query back-end è lenta provare a [ottimizzare la query](optimize-cost-queries.md) ed esaminare i [criteri di indicizzazione correnti](index-overview.md) 

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[La frequenza delle richieste è troppo elevata]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list


