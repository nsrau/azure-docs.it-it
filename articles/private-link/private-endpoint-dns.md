---
title: Configurazione DNS dell'endpoint privato di Azure
description: Informazioni sulla configurazione DNS dell'endpoint privato di Azure
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: allensu
ms.openlocfilehash: df1896caaa0cba1f62dc1466124b393337fa8c83
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87985785"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Configurazione DNS dell'endpoint privato di Azure


Quando si esegue la connessione a una risorsa di collegamento privato usando un nome di dominio completo (FQDN) come parte della stringa di connessione, è importante configurare correttamente le impostazioni DNS per la risoluzione nell'indirizzo IP privato allocato. I servizi di Microsoft Azure esistenti potrebbero avere già una configurazione DNS da usare per la connessione tramite un endpoint pubblico. Per connettersi usando l'endpoint privato è necessario eseguire l'override di questa configurazione. 
 
L'interfaccia di rete associata all'endpoint privato contiene il set completo di informazioni necessarie per configurare il DNS, inclusi FQDN e indirizzi IP privati allocati per una particolare risorsa di collegamento privato. 
 
Per configurare le impostazioni DNS per gli endpoint privati si può procedere nei modi seguenti: 
- **Usare il file host (consigliato solo per i test)**. È possibile usare il file host in una macchina virtuale per eseguire l'override del DNS.  
- **Usare una zona DNS privato**. È possibile usare le [zone DNS private](../dns/private-dns-privatednszone.md) per sostituire la risoluzione DNS per un determinato endpoint privato. Una zona DNS privato può essere collegata alla rete virtuale per risolvere domini specifici.
- **Usare il server di trasmissione DNS (facoltativo)**. È possibile usare il server di trasmissione DNS per sostituire la risoluzione DNS per una particolare risorsa di collegamento privato. Se il [server DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) è ospitato in una rete virtuale, si può creare una regola di invio DNS in modo da usare una zona DNS privato per semplificare la configurazione per tutte le risorse Collegamento privato.
 
> [!IMPORTANT]
> Non è consigliabile eseguire l'override di una zona che viene usata attivamente per risolvere gli endpoint pubblici. Le connessioni alle risorse non potranno essere risolte correttamente senza inoltro DNS al DNS pubblico. Per evitare problemi, creare un nome di dominio diverso o usare il nome suggerito per ogni servizio indicato di seguito. 

## <a name="azure-services-dns-zone-configuration"></a>Configurazione della zona DNS dei servizi di Azure
I servizi di Azure creeranno un record DNS di nome canonico (CNAME) nel servizio DNS pubblico per reindirizzare la risoluzione al nome di dominio privato suggerito. È possibile eseguire l'override della risoluzione con l'indirizzo IP privato degli endpoint privati. 
 
Le applicazioni non devono modificare l'URL di connessione. Quando si tenta di risolvere usando un servizio DNS pubblico, il server DNS verrà risolto negli endpoint privati. Il processo non influisce sulle applicazioni esistenti. 

Per i servizi di Azure, usare i nomi di zona consigliati come descritto nella tabella seguente:

