---
title: Monitorare i log per Firewall applicazione Web di AzureMonitor logs for Azure Web Application Firewall
description: Informazioni su come abilitare e gestire i log e per Firewall applicazione Web di Azure
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: cb1af86e04c0b4ba0b59398161fa111fd8065042
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310059"
---
# <a name="diagnostic-logs-for-azure-web-application-firewall"></a>Log di diagnostica per Il firewall delle applicazioni Web di AzureDiagnostic logs for Azure Web Application Firewall

È possibile monitorare le risorse di Web Application Firewall utilizzando i log. È possibile salvare le prestazioni, l'accesso e altri dati o utilizzarli da una risorsa a scopo di monitoraggio.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="diagnostic-logs"></a>Log di diagnostica

In Azure è possibile usare diversi tipi di log per gestire e risolvere i problemi dei gateway applicazione. Alcuni di questi log sono accessibili tramite il portale. Tutti i log possono essere estratti dall'archiviazione BLOB di Azure e visualizzati in strumenti diversi, ad esempio i [log di Monitoraggio di Azure](../../azure-monitor/insights/azure-networking-analytics.md), Excel e Power BI. L'elenco seguente contiene altre informazioni sui diversi tipi di log:

* **Log attività**: è possibile usare i [log attività di Azure](../../azure-resource-manager/management/view-activity-logs.md), chiamati in precedenza log operativi e log di controllo, per visualizzare tutte le operazioni inviate alla sottoscrizione di Azure e il relativo stato. Le voci dei log attività vengono raccolte per impostazione predefinita e possono essere visualizzate nel portale di Azure.
* **Registro di accesso**: È possibile utilizzare questo registro per visualizzare i modelli di accesso del gateway applicazione e analizzare le informazioni importanti. Sono inclusi l'indirizzo IP del chiamante, l'URL richiesto, la latenza di risposta, il codice restituito e i byte in uscita e in uscita. Ogni 300 secondi viene raccolto un log di accesso. Il log contiene un record per ogni istanza del gateway applicazione. L'istanza del gateway applicazione può essere identificata dalla proprietà instanceId.
* **Log delle prestazioni**: è possibile usare questo log per visualizzare le prestazioni delle istanze del gateway applicazione. Questo log acquisisce le informazioni sulle prestazioni di ogni istanza, inclusi il totale delle richieste servite, la velocità effettiva in byte, il totale delle richieste non riuscite e il numero delle istanze back-end integre e non integre. Il log delle prestazioni viene raccolto ogni 60 secondi. Il log delle prestazioni è disponibile solo per lo SKU v1. Per lo SKU v2, usare [le metriche](../../application-gateway/application-gateway-metrics.md) per i dati sulle prestazioni.
* **Log del firewall**: è possibile usare questo log per visualizzare le richieste registrate tramite la modalità di rilevamento o prevenzione di un gateway applicazione configurato con il web application firewall.

> [!NOTE]
> I log sono disponibili solo per le risorse distribuite nel modello di distribuzione di Azure Resource Manager. Non è possibile usare i log per le risorse nel modello di distribuzione classica. Per altre informazioni sui due modelli, vedere le [informazioni sulla distribuzione di Resource Manager e la distribuzione classica](../../azure-resource-manager/management/deployment-models.md).

Sono disponibili tre opzioni di archiviazione dei log:

* **Account di archiviazione**: ideali quando i log vengono archiviati per un periodo più lungo ed esaminati quando necessario.
* **Hub eventi:** gli hub eventi sono un'ottima opzione per l'integrazione con altre informazioni di sicurezza e strumenti SIEM (Gestione eventi) per ricevere avvisi sulle risorse.
* Log di **Monitoraggio di Azure:** i log di Monitoraggio di Azure sono ottimali per il monitoraggio generale in tempo reale dell'applicazione o per l'esborrazione delle tendenze.

### <a name="enable-logging-through-powershell"></a>Abilitare la registrazione tramite PowerShell

