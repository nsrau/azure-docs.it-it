---
title: Che cos'è l'endpoint privato di Azure?
description: Informazioni sull'endpoint privato di Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: allensu
ms.openlocfilehash: f4e5bbdc50d99eca774dc5b96657ac8bfdebca17
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85414148"
---
# <a name="what-is-azure-private-endpoint"></a>Che cos'è l'endpoint privato di Azure?

L'endpoint privato di Azure è un'interfaccia di rete che connette privatamente e in modo sicuro a un servizio basato su collegamento privato di Azure. L'endpoint privato usa un indirizzo IP privato della rete virtuale, introducendo efficacemente il servizio nella rete virtuale. Il servizio può essere un servizio di Azure, ad esempio Archiviazione di Azure, Azure Cosmos DB, SQL e così via, oppure il [servizio di collegamento privato](private-link-service-overview.md).
  
## <a name="private-endpoint-properties"></a>Proprietà dell'endpoint privato 
 Per un endpoint privato sono specificate le proprietà seguenti: 


|Proprietà  |Descrizione |
|---------|---------|
|Nome    |    Nome univoco all'interno del gruppo di risorse.      |
|Subnet    |  Subnet per la distribuzione e l'allocazione di indirizzi IP privati da una rete virtuale. Per i requisiti della subnet, vedere la sezione Limitazioni in questo articolo.         |
|Risorsa di collegamento privato    |   Risorsa di collegamento privato per la connessione tramite l'ID o l'alias risorsa dall'elenco dei tipi disponibili. Viene generato un identificatore di rete univoco per tutto il traffico inviato a questa risorsa.       |
|Sottorisorsa di destinazione   |      Sottorisorsa a cui connettersi. A ogni tipo di risorsa di collegamento privato sono associate opzioni diverse da selezionare in base alla preferenza.    |
|Metodo di approvazione della connessione    |  Automatico o manuale. A seconda delle autorizzazioni di controllo degli accessi in base al ruolo, l'endpoint privato può essere approvato automaticamente. Se si tenta di connettersi a una risorsa di collegamento privato senza il controllo degli accessi in base al ruolo, usare il metodo manuale per consentire al proprietario della risorsa di approvare la connessione.        |
|Messaggio di richiesta     |  È possibile specificare un messaggio affinché le connessioni richieste vengano approvate manualmente. Questo messaggio può essere usato per identificare una richiesta specifica.        |
|Stato della connessione   |   Proprietà di sola lettura che specifica se l'endpoint privato è attivo. Per inviare il traffico è possibile usare solo endpoint privati in uno stato approvato. Stati aggiuntivi disponibili: <br>-**Approvato**. La connessione è stata approvata automaticamente o manualmente ed è pronta per essere usata.</br><br>-**In sospeso**. La connessione viene creata manualmente ed è in attesa di approvazione dal proprietario della risorsa di collegamento privato.</br><br>-**Rifiutato**. La connessione è stata rifiutata dal proprietario della risorsa di collegamento privato.</br><br>-**Disconnesso**. La connessione è stata rimossa dal proprietario della risorsa di collegamento privato. L'endpoint privato diventa informativo e deve essere eliminato a scopo di pulizia. </br>|

Di seguito vengono indicati alcuni dettagli importanti sugli endpoint privati: 
- L'endpoint privato consente la connettività tra i consumer dalla stessa rete privata virtuale, reti virtuali con peering di area, reti virtuali con peering globale e locale usando [VPN](https://azure.microsoft.com/services/vpn-gateway/) or [Express Route](https://azure.microsoft.com/services/expressroute/) e servizi basati su collegamento privato.
 
- Le connessioni di rete possono essere iniziate solo dai client che si connettono all'endpoint privato, mentre i provider di servizi non dispongono di alcuna configurazione di routing per iniziare le connessioni verso i consumer del servizio. Le connessioni possono essere stabilite solo in un'unica direzione.

