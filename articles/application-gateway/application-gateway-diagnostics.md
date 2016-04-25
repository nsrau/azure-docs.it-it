<properties 
   pageTitle="Monitorare i log di accesso e delle prestazioni per Application Gateway | Microsoft Azure"
   description="Informazioni su come abilitare e gestire i log di accesso e delle prestazioni per il gateway applicazione"
   services="application-gateway"
   documentationCenter="na"
   authors="amitsriva"
   manager="rossort"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/11/2016"
   ms.author="amitsriva" />

#Registrazione diagnostica per il gateway applicazione

In Azure è possibile usare diversi tipi di log per gestire e risolvere i problemi dei gateway applicazione. Alcuni di questi log sono accessibili dal portale e tutti i log possono essere estratti da un archivio BLOB di Azure e visualizzati in diversi strumenti, ad esempio Excel e PowerBI. L'elenco seguente contiene altre informazioni sui diversi tipi di log.

- **Log di controllo:** è possibile usare i [log di controllo di Azure](../azure-portal/insights-debugging-with-events.md) (noti in precedenza come log operativi) per visualizzare tutte le operazioni da inviare alle sottoscrizioni di Azure e il relativo stato. I log di controllo sono abilitati per impostazione predefinita e possono essere visualizzati nel portale di anteprima di Azure.
- **Log di accesso:** è possibile usare questo log per visualizzare il modello di accesso del gateway applicazione e analizzare informazioni importanti, inclusi IP del chiamante, URL richiesto, latenza della risposta, codice restituito, byte in ingresso e in uscita. Il log di accesso viene raccolto ogni 300 secondi. Il log contiene un record per ogni istanza del gateway applicazione. L'istanza del gateway applicazione può essere identificata dalla proprietà 'instanceId'.
- **Log delle prestazioni:** è possibile usare questo log per visualizzare la modalità di esecuzione delle istanze del gateway applicazione. Questo log acquisisce le informazioni sulle prestazioni di ogni singola istanza, inclusi totale delle richieste servite, velocità effettiva in byte, numero di richieste non riuscite e numero di istanze di back-end corrette e non corrette. Il log delle prestazioni viene raccolto ogni 60 secondi.

>[AZURE.WARNING] I log sono disponibili solo per le risorse distribuite nel modello di distribuzione di Gestione risorse. Non è possibile usare i log per le risorse nel modello di distribuzione classica. Per altre informazioni sui due modelli, vedere l'articolo [Comprendere la distribuzione di Gestione risorse e distribuzione classica](../resource-manager-deployment-model.md).

##Abilitazione della registrazione
La registrazione di controllo viene abilitata automaticamente in qualsiasi momento per ogni risorsa di Gestione risorse. È necessario abilitare la registrazione degli accessi e delle prestazioni per iniziare a raccogliere i dati disponibili in tali log. Per abilitare la registrazione, seguire questa procedura.

1. Prendere nota dell'ID di risorsa dell'account di archiviazione in cui verranno archiviati i dati di log. Il formato sarà il seguente: /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>. Può essere usato qualsiasi account di archiviazione della sottoscrizione. È possibile usare il portale di anteprima per reperire queste informazioni. ![Portale di anteprima: Diagnostica del gateway applicazione](./media/application-gateway-diagnostics/diagnostics1.png)
 
2. Prendere nota dell'ID di risorsa del gateway applicazione per cui abilitare la registrazione. Il formato sarà il seguente: /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>. È possibile usare il portale di anteprima per reperire queste informazioni. ![Portale di anteprima: Diagnostica del gateway applicazione](./media/application-gateway-diagnostics/diagnostics2.png)

3. Abilitare la registrazione diagnostica usando il cmdlet di PowerShell seguente.

		Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true 	

>[AZURE.INFORMATION] I log di controllo non richiedono un account di archiviazione separato. Per l'uso del servizio di archiviazione per la registrazione degli accessi e delle prestazioni è previsto un addebito.


