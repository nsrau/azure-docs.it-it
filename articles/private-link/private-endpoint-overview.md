---
title: Che cos'è un endpoint privato di Azure?
description: Informazioni sull'endpoint privato di AzureLearn about Azure Private Endpoint
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: b8f55b0693b7ffdf10516cfb19b67ccc5a94dc5a
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80742901"
---
# <a name="what-is-azure-private-endpoint"></a>Che cos'è l'endpoint privato di Azure?

L'endpoint privato di Azure è un'interfaccia di rete che connette privatamente e in modo sicuro a un servizio basato su collegamento privato di Azure. L'endpoint privato usa un indirizzo IP privato dalla rete virtuale, portando in modo efficace il servizio nella rete virtuale. Il servizio potrebbe essere un servizio di Azure, ad esempio Archiviazione di Azure, Database Cosmos di Azure, SQL e così via, o il proprio [servizio di collegamento privato](private-link-service-overview.md).
  
## <a name="private-endpoint-properties"></a>Proprietà dell'endpoint privatoPrivate Endpoint properties 
 Un endpoint privato specifica le proprietà seguenti:A Private Endpoint specifies the following properties: 


|Proprietà  |Descrizione |
|---------|---------|
|Nome    |    Un nome univoco all'interno del gruppo di risorse.      |
|Subnet    |  Subnet per distribuire e allocare indirizzi IP privati da una rete virtuale. Per i requisiti relativi alle subnet, vedere la sezione Limitazioni in questo articolo.         |
|Risorsa di collegamento privato    |   Risorsa di collegamento privato da connettere tramite l'ID risorsa o l'alias, dall'elenco dei tipi disponibili. Verrà generato un identificatore di rete univoco per tutto il traffico inviato a questa risorsa.       |
|Sottorisorsa di destinazione   |      Risorsa secondaria da connettere. Ogni tipo di risorsa a collegamento privato ha diverse opzioni da selezionare in base alla preferenza.    |
|Metodo di approvazione della connessione    |  Automatico o manuale. In base alle autorizzazioni di controllo degli accessi in base al ruolo, l'endpoint privato può essere approvato automaticamente. Se si tenta di connettersi a una risorsa di collegamento privato senza controllo degli accessi in base al ruolo, utilizzare il metodo manuale per consentire al proprietario della risorsa di approvare la connessione.        |
|Messaggio di richiesta     |  È possibile specificare un messaggio per le connessioni richieste da approvare manualmente. Questo messaggio può essere utilizzato per identificare una richiesta specifica.        |
|Stato della connessione   |   Proprietà di sola lettura che specifica se l'endpoint privato è attivo. Solo gli endpoint privati in uno stato approvato possono essere usati per inviare traffico. Ulteriori stati disponibili: <br>-**Approvato**: La connessione è stata approvata automaticamente o manualmente ed è pronta per l'uso.</br><br>-**In sospeso:** la connessione è stata creata manualmente ed è in attesa di approvazione da parte del proprietario della risorsa collegamento privato.</br><br>-**Rifiutato:** la connessione è stata rifiutata dal proprietario della risorsa collegamento privato.</br><br>-**Disconnesso:** la connessione è stata rimossa dal proprietario della risorsa collegamento privato. L'endpoint privato diventa informativo e deve essere eliminato per la pulizia. </br>|

Di seguito sono riportati alcuni dettagli chiave sugli endpoint privati:Here are some key details about private endpoints: 
- L'endpoint privato consente la connettività tra i consumer dalla stessa rete virtuale, le reti virtuali con peering regionale, le reti virtuali con peering globale e in locale tramite [VPN](https://azure.microsoft.com/services/vpn-gateway/) o [Express Route](https://azure.microsoft.com/services/expressroute/) e i servizi basati su Private Link.
 
- Quando si crea un endpoint privato, viene creata anche un'interfaccia di rete per il ciclo di vita della risorsa. All'interfaccia viene assegnato un indirizzo IP privato dalla subnet che esegue il mapping al servizio di collegamento privato.
 
