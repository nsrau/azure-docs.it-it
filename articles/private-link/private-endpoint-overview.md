---
title: Che cos'è un endpoint privato di Azure?
description: Informazioni sull'endpoint privato di Azure
services: private-link
author: asudbring
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: ccae73b58b7da8e631c081871e17cec221918a76
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228126"
---
# <a name="what-is-azure-private-endpoint"></a>Che cos'è l'endpoint privato di Azure?

Endpoint privato di Azure è un'interfaccia di rete che si connette privatamente e in modo sicuro a un servizio basato sul collegamento privato di Azure. L'endpoint privato usa un indirizzo IP privato della VNet, in modo da portare il servizio in VNet. Il servizio potrebbe essere un servizio di Azure, ad esempio archiviazione di Azure, Azure Cosmos DB, SQL e così via, oppure il [servizio di collegamento privato](private-link-service-overview.md).
  
## <a name="private-endpoint-properties"></a>Proprietà endpoint privato 
 Un endpoint privato specifica le proprietà seguenti: 


|Proprietà  |DESCRIZIONE |
|---------|---------|
|Nome    |    Nome univoco all'interno del gruppo di risorse.      |
|Subnet    |  Subnet per la distribuzione e l'allocazione di indirizzi IP privati da una rete virtuale. Per i requisiti della subnet, vedere la sezione limitazioni di questo articolo.         |
|Risorsa collegamento privato    |   La risorsa di collegamento privato per la connessione usando l'ID risorsa o l'alias, dall'elenco dei tipi disponibili. Verrà generato un identificatore di rete univoco per tutto il traffico inviato a questa risorsa.       |
|Sottorisorsa di destinazione   |      Sottorisorsa da connettere. Ogni tipo di risorsa di collegamento privato ha opzioni diverse da selezionare in base alla preferenza.    |
|Metodo di approvazione della connessione    |  Automatico o manuale. In base alle autorizzazioni di controllo degli accessi in base al ruolo (RBAC), l'endpoint privato può essere approvato automaticamente. Se si tenta di connettersi a una risorsa di collegamento privato senza RBAC, usare il metodo Manual per consentire al proprietario della risorsa di approvare la connessione.        |
|Messaggio di richiesta     |  È possibile specificare un messaggio affinché le connessioni richieste vengano approvate manualmente. Questo messaggio può essere usato per identificare una richiesta specifica.        |
|Stato della connessione   |   Proprietà di sola lettura che specifica se l'endpoint privato è attivo. Per inviare il traffico è possibile usare solo endpoint privati in uno stato approvato. Stati aggiuntivi disponibili: <br>-**approvata**: la connessione è stata approvata automaticamente o manualmente ed è pronta per essere usata.</br><br>-**in sospeso**: la connessione è stata creata manualmente ed è in attesa di approvazione da parte del proprietario della risorsa di collegamento privato.</br><br>-**rifiutato**: la connessione è stata rifiutata dal proprietario della risorsa di collegamento privato.</br><br>-**disconnesso**: la connessione è stata rimossa dal proprietario della risorsa di collegamento privato. L'endpoint privato diventa informativo e deve essere eliminato per la pulizia. </br>|

Ecco alcuni dettagli chiave sugli endpoint privati: 
- L'endpoint privato consente la connettività tra i consumer dalla stessa VNet, reti virtuali con peering di area, reti virtuali con peering globale e locale usando [VPN](https://azure.microsoft.com/services/vpn-gateway/) o [Express Route](https://azure.microsoft.com/services/expressroute/) e servizi basati su collegamento privato.
 
- Quando si crea un endpoint privato, viene creata anche un'interfaccia di rete per il ciclo di vita della risorsa. All'interfaccia viene assegnato un indirizzo IP privato dalla subnet mappato al servizio di collegamento privato.
 
- L'endpoint privato deve essere distribuito nella stessa area della rete virtuale. 
 
- La risorsa collegamento privato può essere distribuita in un'area diversa rispetto alla rete virtuale e all'endpoint privato.
 
- È possibile creare più endpoint privati usando la stessa risorsa di collegamento privato. Per una singola rete che usa una configurazione del server DNS comune, la procedura consigliata consiste nell'usare un singolo endpoint privato per una determinata risorsa di collegamento privato per evitare voci duplicate o conflitti nella risoluzione DNS. 
 
- È possibile creare più endpoint privati nella stessa subnet o in subnet diverse all'interno della stessa rete virtuale. Sono previsti limiti per il numero di endpoint privati che è possibile creare in una sottoscrizione. Per informazioni dettagliate, vedere [limiti di Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits).


 
## <a name="private-link-resource"></a>Risorsa collegamento privato 
Una risorsa di collegamento privato è la destinazione di un endpoint privato specificato. Di seguito è riportato un elenco dei tipi di risorse di collegamento privato disponibili: 
 