Registrazione attività viene abilitata automaticamente per tutte le risorse di Resource Manager. È necessario abilitare la registrazione degli accessi e delle prestazioni per iniziare a raccogliere i dati disponibili tramite tali log. Per abilitare la registrazione, seguire questa procedura:

1. Prendere nota dell'ID risorsa dell'account di archiviazione in cui vengono archiviati i dati dei log. Il valore ha il formato seguente: /subscriptions/\<subscriptionId\>/resourceGroups/\<nome gruppo di risorse\>/providers/Microsoft.Storage/storageAccounts/\<nome account archiviazione\>. È possibile usare qualsiasi account di archiviazione della sottoscrizione. Per trovare queste informazioni è possibile usare il portale di Azure.

    ![Portale: ID risorsa dell'account di archiviazione](../media/web-application-firewall-logs/diagnostics1.png)

2. Prendere nota dell'ID risorsa del gateway applicazione per cui è abilitata la registrazione. Il valore ha il formato seguente: /subscriptions/\<subscriptionId\>/resourceGroups/\<nome gruppo di risorse\>/providers/Microsoft.Network/applicationGateways/\<nome gateway applicazione\>. Per trovare queste informazioni è possibile usare il portale.

    ![Portale: ID risorsa del gateway applicazione](../media/web-application-firewall-logs/diagnostics2.png)

3. Abilitare la registrazione diagnostica usando il cmdlet di PowerShell seguente:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```

> [!TIP]
>I log attività non richiedono un account di archiviazione separato. Per l'uso del servizio di archiviazione per la registrazione degli accessi e delle prestazioni è previsto un addebito.

### <a name="enable-logging-through-the-azure-portal"></a>Abilitare la registrazione tramite il portale di Azure

1. Nel portale di Azure individuare la risorsa e selezionare **Impostazioni di diagnostica**.

   Per il gateway applicazione sono disponibili tre log:

   * Log di accesso
   * Log delle prestazioni
   * Log del firewall

2. Per avviare la raccolta dei dati, selezionare **Attiva diagnostica**.

   ![Attivare la diagnostica][1]

3. La pagina **Impostazioni di diagnostica** include le impostazioni per i log di diagnostica. In questo esempio viene usato Log Analytics per archiviare i log. Per salvare i log di diagnostica è possibile anche usare l'hub eventi e un account di archiviazione.

   ![Avvio del processo di configurazione][2]

5. Digitare un nome per le impostazioni, confermare le impostazioni e selezionare **Salva**.

### <a name="activity-log"></a>Log attività

Azure genera il log attività per impostazione predefinita. I log vengono conservati per 90 giorni nell'archivio dei log eventi di Azure. Per altre informazioni sui log, leggere l'articolo [Visualizzare eventi e log attività](../../azure-resource-manager/management/view-activity-logs.md).

### <a name="access-log"></a>Log di accesso

Il log di accesso viene generato solo se è stato abilitato in ogni istanza del gateway applicazione, come descritto nei passaggi precedenti. I dati vengono archiviati nell'account di archiviazione specificato quando è stata abilitata la registrazione. Ogni accesso al gateway applicazione viene registrato in formato JSON, come illustrato nell'esempio seguente per la versione 1:Each access of Application Gateway is logged in JSON format, as shown in the following example for v1:

|valore  |Descrizione  |
|---------|---------|
|instanceId     | Istanza del gateway applicazione che ha gestito la richiesta.        |
|clientIP     | IP di origine della richiesta.        |
|clientPort     | Porta di origine della richiesta.       |
|httpMethod     | Metodo HTTP usato dalla richiesta.       |
|requestUri     | URI della richiesta ricevuta.        |
|RequestQuery     | **Server-Routed**: istanza del pool back-end a cui è stata inviata la richiesta.</br>**X-AzureApplicationGateway-LOG-ID**: ID correlazione usato per la richiesta. Può essere usato per risolvere i problemi di traffico nei server back-end. </br>**SERVER-STATUS**: codice di risposta HTTP ricevuto dal gateway applicazione dal back-end.       |
|UserAgent     | Agente utente dell'intestazione della richiesta HTTP.        |
|httpStatus     | Codice di stato HTTP restituito al client dal gateway applicazione.       |
|httpVersion     | Versione HTTP della richiesta.        |
|receivedBytes     | Dimensione del pacchetto ricevuto, espressa in byte.        |
|sentBytes| Dimensione del pacchetto inviato, espressa in byte.|
|timeTaken| Periodo di tempo in millisecondi impiegato per l'elaborazione di una richiesta e l'invio della risposta. Questo valore corrisponde all'intervallo di tempo intercorso dal momento in cui il gateway applicazione riceve il primo byte di una richiesta HTTP al termine dell'operazione di invio della risposta. È importante notare che il campo Tempo impiegato include in genere il tempo della trasmissione in rete dei pacchetti di richiesta e risposta. |
|sslEnabled| Se la comunicazione con i pool back-end utilizzava TLS/SSL. I valori validi sono on e off.|
|host| Nome host con cui la richiesta è stata inviata al server back-end. Se viene eseguito l'override del nome host back-end, questo nome lo rifletterà.|
|originalHost| Nome host con cui la richiesta è stata ricevuta dal gateway applicazione dal client.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "ApplicationGatewayRole_IN_0",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "requestQuery": "X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.4.0.4&X-AzureApplicationGateway-LOG-ID=874f1f0f-6807-41c9-b7bc-f3cfa74aa0b1&SERVER-STATUS=404",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "host": "www.contoso.com",
        "originalHost": "www.contoso.com"
    }
}
```
Per il gateway applicazione e WAF v2, i registri mostrano ulteriori informazioni:

|valore  |Descrizione  |
|---------|---------|
|instanceId     | Istanza del gateway applicazione che ha gestito la richiesta.        |
|clientIP     | IP di origine della richiesta.        |
|clientPort     | Porta di origine della richiesta.       |
|httpMethod     | Metodo HTTP usato dalla richiesta.       |
|requestUri     | URI della richiesta ricevuta.        |
|UserAgent     | Agente utente dell'intestazione della richiesta HTTP.        |
|httpStatus     | Codice di stato HTTP restituito al client dal gateway applicazione.       |
|httpVersion     | Versione HTTP della richiesta.        |
|receivedBytes     | Dimensione del pacchetto ricevuto, espressa in byte.        |
|sentBytes| Dimensione del pacchetto inviato, espressa in byte.|
|timeTaken| Periodo di tempo in millisecondi impiegato per l'elaborazione di una richiesta e l'invio della risposta. Questo valore corrisponde all'intervallo di tempo intercorso dal momento in cui il gateway applicazione riceve il primo byte di una richiesta HTTP al termine dell'operazione di invio della risposta. È importante notare che il campo Tempo impiegato include in genere il tempo della trasmissione in rete dei pacchetti di richiesta e risposta. |
|sslEnabled| Se la comunicazione con i pool back-end ha utilizzato TLS. I valori validi sono on e off.|
|sslCipher| Suite di crittografia utilizzata per la comunicazione TLS (se TLS è abilitato).|
|sslProtocol| Protocollo TLS in uso (se TLS è abilitato).|
|serverRouted (in modo inquestoendo)| Il server back-end a cui il gateway applicazione instrada la richiesta.|
|serverStatus| Codice di stato HTTP del server back-end.|
|serverResponseLatency (Latenzaserver)| Latenza della risposta dal server back-end.|
|host| Indirizzo elencato nell'intestazione host della richiesta.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "appgw_1",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "sslCipher": "",
        "sslProtocol": "",
        "serverRouted": "104.41.114.59:80",
        "serverStatus": "200",
        "serverResponseLatency": "0.023",
        "host": "www.contoso.com",
    }
}
```

### <a name="performance-log"></a>Log delle prestazioni

Il log delle prestazioni viene generato solo se è stato abilitato in ogni istanza del gateway applicazione, come descritto nei passaggi precedenti. I dati vengono archiviati nell'account di archiviazione specificato quando è stata abilitata la registrazione. I dati del log delle prestazioni vengono generati a intervalli di un minuto. È disponibile solo per lo SKU v1. Per lo SKU v2, usare [le metriche](../../application-gateway/application-gateway-metrics.md) per i dati sulle prestazioni. Vengono registrati i dati seguenti:


|valore  |Descrizione  |
|---------|---------|
|instanceId     |  Istanza del gateway applicazione per cui vengono generati i dati delle prestazioni. Per un gateway applicazione a più istanze viene visualizzata una riga per ogni istanza.        |
|healthyHostCount     | Numero di host integri nel pool back-end.        |
|unHealthyHostCount     | Numero di host non integri nel pool back-end.        |
|requestCount     | Numero di richieste gestite.        |
|latency | Latenza media in millisecondi delle richieste dall'istanza al back-end che gestisce le richieste. |
|failedRequestCount| Numero di richieste non riuscite.|
|throughput| Velocità effettiva media dall'ultimo log, misurata in byte al secondo.|

```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
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
```

> [!NOTE]
> La latenza è calcolata dal momento in cui viene ricevuto il primo byte della richiesta HTTP al momento in cui viene inviato l'ultimo byte della risposta HTTP. Si tratta della somma del tempo di elaborazione del gateway applicazione, del costo di rete per il back-end e del tempo impiegato dal back-end per elaborare la richiesta.

### <a name="firewall-log"></a>Log del firewall

Il log del firewall viene generato solo se è stato abilitato in ogni gateway applicazione, come descritto nei passaggi precedenti. Questo log richiede anche che il web application firewall sia configurato in un gateway applicazione. I dati vengono archiviati nell'account di archiviazione specificato quando è stata abilitata la registrazione. Vengono registrati i dati seguenti:


|valore  |Descrizione  |
|---------|---------|
|instanceId     | Istanza del gateway applicazione per cui vengono generati i dati del firewall. Per un gateway applicazione a più istanze viene visualizzata una riga per ogni istanza.         |
|clientIp     |   IP di origine della richiesta.      |
|clientPort     |  Porta di origine della richiesta.       |
|requestUri     | URL della richiesta ricevuta.       |
|ruleSetType     | Tipo di set di regole. Il valore disponibile è OWASP.        |
|ruleSetVersion     | Versione del set di regole usata. I valori disponibili sono 2.2.9 e 3.0.     |
|ruleId     | ID regola dell'evento di attivazione.        |
|message     | Messaggio descrittivo dell'evento di attivazione. Altre informazioni sono disponibili nella sezione dei dettagli.        |
|action     |  Azione eseguita sulla richiesta. I valori disponibili sono Blocked e Allowed.      |
|site     | Sito per cui è stato generato il log. Attualmente viene visualizzato solo Global poiché le regole sono globali.|
|dettagli     | Dettagli dell'evento di attivazione.        |
|details.message     | Descrizione della regola.        |
|details.data     | Dati specifici individuati nella richiesta corrispondente alla regola.         |
|details.file     | File di configurazione che conteneva la regola.        |
|details.line     | Numero di riga nel file di configurazione che ha attivato l'evento.       |
|hostname   | Nome host o indirizzo IP del gateway applicazione.    |
|transactionId  | ID univoco per una determinata transazione che consente di raggruppare più violazioni delle regole che si sono verificate all'interno della stessa richiesta.   |
|policyId   | ID univoco dei criteri firewall associati al gateway applicazione, al listener o al percorso.   |
|PolicyScope    | La posizione del criterio - i valori possono essere "Global", "Listener" o "Location".   |
|NomeambitoCriteri   | Nome dell'oggetto a cui viene applicato il criterio.    |

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
      "instanceId": "ApplicationGatewayRole_IN_0",
      "clientIp": "52.161.109.147",
      "clientPort": "0",
      "requestUri": "/",
      "ruleSetType": "OWASP",
      "ruleSetVersion": "3.0",
      "ruleId": "920350",
      "ruleGroup": "920-PROTOCOL-ENFORCEMENT",
      "message": "Host header is a numeric IP address",
      "action": "Matched",
      "site": "Global",
      "details": {
        "message": "Warning. Pattern match \"^[\\\\d.:]+$\" at REQUEST_HEADERS:Host ....",
        "data": "127.0.0.1",
        "file": "rules/REQUEST-920-PROTOCOL-ENFORCEMENT.conf",
        "line": "791"
      },
      "hostname": "127.0.0.1",
      "transactionId": "16861477007022634343",
      "policyId": "/subscriptions/1496a758-b2ff-43ef-b738-8e9eb5161a86/resourceGroups/drewRG/providers/Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/perListener",
      "policyScope": "Listener",
      "policyScopeName": "httpListener1"
    }
  }
}

```

