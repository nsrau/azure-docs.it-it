<properties
	pageTitle="Gestione degli account con la gestione .NET per Batch | Microsoft Azure"
	description="Creare, eliminare e modificare account Azure Batch nelle applicazioni con la libreria di gestione .NET per Batch."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="01/28/2016"
	ms.author="marsma"/>

# Gestire quote e account Batch di Azure con la gestione .NET per Batch

> [AZURE.SELECTOR]
- [Azure portal](batch-account-create-portal.md)
- [Batch Management .NET](batch-management-dotnet.md)

È possibile ridurre il sovraccarico di manutenzione nelle applicazioni Azure Batch usando la libreria di [gestione .NET per Batch][api_mgmt_net] per automatizzare le operazioni di individuazione delle quote, gestione delle chiavi, creazione ed eliminazione di account Batch.

- **Creare ed eliminare account Batch** in qualsiasi area. Se, ad esempio, un fornitore di software indipendente (ISV) offre un servizio per cui a ogni cliente viene assegnato un account Batch separato per la fatturazione, è possibile aggiungere funzionalità di creazione ed eliminazione di account al portale per i clienti.
- **Recuperare e rigenerare chiavi di account** a livello di programmazione per qualsiasi account Batch. Questa funzionalità è particolarmente utile per assicurare la conformità ai criteri di sicurezza che potrebbero imporre il rollover periodico o la scadenza delle chiavi di account. In presenza di un certo numero di account Batch in diverse aree di Azure, l'automazione del processo di rollover permette di aumentare l'efficienza della soluzione.
- **Controllare le quote degli account** e determinare senza errori i limiti dei singoli account Batch. Il controllo delle quote degli account prima di avviare processi, creare pool o aggiungere nodi di calcolo permette di stabilire attivamente dove e quando creare risorse di calcolo. È possibile determinare quali account richiedono un aumento della quota prima dell'allocazione di risorse aggiuntive in tali account.
- **Integrare funzionalità di altri servizi Azure** per un'esperienza di gestione completa sfruttando i vantaggi della gestione .NET per Batch, di [Azure Active Directory][aad_about] e di [Gestione risorse di Azure][resman_overview] nella stessa applicazione. Usando queste funzionalità e le relative API è possibile eliminare i problemi di autenticazione, consentire la creazione e l'eliminazione di gruppi di risorse e fornire le funzionalità descritte in precedenza per una soluzione di gestione end-to-end.

> [AZURE.NOTE] Questo articolo è incentrato sulla gestione di quote, chiavi e account Batch a livello di codice, ma è possibile eseguire molte di queste attività usando il [portale di Azure][azure_portal]. Per altre informazioni, vedere [Creare e gestire un account Batch di Azure nel portale di Azure](batch-account-create-portal.md) e [Quote e limiti per il servizio Azure Batch](batch-quota-limit.md).

## Creare ed eliminare account Batch

Come accennato in precedenza, una delle principali funzionalità dell'API di gestione per Batch è la creazione ed eliminazione di account Batch all'interno di un'area di Azure. A questo scopo è possibile usare [BatchManagementClient.Accounts.CreateAsync][net_create] e [DeleteAsync][net_delete] o le relative controparti sincrone.

Il frammento di codice seguente crea un account, ottiene l'account appena creato dal servizio Batch e quindi lo elimina. In questo e in altri frammenti di codice in questo articolo, `batchManagementClient` è un'istanza completamente inizializzata di [BatchManagementClient][net_mgmt_client].

```
// Create a new Batch account
await batchManagementClient.Accounts.CreateAsync("MyResourceGroup",
	"mynewaccount",
	new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
BatchAccountGetResponse getResponse = await batchManagementClient.Accounts.GetAsync("MyResourceGroup", "mynewaccount");
AccountResource account = getResponse.Resource;

// Delete the account
await batchManagementClient.Accounts.DeleteAsync("MyResourceGroup", account.Name);
```