- L'endpoint privato deve essere distribuito nella stessa area della rete virtuale. 
 
- La risorsa di collegamento privato può essere distribuita in un'area diversa rispetto alla rete virtuale e all'endpoint privato.
 
- È possibile creare più endpoint privati usando la stessa risorsa di collegamento privato. Per una singola rete che utilizza una configurazione server DNS comune, è consigliabile utilizzare un singolo endpoint privato per una determinata risorsa di collegamento privato per evitare voci duplicate o conflitti nella risoluzione DNS. 
 
- È possibile creare più endpoint privati nella stessa subnet o in subnet diverse all'interno della stessa rete virtuale. Esistono limiti al numero di endpoint privati che è possibile creare in una sottoscrizione. Per informazioni dettagliate, vedere Limiti di [Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


 
## <a name="private-link-resource"></a>Risorsa collegamento privato 
Una risorsa di collegamento privato è la destinazione di destinazione di un determinato endpoint privato. Di seguito è riportato un elenco dei tipi di risorse di collegamento privato disponibili:The following is a list of available private link resource types: 
 
|Nome risorsa collegamento privato  |Tipo di risorsa   |Sottorisorse  |
|---------|---------|---------|
|**Servizio Private Link** (Il tuo servizio)   |  Microsoft.Network/privateLinkServices       | empty |
|**Database SQL di Azure** | Microsoft.Sql/servers    |  Sql Server (sqlServer)        |
|**Azure Synapse Analytics** | Microsoft.Sql/servers    |  Sql Server (sqlServer)        | 
|**Archiviazione di AzureAzure Storage**  | Microsoft.Storage/storageAccounts    |  BLOB (BLOB, blob_secondary)<BR> Tabella (tabella, table_secondary)<BR> Coda (coda, queue_secondary)<BR> File (file, file_secondary)<BR> Web (web, web_secondary)        |
|**Azure Data Lake Storage Gen2**  | Microsoft.Storage/storageAccounts    |  BLOB (BLOB, blob_secondary)<BR> Data Lake File System Gen2 (dfs, dfs_secondary)       |
|**Azure Cosmos DB** | Microsoft.AzureCosmosDB/databaseAccounts | Sql, MongoDB, Cassandra, Gremlin, Tabella|
|**Database di Azure per PostgreSQL -Singolo serverAzure Database for PostgreSQL -Single server** | Microsoft.DBforPostgreSQL/servers   | postgresqlServer |
|**Database di Azure per MySQL** | Microsoft.DBforMySQL/servers    | MysqlServer (informazioni in lingua inglese) |
|**Database di Azure per MariaDB** | Microsoft.DBforMariaDB/servers    | mariadbServer (server) |
|**Archivio chiavi di AzureAzure Key Vault** | Microsoft.KeyVault/vaults    | insieme di credenziali |
|**Azure Kubernetes Service - Kubernetes API** | Microsoft.ContainerService/managedClusters | managedCluster |
|**Ricerca di Azure** | Microsoft.Search/searchService| searchService (servizio di ricerca)|  
|**Registro di sistema del contenitore di AzureAzure Container** | Microsoft.ContainerRegistry/registries  | Registro di sistema |
|**Configurazione app di Azure** | Microsoft.Appconfiguration/configurationStores   | configurationStore |
|**Backup di Azure** | Microsoft.RecoveryServices/vaults   | insieme di credenziali |
|**Hub eventi di AzureAzure Event Hub** | Microsoft.EventHub/namespaces    | namespace |
|**Bus di servizio di Azure** | Microsoft.ServiceBus/namespaces | namespace |
|**Servizio di inoltro di Azure** | Microsoft.Relay/namespaces | namespace |
|**Griglia di eventi di AzureAzure Event Grid** | Microsoft.EventGrid/topics  | argomento |
|**Griglia di eventi di AzureAzure Event Grid** | Microsoft.EventGrid/domini | dominio |
|**App Web di AzureAzure WebApps** | Microsoft.Web/sites    | site |
|**Azure Machine Learning** | Microsoft.MachineLearningServices/workspaces  | area di lavoro |
  
 
## <a name="network-security-of-private-endpoints"></a>Sicurezza di rete degli endpoint privatiNetwork security of private endpoints 
Quando si usano endpoint privati per i servizi di Azure, il traffico è protetto a una risorsa di collegamento privato specifica. La piattaforma esegue un controllo di accesso per convalidare le connessioni di rete raggiungendo solo la risorsa di collegamento privato specificata. Per accedere a risorse aggiuntive all'interno dello stesso servizio di Azure, sono necessari endpoint privati aggiuntivi. 
 
