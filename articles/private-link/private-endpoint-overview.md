---
title: Che cos'è un endpoint privato di Azure?
description: Informazioni sull'endpoint privato di Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: a4117acb2fada5c4422e177e9e6b84d7a0a51b69
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82129313"
---
# <a name="what-is-azure-private-endpoint"></a>Che cos'è l'endpoint privato di Azure?

L'endpoint privato di Azure è un'interfaccia di rete che connette privatamente e in modo sicuro a un servizio basato su collegamento privato di Azure. L'endpoint privato usa un indirizzo IP privato della VNet, in modo da portare il servizio in VNet. Il servizio potrebbe essere un servizio di Azure, ad esempio archiviazione di Azure, Azure Cosmos DB, SQL e così via, oppure il [servizio di collegamento privato](private-link-service-overview.md).
  
## <a name="private-endpoint-properties"></a>Proprietà endpoint privato 
 Un endpoint privato specifica le proprietà seguenti: 


|Proprietà  |Descrizione |
|---------|---------|
|Nome    |    Nome univoco all'interno del gruppo di risorse.      |
|Subnet    |  Subnet per la distribuzione e l'allocazione di indirizzi IP privati da una rete virtuale. Per i requisiti della subnet, vedere la sezione limitazioni di questo articolo.         |
|Risorsa collegamento privato    |   La risorsa di collegamento privato per la connessione usando l'ID risorsa o l'alias, dall'elenco dei tipi disponibili. Verrà generato un identificatore di rete univoco per tutto il traffico inviato a questa risorsa.       |
|Sottorisorsa di destinazione   |      Sottorisorsa da connettere. Ogni tipo di risorsa di collegamento privato ha opzioni diverse da selezionare in base alla preferenza.    |
|Metodo di approvazione della connessione    |  Automatico o manuale. In base alle autorizzazioni di controllo degli accessi in base al ruolo (RBAC), l'endpoint privato può essere approvato automaticamente. Se si tenta di connettersi a una risorsa di collegamento privato senza RBAC, usare il metodo Manual per consentire al proprietario della risorsa di approvare la connessione.        |
|Messaggio di richiesta     |  È possibile specificare un messaggio affinché le connessioni richieste vengano approvate manualmente. Questo messaggio può essere usato per identificare una richiesta specifica.        |
|Stato della connessione   |   Proprietà di sola lettura che specifica se l'endpoint privato è attivo. Per inviare il traffico è possibile usare solo endpoint privati in uno stato approvato. Stati aggiuntivi disponibili: <br>-**Approvato**: la connessione è stata approvata automaticamente o manualmente ed è pronta per essere usata.</br><br>-**In sospeso**: la connessione è stata creata manualmente ed è in attesa di approvazione da parte del proprietario della risorsa di collegamento privato.</br><br>-**Rifiutato**: la connessione è stata rifiutata dal proprietario della risorsa di collegamento privato.</br><br>-**Disconnesso**: la connessione è stata rimossa dal proprietario della risorsa di collegamento privato. L'endpoint privato diventa informativo e deve essere eliminato per la pulizia. </br>|

Ecco alcuni dettagli chiave sugli endpoint privati: 
- L'endpoint privato consente la connettività tra i consumer dalla stessa VNet, reti virtuali con peering di area, reti virtuali con peering globale e locale usando [VPN](https://azure.microsoft.com/services/vpn-gateway/) o [Express Route](https://azure.microsoft.com/services/expressroute/) e servizi basati su collegamento privato.
 
- Quando si crea un endpoint privato, viene creata anche un'interfaccia di rete per il ciclo di vita della risorsa. All'interfaccia viene assegnato un indirizzo IP privato dalla subnet mappato al servizio di collegamento privato.
 
- L'endpoint privato deve essere distribuito nella stessa area della rete virtuale. 
 
- La risorsa collegamento privato può essere distribuita in un'area diversa rispetto alla rete virtuale e all'endpoint privato.
 
- È possibile creare più endpoint privati usando la stessa risorsa di collegamento privato. Per una singola rete che usa una configurazione del server DNS comune, la procedura consigliata consiste nell'usare un singolo endpoint privato per una determinata risorsa di collegamento privato per evitare voci duplicate o conflitti nella risoluzione DNS. 
 
- È possibile creare più endpoint privati nella stessa subnet o in subnet diverse all'interno della stessa rete virtuale. Sono previsti limiti per il numero di endpoint privati che è possibile creare in una sottoscrizione. Per informazioni dettagliate, vedere [limiti di Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


 
## <a name="private-link-resource"></a>Risorsa collegamento privato 
Una risorsa di collegamento privato è la destinazione di un endpoint privato specificato. Di seguito è riportato un elenco dei tipi di risorse di collegamento privato disponibili: 
 