- Quando si crea un endpoint privato, viene creata anche un'interfaccia di rete di sola lettura per il ciclo di vita della risorsa. All'interfaccia vengono assegnati indirizzi IP privati dinamici dalla subnet che esegue il mapping alla risorsa di collegamento privato. Il valore dell'indirizzo IP privato rimane invariato per l'intero ciclo di vita dell'endpoint privato.
 
- L'endpoint privato deve essere distribuito nella stessa area della rete virtuale. 
 
- La risorsa di collegamento privato può essere distribuita in un'area diversa rispetto alla rete virtuale e all'endpoint privato.
 
- È possibile creare più endpoint privati usando la stessa risorsa di collegamento privato. Per una singola rete che usa una configurazione del server DNS comune, la procedura consigliata consiste nell'usare un singolo endpoint privato per una determinata risorsa di collegamento privato per evitare voci duplicate o conflitti nella risoluzione DNS. 
 
- È possibile creare più endpoint privati nella stessa subnet o in subnet diverse all'interno della stessa rete virtuale. Sono previsti limiti per il numero di endpoint privati che è possibile creare in una sottoscrizione. Per informazioni dettagliate, vedere  [Limiti di Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


 
## <a name="private-link-resource"></a>Risorsa di collegamento privato 
Una risorsa di collegamento privato è la destinazione di un endpoint privato specifico. Di seguito viene riportato un elenco dei tipi di risorse di collegamento privato disponibili. 
 
