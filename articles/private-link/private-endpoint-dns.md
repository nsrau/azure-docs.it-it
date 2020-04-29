---
title: Configurazione DNS dell'endpoint privato di Azure
description: Informazioni sulla configurazione DNS dell'endpoint privato di Azure
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: allensu
ms.openlocfilehash: 477a5ffa971120d1a98c09ac4ae8ebda1c82b770
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82209027"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Configurazione DNS dell'endpoint privato di Azure


Quando ci si connette a una risorsa di collegamento privato usando un nome di dominio completo (FQDN) come parte della stringa di connessione, è importante configurare correttamente le impostazioni DNS per la risoluzione nell'indirizzo IP privato allocato. I servizi di Azure esistenti potrebbero avere già una configurazione DNS da usare per la connessione tramite un endpoint pubblico. È necessario eseguire l'override di questo per connettersi usando l'endpoint privato. 
 
L'interfaccia di rete associata all'endpoint privato contiene il set completo di informazioni necessarie per configurare il DNS, inclusi FQDN e indirizzi IP privati allocati per una determinata risorsa di collegamento privato. 
 
Per configurare le impostazioni DNS per gli endpoint privati, è possibile usare le opzioni seguenti: 
- **Usare il file host (consigliato solo per i test)**. Per eseguire l'override del DNS, è possibile usare il file host in una macchina virtuale.  
- **Usare una zona DNS privata**. È possibile usare le [zone DNS private](../dns/private-dns-privatednszone.md) per sostituire la risoluzione DNS per un determinato endpoint privato. Una zona DNS privata può essere collegata alla rete virtuale per risolvere domini specifici.
- **Usare il server DNS personalizzato**. È possibile usare il proprio server DNS per sostituire la risoluzione DNS per una determinata risorsa di collegamento privato. Se il [server DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) è ospitato in una rete virtuale, è possibile creare una regola di invio DNS per usare una zona DNS privata per semplificare la configurazione per tutte le risorse di collegamento privato.
 
> [!IMPORTANT]
> Non è consigliabile eseguire l'override di una zona che viene usata attivamente per risolvere gli endpoint pubblici. Le connessioni alle risorse non saranno in grado di risolversi correttamente senza l'invio DNS al DNS pubblico. Per evitare problemi, creare un nome di dominio diverso o seguire il nome suggerito per ogni servizio riportato di seguito. 

## <a name="azure-services-dns-zone-configuration"></a>Configurazione della zona DNS dei servizi di Azure
I servizi di Azure creeranno un record DNS di nome canonico (CNAME) nel DNS pubblico per reindirizzare la risoluzione ai nomi di dominio privati suggeriti. Sarà possibile sostituire la risoluzione con l'indirizzo IP privato degli endpoint privati. 
 
Le applicazioni non devono modificare l'URL di connessione. Quando si tenta di risolvere usando un DNS pubblico, il server DNS verrà risolto negli endpoint privati. Il processo non ha alcun effetto sulle applicazioni esistenti. 

Per i servizi di Azure, usare i nomi di zona consigliati come descritto nella tabella seguente:

|Tipo di risorsa collegamento privato   |Sottorisorsa  |Nome zona  |
|---------|---------|---------|
|DATABASE SQL (Microsoft. SQL/Servers)    |  SQL Server (sqlServer)        |   privatelink.database.windows.net       |
|Analisi delle sinapsi di Azure (Microsoft. SQL/Servers)    |  SQL Server (sqlServer)        | privatelink.database.windows.net |
|Account di archiviazione (Microsoft. storage/storageAccounts)    |  BLOB (BLOB, blob_secondary)        |    privatelink.blob.core.windows.net      |
|Account di archiviazione (Microsoft. storage/storageAccounts)    |    Tabella (tabella, table_secondary)      |   privatelink.table.core.windows.net       |
|Account di archiviazione (Microsoft. storage/storageAccounts)    |    Coda (coda, queue_secondary)     |   privatelink.queue.core.windows.net       |
|Account di archiviazione (Microsoft. storage/storageAccounts)   |    File (file, file_secondary)      |    privatelink.file.core.windows.net      |
|Account di archiviazione (Microsoft. storage/storageAccounts)     |  Web (Web, web_secondary)        |    privatelink.web.core.windows.net      |
|Data Lake file System Gen2 (Microsoft. storage/storageAccounts)  |  Data Lake file System Gen2 (DFS, dfs_secondary)        |     privatelink.dfs.core.windows.net     |
|Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)|SQL    |privatelink.documents.azure.com|
|Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)|MongoDB    |privatelink.mongo.cosmos.azure.com|
|Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)|Cassandra|privatelink.cassandra.cosmos.azure.com|
|Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)|Gremlin    |privatelink.gremlin.cosmos.azure.com|
|Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)|Tabella|privatelink.table.cosmos.azure.com|
|Database di Azure per PostgreSQL-server singolo (Microsoft. DBforPostgreSQL/Servers)|postgresqlServer|privatelink.postgres.database.azure.com|
|Database di Azure per MySQL (Microsoft. DBforMySQL/Servers)|mysqlServer|privatelink.mysql.database.azure.com|
|Database di Azure per MariaDB (Microsoft. DBforMariaDB/Servers)|mariadbServer|privatelink.mariadb.database.azure.com|
|Azure Key Vault (Microsoft. Vault/Vaults)|insieme di credenziali|privatelink.vaultcore.azure.net|
|Servizio Azure Kubernetes-API Kubernetes (Microsoft. servizio contenitore/managedClusters)    | managedCluster | {GUID}. privatelink. {Region}. azmk8s. io|
|Ricerca di Azure (Microsoft. search/searchServices)|searchService|privatelink.search.windows.net|   
|Azure Container Registry (Microsoft. ContainerRegistry/registri) | Registro di sistema | privatelink.azurecr.io |
|Configurazione di app Azure (Microsoft. Appconfiguration/configurationStores)| configurationStore | privatelink.azconfig.io|
|Backup di Azure (Microsoft. RecoveryServices/Vaults)| insieme di credenziali |privatelink. {Region}. backup. WindowsAzure. com|
|Hub eventi di Azure (Microsoft. EventHub/namespaces)| namespace |privatelink.servicebus.windows.net|
|Bus di servizio di Azure (Microsoft. ServiceBus/namespaces) | namespace |privatelink.servicebus.windows.net|
|Inoltro di Azure (Microsoft. Relay/namespaces) | namespace |privatelink.servicebus.windows.net|
|Griglia di eventi di Azure (Microsoft. EventGrid/topics)     | argomento | argomento. {Region}. privatelink. eventgrid. Azure. NET|
|Griglia di eventi di Azure (Microsoft. EventGrid/domains) | dominio | dominio. {Region}. privatelink. eventgrid. Azure. NET |
|App Web di Azure (Microsoft. Web/sites)    | site | privatelink.azurewebsites.net |
|Azure Machine Learning (Microsoft. MachineLearningServices/Workspaces)    | area di lavoro | privatelink.api.azureml.ms |
 


## <a name="dns-configuration-scenarios"></a>Scenari di configurazione DNS

Il nome di dominio completo dei servizi risolve un indirizzo IP pubblico, quindi è necessario modificare la configurazione DNS per risolvere l'indirizzo IP privato dell'endpoint privato.

DNS è un componente fondamentale per garantire il corretto funzionamento dell'applicazione risolvendo in modo corretto l'indirizzo IP dell'endpoint privato.

In base alle proprie preferenze, per la risoluzione DNS integrata sono disponibili gli scenari seguenti:

- [Carichi di lavoro della rete virtuale senza server DNS personalizzato](#virtual-network-workloads-without-custom-dns-server)


## <a name="virtual-network-workloads-without-custom-dns-server"></a>Carichi di lavoro della rete virtuale senza server DNS personalizzato

Questa configurazione è appropriata per i carichi di lavoro della rete virtuale senza server DNS personalizzato. In questo scenario il client esegue una query per l'indirizzo IP dell'endpoint privato per [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md)DNS fornito da Azure. Il servizio DNS di Azure sarà responsabile della risoluzione DNS delle zone DNS private.


 > [!NOTE]
> Questo scenario usa la zona DNS privato consigliata del database SQL di Azure. Per altri servizi è possibile modificare il modello usando la configurazione di [zona DNS dei servizi di Azure](#azure-services-dns-zone-configuration)di riferimento riportata di seguito.

Per la configurazione corretta, è necessario disporre delle risorse seguenti:

- Rete virtuale client

- DNS privato zona [privatelink.database.Windows.NET](../dns/private-dns-privatednszone.md) con [record di tipo a](../dns/dns-zones-records.md#record-types)

- Informazioni sull'endpoint privato (nome del record FQDN e indirizzo IP privato)

Il diagramma seguente illustra la sequenza di risoluzione DNS dei carichi di lavoro della rete virtuale che usa la zona DNS privata

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="rete virtuale singola e DNS fornito da Azure":::

Questo modello può essere esteso a più reti virtuali con peering associate allo stesso endpoint privato. Questa operazione può essere eseguita [aggiungendo nuovi collegamenti di rete virtuale](../dns/private-dns-virtual-network-links.md) alla zona DNS privata per tutte le reti virtuali con peering.

 > [!IMPORTANT]
>  Per questa configurazione è necessaria una singola zona DNS privata. per creare più zone con lo stesso nome per reti virtuali diverse è necessario eseguire operazioni manuali per unire i record DNS

In questo scenario è presente una topologia di rete di [hub & spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) con le reti spoke che condividono un endpoint privato comune e la rete virtuale spoke è collegata alla stessa zona DNS privata. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="Hub e spoke con il DNS fornito da Azure":::


## <a name="next-steps"></a>Passaggi successivi
- [Informazioni sugli endpoint privati](private-endpoint-overview.md)