|Nome della risorsa di collegamento privato  |Tipo di risorsa   |Le sottorisorse  |
|---------|---------|---------|
|**Servizio di collegamento privato** (servizio personalizzato)   |  Microsoft. Network/privateLinkServices       | empty |
|**Database SQL di Azure** | Microsoft.Sql/servers    |  SQL Server (sqlServer)        |
|**Azure Synapse Analytics** | Microsoft.Sql/servers    |  SQL Server (sqlServer)        | 
|**Archiviazione di Azure**  | Microsoft.Storage/storageAccounts    |  BLOB (BLOB, blob_secondary)<BR> Tabella (tabella, table_secondary)<BR> Coda (coda, queue_secondary)<BR> File (file, file_secondary)<BR> Web (Web, web_secondary)        |
|**Azure Data Lake Storage Gen2**  | Microsoft.Storage/storageAccounts    |  BLOB (BLOB, blob_secondary)<BR> Data Lake file System Gen2 (DFS, dfs_secondary)       |
|**Azure Cosmos DB** | Microsoft. AzureCosmosDB/databaseAccounts    | SQL, MongoDB, Cassandra, Gremlin, tabella|
|**Database di Azure per PostgreSQL-server singolo** | Microsoft.DBforPostgreSQL/servers    | postgresqlServer |
|**Database di Azure per MySQL** | Microsoft.DBforMySQL/servers    | mysqlServer |
|**Database di Azure per MariaDB** | Microsoft.DBforMariaDB/servers    | mariadbServer |
|**Insieme di credenziali chiave Azure** | Microsoft.KeyVault/vaults    | insieme di credenziali |
|**Servizio Azure Kubernetes - API Kubernetes** | Microsoft.ContainerService/managedClusters    | managedCluster |
|**Ricerca di Azure** | Microsoft. search/searchService| searchService|  
|**Registro Azure Container** | Microsoft.ContainerRegistry/registries    | Registro di sistema |
|**Configurazione app di Azure** | Microsoft. Appconfiguration/configurationStores    | configurationStore |
|**Azure Backup** | Microsoft.RecoveryServices/vaults    | insieme di credenziali |
|**Hub eventi di Azure** | Microsoft.EventHub/namespaces    | namespace |
|**Bus di servizio di Azure** | Microsoft.ServiceBus/namespaces | namespace |
|**Servizio di inoltro di Azure** | Microsoft.Relay/namespaces | namespace |
|**Griglia di eventi di Azure** | Microsoft.EventGrid/topics    | argomento |
|**Griglia di eventi di Azure** | Microsoft. EventGrid/domini    | dominio |
|**App Web di Azure** | Microsoft.Web/sites    | site |
|**Azure Machine Learning** | Microsoft.MachineLearningServices/workspaces    | area di lavoro |
  
 
## <a name="network-security-of-private-endpoints"></a>Sicurezza di rete degli endpoint privati 
Quando si usano endpoint privati per i servizi di Azure, il traffico viene protetto a una risorsa di collegamento privato specifica. La piattaforma esegue un controllo di accesso per convalidare le connessioni di rete che raggiungono solo la risorsa di collegamento privato specificata. Per accedere ad altre risorse all'interno dello stesso servizio di Azure, sono necessari endpoint privati aggiuntivi. 
 
È possibile bloccare completamente i carichi di lavoro dall'accesso agli endpoint pubblici per connettersi a un servizio di Azure supportato. Questo controllo offre un livello di sicurezza di rete aggiuntivo per le risorse fornendo una protezione Exfiltration incorporata che impedisce l'accesso ad altre risorse ospitate nello stesso servizio di Azure. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>Accesso a una risorsa di collegamento privato mediante il flusso di lavoro di approvazione 
È possibile connettersi a una risorsa di collegamento privato usando i seguenti metodi di approvazione della connessione:
- Approvato **automaticamente** quando si è proprietari o si dispone dell'autorizzazione per la risorsa di collegamento privato specifica. L'autorizzazione richiesta è basata sul tipo di risorsa collegamento privato nel formato seguente: Microsoft. \<> del Provider/<resource_type>/privateendpointconnectionapproval/Action
- Richiesta **manuale** quando non si dispone dell'autorizzazione necessaria e si desidera richiedere l'accesso. Verrà avviato un flusso di lavoro di approvazione. L'endpoint privato e la successiva connessione verranno creati con lo stato "In sospeso". Il proprietario della risorsa di collegamento privato ha la responsabilità di approvare la connessione. Una volta approvata, l'endpoint privato è abilitato per l'invio del traffico normalmente, come illustrato nel seguente diagramma del flusso di lavoro di approvazione.  

![approvazione del flusso di lavoro](media/private-endpoint-overview/private-link-paas-workflow.png)
 
Il proprietario della risorsa di collegamento privato può eseguire le azioni seguenti tramite una connessione all'endpoint privato: 
- Esaminare tutti i dettagli delle connessioni agli endpoint privati. 
- Approva una connessione all'endpoint privato. L'endpoint privato corrispondente verrà abilitato per l'invio del traffico alla risorsa di collegamento privato. 
- Rifiutare una connessione all'endpoint privato. L'endpoint privato corrispondente verrà aggiornato per riflettere lo stato.
- Eliminare una connessione all'endpoint privato in qualsiasi stato. L'endpoint privato corrispondente verrà aggiornato con uno stato disconnesso per riflettere l'azione. il proprietario dell'endpoint privato può solo eliminare la risorsa in questo momento. 
 
