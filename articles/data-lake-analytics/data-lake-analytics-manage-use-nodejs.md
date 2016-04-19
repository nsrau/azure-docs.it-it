<properties
   pageTitle="Gestire Azure Data Lake Analytics tramite Azure SDK per Node.js | Azure"
   description="Informazioni su come gestire gli account, le origini dati, i processi e gli utenti di Data Lake Analytics tramite Azure SDK per Node.js"
   services="data-lake-analytics"
   documentationCenter=""
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="12/11/2015"
   ms.author="jgao"/>

# Gestire Azure Data Lake Analytics tramite Azure SDK per Node.js


[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Azure SDK per Node. js è utilizzabile per gestire account, processi e cataloghi di Azure Data Lake Analytics. Per visualizzare l’argomento relativo alla gestione tramite altri strumenti, fare clic sul selettore di scheda riportato sopra.

**Prerequisiti**

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
- Un **account di Azure Data Lake Analytics**. Vedere [Introduzione ad Azure Data Lake Analytics tramite il portale di Azure](data-lake-analytics-get-started-portal.md) per creare un account.
- **Un'entità servizio con autorizzazioni per accedere all'account Data Lake Analytics**. Vedere [Autenticazione di un'entità servizio con Gestione risorse di Azure](../resource-group-authenticate-service-principal.md).

## Installare l'SDK

Utilizzare la procedura seguente per installare l’SDK:

1. Installare [Node.js](https://nodejs.org/).
2. Eseguire i comandi seguenti dalla finestra del prompt dei comandi, del terminale o della bash:

		npm install async
		npm install adal-node
		npm install azure-common
		npm install azure-arm-datalake-analytics

## Un esempio di Node. js

Nell'esempio seguente si ottiene l'elenco dei processi per un determinato account di Azure Data Lake Analytics.

	var async = require('async');
	var adalNode = require('adal-node');
	var azureCommon = require('azure-common');
	var azureDataLakeAnalytics = require('azure-arm-datalake-analytics');

	var resourceUri = 'https://management.core.windows.net/';
	var loginUri = 'https://login.windows.net/'

	var clientId = 'application_id_(guid)';
	var clientSecret = 'application_password';

	var tenantId = 'aad_tenant_id';
	var subscriptionId = 'azure_subscription_id';
	var resourceGroup = 'adla_resourcegroup_name';

	var accountName = 'adla_account_name';

	var context = new adalNode.AuthenticationContext(loginUri+tenantId);

	var client;
	var response;

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

			client = azureDataLakeAnalytics.createDataLakeAnalyticsJobManagementClient(credentials, 'azuredatalakeanalytics.net');

			next();
		},
		function (next) {
			client.jobs.list(resourceGroup, accountName, function(err, result){
				if (err) throw err;
				console.log(result);
			});
		}
	]);


##Vedere anche

- [Azure SDK per Node.js](http://azure.github.io/azure-sdk-for-node/)
- [Panoramica di Analisi Microsoft Azure Data Lake](data-lake-analytics-overview.md)
- [Introduzione a Analisi Data Lake tramite il portale di Azure](data-lake-analytics-get-started-portal.md)
- [Gestire Analisi Data Lake di Azure tramite il portale di Azure](data-lake-analytics-manage-use-portal.md)
- [Monitorare e risolvere i problemi dei processi di Azure Data Lake Analytics tramite il portale di Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

<!---HONumber=AcomDC_0413_2016-->