### <a name="view-and-analyze-the-activity-log"></a>Visualizzare e analizzare Log attività

È possibile visualizzare e analizzare i dati del log attività usando uno dei metodi seguenti:

* **Strumenti di Azure**: recuperare le informazioni dal log attività con Azure PowerShell, l'interfaccia della riga di comando (CLI) di Azure, l'API REST di Azure o il portale di Azure. Le istruzioni dettagliate di ciascun metodo sono fornite nell'articolo [Operazioni attività con Resource Manager](../../azure-resource-manager/management/view-activity-logs.md).
* **Power BI:** se non si dispone già di un account [Power BI,](https://powerbi.microsoft.com/pricing) è possibile provarlo gratuitamente. Utilizzando le app modello di [Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)è possibile analizzare i dati.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>Visualizzare e analizzare i log di accesso, delle prestazioni e del firewall

I [log di Monitoraggio di Azure](../../azure-monitor/insights/azure-networking-analytics.md) consentono di raccogliere i file dei log contatori ed eventi dall'account di archiviazione BLOB. Include visualizzazioni e funzionalità di ricerca avanzate per analizzare i log.

È anche possibile connettersi all'account di archiviazione e recuperare le voci del log JSON per i log di accesso e delle prestazioni. Dopo avere scaricato i file JSON, è possibile convertirli in CSV e visualizzarli in Excel, PowerBI o un altro strumento di visualizzazione dei dati.

> [!TIP]
> Se si ha familiarità con Visual Studio e i concetti di base della modifica dei valori di costanti e variabili in C#, è possibile usare i [convertitori di log](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponibili in GitHub.
>
>

#### <a name="analyzing-access-logs-through-goaccess"></a>Analisi dei log di accesso con GoAccess

È stato pubblicato un modello di Resource Manager che installa ed esegue il diffuso analizzatore di log [GoAccess](https://goaccess.io/) per i log di accesso del gateway applicazione. GoAccess offre statistiche utili sul traffico HTTP, ad esempio il numero di visitatori unici, file richiesti, host, sistemi operativi, browser, codici di stato HTTP e altro ancora. Per altre informazioni, vedere il [file Readme nella cartella del modello di Resource Manager in GitHub](https://aka.ms/appgwgoaccessreadme).

## <a name="next-steps"></a>Passaggi successivi

* Visualizzare i log contatori ed eventi usando i [log di Monitoraggio di Azure](../../azure-monitor/insights/azure-networking-analytics.md).
* Post di blog [Visualize your Azure activity log with Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) (Visualizzare il log attività di Azure con Power BI).
* Post di blog [View and analyze Azure activity logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) (Visualizzare e analizzare i log attività di Azure in Power BI e altre opzioni).

[1]: ../media/web-application-firewall-logs/figure1.png
[2]: ../media/web-application-firewall-logs/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
