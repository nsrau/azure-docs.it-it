---
title: Configurazione DNS dell'endpoint privato di Azure
description: Informazioni sulla configurazione DNS dell'endpoint privato di Azure
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: allensu
ms.openlocfilehash: 7c8ff0808ada522dc24ef3c27156f4151832fbcd
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715937"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Configurazione DNS dell'endpoint privato di Azure


Quando ci si connette a una risorsa Collegamento privato usando un nome di dominio completo (FQDN) come parte della stringa di connessione, è importante configurare correttamente le impostazioni DNS per la risoluzione nell'indirizzo IP privato allocato. I servizi di Azure esistenti potrebbero avere già una configurazione DNS da usare per la connessione tramite un endpoint pubblico. Per connettersi usando l'endpoint privato è necessario eseguire l'override di questa configurazione. 
 
L'interfaccia di rete associata all'endpoint privato contiene il set completo di informazioni necessarie per configurare il DNS, inclusi FQDN e indirizzi IP privati allocati per una determinata risorsa Collegamento privato. 
 
Per configurare le impostazioni DNS per gli endpoint privati si può procedere nei modi seguenti: 
- **Usare il file host (consigliato solo per il test)** . È possibile usare il file host in una macchina virtuale per eseguire l'override del DNS.  
- **Usare una zona DNS privato**. È possibile usare [zone DNS privato](../dns/private-dns-privatednszone.md) per eseguire l'override della risoluzione DNS per un determinato endpoint privato. Una zona DNS privato può essere collegata alla rete virtuale per risolvere domini specifici.
- **Usare il server d'inoltro DNS (facoltativo)** . È possibile usare il proprio server d'inoltro DNS per eseguire l'override della risoluzione DNS per una determinata risorsa Collegamento privato. Se il [server DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) è ospitato in una rete virtuale, si può creare una regola di invio DNS in modo da usare una zona DNS privato per semplificare la configurazione per tutte le risorse Collegamento privato.
 
> [!IMPORTANT]
> Non è consigliabile eseguire l'override di una zona che viene attivamente usata per risolvere gli endpoint pubblici. Le connessioni alle risorse non potranno essere risolte correttamente senza inoltro DNS al DNS pubblico. Per evitare problemi, creare un nome di dominio diverso o usare il nome suggerito per ogni servizio indicato di seguito. 

## <a name="azure-services-dns-zone-configuration"></a>Configurazione della zona DNS dei servizi di Azure
I servizi di Azure creeranno un record di nome canonico (CNAME) nel DNS pubblico per reindirizzare la risoluzione al nome di dominio privato suggerito. È possibile eseguire l'override della risoluzione con l'indirizzo IP privato degli endpoint privati. 
 
Le applicazioni non devono modificare l'URL di connessione. Quando si tenta la risoluzione usando un DNS pubblico, il server DNS eseguirà la risoluzione negli endpoint privati. Il processo non ha effetti sulle applicazioni esistenti. 

Per i servizi di Azure, usare i nomi di zona consigliati come descritto nella tabella seguente:

| Tipo di risorsa collegamento privato / Sottorisorsa |Nome zona DNS privato | Server d'inoltro zona DNS pubblico |
|---|---|---|---|
| Database SQL (Microsoft.Sql/servers) / SQL Server | privatelink.database.windows.net | database.windows.net |
| Azure Synapse Analytics (Microsoft.Sql/servers) / SQL Server  | privatelink.database.windows.net | database.windows.net |
| Account di archiviazione (Microsoft.Storage/storageAccounts) / BLOB (BLOB, blob_secondary) | privatelink.blob.core.windows.net | blob.core.windows.net |
| Account di archiviazione (Microsoft.Storage/storageAccounts) / Tabella (table, table_secondary) | privatelink.table.core.windows.net | table.core.windows.net |
| Account di archiviazione (Microsoft.Storage/storageAccounts) / Coda (queue, queue_secondary) | privatelink.queue.core.windows.net | queue.core.windows.net |
| Account di archiviazione (Microsoft.Storage/storageAccounts) / File (file, file_secondary) | privatelink.file.core.windows.net | file.core.windows.net |
| Account di archiviazione (Microsoft.Storage/storageAccounts) / Web (web, web_secondary) | privatelink.web.core.windows.net | web.core.windows.net |
| File system Data Lake Gen2 (Microsoft.Storage/storageAccounts) / File system Data Lake Gen2 (dfs, dfs_secondary) | privatelink.dfs.core.windows.net | dfs.core.windows.net |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / SQL | privatelink.documents.azure.com | documents.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / MongoDB | privatelink.mongo.cosmos.azure.com | mongo.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Cassandra | privatelink.cassandra.cosmos.azure.com | cassandra.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Gremlin | privatelink.gremlin.cosmos.azure.com | gremlin.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Table | privatelink.table.cosmos.azure.com | table.cosmos.azure.com |
| Database di Azure per PostgreSQL - Server singolo (Microsoft.DBforPostgreSQL/servers) / postgresqlServer | privatelink.postgres.database.azure.com | postgres.database.azure.com |
| Database di Azure per MySQL (Microsoft.DBforMySQL/servers) / mysqlServer | privatelink.mysql.database.azure.com | mysql.database.azure.com |
| Database di Azure per MariaDB (Microsoft.DBforMariaDB/servers) / mariadbServer | privatelink.mariadb.database.azure.com | mariadb.database.azure.com |
| Azure Key Vault (Microsoft.KeyVault/vaults) / vault | privatelink.vaultcore.azure.net | vault.azure.net <br> vaultcore.azure.net |
| Servizio Azure Kubernetes - API Kubernetes (Microsoft.ContainerService/managedClusters) / managedCluster | privatelink.{region}.azmk8s.io | {region}.azmk8s.io |
| Ricerca di Azure (Microsoft.Search/searchServices) / searchService | privatelink.search.windows.net | search.windows.net |
| Registro Azure Container (Microsoft.ContainerRegistry/registries) / registro | privatelink.azurecr.io | azurecr.io |
| Configurazione app di Azure (Microsoft.AppConfiguration/configurationStores) / configurationStore | privatelink.azconfig.io | azconfig.io |
| Backup di Azure (Microsoft.RecoveryServices/vaults) / vault | privatelink.{region}.backup.windowsazure.com | {region}.backup.windowsazure.com |
| Hub eventi di Azure (Microsoft.EventHub/namespaces) / spazio dei nomi | privatelink.servicebus.windows.net | servicebus.windows.net |
| Bus di servizio di Azure (Microsoft.ServiceBus/namespaces) / spazio dei nomi | privatelink.servicebus.windows.net | servicebus.windows.net |
| Inoltro di Azure (Microsoft.Relay/namespaces) / spazio dei nomi | privatelink.servicebus.windows.net | servicebus.windows.net |
| Griglia di eventi di Azure (Microsoft.EventGrid/topics) / argomento | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Griglia di eventi di Azure (Microsoft.EventGrid/domains) / dominio | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| App Web di Azure (Microsoft.Web/sites) / sito | privatelink.azurewebsites.net | azurewebsites.net |
| Azure Machine Learning(Microsoft.MachineLearningServices/workspaces) / area di lavoro | privatelink.api.azureml.ms | api.azureml.ms |

 


## <a name="dns-configuration-scenarios"></a>Scenari di configurazione DNS

Il nome di dominio completo (FQDN) dei servizi viene risolto automaticamente in un indirizzo IP pubblico. Pertanto, per risolverlo nell'indirizzo IP privato dell'endpoint privato, è necessario modificare la configurazione DNS di conseguenza.

DNS è un componente fondamentale perché l'applicazione funzioni correttamente, risolvendo nel modo giusto l'indirizzo IP dell'endpoint privato.

In base alle proprie preferenze, per la risoluzione DNS integrata sono disponibili gli scenari seguenti:

- [Carichi di lavoro di rete virtuale senza server DNS personalizzato](#virtual-network-workloads-without-custom-dns-server)
- [Carichi di lavoro locali con server d'inoltro DNS](#on-premises-workloads-using-a-dns-forwarder)

## <a name="virtual-network-workloads-without-custom-dns-server"></a>Carichi di lavoro di rete virtuale senza server DNS personalizzato

Questa configurazione è appropriata per i carichi di lavoro di rete virtuale senza server DNS personalizzato. In questo scenario il client esegue una query per l'indirizzo IP dell'endpoint privato sul DNS fornito da Azure [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md). Il servizio DNS di Azure sarà responsabile della risoluzione DNS delle zone DNS privato.


> [!NOTE]
> Questo scenario usa la zona DNS privato consigliata del database SQL di Azure. Per altri servizi è possibile modificare il modello usando la seguente [configurazione della zona DNS dei servizi di Azure](#azure-services-dns-zone-configuration) di riferimento.

Per la configurazione corretta occorrono le risorse seguenti:

- Rete virtuale client

- Zona DNS privato [privatelink.database.windows.net](../dns/private-dns-privatednszone.md) con [record tipo A](../dns/dns-zones-records.md#record-types)

- Informazioni sull'endpoint privato (nome record FQDN e indirizzo IP privato)

Il diagramma seguente illustra la sequenza di risoluzione DNS di carichi di lavoro di rete virtuale che usano la zona DNS privato

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="Rete virtuale singola e DNS fornito da Azure":::

Questo modello può essere esteso a più reti virtuali con peering associate allo stesso endpoint privato. Questa operazione può essere eseguita [aggiungendo nuovi collegamenti di rete virtuale](../dns/private-dns-virtual-network-links.md) alla zona DNS privato per tutte le reti virtuali con peering.

> [!IMPORTANT]
> Per questa configurazione è necessaria una singola zona DNS privato. La creazione di più zone con lo stesso nome per reti virtuali diverse richiederebbe operazioni manuali per unire i record DNS

In questo scenario c'è una topologia di rete [hub-spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) con le reti spoke che condividono un endpoint privato comune e tutte le reti virtuali spoke collegate alla stessa zona DNS privato. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="Hub-spoke con DNS fornito da Azure":::

## <a name="on-premises-workloads-using-a-dns-forwarder"></a>Carichi di lavoro locali con server d'inoltro DNS

Affinché i carichi di lavoro locali possano risolvere il nome di dominio completo di un endpoint privato nell'indirizzo IP privato, è necessario usare un server d'inoltro DNS per implementare in Azure la risoluzione della [zona DNS pubblico](#azure-services-dns-zone-configuration) del servizio di Azure.


Lo scenario seguente è adatto a una rete locale che ha un server d'inoltro DNS in Azure, che a sua volta è responsabile della risoluzione di tutte le query DNS tramite un server d'inoltro a livello di server al DNS [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) fornito da Azure. 

> [!NOTE]
> Questo scenario usa la zona DNS privato consigliata del database SQL di Azure. Per altri servizi è possibile modificare il modello usando i riferimenti in  [Configurazione della zona DNS dei servizi di Azure](#azure-services-dns-zone-configuration).

Per la configurazione corretta occorrono le risorse seguenti:

- Rete locale
- Rete virtuale [connessa alla rete locale](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- Server d'inoltro DNS distribuito in Azure 
- Zone DNS privato [privatelink.database.windows.net](../dns/private-dns-privatednszone.md) con [Record tipo A](../dns/dns-zones-records.md#record-types)
- Informazioni sull'endpoint privato (nome record FQDN e indirizzo IP privato)

Il diagramma seguente illustra la sequenza di risoluzione DNS di una rete locale che usa un server d'inoltro DNS distribuito in Azure, in cui la risoluzione viene eseguita da una zona DNS privato [collegata a una rete virtuale.](../dns/private-dns-virtual-network-links.md)

:::image type="content" source="media/private-endpoint-dns/on-premises-using-azure-dns.png" alt-text="Rete locale che usa DNS di Azure":::

Questa configurazione può essere estesa per una rete locale in cui è già presente una soluzione DNS. 
La soluzione DNS locale deve essere configurata in modo da inoltrare il traffico DNS al servizio DNS di Azure tramite un [server d'inoltro condizionale](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) che faccia riferimento al server d'inoltro DNS distribuito in Azure.

> [!NOTE]
> Questo scenario usa la zona DNS privato consigliata del database SQL di Azure. Per altri servizi è possibile modificare il modello usando i riferimenti in  [Configurazione della zona DNS dei servizi di Azure](#azure-services-dns-zone-configuration).

Per la configurazione corretta occorrono le risorse seguenti:

- Rete locale con una soluzione DNS personalizzata 
- Rete virtuale [connessa alla rete locale](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- Server d'inoltro DNS distribuito in Azure
- Zone DNS privato [privatelink.database.windows.net](../dns/private-dns-privatednszone.md)  con [Record tipo A](../dns/dns-zones-records.md#record-types)
- Informazioni sull'endpoint privato (nome record FQDN e indirizzo IP privato)

Il diagramma seguente illustra la sequenza di risoluzione DNS di una rete locale che inoltra in modo condizionale il traffico DNS, in cui la risoluzione viene eseguita da una zona DNS privato [collegata a una rete virtuale.](../dns/private-dns-virtual-network-links.md)

> [!IMPORTANT]
> L'inoltro condizionale deve essere effettuato nel [**server d'inoltro della zona DNS pubblico consigliato**](#azure-services-dns-zone-configuration) Ad esempio: `database.windows.net`  anziché **privatelink**.database.windows.net

:::image type="content" source="media/private-endpoint-dns/on-premises-forwarding-to-azure.png" alt-text="Inoltro locale al servizio DNS di Azure":::


## <a name="next-steps"></a>Passaggi successivi
- [Altre informazioni sugli endpoint privati](private-endpoint-overview.md)
