---
title: 'Gestire risorse di account Batch con la libreria client per .NET: Azure | Documentazione Microsoft'
description: Creare, eliminare e modificare le risorse dell&quot;account Batch tramite la libreria Batch Management .NET.
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 6b6c548ca1001587e2b40bbe9ee2fcb298f40d72
ms.openlocfilehash: c47e9263e1eefe8d1c3c0e164a186f095ca88a80
ms.lasthandoff: 02/28/2017


---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Gestire le quote e gli account Batch con la libreria client di gestione Batch per .NET

> [!div class="op_single_selector"]
> * [Portale di Azure](batch-account-create-portal.md)
> * [.NET per la gestione di Batch](batch-management-dotnet.md)
> 
> 

È possibile ridurre il sovraccarico di manutenzione nelle applicazioni Azure Batch usando la libreria [Batch Management .NET][api_mgmt_net] per automatizzare le operazioni di creazione ed eliminazione di account Batch, gestione delle chiavi e individuazione delle quote.

* **Creare ed eliminare account Batch** in qualsiasi area. Se, ad esempio, un fornitore di software indipendente (ISV) offre un servizio per cui a ogni cliente viene assegnato un account Batch separato per la fatturazione, è possibile aggiungere funzionalità di creazione ed eliminazione di account al portale per i clienti.
* **Recuperare e rigenerare chiavi di account** a livello di programmazione per qualsiasi account Batch. Questa operazione consente di conformarsi ai criteri di sicurezza che impongono rollover periodici o la scadenza delle chiavi dell'account. In presenza di numerosi account Batch in diverse aree di Azure, l'automazione del processo di rollover permette di aumentare l'efficienza della soluzione.
* **Controllare le quote degli account** e determinare senza errori i limiti dei singoli account Batch. Il controllo delle quote degli account prima di avviare processi, creare pool o aggiungere nodi di calcolo permette di stabilire attivamente dove e quando creare risorse di calcolo. È possibile determinare quali account richiedono un aumento della quota prima dell'allocazione di risorse aggiuntive in tali account.
* **Integrare funzionalità di altri servizi di Azure** per un'esperienza di gestione completa sfruttando i vantaggi di Batch Management .NET, [Azure Active Directory][aad_about] e [Azure Resource Manager][resman_overview] nella stessa applicazione. Usando queste funzionalità e le relative API è possibile eliminare i problemi di autenticazione, consentire la creazione e l'eliminazione di gruppi di risorse e fornire le funzionalità descritte in precedenza per una soluzione di gestione end-to-end.

> [!NOTE]
> Questo articolo è incentrato sulla gestione di quote, chiavi e account Batch a livello di codice, ma è possibile eseguire molte di queste attività usando il [portale di Azure][azure_portal]. Per altre informazioni, vedere [Creare un account Azure Batch usando il portale di Azure](batch-account-create-portal.md) e [Quote e limiti per il servizio Azure Batch](batch-quota-limit.md).
> 
> 

## <a name="create-and-delete-batch-accounts"></a>Creare ed eliminare account Batch
Come accennato, una delle principali funzionalità dell'API di gestione per Batch è la creazione ed eliminazione di account Batch in un'area di Azure. A questo scopo, usare [BatchManagementClient.Account.CreateAsync][net_create] e [DeleteAsync][net_delete] o le relative controparti sincrone.

Il frammento di codice seguente crea un account, ottiene l'account appena creato dal servizio Batch e quindi lo elimina. In questo e in altri frammenti di codice in questo articolo `batchManagementClient` è un'istanza completamente inizializzata di [BatchManagementClient][net_mgmt_client].

```csharp
// Create a new Batch account
await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount",
    new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
AccountResource account = await batchManagementClient.Account.GetAsync(
    "MyResourceGroup",
    "mynewaccount");

// Delete the account
await batchManagementClient.Account.DeleteAsync("MyResourceGroup", account.Name);
```

> [!NOTE]
> Le applicazioni che usano la libreria Batch Management .NET e la relativa classe BatchManagementClient necessitano di accesso come **amministratore del servizio** o **coamministratore** alla sottoscrizione a cui appartiene l'account Batch da gestire. Per altre informazioni, vedere la sezione [Azure Active Directory](#azure-active-directory) e l'esempio di codice [AccountManagement][acct_mgmt_sample].
> 
> 

## <a name="retrieve-and-regenerate-account-keys"></a>Recuperare e rigenerare chiavi di account
È possibile ottenere le chiavi primarie e secondarie da qualsiasi account Batch all'interno della sottoscrizione usando [ListKeysAsync][net_list_keys]. Le chiavi possono essere rigenerate con [RegenerateKeyAsync][net_regenerate_keys].