È possibile bloccare completamente i carichi di lavoro dall'accesso agli endpoint pubblici per connettersi a un servizio di Azure supportato. Questo controllo fornisce un ulteriore livello di sicurezza di rete alle risorse fornendo una protezione di esfiltrazione incorporata che impedisce l'accesso ad altre risorse ospitate nello stesso servizio di Azure.This control provides an additional network security layer to your resources by providing a built-in exfiltration protection that prevents access to other resources hosted on the same Azure service. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>Accesso a una risorsa collegamento privato tramite il flusso di lavoro di approvazione 
È possibile connettersi a una risorsa collegamento privato utilizzando i seguenti metodi di approvazione della connessione:
- **Approvato automaticamente** quando si è proprietari o si dispone dell'autorizzazione per la risorsa collegamento privato specifico. The permission required is based on the private link resource type in the following format: Microsoft. \<> del provider/<resource_type>/privateEndpointConnectionApproval/azione
- **Richiesta manuale** quando non si dispone dell'autorizzazione necessaria e si desidera richiedere l'accesso. Verrà avviato un flusso di lavoro di approvazione. L'endpoint privato e la successiva connessione verranno creati con lo stato "In sospeso". Il proprietario della risorsa di collegamento privato ha la responsabilità di approvare la connessione. Dopo l'approvazione, l'endpoint privato è abilitato all'invio del traffico normalmente, come illustrato nel diagramma del flusso di lavoro di approvazione seguente.  

![approvazione del flusso di lavoro](media/private-endpoint-overview/private-link-paas-workflow.png)
 
Il proprietario della risorsa collegamento privato può eseguire le azioni seguenti su una connessione endpoint privato:The private link resource owner can perform the following actions over a private endpoint connection: 
- Esaminare tutti i dettagli delle connessioni endpoint privati. 
- Approvare una connessione endpoint privata. L'endpoint privato corrispondente verrà abilitato per l'invio del traffico alla risorsa collegamento privato. 
- Rifiutare una connessione endpoint privata. L'endpoint privato corrispondente verrà aggiornato per riflettere lo stato.
- Eliminare una connessione endpoint privata in qualsiasi stato. L'endpoint privato corrispondente verrà aggiornato con uno stato disconnesso per riflettere l'azione, il proprietario dell'endpoint privato può eliminare la risorsa solo a questo punto. 
 
> [!NOTE]
> Solo un endpoint privato in uno stato approvato può inviare traffico a una determinata risorsa di collegamento privato. 

### <a name="connecting-using-alias"></a>Connessione tramite Alias
Alias è un moniker univoco che viene generato quando il proprietario del servizio crea il servizio di collegamento privato dietro un servizio di bilanciamento del carico standard. Il proprietario del servizio può condividere questo alias con i consumer offline. I consumer possono richiedere una connessione al servizio di collegamento privato usando l'URI della risorsa o l'alias. Se si desidera connettersi tramite Alias, è necessario creare un endpoint privato utilizzando il metodo di approvazione della connessione manuale. Per l'utilizzo del metodo di approvazione della connessione manuale, impostare il parametro di richiesta manuale su true durante il flusso di creazione dell'endpoint privato. Per informazioni dettagliate, vedere [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint?view=azps-2.6.0) e [az network private-endpoint create.](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) 