> [AZURE.NOTE] Le applicazioni che usano la libreria di gestione .NET per Batch e la relativa classe BatchManagementClient richiedono l'accesso come **amministratore** o **coamministratore del servizio** alla sottoscrizione a cui appartiene l'account Batch da gestire. Per altre informazioni, vedere la sezione relativa ad [Azure Active Directory](#aad) di seguito e l'esempio di codice [AccountManagement][acct_mgmt_sample].

## Recuperare e rigenerare chiavi di account

È possibile ottenere le chiavi primarie e secondarie da qualsiasi account Batch all'interno della sottoscrizione usando [ListKeysAsync][net_list_keys]. Le chiavi possono essere rigenerate con [RegenerateKeyAsync][net_regenerate_keys].

```
// Get and print the primary and secondary keys
BatchAccountListKeyResponse accountKeys = await batchManagementClient.Accounts.ListKeysAsync("MyResourceGroup", "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.PrimaryKey);
Console.WriteLine("Secondary key: {0}", accountKeys.SecondaryKey);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys = await batchManagementClient.Accounts.RegenerateKeyAsync(
	"MyResourceGroup",
	"mybatchaccount",
	new BatchAccountRegenerateKeyParameters() { KeyName = AccountKeyType.Primary });
```

> [AZURE.TIP] È possibile creare un flusso di lavoro di connessione ottimizzato per le applicazioni di gestione. Ottenere prima di tutto una chiave dell'account per l'account Batch che si vuole gestire con [ListKeysAsync][net_list_keys]. Usare quindi questa chiave durante l'inizializzazione della classe [BatchSharedKeyCredentials][net_sharedkeycred] della libreria Batch .NET usata durante l'inizializzazione di [BatchClient][net_batch_client].

## Verificare le quote di sottoscrizioni di Azure e account Batch

Le sottoscrizioni di Azure e i singoli servizi di Azure come Batch hanno tutti quote predefinite che limitano il numero di determinate entità al loro interno. Per informazioni sulle quote predefinite per le sottoscrizioni di Azure, vedere [Sottoscrizione di Azure e limiti dei servizi, quote e vincoli](./../azure-subscription-service-limits.md). Per le quote predefinite del servizio Batch, vedere [Quote e limiti per il servizio Azure Batch](batch-quota-limit.md). Tramite la libreria di gestione .NET per Batch è possibile controllare queste quote all'interno delle applicazioni. In questo modo è possibile prendere decisioni relative all'allocazione prima di aggiungere account o risorse di calcolo come pool e nodi di calcolo.

### Verificare le quote dell'account Batch di una sottoscrizione di Azure

Prima di creare un account Batch in un'area, è possibile verificare se la sottoscrizione di Azure permette di aggiungere un account in quell'area.

Nel frammento di codice riportato di seguito viene usato prima di tutto [BatchManagementClient.Accounts.ListAsync][net_mgmt_listaccounts] per ottenere una raccolta di tutti gli account Batch all'interno di una sottoscrizione. Una volta ottenuta questa raccolta, è possibile determinare il numero di account nell'area di destinazione. Si userà quindi [BatchManagementClient.Subscriptions][net_mgmt_subscriptions] per ottenere la quota dell'account Batch e determinare se e quanti account è possibile creare in quell'area.

```
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse = await batchManagementClient.Accounts.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}", creds.SubscriptionId, accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Subscriptions.GetSubscriptionQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

Nel frammento riportato sopra, `creds` è un'istanza di [TokenCloudCredentials][azure_tokencreds]. Per un esempio relativo alla creazione di questo oggetto, vedere l'esempio di codice [AccountManagement][acct_mgmt_sample] su GitHub.

### Verificare le quote di risorse di calcolo in un account Batch

Prima di aumentare le risorse di calcolo all'interno della soluzione Batch, è possibile verificare che le risorse che si prevede di allocare non eccedano le quote dell'account attualmente disponibili. Nel frammento di codice riportato di seguito si procede semplicemente alla stampa delle informazioni sulle quote per l'account Batch denominato `mybatchaccount`. Nell'applicazione è possibile usare queste informazioni per stabilire se l'account può gestire le risorse aggiuntive che si intende creare.

```
// First obtain the Batch account
BatchAccountGetResponse getResponse = await batchManagementClient.Accounts.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [AZURE.IMPORTANT] Molti dei limiti delle quote predefinite per i servizi e le sottoscrizioni di Azure possono essere aumentati inviando una richiesta nel [portale di Azure][azure_portal]. Per istruzioni su come aumentare le quote dell'account Batch, vedere ad esempio, [Quote e limiti per il servizio Azure Batch](batch-quota-limit.md).

## Gestione .NET per Batch, Azure AD e Gestione risorse

Quando si usa la libreria di gestione .NET per Batch, in genere è possibile sfruttare le funzionalità sia di [Azure Active Directory][aad_about] (Azure AD) che di [Gestione risorse di Azure][resman_overview]. Il progetto di esempio illustrato di seguito illustra l'API di gestione .NET per Batch usando sia Azure Active Directory che Gestione risorse.

### <a name="aad"></a>Azure Active Directory

Azure stesso usa Azure AD per l'autenticazione dei relativi clienti, amministratori del servizio e utenti dell'organizzazione. Nel contesto della gestione .NET per Batch viene usato per autenticare un amministratore o coamministratore della sottoscrizione. Ciò consente quindi alla libreria di gestione di inviare una query al servizio Batch e di eseguire le operazioni descritte in questo articolo.

Nel progetto di esempio illustrato di seguito, Azure [Active Directory Authentication Library][aad_adal] (ADAL) viene usato per richiedere all'utente le credenziali Microsoft. Quando vengono fornite le credenziali di amministratore o coamministratore del servizio, l'applicazione può richiedere ad Azure un elenco di sottoscrizioni e creare ed eliminare gruppi di risorse e account Batch.

### Gestione risorse

Quando si usa la libreria di gestione .NET per Batch, gli account Batch vengono in genere creati all'interno di un [gruppo di risorse][resman_overview]. È possibile creare il gruppo di risorse a livello di codice usando la classe [ResourceManagementClient][resman_client] nella libreria [.NET per Gestione risorse][resman_api]. In alternativa è possibile aggiungere un account a un gruppo di risorse esistente creato in precedenza con il [portale di Azure][azure_portal].

## <a name="sample"></a>Progetto di esempio su GitHub

Il progetto di esempio [AccountManagment][acct_mgmt_sample] su GitHub permette di vedere la libreria di gestione .NET per Batch in azione. Questa applicazione console illustra la creazione e l'utilizzo di [BatchManagementClient][net_mgmt_client] e [ResourceManagementClient][resman_client]. Illustra anche l'utilizzo di Azure [Active Directory Authentication Library][aad_adal] (ADAL), che richiesta da entrambi i client.

Per eseguire correttamente l'applicazione di esempio, è necessario prima registrarla con Azure AD tramite il portale di Azure. Vedere "Aggiunta di un'applicazione" in [Integrazione di applicazioni con Azure Active Directory][aad_integrate]. Seguire quindi la procedura illustrata nell'articolo per registrare l'applicazione di esempio nella directory predefinita del proprio account. Assicurarsi di selezionare "Applicazione client nativa" come tipo di applicazione. È anche possibile specificare un URI valido (ad esempio, `http://myaccountmanagementsample`) come "URI di reindirizzamento", che non deve necessariamente essere un endpoint effettivo.

Dopo avere aggiunto l'applicazione, delegare l'autorizzazione per accedere alla gestione del servizio Azure come organizzazione all'applicazione *API di gestione del servizio Microsoft Azure* nelle impostazioni dell'applicazione nel portale:

![Autorizzazioni applicazione nel portale di Azure][2]

Una volta aggiunta l'applicazione come descritto sopra, aggiornare `Program.cs` nel progetto di esempio [AccountManagment][acct_mgmt_sample] con l'URI di reindirizzamento e l'ID client dell'applicazione. È possibile trovare questi valori nella scheda "Configura" dell'applicazione:

![Configurazione applicazione nel portale di Azure][3]

L'applicazione di esempio [AccountManagment][acct_mgmt_sample] illustra le operazioni seguenti:

1. Acquisire un token di sicurezza da Azure AD con [ADAL][aad_adal]. Se l'utente non ha già eseguito l'accesso, gli verrà richiesto di fornire le credenziali di Azure.
2. Usando il token di sicurezza ottenuto da Azure AD, creare un oggetto [SubscriptionClient][resman_subclient] per richiedere ad Azure un elenco di sottoscrizioni associate all'account e consentire all'utente di selezionarne una, se sono presenti più sottoscrizioni.
3. Creare un nuovo oggetto credenziali associato alla sottoscrizione selezionata.
4. Creare un oggetto [ResourceManagementClient][resman_client] usando le nuove credenziali.
5. Usare [ResourceManagementClient][resman_client] per creare un nuovo gruppo di risorse.
6. Usare [BatchManagementClient][net_mgmt_client] per eseguire una serie di operazioni sull'account Batch:
  - Creare un nuovo account Batch all'interno del gruppo di risorse appena creato.
  - Ottenere l'account appena creato dal servizio Batch.
  - Stampare le chiavi dell'account per il nuovo account.
  - Rigenerare una nuova chiave primaria per l'account.
  - Stampare le informazioni sulla quota per l'account.
  - Stampare le informazioni sulla quota per la sottoscrizione.
  - Stampare tutti gli account all'interno della sottoscrizione.
  - Eliminare l'account appena creato.
7. Eliminare il gruppo di risorse.

Prima di eliminare l'account Batch e il gruppo di risorse appena creati, è possibile esaminarli entrambi nel [portale di Azure][azure_portal]\:

![Portale di Azure con il gruppo di risorse e l’account Batch][1] <br /> *Portale di Azure con il nuovo gruppo di risorse e il nuovo account Batch*

[aad_about]: ../active-directory/active-directory-whatis.md "Informazioni su Azure Active Directory"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Scenari di autenticazione per Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrazione di applicazioni con Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_mgmt_net]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[azure_portal]: http://portal.azure.com
[azure_storage]: https://azure.microsoft.com/services/storage/
[azure_tokencreds]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.tokencloudcredentials.aspx
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_list_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.listkeysasync.aspx
[net_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.createasync.aspx
[net_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.deleteasync.aspx
[net_regenerate_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.regeneratekeyasync.aspx
[net_sharedkeycred]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.auth.batchsharedkeycredentials.aspx
[net_mgmt_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.aspx
[net_mgmt_subscriptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.subscriptions.aspx
[net_mgmt_listaccounts]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.iaccountoperations.listasync.aspx
[resman_api]: https://msdn.microsoft.com/library/azure/mt418626.aspx
[resman_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx
[resman_subclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.subscriptions.subscriptionclient.aspx
[resman_overview]: ../resource-group-overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png

<!---HONumber=AcomDC_0204_2016-->