```csharp
// Get and print the primary and secondary keys
BatchAccountListKeyResult accountKeys =
    await batchManagementClient.Account.ListKeysAsync(
        "MyResourceGroup",
        "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.Primary);
Console.WriteLine("Secondary key: {0}", accountKeys.Secondary);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys =
    await batchManagementClient.Account.RegenerateKeyAsync(
        "MyResourceGroup",
        "mybatchaccount",
        new BatchAccountRegenerateKeyParameters() {
            KeyName = AccountKeyType.Primary
            });
```

> [!TIP]
> È possibile creare un flusso di lavoro di connessione ottimizzato per le applicazioni di gestione. Ottenere prima una chiave per l'account Batch che si vuole gestire con [ListKeysAsync][net_list_keys]. Usare poi questa chiave durante l'inizializzazione della classe [BatchSharedKeyCredentials][net_sharedkeycred] della libreria Batch .NET usata durante l'inizializzazione di [BatchClient][net_batch_client].
> 
> 

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Verificare le quote di sottoscrizioni di Azure e account Batch
Le sottoscrizioni di Azure e i singoli servizi di Azure come Batch hanno tutti quote predefinite che limitano il numero di determinate entità al loro interno. Per informazioni sulle quote predefinite per le sottoscrizioni di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md). Per le quote predefinite del servizio Batch, vedere [Quote e limiti per il servizio Azure Batch](batch-quota-limit.md). Tramite la libreria Batch Management .NET è possibile controllare queste quote nelle applicazioni. In questo modo è possibile prendere decisioni relative all'allocazione prima di aggiungere account o risorse di calcolo come pool e nodi di calcolo.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Verificare le quote dell'account Batch di una sottoscrizione di Azure
Prima di creare un account Batch in un'area, è possibile verificare se la sottoscrizione di Azure permette di aggiungere un account in quell'area.

Nel frammento di codice riportato di seguito viene usato prima di tutto [BatchManagementClient.Accounts.ListAsync][net_mgmt_listaccounts] per ottenere una raccolta di tutti gli account Batch all'interno di una sottoscrizione. Una volta ottenuta questa raccolta, è possibile determinare il numero di account nell'area di destinazione. Si userà quindi [BatchManagementClient.Subscriptions][net_mgmt_subscriptions] per ottenere la quota dell'account Batch e determinare se e quanti account è possibile creare in quell'area.

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.Account.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

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

Nel frammento riportato sopra `creds` è un'istanza di [TokenCloudCredentials][azure_tokencreds]. Per un esempio relativo alla creazione di questo oggetto, vedere l'esempio di codice [AccountManagement][acct_mgmt_sample] su GitHub.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Verificare le quote di risorse di calcolo in un account Batch
Prima di aumentare le risorse di calcolo nella soluzione Batch, è possibile verificare che le risorse da allocare non superino le quote dell'account. Nel frammento di codice riportato di seguito si procede alla stampa delle informazioni sulle quote per l'account Batch denominato `mybatchaccount`. Nell'applicazione è possibile usare queste informazioni per stabilire se l'account può gestire le risorse aggiuntive da creare.