## Log di controllo
Questi log (noti in precedenza come "log operativi") vengono generati da Azure per impostazione predefinita. I log vengono conservati per 90 giorni nell'archivio dei registri eventi di Azure. Per altre informazioni su questi log, vedere l'articolo [Visualizzare eventi e log di controllo](../azure-portal/insights-debugging-with-events.md).

## Log di accesso
Questo log viene generato solo se è stato abilitato per il singolo gateway applicazione come descritto in precedenza. I dati vengono archiviati nell'account di archiviazione specificato quando è stata abilitata la registrazione. Ogni accesso del gateway applicazione viene registrato nel formato JSON come illustrato di seguito.

	{
		"resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resoource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
		"operationName": "ApplicationGatewayAccess",
		"time": "2016-04-11T04:24:37Z",
		"category": "ApplicationGatewayAccessLog",
		"properties": {
			"instanceId":"ApplicationGatewayRole_IN_0",
			"clientIP":"37.186.113.170",
			"clientPort":"12345",
			"httpMethod":"HEAD",
			"requestUri":"/xyz/portal",
			"requestQuery":"",
			"userAgent":"-",
			"httpStatus":"200",
			"httpVersion":"HTTP/1.0",
			"receivedBytes":"27",
			"sentBytes":"202",
			"timeTaken":"359",
			"sslEnabled":"off"
		}
	}


## Log delle prestazioni
Questo log viene generato solo se è stato abilitato per il singolo gateway applicazione come descritto in precedenza. I dati vengono archiviati nell'account di archiviazione specificato quando è stata abilitata la registrazione. Vengono registrati i dati seguenti:

	{
		"resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
		"operationName": "ApplicationGatewayPerformance",
		"time": "2016-04-09T00:00:00Z",
		"category": "ApplicationGatewayPerformanceLog",
		"properties": 
		{
			"instanceId":"ApplicationGatewayRole_IN_1",
			"healthyHostCount":"4",
			"unHealthyHostCount":"0",
			"requestCount":"185",
			"latency":"0",
			"failedRequestCount":"0",
			"throughput":"119427"
		}
	}

## Visualizzare e analizzare il log di controllo
È possibile visualizzare e analizzare i dati del log di controllo con uno dei metodi seguenti:

- **Strumenti di Azure:** recuperare le informazioni dai log di controllo tramite Azure PowerShell, l'interfaccia della riga di comando di Azure, l'API REST di Azure o il portale di anteprima di Azure. Per istruzioni dettagliate per ogni metodo, vedere l'articolo [Operazioni di controllo con Gestione risorse](../resource-group-audit.md).
- **Power BI:** se non esiste ancora un account [Power BI](https://powerbi.microsoft.com/pricing), è possibile crearne uno di prova gratuitamente. Con il [pacchetto di contenuto dei log di controllo di Azure per Power BI](https://powerbi.microsoft.com/it-IT/documentation/powerbi-content-pack-azure-audit-logs/) è possibile analizzare i dati con dashboard preconfigurati utilizzabili così come sono o personalizzabili.

## Visualizzare e analizzare il registro contatori ed eventi 
È necessario connettersi all'account di archiviazione e recuperare le voci di log JSON per i registri eventi e contatori. Dopo avere scaricato i file JSON, è possibile convertirli in CSV e visualizzarli in Excel, PowerBI o un altro strumento di visualizzazione dei dati.

>[AZURE.TIP] Se si ha familiarità con Visual Studio e i concetti di base della modifica dei valori di costanti e variabili in C#, è possibile usare i [convertitori di log](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponibili in Github.

## Passaggi successivi

- Post di blog sulla [visualizzazione dei log di controllo di Azure con Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx).
- Post di blog su [visualizzazione e analisi dei log di controllo di Azure in Power BI e altri strumenti](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).

<!---HONumber=AcomDC_0413_2016-->