|Nome della risorsa di collegamento privato  |Tipo di risorsa   |Le sottorisorse  |
|---------|---------|---------|
|**Servizio di collegamento privato** (servizio personalizzato)   |  Microsoft. Network/privateLinkServices       | empty |
|**Database SQL di Azure** | Microsoft.Sql/servers    |  SQL Server (sqlServer)        |
|**Azure SQL Data Warehouse** | Microsoft.Sql/servers    |  SQL Server (sqlServer)        |
|**Archiviazione di Azure**  | Microsoft.Storage/storageAccounts    |  BLOB (BLOB, blob_secondary)<BR> Tabella (tabella, table_secondary)<BR> Coda (coda, queue_secondary)<BR> File (file, file_secondary)<BR> Web (Web, web_secondary)        |
|**Azure Data Lake Storage Gen2**  | Microsoft.Storage/storageAccounts    |  BLOB (BLOB, blob_secondary)       |
|**Azure Cosmos DB** | Microsoft. AzureCosmosDB/databaseAccounts | SQL, MongoDB, Cassandra, Gremlin, tabella|
 
## <a name="network-security-of-private-endpoints"></a>Sicurezza di rete degli endpoint privati 
Quando si usano endpoint privati per i servizi di Azure, il traffico viene protetto a una risorsa di collegamento privato specifica. La piattaforma esegue un controllo di accesso per convalidare le connessioni di rete che raggiungono solo la risorsa di collegamento privato specificata. Per accedere ad altre risorse all'interno dello stesso servizio di Azure, sono necessari endpoint privati aggiuntivi. 
 
È possibile bloccare completamente i carichi di lavoro dall'accesso agli endpoint pubblici per connettersi a un servizio di Azure supportato. Questo controllo offre un livello di sicurezza di rete aggiuntivo per le risorse fornendo una protezione Exfiltration incorporata che impedisce l'accesso ad altre risorse ospitate nello stesso servizio di Azure. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>Accesso a una risorsa di collegamento privato mediante il flusso di lavoro di approvazione 
È possibile connettersi a una risorsa di collegamento privato usando i seguenti metodi di approvazione della connessione:
- Approvato **automaticamente** quando si è proprietari o si dispone dell'autorizzazione per la risorsa di collegamento privato specifica. L'autorizzazione richiesta è basata sul tipo di risorsa collegamento privato nel formato seguente: Microsoft.\<provider >/< resource_type >/privateEndpointConnectionApproval/action
- Richiesta **manuale** quando non si dispone dell'autorizzazione necessaria e si desidera richiedere l'accesso. Verrà avviato un flusso di lavoro di approvazione. L'endpoint privato e la connessione dell'endpoint privato successiva verranno creati in uno stato "in sospeso". Il proprietario della risorsa di collegamento privato è responsabile dell'approvazione della connessione. Una volta approvata, l'endpoint privato è abilitato per l'invio del traffico normalmente, come illustrato nel seguente diagramma del flusso di lavoro di approvazione.  

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
 
Per configurare le impostazioni DNS per gli endpoint privati, è possibile usare le opzioni seguenti: 
- **Usare il file host (consigliato solo per i test)** . Per eseguire l'override del DNS, è possibile usare il file host in una macchina virtuale.  
- **Usare una zona DNS privata**. È possibile usare le zone DNS private per sostituire la risoluzione DNS per un determinato endpoint privato. Una zona DNS privata può essere collegata alla rete virtuale per risolvere domini specifici.
- **Usare il server DNS personalizzato**. È possibile usare il proprio server DNS per sostituire la risoluzione DNS per una determinata risorsa di collegamento privato. Se il [server DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) è ospitato in una rete virtuale, è possibile creare una regola di invio DNS per usare una zona DNS privata per semplificare la configurazione per tutte le risorse di collegamento privato.
 
> [!IMPORTANT]
> Non è consigliabile eseguire l'override di una zona che viene usata attivamente per risolvere gli endpoint pubblici. Le connessioni alle risorse non saranno in grado di risolversi correttamente senza l'invio DNS al DNS pubblico. Per evitare problemi, creare un nome di dominio diverso o seguire il nome suggerito per ogni servizio riportato di seguito. 
 
Per i servizi di Azure, usare i nomi di zona consigliati come descritto nella tabella seguente:

|Tipo di risorsa collegamento privato   |Sottorisorsa  |Nome zona  |
|---------|---------|---------|
|DATABASE SQL/DW (Microsoft. SQL/Servers)    |  SQL Server (sqlServer)        |   privatelink.database.windows.net       |
|Account di archiviazione (Microsoft. storage/storageAccounts)    |  BLOB (BLOB, blob_secondary)        |    privatelink.blob.core.windows.net      |
|Account di archiviazione (Microsoft. storage/storageAccounts)    |    Tabella (tabella, table_secondary)      |   privatelink.table.core.windows.net       |
|Account di archiviazione (Microsoft. storage/storageAccounts)    |    Coda (coda, queue_secondary)     |   privatelink.queue.core.windows.net       |
|Account di archiviazione (Microsoft. storage/storageAccounts)   |    File (file, file_secondary)      |    privatelink.file.core.windows.net      |
|Account di archiviazione (Microsoft. storage/storageAccounts)     |  Web (Web, web_secondary)        |    privatelink.web.core.windows.net      |
|Data Lake file System Gen2 (Microsoft. storage/storageAccounts)  |  Data Lake file System Gen2 (DFS, dfs_secondary)        |     privatelink.dfs.core.windows.net     |
|Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)|SQL |privatelink.documents.azure.com|
|Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)|MongoDB |privatelink.mongo.cosmos.azure.com|
|Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)|Cassandra|privatelink.cassandra.cosmos.azure.com|
|Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)|Gremlin |privatelink.gremlin.cosmos.azure.com|
|Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)|tabella|privatelink.table.cosmos.azure.com|
 
Azure creerà un record DNS di nome canonico (CNAME) nel DNS pubblico per reindirizzare la risoluzione ai nomi di dominio suggeriti. Sarà possibile sostituire la risoluzione con l'indirizzo IP privato degli endpoint privati. 
 
Le applicazioni non devono modificare l'URL di connessione. Quando si tenta di risolvere usando un DNS pubblico, il server DNS verrà risolto negli endpoint privati. Il processo non ha alcun effetto sulle applicazioni. 
 
## <a name="limitations"></a>Limitazioni
 
La tabella seguente include un elenco di limitazioni note quando si usano gli endpoint privati: 


|Limitazione |DESCRIZIONE |Mitigazione  |
|---------|---------|---------|
|Le regole del gruppo di sicurezza di rete (NSG) e le route definite dall'utente non si applicano all'endpoint privato    |NSG non è supportato in endpoint privati. Mentre le subnet contenenti l'endpoint privato possono avere NSG associate, le regole non saranno valide per il traffico elaborato dall'endpoint privato. Per distribuire endpoint privati in una subnet, è necessario che l' [applicazione di criteri di rete sia disabilitata](disable-private-endpoint-network-policy.md) . NSG viene ancora applicato ad altri carichi di lavoro ospitati nella stessa subnet. Le route in qualsiasi subnet client utilizzeranno un prefisso/32. la modifica del comportamento di routing predefinito richiede un UDR simile  | Controllare il traffico usando le regole NSG per il traffico in uscita nei client di origine. Distribuire le singole route con prefisso/32 per eseguire l'override delle route di endpoint privato        |
|  La rete virtuale con peering con endpoint privati non è supportata   |   Quando ci si connette a endpoint privati in una rete virtuale con peering senza altri carichi di lavoro non è supportato       | Distribuire una singola VM nella rete virtuale con peering per abilitare la connettività |
|Carichi di lavoro specializzati non possono accedere agli endpoint privati    |   I servizi seguenti distribuiti nella rete virtuale non possono accedere a una risorsa di collegamento privato tramite endpoint privati:<br>Piano di servizio app</br>Istanza di contenitore di Azure</br>Azure NetApp Files</br>Modulo di protezione hardware dedicato di Azure<br>       |   Nessuna attenuazione durante l'anteprima.       |


## <a name="next-steps"></a>Passaggi successivi
- [Creare un endpoint privato per il server di database SQL con il portale ](create-private-endpoint-portal.md)
- [Creare un endpoint privato per il server di database SQL con PowerShell ](create-private-endpoint-powershell.md)
- [Creare un endpoint privato per il server di database SQL con l'interfaccia della riga di comando ](create-private-endpoint-cli.md)
- [Creare un endpoint privato per l'account di archiviazione con il portale ](create-private-endpoint-storage-portal.md)
- [Creare un endpoint privato per l'account Azure Cosmos con il portale ](../cosmos-db/how-to-configure-private-endpoints.md)
- [Creare un servizio Collegamento privato con Azure PowerShell](create-private-link-service-powershell.md)