## <a name="dns-configuration"></a>Configurazione del DNS 
Quando ci si connette a una risorsa di collegamento privato utilizzando un nome di dominio completo (FQDN) come parte della stringa di connessione, è importante configurare correttamente le impostazioni DNS per risolvere l'indirizzo IP privato allocato. I servizi di Azure esistenti potrebbero già avere una configurazione DNS da usare per la connessione tramite un endpoint pubblico. Questo deve essere sottoposto a override per connettersi utilizzando l'endpoint privato. 
 
L'interfaccia di rete associata all'endpoint privato contiene il set completo di informazioni necessarie per configurare il DNS, inclusi gli indirizzi FQDN e IP privati allocati per una determinata risorsa collegamento privato. 
 
È possibile utilizzare le opzioni seguenti per configurare le impostazioni DNS per gli endpoint privati: 
- **Utilizzare il file Host (consigliato solo per il test)**. È possibile utilizzare il file host in una macchina virtuale per eseguire l'override del DNS.  
- **Utilizzare una zona DNS privata**. È possibile utilizzare le zone DNS private per eseguire l'override della risoluzione DNS per un determinato endpoint privato. Una zona DNS privata può essere collegata alla rete virtuale per risolvere domini specifici.
- **Utilizzare il server DNS personalizzato**. È possibile utilizzare il proprio server DNS per ignorare la risoluzione DNS per una determinata risorsa di collegamento privato. Se il [server DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) è ospitato in una rete virtuale, è possibile creare una regola di inoltro DNS per utilizzare una zona DNS privata per semplificare la configurazione per tutte le risorse di collegamento private.
 
> [!IMPORTANT]
> Non è consigliabile eseguire l'override di una zona utilizzata attivamente per risolvere gli endpoint pubblici. Le connessioni alle risorse non saranno in grado di risolversi correttamente senza l'inoltro DNS al DNS pubblico. Per evitare problemi, creare un nome di dominio diverso o seguire il nome suggerito per ogni servizio di seguito. 
 
Per i servizi di Azure, usare i nomi delle zone consigliate come descritto nella tabella seguente:For Azure services, use the recommended zone names as described in the following table:

|Tipo di risorsa Collegamento privatoPrivate Link resource type   |Sottorisorsa  |Nome zona  |
|---------|---------|---------|
|DB SQL (Microsoft.Sql/servers)    |  Sql Server (sqlServer)        |   privatelink.database.windows.net       |
|Analisi synapse di Azure (Microsoft.Sql/servers)Azure Synapse Analytics (Microsoft.Sql/servers)    |  Sql Server (sqlServer)        | privatelink.database.windows.net |
|Account di archiviazione (Microsoft.Storage/storageAccounts)    |  BLOB (BLOB, blob_secondary)        |    privatelink.blob.core.windows.net      |
|Account di archiviazione (Microsoft.Storage/storageAccounts)    |    Tabella (tabella, table_secondary)      |   privatelink.table.core.windows.net       |
|Account di archiviazione (Microsoft.Storage/storageAccounts)    |    Coda (coda, queue_secondary)     |   privatelink.queue.core.windows.net       |
|Account di archiviazione (Microsoft.Storage/storageAccounts)   |    File (file, file_secondary)      |    privatelink.file.core.windows.net      |
|Account di archiviazione (Microsoft.Storage/storageAccounts)     |  Web (web, web_secondary)        |    privatelink.web.core.windows.net      |
|Data Lake File System Gen2 (Microsoft.Storage/storageAccounts)  |  Data Lake File System Gen2 (dfs, dfs_secondary)        |     privatelink.dfs.core.windows.net     |
|Database Cosmos di Azure (Microsoft.AzureCosmosDB/databaseAccounts)Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|SQL |privatelink.documents.azure.com|
|Database Cosmos di Azure (Microsoft.AzureCosmosDB/databaseAccounts)Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|MongoDB |privatelink.mongo.cosmos.azure.com|
|Database Cosmos di Azure (Microsoft.AzureCosmosDB/databaseAccounts)Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|Cassandra|privatelink.cassandra.cosmos.azure.com|
|Database Cosmos di Azure (Microsoft.AzureCosmosDB/databaseAccounts)Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|Gremlin |privatelink.gremlin.cosmos.azure.com|
|Database Cosmos di Azure (Microsoft.AzureCosmosDB/databaseAccounts)Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|Tabella|privatelink.table.cosmos.azure.com|
|Database di Azure per PostgreSQL - Server singolo (Microsoft.DBforPostgreSQL/server)Azure Database for PostgreSQL - Single server (Microsoft.DBforPostgreSQL/servers)|postgresqlServer|privatelink.postgres.database.azure.com|
|Database di Azure per MySQL (Microsoft.DBforMySQL/servers)Azure Database for MySQL (Microsoft.DBforMySQL/servers)|MysqlServer (informazioni in lingua inglese)|privatelink.mysql.database.azure.com|
|Database di Azure per MariaDB (Microsoft.DBforMariaDB/servers)|mariadbServer (server)|privatelink.mariadb.database.azure.com|
|Insieme di credenziali delle chiavi di Azure (Microsoft.KeyVault/vaults)Azure Key Vault (Microsoft.KeyVault/vaults)|insieme di credenziali|privatelink.vaultcore.azure.net|
|Servizio Azure Kubernetes - API Kubernetes (Microsoft.ContainerService/managedClusters) | managedCluster | .guid.privatelink. <region>File con estensione azmk8s.io|
|Ricerca di Azure (Microsoft.Search/searchServices)|searchService (servizio di ricerca)|privatelink.search.windows.net|   
|Registro contenitori di Azure (Microsoft.ContainerRegistry/registri)Azure Container Registry (Microsoft.ContainerRegistry/registries) | Registro di sistema | privatelink.azurecr.io |
|Configurazione delle app di Azure (Microsoft.Appconfiguration/configurationStores)Azure App Configuration (Microsoft.Appconfiguration/configurationStores)| configurationStore | privatelink.azconfig.io|
|Backup di Azure (Microsoft.RecoveryServices/vaults)| insieme di credenziali |privatelink. .backup.windowsazure.com|
|Hub eventi di Azure (Microsoft.EventHub/spazi dei nomi)Azure Event Hub (Microsoft.EventHub/namespaces)| namespace |privatelink.servicebus.windows.net|
|Bus di servizio di Azure (Microsoft.ServiceBus/spazi dei nomi)Azure Service Bus (Microsoft.ServiceBus/namespaces) | namespace |privatelink.servicebus.windows.net|
|Inoltro di Azure (Microsoft.Relay/namespace)Azure Relay (Microsoft.Relay/namespaces) | namespace |privatelink.servicebus.windows.net|
|Griglia di eventi di Azure (Microsoft.EventGrid/topics)Azure Event Grid (Microsoft.EventGrid/topics)   | argomento | Argomento. .region.privatelink.eventgrid.azure.net|
|Griglia di eventi di Azure (Microsoft.EventGrid/domains) | dominio | Dominio. .region.privatelink.eventgrid.azure.net |
|App Web di Azure (Microsoft.Web/sites)    | site | privatelink.azurewebsites.net |
|Azure Machine Learning(Microsoft.MachineLearningServices/workspaces)   | area di lavoro | privatelink.api.azureml.ms |
 
Azure creerà un record DNS con nome canonico (CNAME) nel DNS pubblico per reindirizzare la risoluzione ai nomi di dominio suggeriti. Sarà possibile sostituire la risoluzione con l'indirizzo IP privato degli endpoint privati. 
 
