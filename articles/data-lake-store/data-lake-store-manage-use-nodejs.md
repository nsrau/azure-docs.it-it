<properties 
   pageTitle="Gestire Archivio Azure Data Lake tramite Azure SDK per Node.js | Azure" 
   description="Informazioni su come gestire gli account Archivio Data Lake e il file system." 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="04/07/2016"
   ms.author="nitinme"/>

# Gestire Archivio Azure Data Lake utilizzando Azure SDK per Node.js

> [AZURE.SELECTOR]
- [Portale](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [SDK per Java](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [Interfaccia della riga di comando di Azure](data-lake-store-get-started-cli.md)
- [Node.JS](data-lake-store-manage-use-nodejs.md)

L’ADK di Azure per Node.js è utilizzabile per gestire account di Archivio Azure Data Lake e il file system:

- Gestione Account: creare, ottenere, elencare, aggiornare ed eliminare.
- Gestione file system: creare, ottenere, caricare, aggiungere, scaricare, leggere, eliminare, elencare.

**Prerequisiti**

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Un account di Archivio Azure Data Lake**. Vedere [Introduzione ad Archivio Azure Data Lake tramite il portale di Azure](data-lake-store-get-started-portal.md) per creare un account.
- **Un'entità servizio con autorizzazioni per accedere all'account Data Lake Analytics**. Vedere [Autenticazione di un'entità servizio con Gestione risorse di Azure](../resource-group-authenticate-service-principal.md).

## Installare l'SDK

Utilizzare la procedura seguente per installare l’SDK:

1. Installare [Node.js](https://nodejs.org/).
2. Eseguire i comandi seguenti dalla finestra del prompt dei comandi, del terminale o della bash:

		npm install async
		npm install adal-node
		npm install azure-common
		npm install azure-arm-datalake-store
	
## Un esempio di Node.js

Nell'esempio seguente viene creato un file in un account Archivio Data Lake e vengono allegati dati a esso.

	var async = require('async');
	var adalNode = require('adal-node');
	var azureCommon = require('azure-common');
	var azureDataLakeStore = require('azure-arm-datalake-store');
	
	var resourceUri = 'https://management.core.windows.net/';
	var loginUri = 'https://login.windows.net/'
	
	var clientId = 'application_id_(guid)';
	var clientSecret = 'application_password';
	
	var tenantId = 'aad_tenant_id';
	var subscriptionId = 'azure_subscription_id';
	var resourceGroup = 'adls_resourcegroup_name';
	
	var accountName = 'adls_account_name';
	
	var context = new adalNode.AuthenticationContext(loginUri+tenantId);
	
	var client;
	var response;
	
	var destinationFilePath = '/newFileName.txt';
	var content = 'desired file contents';
	
	async.series([
		function (next) {
			context.acquireTokenWithClientCredentials(resourceUri, clientId, clientSecret, function(err, result){
				if (err) throw err;
				response = result;
				next();
			});
		},
		function (next) {
			var credentials = new azureCommon.TokenCloudCredentials({
				subscriptionId : subscriptionId,
				authorizationScheme : response.tokenType,
				token : response.accessToken
			});
		
			client = azureDataLakeStore.createDataLakeStoreFileSystemManagementClient(credentials, 'azuredatalakestore.net');
	
			next();
		},
		function (next) {
			client.fileSystem.directCreate(destinationFilePath, accountName, content, function(err, result){
				if (err) throw err;
			});
		}
	]);


##Vedere anche 

- [Azure SDK per Node.js](http://azure.github.io/azure-sdk-for-node/)
- [Gestire Azure Data Lake Analytics tramite Node.js](../data-lake-analytics/data-lake-analytics-manage-use-nodejs.md)

<!---HONumber=AcomDC_0413_2016-->