|Nome risorsa di collegamento privato  |Tipo di risorsa   |Sottorisorse  |
|---------|---------|---------|
|**Servizio di collegamento privato** (servizio dell'utente)   |  Microsoft.Network/privateLinkServices       | empty |
|**Automazione di Azure** |  Microsoft.Automation/automationAccounts | Webhook, DSCAndHybridWorker |
|**Database SQL di Azure** | Microsoft.Sql/servers    |  Sql Server (sqlServer)        |
|**Azure Synapse Analytics** | Microsoft.Sql/servers    |  Sql Server (sqlServer)        | 
|**Archiviazione di Azure**  | Microsoft.Storage/storageAccounts    |  BLOB (BLOB, blob_secondari)<BR> Tabella (tabella, tabella_secondaria)<BR> Coda (coda, cosa_secondaria)<BR> File (file, file_secondario)<BR> Web (Web, web_secondario)        |
|**Azure Data Lake Storage Gen2**  | Microsoft.Storage/storageAccounts    |  BLOB (BLOB, blob_secondari)<BR> Data Lake File System Gen2 (dfs, dfs_secondario)       |
|**Azure Cosmos DB** | Microsoft.AzureCosmosDB/databaseAccounts    | Sql, MongoDB, Cassandra, Gremlin, Tabella|
|**Database di Azure per PostgreSQL - Server singolo** | Microsoft.DBforPostgreSQL/servers    | postgresqlServer |
|**Database di Azure per MySQL** | Microsoft.DBforMySQL/servers    | mysqlServer |
|**Database di Azure per MariaDB** | Microsoft.DBforMariaDB/servers    | mariadbServer |
|**Hub IoT Azure** | Microsoft.Devices/IotHubs    | iotHub |
|**Insieme di credenziali chiave Azure** | Microsoft.KeyVault/vaults    | insieme di credenziali |
|**Servizio Azure Kubernetes - API Kubernetes** | Microsoft.ContainerService/managedClusters    | managedCluster |
|**Ricerca di Azure** | Microsoft.Search/searchService| searchService|  
|**Registro Azure Container** | Microsoft.ContainerRegistry/registries    | Registro di sistema |
|**Configurazione app di Azure** | Microsoft.Appconfiguration/configurationStores    | configurationStore |
|**Backup di Azure** | Microsoft.RecoveryServices/vaults    | insieme di credenziali |
|**Hub eventi di Azure** | Microsoft.EventHub/namespaces    | spazio dei nomi |
|**Bus di servizio di Azure** | Microsoft.ServiceBus/namespaces | spazio dei nomi |
|**Inoltro di Azure** | Microsoft.Relay/namespaces | spazio dei nomi |
|**Griglia di eventi di Azure** | Microsoft.EventGrid/topics    | argomento |
|**Griglia di eventi di Azure** | Microsoft.EventGrid/domains    | dominio |
|**App Web di Azure** | Microsoft.Web/sites    | siti |
|**Azure Machine Learning** | Microsoft.MachineLearningServices/workspaces    | area di lavoro |
  
 
## <a name="network-security-of-private-endpoints"></a>Sicurezza di rete degli endpoint privati 
Quando si usano endpoint privati per i servizi di Azure, il traffico viene protetto rispetto a una risorsa di collegamento privato specifica. La piattaforma esegue un controllo di accesso per convalidare le connessioni di rete che raggiungono solo la risorsa di collegamento privato specificata. Per accedere ad altre risorse all'interno dello stesso servizio di Azure, sono necessari endpoint privati aggiuntivi. 
 
È possibile bloccare completamente i carichi di lavoro per impedire l'accesso agli endpoint pubblici per connettersi a un servizio di Azure supportato. Questo controllo offre un livello di sicurezza di rete aggiuntivo per le risorse, grazie a una protezione di esfiltrazione incorporata che impedisce l'accesso ad altre risorse ospitate nello stesso servizio di Azure. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>Accedere a una risorsa di collegamento privato tramite il flusso di lavoro di approvazione 
È possibile connettersi a una risorsa di collegamento privato tramite i metodi di approvazione della connessione seguenti:
- Approvazione **automatica** approvata quando si è proprietari della risorsa di collegamento privato specifica o si dispone delle autorizzazioni appropriate. L'autorizzazione richiesta è basata sul tipo di risorsa collegamento privato nel formato seguente: Microsoft. \<Provider> /<resource_type>/privateEndpointConnectionApproval/action
- Richiesta **manuale** quando non si dispone dell'autorizzazione necessaria e si desidera richiedere l'accesso. Viene avviato un flusso di lavoro di approvazione. L'endpoint privato e la successiva connessione verranno creati con lo stato "In sospeso". Il proprietario della risorsa di collegamento privato ha la responsabilità di approvare la connessione. Dopo l'approvazione, l'endpoint privato è abilitato per l'invio normale del traffico, come mostrato nel diagramma sul flusso di lavoro di approvazione seguente.  

![approvazione del flusso di lavoro](media/private-endpoint-overview/private-link-paas-workflow.png)
 
Il proprietario della risorsa di collegamento privato può eseguire le azioni seguenti tramite una connessione all'endpoint privato: 
- Esaminare tutti i dettagli delle connessioni agli endpoint privati. 
- Approvare una connessione a un endpoint privato. L'endpoint privato corrispondente verrà abilitato in modo da inviare traffico alla risorsa di collegamento privato. 
- Rifiutare una connessione a un endpoint privato. L'endpoint privato corrispondente verrà aggiornato per riflettere lo stato.
- Eliminare una connessione a un endpoint privato in qualsiasi stato. L'endpoint privato corrispondente verrà aggiornato con uno stato disconnesso per riflettere l'azione e il proprietario dell'endpoint privato in questo momento può solo eliminare la risorsa. 
 
> [!NOTE]
> Solo un endpoint privato in uno stato approvato può inviare il traffico a una risorsa del collegamento privato specificata. 

### <a name="connecting-using-alias"></a>Connessione tramite alias
L'alias è un moniker univoco generato quando il proprietario del servizio crea il servizio di collegamento privato in relazione a un servizio di bilanciamento del carico standard. Il proprietario del servizio può condividere tale alias con i relativi consumer offline. I consumer possono richiedere una connessione al servizio di collegamento privato usando l'URI della risorsa o l'alias. Se si desidera connettersi tramite alias, è necessario creare un endpoint privato tramite il metodo di approvazione manuale della connessione. Per usare il metodo di approvazione manuale della connessione, impostare il parametro della richiesta manuale su true durante la creazione del flusso di endpoint privato. Per informazioni dettagliate, vedere [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint?view=azps-2.6.0) e [az network private-endpoint create](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create). 

## <a name="dns-configuration"></a>Configurazione del DNS 
Quando ci si connette a una risorsa di collegamento privato usando un nome di dominio completo (FQDN) come parte della stringa di connessione, è importante configurare correttamente le impostazioni DNS per la risoluzione nell'indirizzo IP privato allocato. I servizi di Azure esistenti potrebbero avere già una configurazione DNS da usare per la connessione tramite un endpoint pubblico. Per connettersi usando l'endpoint privato, è necessario ignorarla. 
 
L'interfaccia di rete associata all'endpoint privato contiene il set completo di informazioni necessarie per configurare il DNS, inclusi FQDN e indirizzi IP privati allocati per una determinata risorsa di collegamento privato. 

Per informazioni dettagliate sulle procedure consigliate e sui consigli per la configurazione del DNS per gli endpoint privati, vedere l'articolo [Configurazione DNS degli endpoint privati](private-endpoint-dns.md).



 
## <a name="limitations"></a>Limitazioni
 
La tabella seguente contiene un elenco di limitazioni note quando si usano gli endpoint privati. 


|Limitazione |Descrizione |Strategia di riduzione del rischio  |
|---------|---------|---------|
|Le regole del gruppo di sicurezza di rete e le route definite dall'utente non si applicano all'endpoint privato    |Il gruppo di sicurezza di rete non è supportato in endpoint privati. Mentre alle subnet contenenti l'endpoint privato possono essere associati gruppi di sicurezza di rete, le regole non sono valide per il traffico elaborato dall'endpoint privato. Per distribuire endpoint privati in una subnet, è necessario che [l'applicazione dei criteri di rete sia disabilitata](disable-private-endpoint-network-policy.md). Il gruppo di sicurezza di rete viene ancora applicato ad altri carichi di lavoro ospitati nella stessa subnet. Le route in qualsiasi subnet client useranno un prefisso /32. La modifica del comportamento di routing predefinito richiede un UDR simile.  | Controllare il traffico usando le regole del gruppo di sicurezza di rete per il traffico in uscita nei client di origine. Distribuire le singole route con prefisso /32 per eseguire l'override delle route degli endpoint privati. I log di flusso e le informazioni di monitoraggio del gruppo di sicurezza di rete per le connessioni in uscita sono ancora supportati e possono essere usati.        |


## <a name="next-steps"></a>Passaggi successivi
- [Creare un endpoint privato per il database SQL tramite il portale](create-private-endpoint-portal.md)
- [Creare un endpoint privato per il database SQL con PowerShell](create-private-endpoint-powershell.md)
- [Creare un endpoint privato per il database SQL usando l'interfaccia della riga di comando](create-private-endpoint-cli.md)
- [Creare un endpoint privato per l'account di archiviazione con il portale ](create-private-endpoint-storage-portal.md)
- [Creare un endpoint privato per l'account Azure Cosmos con il portale ](../cosmos-db/how-to-configure-private-endpoints.md)
- [Creare un servizio Collegamento privato con Azure PowerShell](create-private-link-service-powershell.md)
- [Creare il proprio collegamento privato per Database di Azure per PostgreSQL - Server singolo tramite il portale](../postgresql/howto-configure-privatelink-portal.md)
- [Creare il proprio collegamento privato per Database di Azure per PostgreSQL - Server singolo tramite l'interfaccia della riga di comando](../postgresql/howto-configure-privatelink-cli.md)
- [Creare il proprio collegamento privato per Database di Azure per MySQL tramite il portale](../mysql/howto-configure-privatelink-portal.md)
- [Creare il proprio collegamento privato per Database di Azure per MySQL tramite l'interfaccia della riga di comando](../mysql/howto-configure-privatelink-cli.md)
- [Creare il proprio collegamento privato per Database di Azure per MariaDB tramite il portale](../mariadb/howto-configure-privatelink-portal.md)
- [Creare il proprio collegamento privato per Database di Azure per MariaDB tramite l'interfaccia della riga di comando](../mariadb/howto-configure-privatelink-cli.md)
