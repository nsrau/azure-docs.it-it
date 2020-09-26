---
title: Diagnosticare e risolvere i problemi quando si usa .NET SDK di Azure Cosmos DB
description: Usare funzionalità come la registrazione lato client e altri strumenti di terze parti per identificare, diagnosticare e risolvere i problemi Azure Cosmos DB quando si usa .NET SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 09/12/2020
ms.author: anfeldma
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 3093996156a31d4a06f0d91dbca1bd00d58eacdb
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91330452"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Diagnosticare e risolvere i problemi quando si usa .NET SDK di Azure Cosmos DB

> [!div class="op_single_selector"]
> * [Java SDK v4](troubleshoot-java-sdk-v4-sql.md)
> * [Async Java SDK v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

Questo articolo descrive i problemi comuni, le soluzioni alternative, i passaggi di diagnostica e gli strumenti quando si usa [.NET SDK](sql-api-sdk-dotnet.md) con Azure Cosmos DB account API SQL.
.NET SDK fornisce una rappresentazione logica sul lato client per accedere all'API SQL Azure Cosmos DB. Questo articolo descrive strumenti e approcci utili ad affrontare eventuali problemi.

## <a name="checklist-for-troubleshooting-issues"></a>Elenco di controllo per la risoluzione dei problemi

Prendere in considerazione il seguente elenco di controllo prima di spostare l'applicazione in produzione. Utilizzando l'elenco di controllo, si eviteranno diversi problemi comuni che potrebbero verificarsi. È anche possibile diagnosticare rapidamente quando si verifica un problema:

*    Usare l' [SDK](sql-api-sdk-dotnet-standard.md)più recente. Gli SDK di anteprima non devono essere usati per la produzione. In questo modo si eviteranno problemi noti già corretti.
*    Esaminare i [suggerimenti sulle prestazioni](performance-tips.md) e seguire le procedure consigliate. Ciò consentirà di evitare la scalabilità, la latenza e altri problemi di prestazioni.
*    Abilitare la registrazione dell'SDK per facilitare la risoluzione di un problema. L'abilitazione della registrazione può influire sulle prestazioni, pertanto è consigliabile abilitarla solo quando si risolvono i problemi. È possibile abilitare i log seguenti:
*    [Registrare le metriche](monitor-accounts.md) usando il portale di Azure. Le metriche del portale mostrano i dati di telemetria Azure Cosmos DB, che risulta utile per determinare se il problema corrisponde a Azure Cosmos DB o se è dal lato client.
*    Registrare la [stringa di diagnostica](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring) nell'SDK v2 o nella [diagnostica](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics) in V3 SDK dalle risposte dell'operazione punto.
*    Registra le [metriche della query SQL](sql-api-query-metrics.md) da tutte le risposte alle query 
*    Seguire la configurazione per la [registrazione dell'SDK]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)

Diamo un'occhiata alla sezione [Problemi comuni e soluzioni alternative](#common-issues-workarounds) in questo articolo.

Vedere la [sezione relativa ai problemi di GitHub](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) monitorati attivamente. Verificare se è già stato pubblicato un problema simile con una soluzione alternativa. Se non è stata trovata alcuna soluzione, archiviare un problema di GitHub. È possibile aprire un ciclo di supporto per i problemi urgenti.


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Problemi e soluzioni alternative comuni

### <a name="general-suggestions"></a>Suggerimenti generici
* Eseguire l'app nella stessa area di Azure dell'account di Azure Cosmos DB, laddove possibile. 
* È possibile che si verifichino problemi di connettività/disponibilità a causa di risorse insufficienti nel computer client. È consigliabile monitorare l'utilizzo della CPU nei nodi che eseguono il client di Azure Cosmos DB e aumentare o ridurre le prestazioni se sono in esecuzione con un carico elevato.

### <a name="check-the-portal-metrics"></a>Controllare le metriche del portale
Il controllo delle [metriche del portale](monitor-accounts.md) consente di determinare se si tratta di un problema sul lato client o se si verifica un problema con il servizio. Se, ad esempio, le metriche contengono una frequenza elevata di richieste con limitazioni di frequenza (codice di stato HTTP 429) che indica che la richiesta è soggetta a limitazioni, controllare la sezione frequenza delle richieste [troppo grande](troubleshoot-request-rate-too-large.md) . 

## <a name="common-error-status-codes"></a>Codici di stato di errore comuni <a id="error-codes"></a>

| Codice di stato | Descrizione | 
|----------|-------------|
| 400 | Richiesta non valida (dipende dal messaggio di errore)| 
| 401 | [Non autorizzato](troubleshoot-unauthorized.md) | 
| 404 | [La risorsa non è stata trovata](troubleshoot-not-found.md) |
| 408 | [Timeout della richiesta](troubleshoot-dot-net-sdk-request-timeout.md) |
| 409 | Un errore di conflitto si verifica quando l'ID fornito per una risorsa in un'operazione di scrittura è stato accettato da una risorsa esistente. Usare un altro ID per la risorsa per risolvere questo problema perché l'ID deve essere univoco all'interno di tutti i documenti con lo stesso valore della chiave di partizione. |
| 410 | Eccezioni finite (errore temporaneo che non deve violare il contratto di contratto) |
| 412 | L'errore di precondizione è il punto in cui l'operazione ha specificato un valore eTag diverso dalla versione disponibile nel server. Si tratta di un errore di concorrenza ottimistica. Riprovare a eseguire la richiesta dopo aver letto la versione più recente della risorsa e aver aggiornato l'ETag sulla richiesta.
| 413 | [Entità della richiesta troppo grande](concepts-limits.md#per-item-limits) |
| 429 | [Numero eccessivo di richieste](troubleshoot-request-rate-too-large.md) |
| 449 | Errore temporaneo che si verifica solo durante le operazioni di scrittura ed è sicuro da riprovare |
| 500 | L'operazione non è riuscita a causa di un errore imprevisto del servizio. Contattare il supporto tecnico. Vedere presentazione di un [problema di supporto tecnico di Azure](https://aka.ms/azure-support). |
| 503 | [Servizio non disponibile](troubleshoot-service-unavailable.md) | 

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Esaurimento delle porte SNAT (PAT) di Azure

Se l'app viene distribuita in [macchine virtuali di Azure senza un indirizzo IP pubblico](../load-balancer/load-balancer-outbound-connections.md), per impostazione predefinita le [porte SNAT di Azure](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) stabiliscono connessioni a qualsiasi endpoint esterno alla macchina virtuale. Il numero di connessioni consentite dalla macchina virtuale all'endpoint di Azure Cosmos DB è limitato dalla [configurazione SNAT di Azure](../load-balancer/load-balancer-outbound-connections.md#preallocatedports). Questa situazione può causare la limitazione della connessione, la chiusura della connessione o il [timeout della richiesta](troubleshoot-dot-net-sdk-request-timeout.md)menzionato in precedenza.

 Le porte di Azure SNAT vengono usate solo quando la macchina virtuale ha un indirizzo IP privato che si connette a un indirizzo IP pubblico. Esistono due soluzioni alternative per evitare la limitazione di Azure SNAT (purché si stia già usando una singola istanza client nell'intera applicazione):

* Aggiungere l'endpoint del servizio Azure Cosmos DB alla subnet della rete virtuale di macchine virtuali di Azure. Per altre informazioni, vedere [Endpoint servizio di rete virtuale di Azure](../virtual-network/virtual-network-service-endpoints-overview.md). 

    Quando l'endpoint del servizio è abilitato, le richieste non vengono più inviate da un indirizzo IP pubblico ad Azure Cosmos DB. Vengono invece inviate le identità di rete virtuale e subnet. Questa modifica può comportare blocchi del firewall se sono consentiti solo indirizzi IP pubblici. Se si usa un firewall, quando si abilita l'endpoint del servizio, aggiungere una subnet al firewall tramite [ACL di rete virtuale](../virtual-network/virtual-networks-acl.md).
* Assegnare un [indirizzo IP pubblico alla macchina virtuale di Azure](../load-balancer/troubleshoot-outbound-connection.md#assignilpip).

### <a name="high-network-latency"></a><a name="high-network-latency"></a>Latenza di rete elevata
Una latenza di rete elevata può essere identificata usando la [stringa di diagnostica](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) nell'SDK v2 o [diagnostica](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?view=azure-dotnet#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics) in V3 SDK.

Se non sono presenti [timeout](troubleshoot-dot-net-sdk-request-timeout.md) e la diagnostica Mostra singole richieste in cui la latenza elevata è evidente sulla differenza tra `ResponseTime` e `RequestStartTime` , ad esempio (>300 millisecondi in questo esempio):

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```

Questa latenza può avere più cause:

* L'applicazione non è in esecuzione nella stessa area dell'account Azure Cosmos DB.
* La configurazione di [PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) o [ApplicationRegion](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) non è corretta e sta provando a connettersi a un'area diversa in cui è attualmente in esecuzione l'applicazione.
* Potrebbe essere presente un collo di bottiglia sull'interfaccia di rete a causa del traffico elevato. Se l'applicazione è in esecuzione in macchine virtuali di Azure, è possibile che si verifichino soluzioni alternative:
    * Provare a usare una [macchina virtuale con rete accelerata abilitata](../virtual-network/create-vm-accelerated-networking-powershell.md).
    * Abilitare [la rete accelerata in una macchina virtuale esistente](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms).
    * Provare a usare una [macchina virtuale di fascia superiore](../virtual-machines/windows/sizes.md).

### <a name="common-query-issues"></a>Problemi comuni di query

La [metrica della query](sql-api-query-metrics.md) consente di determinare la posizione in cui la query sta spendendo la maggior parte del tempo. Dalla metrica della query è possibile vedere la quantità di spazio dedicato al back-end rispetto al client. Altre informazioni sulla [risoluzione dei problemi relativi alle prestazioni delle query](troubleshoot-query-performance.md).

* Se la query back-end viene restituita rapidamente e trascorre molto tempo sul client, controllare il carico sul computer. È probabile che la risorsa non sia sufficiente e che l'SDK sia in attesa della disponibilità di risorse per la gestione della risposta.
* Se la query back-end è lenta, provare ad [ottimizzare la query](troubleshoot-query-performance.md) e a esaminare i [criteri di indicizzazione](index-overview.md) correnti

    > [!NOTE]
    > Per migliorare le prestazioni, è consigliabile l'elaborazione host di Windows a 64 bit. L'SDK SQL include un file ServiceInterop.dll nativo che consente di analizzare e ottimizzare le query in locale. Il file ServiceInterop.dll è supportato solo nella piattaforma Windows x64. Per Linux e altre piattaforme non supportate in cui ServiceInterop.dll non è disponibile, viene effettuata una chiamata di rete aggiuntiva al gateway per ottenere la query ottimizzata.

Se si verifica l'errore seguente: `Unable to load DLL 'Microsoft.Azure.Cosmos.ServiceInterop.dll' or one of its dependencies:` e utilizzano Windows, è necessario eseguire l'aggiornamento alla versione più recente di Windows.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulle linee guida sulle prestazioni per [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET v2](performance-tips.md)
* Informazioni sugli [SDK Java basati su Reactor](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md)

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list
