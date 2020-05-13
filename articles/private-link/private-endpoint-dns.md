---
title: Configurazione DNS dell'endpoint privato di Azure
description: Informazioni sulla configurazione DNS dell'endpoint privato di Azure
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: allensu
ms.openlocfilehash: 7db02546b562f1b542080efdbda8968940655e95
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121288"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Configurazione DNS dell'endpoint privato di Azure


Quando ci si connette a una risorsa di collegamento privato usando un nome di dominio completo (FQDN) come parte della stringa di connessione, è importante configurare correttamente le impostazioni DNS per la risoluzione nell'indirizzo IP privato allocato. I servizi di Azure esistenti potrebbero avere già una configurazione DNS da usare per la connessione tramite un endpoint pubblico. È necessario eseguire l'override di questa configurazione per connettersi usando l'endpoint privato. 
 
L'interfaccia di rete associata all'endpoint privato contiene il set completo di informazioni necessarie per configurare il DNS, inclusi FQDN e indirizzi IP privati allocati per una determinata risorsa di collegamento privato. 
 
Per configurare le impostazioni DNS per gli endpoint privati, è possibile usare le opzioni seguenti: 
- **Usare il file host (consigliato solo per i test)**. Per eseguire l'override del DNS, è possibile usare il file host in una macchina virtuale.  
- **Usare una zona DNS privata**. È possibile usare le [zone DNS private](../dns/private-dns-privatednszone.md) per sostituire la risoluzione DNS per un determinato endpoint privato. Una zona DNS privata può essere collegata alla rete virtuale per risolvere domini specifici.
- **Usare il server di trasmissione DNS (facoltativo)**. Per eseguire l'override della risoluzione DNS per una determinata risorsa di collegamento privato, è possibile usare il server di server d'utilità di dominio. Se il [server DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) è ospitato in una rete virtuale, è possibile creare una regola di invio DNS per usare una zona DNS privata per semplificare la configurazione per tutte le risorse di collegamento privato.
 
> [!IMPORTANT]
> Non è consigliabile eseguire l'override di una zona che viene usata attivamente per risolvere gli endpoint pubblici. Le connessioni alle risorse non saranno in grado di risolversi correttamente senza l'invio DNS al DNS pubblico. Per evitare problemi, creare un nome di dominio diverso o seguire il nome suggerito per ogni servizio riportato di seguito. 

## <a name="azure-services-dns-zone-configuration"></a>Configurazione della zona DNS dei servizi di Azure
I servizi di Azure creeranno un record DNS di nome canonico (CNAME) nel DNS pubblico per reindirizzare la risoluzione al nome di dominio privato suggerito. È possibile eseguire l'override della risoluzione con l'indirizzo IP privato degli endpoint privati. 
 
Le applicazioni non devono modificare l'URL di connessione. Quando si tenta di risolvere usando un DNS pubblico, il server DNS verrà risolto negli endpoint privati. Il processo non ha alcun effetto sulle applicazioni esistenti. 

Per i servizi di Azure, usare i nomi di zona consigliati come descritto nella tabella seguente:

| Tipo di risorsa collegamento privato/sottorisorsa |Nome zona DNS privato | Nome della zona DNS pubblica |
|---|---|---|---|
| DATABASE SQL (Microsoft. SQL/Servers)/SQL Server | privatelink.database.windows.net | database.windows.net |
| Analisi delle sinapsi di Azure (Microsoft. SQL/Servers)/SQL Server  | privatelink.database.windows.net | database.windows.net |
| Account di archiviazione (Microsoft. storage/storageAccounts)/BLOB (BLOB, blob_secondary) | privatelink.blob.core.windows.net | blob.core.windows.net |
| Account di archiviazione (Microsoft. storage/storageAccounts)/table (tabella, table_secondary) | privatelink.table.core.windows.net | table.core.windows.net |
| Account di archiviazione (Microsoft. storage/storageAccounts)/Queue (Queue, queue_secondary) | privatelink.queue.core.windows.net | queue.core.windows.net |
| Account di archiviazione (Microsoft. storage/storageAccounts)/file (file, file_secondary) | privatelink.file.core.windows.net | file.core.windows.net |
| Account di archiviazione (Microsoft. storage/storageAccounts)/Web (Web, web_secondary) | privatelink.web.core.windows.net | web.core.windows.net |
| Data Lake file System Gen2 (Microsoft. storage/storageAccounts)/Data Lake file System Gen2 (DFS, dfs_secondary) | privatelink.dfs.core.windows.net | dfs.core.windows.net |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/SQL | privatelink.documents.azure.com | documents.azure.com |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/MongoDB | privatelink.mongo.cosmos.azure.com | mongo.cosmos.azure.com |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/Cassandra | privatelink.cassandra.cosmos.azure.com | cassandra.cosmos.azure.com |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/Gremlin | privatelink.gremlin.cosmos.azure.com | gremlin.cosmos.azure.com |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/tabella | privatelink.table.cosmos.azure.com | table.cosmos.azure.com |
| Database di Azure per PostgreSQL-server singolo (Microsoft. DBforPostgreSQL/Servers)/postgresqlServer | privatelink.postgres.database.azure.com | postgres.database.azure.com |
| Database di Azure per MySQL (Microsoft. DBforMySQL/Servers)/sqlServer | privatelink.mysql.database.azure.com | mysql.database.azure.com |
| Database di Azure per MariaDB (Microsoft. DBforMariaDB/Servers)/mariadbServer | privatelink.mariadb.database.azure.com | mariadb.database.azure.com |
| Azure Key Vault (Microsoft. insieme di credenziali/insiemi di credenziali)/insieme di credenziali | privatelink.vaultcore.azure.net | vault.azure.net |
| Servizio Azure Kubernetes-API Kubernetes (Microsoft. servizio contenitore/managedClusters)/managedCluster | privatelink. {Region}. azmk8s. io | {Region}. azmk8s. io |
| Ricerca di Azure (Microsoft. search/searchServices)/searchService | privatelink.search.windows.net | search.windows.net |
| Azure Container Registry (Microsoft. ContainerRegistry/registri)/registro di sistema | privatelink.azurecr.io | azurecr.io |
| Configurazione di app Azure (Microsoft. AppConfiguration/configurationStores)/configurationStore | privatelink.azconfig.io | azconfig.io |
| Backup di Azure (Microsoft. RecoveryServices/Vaults)/insieme di credenziali | privatelink. {Region}. backup. WindowsAzure. com | {Region}. backup. WindowsAzure. com |
| Hub eventi di Azure (Microsoft. EventHub/namespaces)/spazio dei nomi | privatelink.servicebus.windows.net | servicebus.windows.net |
| Bus di servizio di Azure (Microsoft. ServiceBus/namespaces)/spazio dei nomi | privatelink.servicebus.windows.net | servicebus.windows.net |
| Inoltro di Azure (Microsoft. Relay/namespaces)/spazio dei nomi | privatelink.servicebus.windows.net | servicebus.windows.net |
| Griglia di eventi di Azure (Microsoft. EventGrid/topics)/argomento | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Griglia di eventi di Azure (Microsoft. EventGrid/domains)/Domain | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| App Web di Azure (Microsoft. Web/sites)/sito | privatelink.azurewebsites.net | azurewebsites.net |
| Azure Machine Learning (Microsoft. MachineLearningServices/Workspaces)/area di lavoro | privatelink.api.azureml.ms | api.azureml.ms |

 


## <a name="dns-configuration-scenarios"></a>Scenari di configurazione DNS

Il nome di dominio completo (FQDN) dei servizi viene risolto automaticamente in un indirizzo IP pubblico. Pertanto, per risolvere l'indirizzo IP privato dell'endpoint privato, è necessario modificare di conseguenza la configurazione DNS.

DNS è un componente fondamentale per garantire il corretto funzionamento dell'applicazione risolvendo in modo corretto l'indirizzo IP dell'endpoint privato.

In base alle proprie preferenze, per la risoluzione DNS integrata sono disponibili gli scenari seguenti:

- [Carichi di lavoro della rete virtuale senza server DNS personalizzato](#virtual-network-workloads-without-custom-dns-server)
- [Carichi di lavoro locali che usano un server di un server d'invio DNS](#on-premises-workloads-using-a-dns-forwarder)

## <a name="virtual-network-workloads-without-custom-dns-server"></a>Carichi di lavoro della rete virtuale senza server DNS personalizzato

Questa configurazione è appropriata per i carichi di lavoro della rete virtuale senza server DNS personalizzato. In questo scenario il client esegue una query per l'indirizzo IP dell'endpoint privato per [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md)DNS fornito da Azure. Il servizio DNS di Azure sarà responsabile della risoluzione DNS delle zone DNS private.


> [!NOTE]
> Questo scenario usa la zona DNS privato consigliata del database SQL di Azure. Per altri servizi è possibile modificare il modello usando la configurazione di [zona DNS dei servizi di Azure](#azure-services-dns-zone-configuration)di riferimento riportata di seguito.

Per configurare correttamente, sono necessarie le risorse seguenti:

- Rete virtuale client

- DNS privato zona [privatelink.database.Windows.NET](../dns/private-dns-privatednszone.md) con [record di tipo a](../dns/dns-zones-records.md#record-types)

- Informazioni sull'endpoint privato (nome del record FQDN e indirizzo IP privato)

Il diagramma seguente illustra la sequenza di risoluzione DNS dei carichi di lavoro della rete virtuale che usa la zona DNS privata

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="Rete virtuale singola e DNS fornito da Azure":::

Questo modello può essere esteso a più reti virtuali con peering associate allo stesso endpoint privato. Questa operazione può essere eseguita [aggiungendo nuovi collegamenti di rete virtuale](../dns/private-dns-virtual-network-links.md) alla zona DNS privata per tutte le reti virtuali con peering.

> [!IMPORTANT]
>  Per questa configurazione è necessaria una singola zona DNS privata. per creare più zone con lo stesso nome per reti virtuali diverse è necessario eseguire operazioni manuali per unire i record DNS

In questo scenario è presente una topologia di rete di [hub & spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) con le reti spoke che condividono un endpoint privato comune e tutte le reti virtuali spoke sono collegate alla stessa zona DNS privata. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="Hub e spoke con DNS fornito da Azure":::

## <a name="on-premises-workloads-using-a-dns-forwarder"></a>Carichi di lavoro locali che usano un server di un server d'invio DNS
 
Affinché i carichi di lavoro locali siano in grado di risolvere un nome di dominio completo (FQDN) di un endpoint privato nell'indirizzo IP privato, è necessario usare un server di un server di un server di un server di un server d'utilità per eseguire la risoluzione della [zona DNS pubblica](#azure-services-dns-zone-configuration) dei servizi di Azure in Azure


Lo scenario seguente è adatto a una rete locale che dispone di un server di un server d'esecuzione DNS in Azure, che a sua volta è responsabile della risoluzione di tutte le query DNS tramite un server di un server d'un server di un server d'azione per il DNS fornito da Azure [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) 

> [!NOTE]
> Questo scenario usa la zona DNS privato consigliata del database SQL di Azure.Per altri servizi è possibile modificare il modello usando la configurazione di [zona DNS dei servizi di Azure](#azure-services-dns-zone-configuration)di riferimento riportata di seguito.

Per configurare correttamente, sono necessarie le risorse seguenti:

- Rete locale
- Rete virtuale [connessa a locale](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- Server di distribuzione DNS distribuito in Azure 
- DNS privato zone [privatelink.database.Windows.NET](../dns/private-dns-privatednszone.md)   con [record di tipo a](../dns/dns-zones-records.md#record-types)
- Informazioni sull'endpoint privato (nome del record FQDN e indirizzo IP privato)

Il diagramma seguente illustra la sequenza di risoluzione DNS da una rete locale che usa un server di destinazione DNS distribuito in Azure, in cui la risoluzione viene eseguita da una zona DNS privata collegata a una rete virtuale.

:::image type="content" source="media/private-endpoint-dns/on-premises-using-azure-dns.png" alt-text="Locale con DNS di Azure":::

Questa configurazione può essere estesa per una rete locale in cui è già presente una soluzione DNS. 
La soluzione DNS locale deve essere configurata per l'invio del traffico DNS al DNS di Azure tramite un [Server](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) d'istruzione locale che fa riferimento al server di distribuzione DNS distribuito in Azure.

> [!NOTE]
> Questo scenario usa la zona DNS privato consigliata del database SQL di Azure.Per altri servizi è possibile modificare il modello usando la configurazione di [zona DNS dei servizi di Azure](#azure-services-dns-zone-configuration)di riferimento riportata di seguito.

Per configurare correttamente, sono necessarie le risorse seguenti:


- Rete locale con una soluzione DNS personalizzata 
- Rete virtuale [connessa a locale](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- Server di distribuzione DNS distribuito in Azure
- DNS privato zone [privatelink.database.Windows.NET](../dns/private-dns-privatednszone.md)    con [record di tipo a](../dns/dns-zones-records.md#record-types)
- Informazioni sull'endpoint privato (nome del record FQDN e indirizzo IP privato)

Il diagramma seguente illustra la sequenza di risoluzione DNS da una rete locale che invia in modo condizionale il traffico DNS ad Azure, in cui la risoluzione viene eseguita da una zona DNS privata collegata a una rete virtuale

> [!IMPORTANT]
> L'invio condizionale deve essere eseguito alla [zona DNS pubblica](#azure-services-dns-zone-configuration)   , ad esempio:  `database.windows.net`   , anziché **privatelink**. database.Windows.NET

:::image type="content" source="media/private-endpoint-dns/on-premises-forwarding-to-azure.png" alt-text="L'invio locale al servizio DNS di Azure":::


## <a name="next-steps"></a>Passaggi successivi
- [Informazioni sugli endpoint privati](private-endpoint-overview.md)
