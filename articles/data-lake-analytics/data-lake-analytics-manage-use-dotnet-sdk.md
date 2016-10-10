<properties 
   pageTitle="Gestire Azure Data Lake Analytics tramite .NET SDK di Azure | Azure" 
   description="Informazioni su come gestire processi, origini dati e utenti di Data Lake Analytics. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/23/2016"
   ms.author="jgao"/>

# Gestire Azure Data Lake Analytics tramite .NET SDK di Azure

[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Informazioni su come gestire gli account, le origini dati, gli utenti e i processi di Azure Data Lake Analytics tramite .NET SDK di Azure. Per visualizzare gli argomenti relativi alla gestione tramite altri strumenti, fare clic sul selettore di scheda riportato sopra.

**Prerequisiti**

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).


<!-- ################################ -->
<!-- ################################ -->


## Connettersi ad Azure Data Lake Analytics

Sono necessari i pacchetti NuGet seguenti:

	Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
	Install-Package Microsoft.Azure.Common 
	Install-Package Microsoft.Azure.Management.ResourceManager -Pre
	Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre


L'esempio di codice seguente indica come connettersi ad Azure ed elenca gli account di Data Lake Analytics nella sottoscrizione di Azure.

	using System;
	using System.Collections.Generic;
	using System.Threading;

	using Microsoft.Rest;
	using Microsoft.Rest.Azure.Authentication;

	using Microsoft.Azure.Management.ResourceManager;
	using Microsoft.Azure.Management.DataLake.Store;
	using Microsoft.Azure.Management.DataLake.Analytics;
	using Microsoft.Azure.Management.DataLake.Analytics.Models;

	namespace ConsoleAcplication1
	{
		class Program
		{

			private const string SUBSCRIPTIONID = "<Enter Your Azure Subscription ID>";
			private const string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
			private const string DOMAINNAME = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.

			private static DataLakeAnalyticsAccountManagementClient _adlaClient;

			private static void Main(string[] args)
			{

				var creds = AuthenticateAzure(DOMAINNAME, CLIENTID);

				_adlaClient = new DataLakeAnalyticsAccountManagementClient(creds);
				_adlaClient.SubscriptionId = SUBSCRIPTIONID;

				var adlaAccounts = ListADLAAccounts();

				Console.WriteLine("You have %i Data Lake Analytics account(s).", adlaAccounts.Count);
				for (int i = 0; i < adlaAccounts.Count; i ++)
				{
					Console.WriteLine(adlaAccounts[i].Name);
				}

				System.Console.WriteLine("Press ENTER to continue");
				System.Console.ReadLine();
			}

			public static ServiceClientCredentials AuthenticateAzure(
			string domainName,
			string nativeClientAppCLIENTID)
			{
				// User login via interactive popup
				SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
				// Use the client ID of an existing AAD "Native Client" application.
				var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientAppCLIENTID, new Uri("urn:ietf:wg:oauth:2.0:oob"));
				return UserTokenProvider.LoginWithPromptAsync(domainName, activeDirectoryClientSettings).Result;
			}

			public static List<DataLakeAnalyticsAccount> ListADLAAccounts()
			{
				var response = _adlaClient.Account.List();
				var accounts = new List<DataLakeAnalyticsAccount>(response);

				while (response.NextPageLink != null)
				{
					response = _adlaClient.Account.ListNext(response.NextPageLink);
					accounts.AddRange(response);
				}

				return accounts;
			}
		}
	}


## Gestire account

Prima di eseguire qualsiasi processo di Analisi Data Lake, è necessario disporre di un account di Analisi Data Lake. A differenza di Azure HDInsight, un account di Analisi non è soggetto ad alcun pagamento fino a quando il processo non è in esecuzione. Il pagamento, infatti, viene richiesto solo per la durata di esecuzione di un processo. Per altre informazioni, vedere [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md).

###Creare account

È necessario disporre di un gruppo di gestione delle risorse di Azure e un account Data Lake Store prima di poter eseguire l'esempio seguente.

Il codice seguente mostra come creare il gruppo di risorse:

	public static async Task<ResourceGroup> CreateResourceGroupAsync(
		ServiceClientCredentials credential,
		string groupName,
		string subscriptionId,
		string location)
	{

		Console.WriteLine("Creating the resource group...");
		var resourceManagementClient = new ResourceManagementClient(credential)
		{ SubscriptionId = subscriptionId };
		var resourceGroup = new ResourceGroup { Location = location };
		return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
	}