```csharp
// First obtain the Batch account
BatchAccountGetResponse getResponse =
    await batchManagementClient.Account.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [!IMPORTANT]
> Molti dei limiti delle quote predefinite per i servizi e le sottoscrizioni di Azure possono essere aumentati inviando una richiesta nel [portale di Azure][azure_portal]. Per istruzioni su come aumentare le quote dell'account Batch, vedere ad esempio [Quote e limiti per il servizio Azure Batch](batch-quota-limit.md) .
> 
> 

## <a name="batch-management-net-azure-ad-and-resource-manager"></a>Gestione .NET per Batch, Azure AD e Gestione risorse
Quando si usa la libreria Batch Management .NET, in genere è possibile sfruttare le funzionalità sia di [Azure Active Directory][aad_about] (Azure AD) che di [Azure Resource Manager][resman_overview]. Il progetto di esempio illustrato di seguito usa l'API di Batch Management .NET usando sia Azure Active Directory che Resource Manager.

### <a name="azure-active-directory"></a>Azure Active Directory
Azure stesso usa Azure AD per l'autenticazione dei relativi clienti, amministratori del servizio e utenti dell'organizzazione. Nel contesto di Batch Management .NET Azure AD viene usato per autenticare un amministratore o coamministratore della sottoscrizione. Ciò consente quindi alla libreria di gestione di inviare una query al servizio Batch e di eseguire le operazioni descritte in questo articolo.

Nel progetto di esempio illustrato di seguito si usa Azure [Active Directory Authentication Library][aad_adal] (ADAL) per chiedere all'utente le credenziali Microsoft. Quando vengono fornite le credenziali di amministratore o coamministratore del servizio, l'applicazione può richiedere ad Azure un elenco di sottoscrizioni e creare ed eliminare gruppi di risorse e account Batch.

### <a name="resource-manager"></a>Gestione risorse
Quando si usa la libreria Batch Management .NET per creare gli account Batch, gli account vengono in genere creati all'interno di un [gruppo di risorse][resman_overview]. È possibile creare il gruppo di risorse a livello di codice usando la classe [ResourceManagementClient][resman_client] nella libreria [Resource Manager .NET][resman_api]. In alternativa è possibile aggiungere un account a un gruppo di risorse esistente creato in precedenza con il [portale di Azure][azure_portal].

## <a name="sample-project-on-github"></a>Progetto di esempio su GitHub
Il progetto di esempio [AccountManagment][acct_mgmt_sample] su GitHub permette di vedere Batch Management .NET in azione. Questa applicazione console illustra la creazione e l'uso di [BatchManagementClient][net_mgmt_client] e [ResourceManagementClient][resman_client]. Illustra anche l'uso di Azure [Active Directory Authentication Library][aad_adal] (ADAL), necessaria per entrambi i client.

Per eseguire correttamente l'applicazione di esempio, è necessario prima registrarla con Azure AD tramite il portale di Azure. Seguire la procedura descritta nella sezione [Aggiunta di un'applicazione](../active-directory/develop/active-directory-integrating-applications.md#adding-an-application) nella pagina [Integrazione di applicazioni con Azure Active Directory][aad_integrate] per registrare l'applicazione di esempio all'interno della directory predefinita dell'account. Assicurarsi di selezionare **Applicazione client nativa** come tipo di applicazione. È anche possibile specificare un URI valido, ad esempio `http://myaccountmanagementsample`, come **URI di reindirizzamento** che non deve necessariamente essere un endpoint effettivo.

Dopo avere aggiunto l'applicazione, delegare l'autorizzazione ad **accedere alla gestione del servizio Azure come organizzazione** all'applicazione *API di gestione del servizio Windows Azure* nelle impostazioni dell'applicazione nel portale:

![Autorizzazioni applicazione nel portale di Azure][2]

> [!TIP]
> Se l'**API di gestione del servizio Windows Azure** non è disponibile in *autorizzazioni per altre applicazioni*, fare clic su **Aggiungi applicazione**, selezionare **API di gestione del servizio Windows Azure** e quindi selezionare la casella di spunta. A questo punto è possibile delegare le autorizzazioni come specificato in precedenza.
> 
> 

Una volta aggiunta l'applicazione come descritto sopra, aggiornare `Program.cs` nel progetto di esempio [AccountManagment][acct_mgmt_sample] con l'URI di reindirizzamento e l'ID client dell'applicazione. È possibile trovare questi valori nella scheda **Configura** dell'applicazione:

![Configurazione applicazione nel portale di Azure][3]

L'applicazione di esempio [AccountManagment][acct_mgmt_sample] illustra le operazioni seguenti:

1. Acquisire un token di sicurezza da Azure AD con [ADAL][aad_adal]. Se l'utente non ha già eseguito l'accesso, gli viene richiesto di fornire le credenziali di Azure.
2. Usando il token di sicurezza ottenuto da Azure AD, creare un oggetto [SubscriptionClient][resman_subclient] per richiedere ad Azure un elenco di sottoscrizioni associate all'account. Ciò consente di selezionarne una, se sono presenti più sottoscrizioni.
3. Creare un nuovo oggetto credenziali associato alla sottoscrizione selezionata.
4. Creare un oggetto [ResourceManagementClient][resman_client] usando le nuove credenziali.
5. Usare l'oggetto [ResourceManagementClient][resman_client] per creare un gruppo di risorse.
6. Usare l'oggetto [BatchManagementClient][net_mgmt_client] per eseguire una serie di operazioni sull'account Batch:
   * Creare un account Batch nel nuovo gruppo di risorse.
   * Ottenere l'account appena creato dal servizio Batch.
   * Stampare le chiavi dell'account per il nuovo account.
   * Rigenerare una nuova chiave primaria per l'account.
   * Stampare le informazioni sulla quota per l'account.
   * Stampare le informazioni sulla quota per la sottoscrizione.
   * Stampare tutti gli account all'interno della sottoscrizione.
   * Eliminare l'account appena creato.
7. Eliminare il gruppo di risorse.

Prima di eliminare l'account Batch e il gruppo di risorse appena creati, è possibile esaminarli entrambi nel [portale di Azure][azure_portal]:

![Portale di Azure con il gruppo di risorse e l'account Batch][1]
<br />
*Portale di Azure con il nuovo gruppo di risorse e il nuovo account Batch*

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
[resman_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspxs
[resman_subclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.subscriptions.subscriptionclient.aspx
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png

