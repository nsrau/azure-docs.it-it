---
title: Diagnosticare e risolvere i problemi relativi all'uso di Azure Cosmos DB .NET SDK
description: Usare funzionalità come la registrazione lato client e altri strumenti di terze parti per identificare, diagnosticare e risolvere i problemi Azure Cosmos DB quando si usa .NET SDK.
author: j82w
ms.service: cosmos-db
ms.date: 05/28/2019
ms.author: jawilley
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 51b37c43b94ad59090f32af0d57bbefaa57f30fa
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70932564"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Diagnosticare e risolvere i problemi relativi all'uso di Azure Cosmos DB .NET SDK
Questo articolo descrive i problemi comuni, le soluzioni alternative, i passaggi di diagnostica e gli strumenti quando si usa [.NET SDK](sql-api-sdk-dotnet.md) con Azure Cosmos DB account API SQL.
.NET SDK fornisce una rappresentazione logica sul lato client per accedere all'API SQL Azure Cosmos DB. Questo articolo descrive strumenti e approcci utili ad affrontare eventuali problemi.

## <a name="checklist-for-troubleshooting-issues"></a>Elenco di controllo per la risoluzione dei problemi:
Prendere in considerazione il seguente elenco di controllo prima di spostare l'applicazione in produzione. Utilizzando l'elenco di controllo, si eviteranno diversi problemi comuni che potrebbero verificarsi. È anche possibile diagnosticare rapidamente quando si verifica un problema:

*   Usare l' [SDK](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md)più recente. Gli SDK di anteprima non devono essere usati per la produzione. In questo modo si eviteranno problemi noti già corretti.
*   Esaminare i [suggerimenti sulle prestazioni](performance-tips.md) e seguire le procedure consigliate. Ciò consentirà di evitare la scalabilità, la latenza e altri problemi di prestazioni.
*   Abilitare la registrazione dell'SDK per facilitare la risoluzione di un problema. L'abilitazione della registrazione può influire sulle prestazioni, pertanto è consigliabile abilitarla solo quando si risolvono i problemi. È possibile abilitare i log seguenti:
    *   [Registrare le metriche](monitor-accounts.md) usando il portale di Azure. Le metriche del portale mostrano i dati di telemetria Azure Cosmos DB, che risulta utile per determinare se il problema corrisponde a Azure Cosmos DB o se è dal lato client.
    *   Registrare la [stringa di diagnostica](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) dalle risposte dell'operazione punto.
    *   Registra le [metriche della query SQL](sql-api-query-metrics.md) da tutte le risposte alle query 
    *   Seguire la configurazione per la [registrazione dell'SDK]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)

Diamo un'occhiata alla sezione [Problemi e soluzioni alternative comuni](#common-issues-workarounds) in questo articolo.

Vedere la [sezione relativa ai problemi di GitHub](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) monitorati attivamente. Verificare se è già stato pubblicato un problema simile con una soluzione alternativa. Se non è stata trovata alcuna soluzione, archiviare un problema di GitHub. È possibile aprire un ciclo di supporto per i problemi urgenti.


## <a name="common-issues-workarounds"></a>Problemi e soluzioni alternative comuni

### <a name="general-suggestions"></a>Suggerimenti generici
* Eseguire l'app nella stessa area di Azure dell'account di Azure Cosmos DB, laddove possibile. 
* È possibile che si verifichino problemi di connettività/disponibilità a causa di risorse insufficienti nel computer client. È consigliabile monitorare l'utilizzo della CPU nei nodi che eseguono il client di Azure Cosmos DB e aumentare o ridurre le prestazioni se sono in esecuzione con un carico elevato.

### <a name="check-the-portal-metrics"></a>Controllare le metriche del portale
Il controllo delle [metriche del portale](monitor-accounts.md) consente di determinare se si tratta di un problema lato client o se si verifica un problema con il servizio. Se, ad esempio, le metriche contengono una frequenza elevata di richieste con limitazioni di frequenza (codice di stato HTTP 429) che indica che la richiesta è soggetta a limitazioni, controllare la sezione frequenza delle richieste [Frequenza delle richieste troppo grande] . 

### <a name="request-timeouts"></a>Timeout richieste
RequestTimeout in genere si verifica quando si usa Direct/TCP, ma può verificarsi in modalità gateway. Queste sono le cause più comuni e suggerimenti su come risolvere il problema.

* L'utilizzo della CPU è elevato, che provocherà timeout di latenza e/o richiesta. Il cliente può scalare verticalmente il computer host in modo da fornire più risorse oppure il carico può essere distribuito tra più computer.
* La disponibilità del socket/porta potrebbe essere bassa. Quando è in esecuzione in Azure, i client che usano .NET SDK possono raggiungere l'esaurimento delle porte di Azure SNAT (PAT). Per ridurre la possibilità di raggiungere questo problema, usare la versione 2. x o 3. x più recente di .NET SDK. Questo è un esempio del motivo per cui è consigliabile eseguire sempre la versione più recente dell'SDK.
* La creazione di più istanze di DocumentClient può comportare problemi di conflitti e conflitti di connessione. Seguire i [suggerimenti](performance-tips.md)per le prestazioni e usare una singola istanza di DocumentClient in un intero processo.
* Gli utenti talvolta visualizzano i timeout di richieste o latenze elevate perché il provisioning delle raccolte viene effettuato in modo insufficiente, il back-end limita le richieste e il client ritenta internamente senza che questo venga sottoposto al chiamante. Controllare le [metriche del portale](monitor-accounts.md).
* Azure Cosmos DB distribuisce in modo uniforme la velocità effettiva di provisioning complessiva tra le partizioni fisiche. Controllare le metriche del portale per verificare se il carico di lavoro sta riscontrando una [chiave di partizione](partition-data.md)attiva. In questo modo, la velocità effettiva aggregata utilizzata (UR/sec) verrà visualizzata sotto le UR sottoposte a provisioning, ma una singola partizione utilizzata (UR/sec) supererà la velocità effettiva con provisioning. 
* Inoltre, 2,0 SDK aggiunge la semantica del canale alle connessioni dirette/TCP. Una connessione TCP viene utilizzata per più richieste allo stesso tempo. Questo può causare due problemi in casi specifici:
    * Un elevato livello di concorrenza può causare conflitti sul canale.
    * Le richieste o le risposte di grandi dimensioni possono compromettere il blocco del canale ed esacerbare i conflitti, anche con un livello di concorrenza relativamente basso.
    * Se il case rientra in una di queste due categorie (o se si sospetta un utilizzo elevato della CPU), si tratta di possibili mitigazioni:
        * Provare a ridimensionare l'applicazione.
        * Inoltre, i log SDK possono essere acquisiti tramite il [listener di traccia](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md) per ottenere maggiori dettagli.

### <a name="connection-throttling"></a>Limitazione della connessione
La limitazione della connessione può verificarsi a causa di un limite di connessione in un computer host. Nelle precedenti 2,0, i client in esecuzione in Azure potevano raggiungere l' [Esaurimento delle porte SNAT (PAT) di Azure].

### <a name="snat"></a>Esaurimento delle porte SNAT (PAT) di Azure

Se l'app viene distribuita in macchine virtuali di Azure senza un indirizzo IP pubblico, per impostazione predefinita le [porte SNAT di Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) stabiliscono le connessioni a qualsiasi endpoint all'esterno della macchina virtuale. Il numero di connessioni consentite dalla macchina virtuale all'endpoint di Azure Cosmos DB è limitato dalla [configurazione SNAT di Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).

 Le porte SNAT di Azure vengono usate solo quando la macchina virtuale ha un indirizzo IP privato e un processo dalla macchina virtuale prova a connettersi a un indirizzo IP pubblico. Esistono due soluzioni alternative per evitare la limitazione per le porte SNAT di Azure:

* Aggiungere l'endpoint del servizio Azure Cosmos DB alla subnet della rete virtuale di macchine virtuali di Azure. Per altre informazioni, vedere [Endpoint servizio di rete virtuale di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). 

    Quando l'endpoint del servizio è abilitato, le richieste non vengono più inviate da un indirizzo IP pubblico ad Azure Cosmos DB. Vengono invece inviate le identità di rete virtuale e subnet. Questa modifica può comportare blocchi del firewall se sono consentiti solo indirizzi IP pubblici. Se si usa un firewall, quando si abilita l'endpoint del servizio, aggiungere una subnet al firewall tramite [ACL di rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl).
* Assegnare un indirizzo IP pubblico alla macchina virtuale di Azure.

### <a name="http-proxy"></a>Proxy HTTP
Se si usa un proxy HTTP, assicurarsi che possa supportare il numero di connessioni configurate in `ConnectionPolicy` dell'SDK.
In caso contrario, verranno riscontrati problemi di connessione.

### Frequenza delle richieste troppo grande<a name="request-rate-too-large"></a>
' Frequenza richieste troppo grande ' o codice di errore 429 indica che le richieste sono in fase di limitazione, perché la velocità effettiva utilizzata (UR/sec) ha superato la velocità effettiva con provisioning. L'SDK tenterà automaticamente di ritentare le richieste in base ai [criteri di ripetizione](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet)specificati. Se questo errore si verifica spesso, è consigliabile aumentare la velocità effettiva della raccolta. Controllare le [metriche del portale](use-metrics.md) per vedere se si ricevono errori 429. Esaminare la [chiave di partizione](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey) per assicurarsi che venga generata una distribuzione uniforme del volume di archiviazione e di richiesta. 

### <a name="slow-query-performance"></a>Rallentamento delle prestazioni delle query
La [metrica della query](sql-api-query-metrics.md) consente di determinare la posizione in cui la query sta spendendo la maggior parte del tempo. Dalla metrica della query è possibile vedere la quantità di spazio dedicato al back-end rispetto al client.
* Se la query back-end viene restituita rapidamente e trascorre molto tempo sul client, controllare il carico sul computer. È probabile che la risorsa non sia sufficiente e che l'SDK sia in attesa della disponibilità di risorse per la gestione della risposta.
* Se la query back-end è lenta, provare a [ottimizzare la query](optimize-cost-queries.md) e a esaminare i [criteri di indicizzazione](index-overview.md) correnti 

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Frequenza delle richieste troppo grande]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Esaurimento delle porte SNAT (PAT) di Azure]: #snat
[Production check list]: #production-check-list