Non è necessario che le applicazioni modifichino l'URL della connessione. Quando si tenta di risolvere utilizzando un DNS pubblico, il server DNS verrà risolto negli endpoint privati. Il processo non influisce sulle applicazioni. 
 
## <a name="limitations"></a>Limitazioni
 
La tabella seguente include un elenco di limitazioni note quando si usano endpoint privati:The following table includes a list of known limitations when using private endpoints: 


|Limitazione |Descrizione |Strategia di riduzione del rischio  |
|---------|---------|---------|
|Le regole del gruppo ns (NSG) di rete e le route definite dall'utente non si applicano all'endpoint privatoNetwork Security Group (NSG) rules (NSG) Rules (NSG) Routes do not apply to Private Endpoint    |Il gruppo di sicurezza di rete non è supportato negli endpoint privati. Mentre alle subnet contenenti l'endpoint privato possono essere associate un gruppo di sicurezza di rete, le regole non saranno valide sul traffico elaborato dall'endpoint privato. È necessario che [l'imposizione](disable-private-endpoint-network-policy.md) dei criteri di rete sia disabilitata per distribuire gli endpoint privati in una subnet. Il gruppo di sicurezza di rete viene comunque applicato ad altri carichi di lavoro ospitati nella stessa subnet. Le route su qualsiasi subnet client utilizzeranno un prefisso /32, la modifica del comportamento di routing predefinito richiede un UDR simile  | Controllare il traffico usando le regole del gruppo di sicurezza di rete per il traffico in uscita nei client di origine. Distribuire singole route con prefisso /32 per eseguire l'override delle route dell'endpoint privato. I registri di flusso del gruppo di sicurezza di rete e le informazioni di monitoraggio per le connessioni in uscita sono ancora supportati e possono essere utilizzati        |


## <a name="next-steps"></a>Passaggi successivi
- [Creare un endpoint privato per il server di database SQL con il portale ](create-private-endpoint-portal.md)
- [Creare un endpoint privato per il server di database SQL con PowerShell ](create-private-endpoint-powershell.md)
- [Creare un endpoint privato per il server di database SQL con l'interfaccia della riga di comando ](create-private-endpoint-cli.md)
- [Creare un endpoint privato per l'account di archiviazione con il portale ](create-private-endpoint-storage-portal.md)
- [Creare un endpoint privato per l'account Azure Cosmos con il portale ](../cosmos-db/how-to-configure-private-endpoints.md)
- [Creare un servizio Collegamento privato con Azure PowerShell](create-private-link-service-powershell.md)
- [Creare il proprio collegamento privato per il database di Azure per PostgreSQL - singolo server tramite il portaleCreate your own Private Link for Azure Database for PostgreSQL - Single server using Portal](../postgresql/howto-configure-privatelink-portal.md)
- [Creare il proprio collegamento privato per il database di Azure per PostgreSQL - singolo server tramite l'interfaccia della riga di comandoCreate your own Private Link for Azure Database for PostgreSQL - Single server using CLI](../postgresql/howto-configure-privatelink-cli.md)
- [Creare il proprio collegamento privato per il database di Azure per MySQL usando il portaleCreate your own Private Link for Azure Database for MySQL using Portal](../mysql/howto-configure-privatelink-portal.md)
- [Creare il proprio collegamento privato per il database di Azure per MySQL usando l'interfaccia della riga di comandoCreate your own Private Link for Azure Database for MySQL using CLI](../mysql/howto-configure-privatelink-cli.md)
- [Creare il proprio collegamento privato per il database di Azure per MariaDB usando il portaleCreate your own Private Link for Azure Database for MariaDB using Portal](../mariadb/howto-configure-privatelink-portal.md)
- [Creare il proprio collegamento privato per il database di Azure per MariaDB usando l'interfaccia della riga di comandoCreate your own Private Link for Azure Database for MariaDB using CLI](../mariadb/howto-configure-privatelink-cli.md)