> [!NOTE]
> Solo un endpoint privato in stato approvato può inviare il traffico a una risorsa di collegamento privato specificata. 

### <a name="connecting-using-alias"></a>Connessione tramite alias
Alias è un moniker univoco generato quando il proprietario del servizio crea il servizio di collegamento privato dietro un servizio di bilanciamento del carico standard. Il proprietario del servizio può condividere questo alias con i relativi consumer offline. I consumer possono richiedere una connessione al servizio di collegamento privato usando l'URI della risorsa o l'alias. Se si desidera connettersi tramite alias, è necessario creare un endpoint privato utilizzando il metodo di approvazione della connessione manuale. Per usare il metodo di approvazione della connessione manuale, impostare il parametro della richiesta manuale su true durante la creazione del flusso di endpoint privato. Per informazioni dettagliate, vedere [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint?view=azps-2.6.0) e [AZ network private-endpoint create](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) . 

## <a name="dns-configuration"></a>Configurazione del DNS 
Quando ci si connette a una risorsa di collegamento privato usando un nome di dominio completo (FQDN) come parte della stringa di connessione, è importante configurare correttamente le impostazioni DNS per la risoluzione nell'indirizzo IP privato allocato. I servizi di Azure esistenti potrebbero avere già una configurazione DNS da usare per la connessione tramite un endpoint pubblico. È necessario eseguire l'override di questo per connettersi usando l'endpoint privato. 
 
L'interfaccia di rete associata all'endpoint privato contiene il set completo di informazioni necessarie per configurare il DNS, inclusi FQDN e indirizzi IP privati allocati per una determinata risorsa di collegamento privato. 

Per informazioni dettagliate sulle procedure consigliate e sui consigli per configurare il DNS per gli endpoint privati, vedere l' [articolo configurazione di DNS per endpoint privati](private-endpoint-dns.md).



 
## <a name="limitations"></a>Limitazioni
 
La tabella seguente include un elenco di limitazioni note quando si usano gli endpoint privati: 


|Limitazione |Descrizione |Strategia di riduzione del rischio  |
|---------|---------|---------|
|Le regole del gruppo di sicurezza di rete (NSG) e le route definite dall'utente non si applicano all'endpoint privato    |NSG non è supportato in endpoint privati. Mentre le subnet contenenti l'endpoint privato possono avere NSG associate, le regole non saranno valide per il traffico elaborato dall'endpoint privato. Per distribuire endpoint privati in una subnet, è necessario che l' [applicazione di criteri di rete sia disabilitata](disable-private-endpoint-network-policy.md) . NSG viene ancora applicato ad altri carichi di lavoro ospitati nella stessa subnet. Le route in qualsiasi subnet client utilizzeranno un prefisso/32. la modifica del comportamento di routing predefinito richiede un UDR simile  | Controllare il traffico usando le regole NSG per il traffico in uscita nei client di origine. Distribuire le singole route con prefisso/32 per eseguire l'override delle route degli endpoint privati. I log di flusso e le informazioni di monitoraggio NSG per le connessioni in uscita sono ancora supportati e possono essere usati        |


## <a name="next-steps"></a>Passaggi successivi
- [Creare un endpoint privato per il server di database SQL con il portale ](create-private-endpoint-portal.md)
- [Creare un endpoint privato per il server di database SQL con PowerShell ](create-private-endpoint-powershell.md)
- [Creare un endpoint privato per il server di database SQL con l'interfaccia della riga di comando ](create-private-endpoint-cli.md)
- [Creare un endpoint privato per l'account di archiviazione con il portale ](create-private-endpoint-storage-portal.md)
- [Creare un endpoint privato per l'account Azure Cosmos con il portale ](../cosmos-db/how-to-configure-private-endpoints.md)
- [Creare un servizio Collegamento privato con Azure PowerShell](create-private-link-service-powershell.md)
- [Creare il proprio collegamento privato per database di Azure per PostgreSQL-server singolo con il portale](../postgresql/howto-configure-privatelink-portal.md)
- [Creare il proprio collegamento privato per database di Azure per PostgreSQL-server singolo con l'interfaccia della riga di comando](../postgresql/howto-configure-privatelink-cli.md)
- [Creare il proprio collegamento privato per database di Azure per MySQL con il portale](../mysql/howto-configure-privatelink-portal.md)
- [Creare il proprio collegamento privato per database di Azure per MySQL usando l'interfaccia della riga di comando](../mysql/howto-configure-privatelink-cli.md)
- [Creare il proprio collegamento privato per database di Azure per MariaDB usando il portale](../mariadb/howto-configure-privatelink-portal.md)
- [Creare il proprio collegamento privato per il database di Azure per MariaDB usando l'interfaccia della riga di comando](../mariadb/howto-configure-privatelink-cli.md)