| Tipo di risorsa collegamento privato/sottorisorsa |Nome zona DNS privato | Server d'inoltro zona DNS pubblico |
|---|---|---|---|
| Automazione di Azure/(Microsoft. Automation/automationAccounts)/webhook, DSCAndHybridWorker | privatelink.azure-automation.net | azure-automation.net |
| Database SQL di Azure (Microsoft. SQL/Servers)/SQL Server | privatelink.database.windows.net | database.windows.net |
| Analisi delle sinapsi di Azure (Microsoft. SQL/Servers)/SQL Server  | privatelink.database.windows.net | database.windows.net |
| Account di archiviazione (Microsoft. storage/storageAccounts)/BLOB (BLOB, blob_secondary) | privatelink.blob.core.windows.net | blob.core.windows.net |
| Account di archiviazione (Microsoft. storage/storageAccounts)/table (tabella, table_secondary) | privatelink.table.core.windows.net | table.core.windows.net |
| Account di archiviazione (Microsoft. storage/storageAccounts)/Queue (Queue, queue_secondary) | privatelink.queue.core.windows.net | queue.core.windows.net |
| Account di archiviazione (Microsoft. storage/storageAccounts)/file (file, file_secondary) | privatelink.file.core.windows.net | file.core.windows.net |
| Account di archiviazione (Microsoft. storage/storageAccounts)/Web (Web, web_secondary) | privatelink.web.core.windows.net | web.core.windows.net |
| Azure Data Lake file System Gen2 (Microsoft. storage/storageAccounts)/Data Lake file System Gen2 (DFS, dfs_secondary) | privatelink.dfs.core.windows.net | dfs.core.windows.net |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / SQL | privatelink.documents.azure.com | documents.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / MongoDB | privatelink.mongo.cosmos.azure.com | mongo.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Cassandra | privatelink.cassandra.cosmos.azure.com | cassandra.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Gremlin | privatelink.gremlin.cosmos.azure.com | gremlin.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Table | privatelink.table.cosmos.azure.com | table.cosmos.azure.com |
| Database di Azure per PostgreSQL - Server singolo (Microsoft.DBforPostgreSQL/servers) / postgresqlServer | privatelink.postgres.database.azure.com | postgres.database.azure.com |
| Database di Azure per MySQL (Microsoft.DBforMySQL/servers) / mysqlServer | privatelink.mysql.database.azure.com | mysql.database.azure.com |
| Database di Azure per MariaDB (Microsoft.DBforMariaDB/servers) / mariadbServer | privatelink.mariadb.database.azure.com | mariadb.database.azure.com |
| Azure Key Vault (Microsoft.KeyVault/vaults) / vault | privatelink.vaultcore.azure.net | vault.azure.net <br> vaultcore.azure.net |
| Servizio Azure Kubernetes-API Kubernetes (Microsoft. servizio contenitore/managedClusters)/gestione | privatelink.{region}.azmk8s.io | {region}.azmk8s.io |
| Ricerca di Azure (Microsoft.Search/searchServices) / searchService | privatelink.search.windows.net | search.windows.net |
| Registro Azure Container (Microsoft.ContainerRegistry/registries) / registro | privatelink.azurecr.io | azurecr.io |
| Configurazione app di Azure (Microsoft.AppConfiguration/configurationStores) / configurationStore | privatelink.azconfig.io | azconfig.io |
| Backup di Azure (Microsoft.RecoveryServices/vaults) / vault | privatelink.{region}.backup.windowsazure.com | {region}.backup.windowsazure.com |
| Hub eventi di Azure (Microsoft. EventHub/namespaces)/spazio dei nomi | privatelink.servicebus.windows.net | servicebus.windows.net |
| Bus di servizio di Azure (Microsoft.ServiceBus/namespaces) / spazio dei nomi | privatelink.servicebus.windows.net | servicebus.windows.net |
| Hub Azure Internet (Microsoft. Devices/IotHubs)/iotHub | privatelink.azure-devices.net | azure-devices.net |
| Inoltro di Azure (Microsoft.Relay/namespaces) / spazio dei nomi | privatelink.servicebus.windows.net | servicebus.windows.net |
| Griglia di eventi di Azure (Microsoft.EventGrid/topics) / argomento | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Griglia di eventi di Azure (Microsoft.EventGrid/domains) / dominio | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| App Web di Azure (Microsoft. Web/sites)/siti | privatelink.azurewebsites.net | azurewebsites.net |
| Azure Machine Learning (Microsoft. MachineLearningServices/Workspaces)/area di lavoro | privatelink.api.azureml.ms | api.azureml.ms |
| Hub Internet (Microsoft. Devices/IotHubs)/IotHub | privatelink.azure-devices.net | azure-devices.net |
| SignalR (Microsoft. SignalRService/SignalR)/SignalR | privatelink.service.signalr.net | service.signalr.net |
| Monitoraggio di Azure (Microsoft. Insights/privateLinkScopes)/azuremonitor | privatelink.monitor.azure.com<br/> privatelink.oms.opinsights.azure.com <br/> privatelink.ods.opinsights.azure.com <br/> privatelink.agentsvc.azure-automation.com | monitor.azure.com<br/> oms.opinsights.azure.com<br/> ods.opinsights.azure.com<br/> agentsvc.azure-automation.com |
| Servizi cognitivi (Microsoft. CognitiveServices/accounts)/account | privatelink.cognitiveservices.azure.com  | cognitiveservices.azure.com  |
| Sincronizzazione file di Azure (Microsoft. StorageSync/storageSyncServices)/AFS |  privatelink.afs.azure.net  |  afs.azure.net  |

 
## <a name="dns-configuration-scenarios"></a>Scenari di configurazione DNS

Il nome di dominio completo (FQDN) dei servizi viene risolto automaticamente in un indirizzo IP pubblico. Per risolvere l'indirizzo IP privato dell'endpoint privato, è necessario modificare di conseguenza la configurazione DNS.