Il codice seguente mostra come creare un account Data Lake Store:

	var adlsParameters = new DataLakeStoreAccount(location: _location);
	_adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

Il codice seguente mostra come creare un account Data Lake Analytics:

	var defaultAdlsAccount = new List<DataLakeStoreAccountInfo> { new DataLakeStoreAccountInfo(adlsAccountName, new DataLakeStoreAccountInfoProperties()) };
	var adlaProperties = new DataLakeAnalyticsAccountProperties(defaultDataLakeStoreAccount: adlsAccountName, dataLakeStoreAccounts: defaultAdlsAccount);
	var adlaParameters = new DataLakeAnalyticsAccount(properties: adlaProperties, location: location);
	var adlaAccount = _adlaClient.Account.Create(resourceGroupName, adlaAccountName, adlaParameters);

###Elencare gli account

Vedere [Connettersi ad Azure Data Lake Analytics](#connect_to_azure_data_lake_analytics).

###Trovare un account

Dopo aver ottenuto un oggetto di un elenco di account Data Lake Analytics, è possibile usare quanto segue per trovare un account:

	Predicate<DataLakeAnalyticsAccount> accountFinder = (DataLakeAnalyticsAccount a) => { return a.Name == adlaAccountName; };
	var myAdlaAccount = adlaAccounts.Find(accountFinder);

###Eliminare gli account di Data Lake Analytics

Il frammento di codice seguente elimina un account Data Lake Analytics:

	_adlaClient.Account.Delete(resourceGroupName, adlaAccountName);

<!-- ################################ -->
<!-- ################################ -->
## Gestire le origini dati degli account

Data Lake Analytics attualmente supporta le seguenti origini dati:

- [Archiviazione di Azure Data Lake](../data-lake-store/data-lake-store-overview.md)
- [Archiviazione di Azure](../storage/storage-introduction.md)

Quando si crea un account di Analytics, è necessario impostare un account di archiviazione di Azure Data Lake come account di archiviazione predefinito. L'account di Data Lake Store predefinito viene usato per archiviare i metadati e i log di controllo dei processi. Dopo aver creato un account di Analytics, è possibile aggiungere altri account di archiviazione di Data Lake e/o account di archiviazione di Azure.

### Trovare l'account di Data Lake Store predefinito

Consultare la sezione Trovare un account di questo articolo per trovare l'account Data Lake Analytics, quindi usare il comando seguente:

	string adlaDefaultDataLakeStoreAccountName = myAccount.Properties.DefaultDataLakeStoreAccount;


## Usare i gruppi Gestione risorse di Azure

Le applicazioni sono in genere costituite da molti componenti, ad esempio app Web, database, server di database, risorsa di archiviazione e servizi di terze parti. Azure Resource Manager consente di usare le risorse dell'applicazione come gruppo, detto Gruppo di risorse di Azure. È quindi possibile distribuire, aggiornare, monitorare o eliminare tutte le risorse per l'applicazione con una singola operazione coordinata. È possibile descrivere le risorse del gruppo in un modello JSON per la distribuzione e quindi usare tale modello per ambienti diversi, ad esempio di testing, staging e produzione. È possibile chiarire la fatturazione per l'organizzazione visualizzando i costi per l'intero gruppo. Per altre informazioni, vedere [Panoramica di Gestione risorse di Azure](../resource-group-overview.md).

Un servizio di Analisi Data Lake può includere i componenti seguenti:

- Account di Azure Data Lake Analytics
- Account di archiviazione predefinito obbligatorio di Azure Data Lake
- Account di archiviazione aggiuntivi di Azure Data Lake
- Account di archiviazione aggiuntivi di Azure

È possibile creare tutti questi componenti in un unico gruppo di gestione delle risorse per semplificarne la gestione.

![Account e archiviazione di Azure Data Lake Analytics](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Un account di Analisi Data Lake e gli account di archiviazione dipendenti devono trovarsi nello stesso data center di Azure, mentre il gruppo di gestione delle risorse può trovarsi anche in un data center diverso.

##Vedere anche 

- [Panoramica di Analisi Microsoft Azure Data Lake](data-lake-analytics-overview.md)
- [Esercitazione: Introduzione ad Azure Data Lake Analytics con il portale di Azure](data-lake-analytics-get-started-portal.md)
- [Gestire Azure Data Lake Analytics tramite il portale di Azure](data-lake-analytics-manage-use-portal.md)
- [Monitorare e risolvere i problemi dei processi di Azure Data Lake Analytics tramite il portale di Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

<!---HONumber=AcomDC_0928_2016-->