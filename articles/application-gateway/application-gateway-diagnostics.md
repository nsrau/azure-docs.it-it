<properties 
   pageTitle="Monitorare i log di accesso e delle prestazioni e le Metriche per il gateway applicazione | Microsoft Azure"
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
   ms.date="09/26/2016"
   ms.author="amitsriva" />

# Registrazione diagnostica e metriche per il gateway applicazione

Azure offre la possibilità di monitorare le risorse tramite la registrazione e le metriche

[**Registrazione**](#enable-logging-with-powershell): la registrazione consente di salvare o usare i log delle prestazioni, di accesso e di altre tipologie relativi a una risorsa per il monitoraggio.

[**Metriche**](#metrics): attualmente, il gateway applicazione dispone di una metrica. Questa metrica misura la velocità effettiva del gateway applicazione in byte al secondo.

In Azure è possibile usare diversi tipi di log per gestire e risolvere i problemi dei gateway applicazione. Alcuni di questi log sono accessibili dal portale e tutti i log possono essere estratti da un archivio BLOB di Azure e visualizzati in diversi strumenti, ad esempio [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md), Excel e PowerBI. L'elenco seguente contiene altre informazioni sui diversi tipi di log:

- **Log di controllo:** è possibile usare i [log di controllo di Azure](../azure-portal/insights-debugging-with-events.md) (noti in precedenza come log operativi) per visualizzare tutte le operazioni inviate alla sottoscrizione di Azure e il relativo stato. I log di controllo sono abilitati per impostazione predefinita e possono essere visualizzati nel portale di anteprima di Azure.
- **Log di accesso:** è possibile usare questo log per visualizzare il modello di accesso del gateway applicazione e analizzare informazioni importanti, inclusi IP del chiamante, URL richiesto, latenza della risposta, codice restituito, byte in ingresso e in uscita. Il log di accesso viene raccolto ogni 300 secondi. Il log contiene un record per ogni istanza del gateway applicazione. L'istanza del gateway applicazione può essere identificata dalla proprietà 'instanceId'.
- **Log delle prestazioni:** è possibile usare questo log per visualizzare le prestazioni delle istanze del gateway applicazione. Questo log acquisisce le informazioni sulle prestazioni di ogni singola istanza, come il totale delle richieste servite, la velocità effettiva in byte, il numero delle richieste non riuscite e il numero delle istanze back-end integre e non integre. Il log delle prestazioni viene raccolto ogni 60 secondi.
- **Log del firewall:** è possibile usare questo log per visualizzare le richieste registrate tramite la modalità di rilevamento o prevenzione di un gateway applicazione configurato con il firewall applicazione Web.

>[AZURE.WARNING] I log sono disponibili solo per le risorse distribuite nel modello di distribuzione di Gestione risorse. Non è possibile usare i log per le risorse nel modello di distribuzione classica. Per altre informazioni sui due modelli, vedere l'articolo [Comprendere la distribuzione di Gestione risorse e distribuzione classica](../resource-manager-deployment-model.md).

## Abilitare la registrazione con PowerShell

La registrazione di controllo viene abilitata automaticamente per ogni risorsa di Resource Manager. È necessario abilitare la registrazione degli accessi e delle prestazioni per iniziare a raccogliere i dati disponibili tramite tali log. Per abilitare la registrazione, seguire questa procedura:

1. Prendere nota dell'ID risorsa dell'account di archiviazione in cui vengono archiviati i dati dei log. Il formato sarà: /subscriptions/<ID sottoscrizione>/resourceGroups/<nome gruppo di risorse>/providers/Microsoft.Storage/storageAccounts/<nome account di archiviazione>. Può essere usato qualsiasi account di archiviazione della sottoscrizione. Per reperire queste informazioni è possibile usare il portale di anteprima.

	![Portale di anteprima: Diagnostica del gateway applicazione](./media/application-gateway-diagnostics/diagnostics1.png)

2. Prendere nota dell'ID di risorsa del gateway applicazione per cui abilitare la registrazione. Il formato sarà: /subscriptions/<ID sottoscrizione>/resourceGroups/<nome gruppo di risorse>/providers/Microsoft.Network/applicationGateways/<nome gateway applicazione>. Per reperire queste informazioni è possibile usare il portale di anteprima.

	![Portale di anteprima: Diagnostica del gateway applicazione](./media/application-gateway-diagnostics/diagnostics2.png)

3. Abilitare la registrazione diagnostica usando il cmdlet di PowerShell seguente:

		Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true 	

>[AZURE.INFORMATION] I log di controllo non richiedono un account di archiviazione separato. Per l'uso del servizio di archiviazione per la registrazione degli accessi e delle prestazioni è previsto un addebito.

## Abilitare la registrazione con il portale di Azure

### Passaggio 1

Nel portale di Azure passare alla risorsa. Fare clic su **Log diagnostici**. Se è la prima volta che si esegue la configurazione della diagnostica, il pannello sarà simile al seguente:

Per il gateway applicazione, sono disponibili 3 log.

- Log di accesso
- Log delle prestazioni
- Log del firewall

Fare clic su **Attiva diagnostica** per avviare la raccolta dei dati.

![pannello impostazioni di diagnostica][1]

### Passaggio 2

Nel pannello **Impostazioni diagnostica** vengono configurate le impostazioni per i log di diagnostica. In questo esempio viene usato Log Analytics per archiviare i log. Fare clic su **Configura** in **Log Analytics** per configurare l'area di lavoro. Per salvare i log di diagnostica, è possibile anche usare l'hub eventi e un account di archiviazione.

![pannello Diagnostica][2]

### Passaggio 3

Scegliere un'area di lavoro di OMS oppure crearne una nuova. In questo esempio viene usata un'area di lavoro esistente.

![area di lavoro oms][3]

### Passaggio 4

Al termine, verificare le impostazioni e fare clic su **Salva** per salvarle.

![confermare la selezione][4]

## Log di controllo

Questi log (noti in precedenza come "log operativi") vengono generati da Azure per impostazione predefinita. I log vengono conservati per 90 giorni nell'archivio dei registri eventi di Azure. Per altre informazioni su questi log, vedere l'articolo [Visualizzare eventi e log di controllo](../azure-portal/insights-debugging-with-events.md).

## Log di accesso

Questo log viene generato solo se è stato abilitato per il singolo gateway applicazione come descritto nei passaggi precedenti. I dati vengono archiviati nell'account di archiviazione specificato quando è stata abilitata la registrazione. Ogni accesso del gateway applicazione viene registrato in formato JSON, come illustrato nell'esempio seguente:

	{
		"resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
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

Questo log viene generato solo se è stato abilitato per il singolo gateway applicazione come descritto nei passaggi precedenti. I dati vengono archiviati nell'account di archiviazione specificato quando è stata abilitata la registrazione. Vengono registrati i dati seguenti:

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


## Log del firewall

Questo log viene generato solo se è stato abilitato per il singolo gateway applicazione come descritto nei passaggi precedenti. Questo log richiede anche che il firewall applicazione Web sia configurato in un gateway applicazione. I dati vengono archiviati nell'account di archiviazione specificato quando è stata abilitata la registrazione. Vengono registrati i dati seguenti:

	{
		"resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<applicationGatewayName>",
		"operationName": "ApplicationGatewayFirewall",
		"time": "2016-09-20T00:40:04.9138513Z",
		"category": "ApplicationGatewayFirewallLog",
		"properties":     {
			"instanceId":"ApplicationGatewayRole_IN_0",
			"clientIp":"108.41.16.164",
			"clientPort":1815,
			"requestUri":"/wavsep/active/RXSS-Detection-Evaluation-POST/",
			"ruleId":"OWASP_973336",
			"message":"XSS Filter - Category 1: Script Tag Vector",
			"action":"Logged",
			"site":"Global",
			"message":"XSS Filter - Category 1: Script Tag Vector",
			"details":{"message":" Warning. Pattern match "(?i)(<script","file":"/owasp_crs/base_rules/modsecurity_crs_41_xss_attacks.conf","line":"14"}}
	}

## Visualizzare e analizzare il log di controllo

È possibile visualizzare e analizzare i dati del log di controllo con uno dei metodi seguenti:

- **Strumenti di Azure:** recuperare le informazioni dai log di controllo tramite Azure PowerShell, l'interfaccia della riga di comando di Azure, l'API REST di Azure o il portale di anteprima di Azure. Per istruzioni dettagliate per ogni metodo, vedere l'articolo [Operazioni di controllo con Gestione risorse](../resource-group-audit.md).
- **Power BI:** se non esiste ancora un account [Power BI](https://powerbi.microsoft.com/pricing), è possibile crearne uno di prova gratuitamente. Con il [pacchetto di contenuto dei log di controllo di Azure per Power BI](https://powerbi.microsoft.com/it-IT/documentation/powerbi-content-pack-azure-audit-logs/) è possibile analizzare i dati con dashboard preconfigurati utilizzabili così come sono o personalizzabili.

## Visualizzare e analizzare il log di accesso, delle prestazioni e del firewall

Azure [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) consente di raccogliere i file del contatore e del registro eventi dall'account di archiviazione BLOB e include visualizzazioni e funzionalità di ricerca avanzate per analizzare i log.

È anche possibile connettersi all'account di archiviazione e recuperare le voci del log JSON per i log di accesso e delle prestazioni. Dopo avere scaricato i file JSON, è possibile convertirli in CSV e visualizzarli in Excel, PowerBI o un altro strumento di visualizzazione dei dati.

>[AZURE.TIP] Se si ha familiarità con Visual Studio e i concetti di base della modifica dei valori di costanti e variabili in C#, è possibile usare i [convertitori di log](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponibili in Github.

## Metrica

Metrica è una funzionalità di alcune risorse di Azure che consente di visualizzare i contatori delle prestazioni nel portale. Per il gateway applicazione è disponibile una metrica al momento della stesura di questo articolo. Questa metrica è la velocità effettiva e può essere visualizzata nel portale. Passare a un gateway applicazione e fare clic su **Metrics** (Metriche). Selezionare Throughput (Velocità effettiva) nella sezione **Available metrics** (Metriche disponibili) per visualizzare i valori. Nella figura seguente è possibile vedere un esempio dei filtri che possono essere applicati per visualizzare i dati in diversi intervalli di tempo.

Per un elenco delle metriche attualmente supportate, visitare [Supported metrics with Azure Monitor](../azure-portal/monitoring-supported-metrics.md) (Metriche supportate con Monitoraggio di Azure).

![visualizzazione della metrica][5]

## Regole di avviso

Le regole di avviso possono essere avviate in base alle metriche su una risorsa. Per il gateway applicazione, questo significa che un avviso può chiamare un webhook o inviare un messaggio di posta elettronica a un amministratore se la velocità effettiva del gateway applicazione è al di sopra, al di sotto o sul limite per un periodo di tempo specificato.

L'esempio seguente illustra la creazione di una regola di avviso per l'invio di un messaggio di posta elettronica a un amministratore al superamento del limite della velocità effettiva.

### Passaggio 1

Per iniziare, fare clic su **Add metric alert** (Aggiungi avviso sulla metrica). Questo può essere visualizzato anche dal pannello delle metriche.

![pannello Regole di avviso][6]

### Passaggio 2

Nel pannello **Add rule** (Aggiungi regola) compilare le sezioni relative a nome, condizione e notifica, quindi fare clic su **OK** al termine.

Lo strumento di selezione **Condition** (Condizione) accetta 4 valori: **Greater than** (Maggiore di), **Greater than or equal** (Maggiore o uguale a), **Less than** (Minore di) o **Less than or equal to** (Minore o uguale a).

Lo strumento di selezione **Period** (Periodo) è possibile inserire valori compresi tra 5 minuti e 6 ore.

Selezionando **Email owners, contributors, and readers** (Invia messaggio a proprietari, collaboratori e lettori), il messaggio di posta elettronica può essere dinamicamente basato sugli utenti che dispongono di accesso a questa risorsa. In alternativa, è possibile inserire un elenco di utenti separato da virgole nella casella di testo **Additional administrator email(s)** (Indirizzi di posta elettronica aggiuntivi dell'amministratore).

![aggiungere regole di avviso][7]

Se il limite viene superato, l'utente riceve un messaggio di posta elettronica simile al seguente:

![messaggio di posta elettronica sul superamento della soglia][8]

Dopo la creazione dell'avviso sulla metrica, viene visualizzato un elenco degli avvisi e viene fornita una panoramica su tutte le regole di avviso.

![visualizzazione della regola di avviso][9]

Per altre informazioni sulle notifiche di avviso, visitare [Receive alert notifications](../azure-portal/insights-receive-alert-notifications.md) (Ricevere notifiche di avviso).

Per maggiori informazioni sui webhook e su come usarli con gli avvisi, visitare [Come configurare i webhook per gli avvisi](../azure-portal/insights-webhooks-alerts.md)

## Passaggi successivi

- Visualizzare il registro contatori ed eventi con [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)
- Post di blog sulla [visualizzazione dei log di controllo di Azure con Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx).
- Post di blog su [visualizzazione e analisi dei log di controllo di Azure in Power BI e altri strumenti](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png

<!---HONumber=AcomDC_0928_2016-->