DNS è un componente fondamentale per garantire il corretto funzionamento dell'applicazione risolvendo correttamente l'indirizzo IP dell'endpoint privato.

In base alle proprie preferenze, con la risoluzione DNS integrata sono disponibili gli scenari seguenti:

- [Carichi di lavoro di rete virtuale senza server DNS personalizzato](#virtual-network-workloads-without-custom-dns-server)
- [Carichi di lavoro locali con server d'inoltro DNS](#on-premises-workloads-using-a-dns-forwarder)
- [Rete virtuale e carichi di lavoro locali usando un server di un server d'invio](#virtual-network-and-on-premises-workloads-using-a-dns-forwarder)


## <a name="virtual-network-workloads-without-custom-dns-server"></a>Carichi di lavoro di rete virtuale senza server DNS personalizzato

Questa configurazione è appropriata per i carichi di lavoro della rete virtuale senza un server DNS personalizzato. In questo scenario, il client esegue una query per l'indirizzo IP dell'endpoint privato per il servizio DNS fornito da Azure [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md). Il servizio DNS di Azure sarà responsabile della risoluzione DNS delle zone DNS privato.

> [!NOTE]
> Questo scenario usa la zona DNS privata consigliata del database SQL di Azure. Per altri servizi, è possibile modificare il modello usando il riferimento seguente: [configurazione della zona DNS dei servizi di Azure](#azure-services-dns-zone-configuration).

Per configurare correttamente, sono necessarie le risorse seguenti:

- Rete virtuale client

- DNS privato zona [privatelink.database.Windows.NET](../dns/private-dns-privatednszone.md) con [record di tipo a](../dns/dns-zones-records.md#record-types)

- Informazioni sull'endpoint privato (nome del record FQDN e indirizzo IP privato)

Lo screenshot seguente illustra la sequenza di risoluzione DNS dei carichi di lavoro della rete virtuale con la zona DNS privata:

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="Rete virtuale singola e DNS fornito da Azure":::

Questo modello può essere esteso a più reti virtuali con peering associate allo stesso endpoint privato. Questa operazione può essere eseguita [aggiungendo nuovi collegamenti di rete virtuale](../dns/private-dns-virtual-network-links.md) alla zona DNS privato per tutte le reti virtuali con peering.

> [!IMPORTANT]
> Per questa configurazione è necessaria una singola zona DNS privata. Per creare più zone con lo stesso nome per reti virtuali diverse è necessario eseguire operazioni manuali per unire i record DNS.

> [!IMPORTANT]
> Se si usa un endpoint privato in un modello hub e spoke da una sottoscrizione diversa, riusare la stessa zona DNS privata nell'hub.

In questo scenario è presente una topologia di rete [hub-spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) con le reti spoke che condividono un endpoint privato comune e tutte le reti virtuali spoke sono collegate alla stessa zona DNS privata. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="Hub-spoke con DNS fornito da Azure":::

## <a name="on-premises-workloads-using-a-dns-forwarder"></a>Carichi di lavoro locali con server d'inoltro DNS

Per i carichi di lavoro locali per la risoluzione di un nome di dominio completo di un endpoint privato nell'indirizzo IP privato, è necessario usare un server di un server di un server di un server di un server d'utilità per distribuire la risoluzione della [zona DNS pubblica](#azure-services-dns-zone-configuration) del servizio di Azure.

Lo scenario seguente è adatto a una rete locale che dispone di un server di un server d'invio DNS in Azure, che a sua volta è responsabile della risoluzione di tutte le query DNS tramite un server di un server d'un server di un server di un server di un server di un server di un server di [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md). 

> [!NOTE]
> Questo scenario usa la zona DNS privata consigliata del database SQL di Azure.Per altri servizi, è possibile modificare il modello usando il riferimento seguente: [configurazione della zona DNS dei servizi di Azure](#azure-services-dns-zone-configuration).

Per configurare correttamente, sono necessarie le risorse seguenti:

- Rete locale
- Rete virtuale [connessa alla rete locale](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- Server d'inoltro DNS distribuito in Azure 
- DNS privato zone [privatelink.database.Windows.NET](../dns/private-dns-privatednszone.md)   con [record di tipo a](../dns/dns-zones-records.md#record-types)
- Informazioni sull'endpoint privato (nome del record FQDN e indirizzo IP privato)

Il diagramma seguente illustra la sequenza di risoluzione DNS da una rete locale che usa un server di destinazione DNS distribuito in Azure, in cui la risoluzione viene eseguita da una zona DNS privata [collegata a una rete virtuale](../dns/private-dns-virtual-network-links.md):

:::image type="content" source="media/private-endpoint-dns/on-premises-using-azure-dns.png" alt-text="Rete locale che usa DNS di Azure":::

Questa configurazione può essere estesa per una rete locale in cui è già presente una soluzione DNS. 
La soluzione DNS locale deve essere configurata per l'invio del traffico DNS a DNS di Azure tramite un server d'istruzione [condizionale](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) che fa riferimento al server di distribuzione DNS distribuito in Azure.

> [!NOTE]
> Questo scenario usa la zona DNS privata consigliata del database SQL di Azure. Per altri servizi, è possibile modificare il modello usando il riferimento seguente: [configurazione della zona DNS dei servizi di Azure](#azure-services-dns-zone-configuration)

Per configurare correttamente, sono necessarie le risorse seguenti:

- Rete locale con una soluzione DNS personalizzata 
- Rete virtuale [connessa alla rete locale](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- Server d'inoltro DNS distribuito in Azure
- DNS privato zone [privatelink.database.Windows.NET](../dns/private-dns-privatednszone.md)    con [record di tipo a](../dns/dns-zones-records.md#record-types)
- Informazioni sull'endpoint privato (nome del record FQDN e indirizzo IP privato)

Il diagramma seguente illustra la sequenza di risoluzione DNS da una rete locale che invia in modo condizionale il traffico DNS ad Azure, in cui la risoluzione viene eseguita da una zona DNS privata [collegata a una rete virtuale](../dns/private-dns-virtual-network-links.md).

> [!IMPORTANT]
> L'invio condizionale deve essere eseguito al server d'istruzione [pubblico di zona DNS pubblico](#azure-services-dns-zone-configuration)consigliato.Ad esempio:  `database.windows.net`   invece di **privatelink**. database.Windows.NET.

:::image type="content" source="media/private-endpoint-dns/on-premises-forwarding-to-azure.png" alt-text="Inoltro locale al servizio DNS di Azure":::

## <a name="virtual-network-and-on-premises-workloads-using-a-dns-forwarder"></a>Rete virtuale e carichi di lavoro locali usando un server di un server d'invio

Per un approccio generale adatto per i carichi di lavoro che richiedono l'accesso a un endpoint privato da reti virtuali e locali, è necessario usare un server di un server di un server di un server di servizi di dominio condiviso per eseguire la risoluzione della [zona DNS pubblica](#azure-services-dns-zone-configuration) del servizio di Azure distribuita in Azure.

Lo scenario seguente è appropriato per una rete locale che dispone di un server d'accesso DNS in Azure e per le reti virtuali che richiedono l'accesso all'endpoint privato che si trova in una rete Hub condivisa.  

Questo server d'inoltre DNS è responsabile della risoluzione di tutte le query DNS tramite un server di un server d'invio al servizio DNS fornito da Azure [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md).

> [!IMPORTANT]
> Per questa configurazione è necessaria una singola zona DNS privata. Tutte le connessioni client effettuate da reti virtuali locali e con [peering](../virtual-network/virtual-network-peering-overview.md) devono usare anche la stessa zona DNS privata.

> [!NOTE]
> Questo scenario usa la zona DNS privata consigliata del database SQL di Azure. Per altri servizi, è possibile modificare il modello usando il riferimento seguente: [configurazione della zona DNS dei servizi di Azure](#azure-services-dns-zone-configuration).

Per configurare correttamente, sono necessarie le risorse seguenti:

- Rete locale
- Rete virtuale [connessa alla rete locale](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- [Rete virtuale con peering](../virtual-network/virtual-network-peering-overview.md) 
- Server d'inoltro DNS distribuito in Azure
- DNS privato zone [privatelink.database.Windows.NET](../dns/private-dns-privatednszone.md)    con [record di tipo a](../dns/dns-zones-records.md#record-types)
- Informazioni sull'endpoint privato (nome del record FQDN e indirizzo IP privato)

Il diagramma seguente illustra la sequenza di risoluzione DNS da una rete locale e una rete virtuale che usa un server di destinazione DNS distribuito in Azure, in cui la risoluzione viene eseguita da una zona DNS privata [collegata a una rete virtuale](../dns/private-dns-virtual-network-links.md):

:::image type="content" source="media/private-endpoint-dns/hybrid-scenario.png" alt-text="Scenario ibrido":::

## <a name="next-steps"></a>Passaggi successivi
- [Informazioni sugli endpoint privati](private-endpoint-overview.md)
