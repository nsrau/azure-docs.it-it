---
title: Diagnosticare e risolvere i problemi quando si usa Azure Cosmos DB .NET SDK
description: Usare le funzionalità come la registrazione lato client e altri strumenti di terze parti per identificare, diagnosticare e risolvere i problemi di Azure Cosmos DB quando si usa .NET SDK.
author: j82w
ms.service: cosmos-db
ms.date: 01/19/2019
ms.author: jawilley
ms.devlang: c#
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 7f969ab6059140ec32c9c5bf5045c546602a3c15
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60404711"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Diagnosticare e risolvere i problemi quando si usa Azure Cosmos DB .NET SDK
Questo articolo descrive i problemi comuni, le soluzioni alternative, i passaggi di diagnostica e strumenti quando si usa la [.NET SDK](sql-api-sdk-dotnet.md) con gli account API SQL di Azure Cosmos DB.
.NET SDK fornisce una rappresentazione logica sul lato client per accedere alle API SQL di Azure Cosmos DB. Questo articolo descrive strumenti e approcci utili ad affrontare eventuali problemi.

## <a name="checklist-for-troubleshooting-issues"></a>Elenco di controllo per la risoluzione dei problemi:
Si consideri il seguente elenco di controllo prima di spostare l'applicazione nell'ambiente di produzione. Usando l'elenco di controllo, si impedirà di alcuni problemi comuni che potrebbero essere visualizzati. È possibile diagnosticare rapidamente quando si verifica un problema:

*   Usare la versione più recente [SDK](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md). Non usare gli SDK di anteprima per la produzione. Sarà in grado di raggiungere i noti problemi già risolti.
*   Esaminare i [suggerimenti sulle prestazioni](performance-tips.md) e seguire le procedure consigliate. Ciò impedirà altri problemi di prestazioni, latenza e scalabilità.
*   Abilitare la registrazione di SDK che consentono di risolvere un problema. Abilitare la registrazione può influire sulle prestazioni è consigliabile abilitarlo solo durante la risoluzione dei problemi. È possibile abilitare i log seguenti:
    *   [Registrare metriche](monitor-accounts.md) usando il portale di Azure. Metriche del portale mostrano i dati di telemetria di Azure Cosmos DB, ciò è utile per determinare se il problema corrispondente per Azure Cosmos DB o se si tratta del lato client.
    *   Log di [stringa di diagnostica](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) da risposte dell'operazione di punto.
    *   Log di [metriche di Query SQL](sql-api-query-metrics.md) da tutte le risposte alle query 
    *   Eseguire il programma di installazione per [registrazione SDK]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)

Diamo un'occhiata alla sezione [Problemi e soluzioni alternative comuni](#common-issues-workarounds) in questo articolo.

Verificare i [sezione dei problemi di GitHub](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) che viene attivamente monitorato. Verificare se è già stato pubblicato un problema simile con una soluzione alternativa. Se non è stata trovata una soluzione, quindi segnalare un problema di GitHub. È possibile aprire un tick per il supporto per problemi urgenti.


## <a name="common-issues-workarounds"></a>Problemi e soluzioni alternative comuni

### <a name="general-suggestions"></a>Suggerimenti generici
* Eseguire l'app nella stessa area di Azure con l'account Azure Cosmos DB, laddove possibile. 
* È possibile riscontrare problemi di connettività/disponibilità a causa di mancanza di risorse del computer client. È consigliabile monitorare l'utilizzo della CPU nei nodi che eseguono il client Azure Cosmos DB e la scalabilità verticale/orizzontale se sono in esecuzione con carico elevato.

### <a name="check-the-portal-metrics"></a>Controllare le metriche del portale
Verifica la [le metriche del portale](monitor-accounts.md) aiuterà a determinare se si tratta di un problema sul lato client o se si verifica un problema con il servizio. Ad esempio se le metriche contengono una frequenza elevata delle richieste soggette a limitazioni (codice di stato HTTP 429) che indica la richiesta è limitazioni quindi controlla la [Frequenza delle richieste troppo grande] sezione. 

### <a name="request-timeouts"></a>Timeout delle richieste
RequestTimeout avviene in genere quando si usa Direct/TCP, ma può avvenire in modalità Gateway. Queste sono le cause note più comuni e i suggerimenti su come risolvere il problema.

* Utilizzo della CPU è elevato, che verrà causano latenza e/o timeout richieste. Il cliente può aumentare il computer host per assegnare più risorse o il carico può essere distribuito tra più computer.
* Socket o della disponibilità delle porte potrebbe essere insufficiente. Quando si usano gli SDK di .NET, rilasciata prima della versione 2.0, i client in esecuzione in Azure è stato raggiunto il [Esaurimento delle porte SNAT (PAT) di Azure]. Questo esempio del motivo per cui è consigliabile eseguire sempre l'ultima versione del SDK.
* Creazione di più istanze di DocumentClient potrebbe causare contese di connessione e i problemi di timeout. Seguire le [suggerimenti sulle prestazioni](performance-tips.md)e usare una singola istanza di DocumentClient in un intero processo.
* Gli utenti visualizzano i timeout di richiesta o la latenza con privilegi elevati in alcuni casi perché le relative raccolte vengono effettuato il provisioning rilevavano, il back-end limita le richieste e il client esegue nuovi tentativi internamente senza questo esponendo al chiamante. Verificare i [le metriche del portale](monitor-accounts.md).
* Azure Cosmos DB distribuisce la velocità effettiva con provisioning globale in modo uniforme tra le partizioni fisiche. Controllare le metriche del portale per vedere se il carico di lavoro sta riscontrando un accesso frequente [chiave di partizione](partition-data.md). In questo modo la velocità effettiva usata aggregata (UR/sec) per essere visualizzati all'interno delle unità richiesta con provisioning, ma una singolo partizioni utilizzate le unità elaborate (UR/sec) supera la velocità effettiva con provisioning. 
* Inoltre, il SDK 2.0 aggiunge la semantica di canale alle connessioni direct/TCP. Una connessione TCP viene utilizzata per più richieste contemporaneamente. Questo può causare due problemi in casi specifici:
    * Un elevato livello di concorrenza può causare la contesa nel canale.
    * Richieste di grandi dimensioni o le risposte possono causare il blocco head-of-line sul canale e aggravare contesa, anche con un livello relativamente basso di concorrenza.
    * Se il case rientra in uno qualsiasi di queste due categorie (o se si sospetta un utilizzo elevato della CPU), queste sono le soluzioni possibili:
        * Provare a scalabilità verticale/orizzontale dell'applicazione.
        * Inoltre, i log del SDK possono essere acquisiti tramite [Listener di traccia](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md) per ottenere altri dettagli.

### <a name="connection-throttling"></a>Limitazione della connessione
La limitazione delle richieste di connessione può verificarsi a causa di un limite di connessione in un computer host. Precedenti a 2.0, i client in esecuzione in Azure è stato raggiunto il [Esaurimento delle porte SNAT (PAT) di Azure].

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
'Troppo grande percentuale di richieste' o codice di errore 429 indica che le richieste sono limitate, poiché la velocità effettiva usata (UR/sec) ha superato la velocità effettiva con provisioning. il SDK ritenterà automaticamente le richieste basate sull'oggetto specificato [criterio di ripetizione](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet). Se questo errore si verifica spesso, provare ad aumentare la velocità effettiva per la raccolta. Verificare i [le metriche del portale](use-metrics.md) per vedere se si ricevono 429 errori. Rivedere le [chiave di partizione](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey) per assicurarsi che il risultato è una distribuzione uniforme dei volumi di archiviazione e richiesta. 

### <a name="slow-query-performance"></a>Prestazioni delle query lente
Il [una query sulle metriche](sql-api-query-metrics.md) aiuterà a determinare in cui la query trascorre la maggior parte dei casi. Le metriche di query, si noterà quanta parte di esso viene impiegato in Visual Studio back-end del client.
* Se la query di back-end restituisce rapidamente e trascorre un tempo elevato per il client di controllare il carico nel computer. È probabile che non sono disponibili risorse sufficienti e il SDK è in attesa di risorse siano disponibili per gestire la risposta.
* Se la query di back-end è lenta, provare [ottimizzazione della query](optimize-cost-queries.md) ed esaminando l'oggetto corrente [criteri di indicizzazione](index-overview.md) 

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Frequenza delle richieste troppo grande]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Esaurimento delle porte SNAT (PAT) di Azure]: #snat
[Production check list]: #production-check-list


