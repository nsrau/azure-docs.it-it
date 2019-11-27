---
title: Log di diagnostica e integrità back-end
titleSuffix: Azure Application Gateway
description: Informazioni su come abilitare e gestire i log di accesso e delle prestazioni per il gateway applicazione
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/22/2019
ms.author: victorh
ms.openlocfilehash: bfae540af1c501c09ec026b97ac11e8a14b177a9
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326540"
---
# <a name="back-end-health-and-diagnostic-logs-for-application-gateway"></a>Log di diagnostica e integrità back-end per il gateway applicazione

È possibile monitorare applicazione Azure risorse del gateway nei modi seguenti:

* [Integrità back-end](#back-end-health): il gateway applicazione consente di monitorare l'integrità dei server nei pool back-end tramite il portale di Azure e PowerShell. L'integrità dei pool back-end è disponibile anche tramite i log di diagnostica delle prestazioni.

* [Log](#diagnostic-logging): i log consentono di salvare o usare i dati delle prestazioni, di accesso e di altre tipologie da una risorsa a fini di monitoraggio.

* [Metriche](application-gateway-metrics.md): il gateway applicazione include diverse metriche che consentono di verificare che il sistema funzioni come previsto.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="back-end-health"></a>Integrità back-end

Il gateway applicazione consente di monitorare l'integrità dei singoli membri dei pool back-end tramite il portale, PowerShell e l'interfaccia della riga di comando (CLI). Le informazioni di riepilogo dell'integrità dei pool back-end sono disponibili anche tramite i log di diagnostica delle prestazioni. 

Il report sull'integrità back-end riflette l'output del probe di integrità del gateway applicazione nelle istanze back-end. Quando l'esecuzione del probe ha esito positivo e il back-end può ricevere il traffico, il membro è considerato integro. In caso contrario, viene considerato non integro.

> [!IMPORTANT]
> Se è presente un gruppo di sicurezza di rete (NSG) in una subnet del gateway applicazione, aprire gli intervalli di porte 65503-65534 per gli SKU V1 e 65200-65535 per gli SKU V2 nella subnet del gateway applicazione per il traffico in ingresso. Questo intervallo di porte è necessario per la comunicazione di infrastruttura di Azure. Sono protette (bloccate) dai certificati di Azure. Senza certificati appropriati, le entità esterne, compresi i clienti di questi gateway, non saranno in grado di avviare alcuna modifica su tali endpoint.


### <a name="view-back-end-health-through-the-portal"></a>Visualizzare l'integrità back-end tramite il portale

Nel portale l'integrità back-end viene visualizzata automaticamente. In un gateway applicazione esistente selezionare **Monitoraggio** > **Integrità back-end**. 

Ogni membro del pool back-end è elencato in questa pagina, indipendentemente dal fatto che si tratti di schede di interfaccia di rete, IP o FQDN. Vengono visualizzati il nome del pool back-end, la porta, il nome delle impostazioni HTTP back-end e lo stato di integrità. I valori validi per lo stato di integrità sono **Integro**, **Danneggiato** e **Sconosciuto**.

> [!NOTE]
> Se viene visualizzato lo stato di integrità back-end **Sconosciuto**, assicurarsi che l'accesso al back-end non sia bloccato da una regola del gruppo di sicurezza di rete, da una route definita dall'utente (UDR) o da un DNS personalizzato nella rete virtuale.

![Integrità back-end][10]

### <a name="view-back-end-health-through-powershell"></a>Visualizzare l'integrità back-end tramite PowerShell

Il codice PowerShell seguente illustra come visualizzare l'integrità back-end usando il cmdlet `Get-AzApplicationGatewayBackendHealth`:

```powershell
Get-AzApplicationGatewayBackendHealth -Name ApplicationGateway1 -ResourceGroupName Contoso
```

### <a name="view-back-end-health-through-azure-cli"></a>Visualizzare l'integrità back-end tramite l'interfaccia della riga di comando di Azure

```azurecli
az network application-gateway show-backend-health --resource-group AdatumAppGatewayRG --name AdatumAppGateway
```

### <a name="results"></a>Risultati

Il frammento seguente illustra un esempio della risposta:

```json
{
"BackendAddressPool": {
    "Id": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/appGatewayBackendPool"
},
"BackendHttpSettingsCollection": [
    {
    "BackendHttpSettings": {
        "Id": "/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
    },
    "Servers": [
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        },
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        }
    ]
    }
]
}
```

## <a name="diagnostic-logging"></a>Log di diagnostica

In Azure è possibile usare diversi tipi di log per gestire e risolvere i problemi dei gateway applicazione. Alcuni di questi log sono accessibili tramite il portale. Tutti i log possono essere estratti dall'archiviazione BLOB di Azure e visualizzati in strumenti diversi, ad esempio i [log di Monitoraggio di Azure](../azure-monitor/insights/azure-networking-analytics.md), Excel e Power BI. L'elenco seguente contiene altre informazioni sui diversi tipi di log:

* **Log attività**: è possibile usare i [log attività di Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md), chiamati in precedenza log operativi e log di controllo, per visualizzare tutte le operazioni inviate alla sottoscrizione di Azure e il relativo stato. Le voci dei log attività vengono raccolte per impostazione predefinita e possono essere visualizzate nel portale di Azure.
* **Log di accesso**: è possibile usare questo log per visualizzare i modelli di accesso del gateway applicazione e analizzare informazioni importanti. Sono inclusi l'indirizzo IP del chiamante, l'URL richiesto, la latenza della risposta, il codice restituito e i byte in uscita e in uscita. Un log di accesso viene raccolto ogni 300 secondi. Il log contiene un record per ogni istanza del gateway applicazione. L'istanza del gateway applicazione può essere identificata dalla proprietà instanceId.
* **Log delle prestazioni**: è possibile usare questo log per visualizzare le prestazioni delle istanze del gateway applicazione. Questo log acquisisce le informazioni sulle prestazioni di ogni istanza, inclusi il totale delle richieste servite, la velocità effettiva in byte, il totale delle richieste non riuscite e il numero delle istanze back-end integre e non integre. Il log delle prestazioni viene raccolto ogni 60 secondi. Il log delle prestazioni è disponibile solo per lo SKU V1. Per lo SKU V2, usare le [metriche](application-gateway-metrics.md) per i dati sulle prestazioni.
* **Log del firewall**: è possibile usare questo log per visualizzare le richieste registrate tramite la modalità di rilevamento o prevenzione di un gateway applicazione configurato con il web application firewall.

> [!NOTE]
> I log sono disponibili solo per le risorse distribuite nel modello di distribuzione di Azure Resource Manager. Non è possibile usare i log per le risorse nel modello di distribuzione classica. Per altre informazioni sui due modelli, vedere le [informazioni sulla distribuzione di Resource Manager e la distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md).

Sono disponibili tre opzioni di archiviazione dei log:

* **Account di archiviazione**: ideali quando i log vengono archiviati per un periodo più lungo ed esaminati quando necessario.
* **Hub eventi**: gli hub eventi sono un'ottima opzione per l'integrazione con altri strumenti di gestione di informazioni ed eventi di sicurezza (Siem) per ricevere avvisi sulle risorse.
* **Log di monitoraggio di Azure**: i log di monitoraggio di Azure sono ideali per il monitoraggio generale in tempo reale dell'applicazione o l'analisi delle tendenze.

### <a name="enable-logging-through-powershell"></a>Abilitare la registrazione tramite PowerShell

Registrazione attività viene abilitata automaticamente per tutte le risorse di Resource Manager. È necessario abilitare la registrazione degli accessi e delle prestazioni per iniziare a raccogliere i dati disponibili tramite tali log. Per abilitare la registrazione, seguire questa procedura:

1. Prendere nota dell'ID risorsa dell'account di archiviazione in cui vengono archiviati i dati dei log. Il valore ha il formato seguente: /subscriptions/\<subscriptionId\>/resourceGroups/\<nome gruppo di risorse\>/providers/Microsoft.Storage/storageAccounts/\<nome account archiviazione\>. È possibile usare qualsiasi account di archiviazione della sottoscrizione. Per trovare queste informazioni è possibile usare il portale di Azure.

    ![Portale: ID risorsa dell'account di archiviazione](./media/application-gateway-diagnostics/diagnostics1.png)

2. Prendere nota dell'ID risorsa del gateway applicazione per cui è abilitata la registrazione. Il valore ha il formato seguente: /subscriptions/\<subscriptionId\>/resourceGroups/\<nome gruppo di risorse\>/providers/Microsoft.Network/applicationGateways/\<nome gateway applicazione\>. Per trovare queste informazioni è possibile usare il portale.

    ![Portale: ID risorsa del gateway applicazione](./media/application-gateway-diagnostics/diagnostics2.png)

3. Abilitare la registrazione diagnostica usando il cmdlet di PowerShell seguente:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```
    
> [!TIP] 
>I log attività non richiedono un account di archiviazione separato. Per l'uso del servizio di archiviazione per la registrazione degli accessi e delle prestazioni è previsto un addebito.

### <a name="enable-logging-through-the-azure-portal"></a>Abilitare la registrazione tramite il portale di Azure

1. Nella portale di Azure trovare la risorsa e selezionare impostazioni di **diagnostica**.

   Per il gateway applicazione sono disponibili tre log:

   * Log di accesso
   * Log delle prestazioni
   * Log del firewall

2. Per avviare la raccolta dei dati, selezionare **attiva diagnostica**.

   ![Attivare la diagnostica][1]

3. La pagina **Impostazioni di diagnostica** include le impostazioni per i log di diagnostica. In questo esempio viene usato Log Analytics per archiviare i log. Per salvare i log di diagnostica è possibile anche usare l'hub eventi e un account di archiviazione.

   ![Avvio del processo di configurazione][2]

5. Digitare un nome per le impostazioni, confermare le impostazioni e selezionare **Salva**.

### <a name="activity-log"></a>Log attività

Azure genera il log attività per impostazione predefinita. I log vengono conservati per 90 giorni nell'archivio dei log eventi di Azure. Per altre informazioni sui log, leggere l'articolo [Visualizzare eventi e log attività](../monitoring-and-diagnostics/insights-debugging-with-events.md).

### <a name="access-log"></a>Log di accesso

Il log di accesso viene generato solo se è stato abilitato in ogni istanza del gateway applicazione, come descritto nei passaggi precedenti. I dati vengono archiviati nell'account di archiviazione specificato quando è stata abilitata la registrazione. Ogni accesso del gateway applicazione viene registrato in formato JSON, come illustrato nell'esempio seguente per V1:

|Valore  |DESCRIZIONE  |
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
|sslEnabled| Indica se la comunicazione con i pool back-end ha usato SSL. I valori validi sono on e off.|
|host| Nome host a cui è stata inviata la richiesta al server back-end. Se viene eseguito l'override del nome host back-end, questo nome rifletterà tale nome.|
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
Per il gateway applicazione e WAF V2, i log mostrano alcune altre informazioni:

|Valore  |DESCRIZIONE  |
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
|timeTaken| Periodo di tempo (in **secondi**) necessario per l'elaborazione di una richiesta e la relativa risposta da inviare. Questo valore corrisponde all'intervallo di tempo intercorso dal momento in cui il gateway applicazione riceve il primo byte di una richiesta HTTP al termine dell'operazione di invio della risposta. È importante notare che il campo Tempo impiegato include in genere il tempo della trasmissione in rete dei pacchetti di richiesta e risposta. |
|sslEnabled| Indica se la comunicazione con i pool back-end ha usato SSL. I valori validi sono on e off.|
|sslCipher| Pacchetto di crittografia usato per la comunicazione SSL (se SSL è abilitato).|
|sslProtocol| Protocollo SSL/TLS usato (se SSL è abilitato).|
|serverRouted| Il server back-end a cui il gateway applicazione instrada la richiesta.|
|serverStatus| Codice di stato HTTP del server back-end.|
|serverResponseLatency| Latenza della risposta dal server back-end.|
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

Il log delle prestazioni viene generato solo se è stato abilitato in ogni istanza del gateway applicazione, come descritto nei passaggi precedenti. I dati vengono archiviati nell'account di archiviazione specificato quando è stata abilitata la registrazione. I dati del log delle prestazioni vengono generati a intervalli di un minuto. È disponibile solo per lo SKU V1. Per lo SKU V2, usare le [metriche](application-gateway-metrics.md) per i dati sulle prestazioni. Vengono registrati i dati seguenti:


|Valore  |DESCRIZIONE  |
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


|Valore  |DESCRIZIONE  |
|---------|---------|
|instanceId     | Istanza del gateway applicazione per cui vengono generati i dati del firewall. Per un gateway applicazione a più istanze viene visualizzata una riga per ogni istanza.         |
|clientIp     |   IP di origine della richiesta.      |
|clientPort     |  Porta di origine della richiesta.       |
|requestUri     | URL della richiesta ricevuta.       |
|ruleSetType     | Tipo di set di regole. Il valore disponibile è OWASP.        |
|ruleSetVersion     | Versione del set di regole usata. I valori disponibili sono 2.2.9 e 3.0.     |
|ruleId     | ID regola dell'evento di attivazione.        |
|message     | Messaggio descrittivo dell'evento di attivazione. Altre informazioni sono disponibili nella sezione dei dettagli.        |
|action     |  Azione eseguita sulla richiesta. I valori disponibili vengono abbinati e bloccati.      |
|site     | Sito per cui è stato generato il log. Attualmente viene visualizzato solo Global poiché le regole sono globali.|
|informazioni dettagliate     | Dettagli dell'evento di attivazione.        |
|details.message     | Descrizione della regola.        |
|details.data     | Dati specifici individuati nella richiesta corrispondente alla regola.         |
|details.file     | File di configurazione che conteneva la regola.        |
|details.line     | Numero di riga nel file di configurazione che ha attivato l'evento.       |
|hostname   | Nome host o indirizzo IP del gateway applicazione.    |
|transactionId  | ID univoco per una transazione specifica che consente di raggruppare più violazioni delle regole che si sono verificate nella stessa richiesta.   |

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    "instanceId": "ApplicationGatewayRole_IN_0",
    "clientIp": "104.210.252.3",
    "clientPort": "4835",
    "requestUri": "/?a=%3Cscript%3Ealert(%22Hello%22);%3C/script%3E",
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "ruleId": "941320",
    "message": "Possible XSS Attack Detected - HTML Tag Handler",
    "action": "Blocked",
    "site": "Global",
    "details": {
      "message": "Warning. Pattern match \"<(a|abbr|acronym|address|applet|area|audioscope|b|base|basefront|bdo|bgsound|big|blackface|blink|blockquote|body|bq|br|button|caption|center|cite|code|col|colgroup|comment|dd|del|dfn|dir|div|dl|dt|em|embed|fieldset|fn|font|form|frame|frameset|h1|head|h ...\" at ARGS:a.",
      "data": "Matched Data: <script> found within ARGS:a: <script>alert(\\x22hello\\x22);</script>",
      "file": "rules/REQUEST-941-APPLICATION-ATTACK-XSS.conf",
      "line": "865"
    }
    "hostname": "40.90.218.100", 
    "transactionId": "AYAcUqAcAcAcAcAcASAcAcAc"
  }
} 

```

### <a name="view-and-analyze-the-activity-log"></a>Visualizzare e analizzare Log attività

È possibile visualizzare e analizzare i dati del log attività usando uno dei metodi seguenti:

* **Strumenti di Azure**: recuperare le informazioni dal log attività con Azure PowerShell, l'interfaccia della riga di comando (CLI) di Azure, l'API REST di Azure o il portale di Azure. Le istruzioni dettagliate di ciascun metodo sono fornite nell'articolo [Operazioni attività con Resource Manager](../azure-resource-manager/resource-group-audit.md).
* **Power BI**: se non si ha ancora un account [Power BI](https://powerbi.microsoft.com/pricing) , è possibile crearne uno di prova gratuitamente. Usando le [app modello di Power bi](https://docs.microsoft.com/power-bi/service-template-apps-overview), è possibile analizzare i dati.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>Visualizzare e analizzare i log di accesso, delle prestazioni e del firewall

I [log di Monitoraggio di Azure](../azure-monitor/insights/azure-networking-analytics.md) consentono di raccogliere i file dei log contatori ed eventi dall'account di archiviazione BLOB. Include visualizzazioni e funzionalità di ricerca avanzate per analizzare i log.

È anche possibile connettersi all'account di archiviazione e recuperare le voci del log JSON per i log di accesso e delle prestazioni. Dopo avere scaricato i file JSON, è possibile convertirli in CSV e visualizzarli in Excel, PowerBI o un altro strumento di visualizzazione dei dati.

> [!TIP]
> Se si ha familiarità con Visual Studio e i concetti di base della modifica dei valori di costanti e variabili in C#, è possibile usare i [convertitori di log](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponibili in GitHub.
> 
> 

#### <a name="analyzing-access-logs-through-goaccess"></a>Analisi dei log di accesso con GoAccess

È stato pubblicato un modello di Resource Manager che installa ed esegue il diffuso analizzatore di log [GoAccess](https://goaccess.io/) per i log di accesso del gateway applicazione. GoAccess offre utili statistiche sul traffico HTTP, come ad esempio visitatori univoci, file richiesti, host, sistemi operativi, browser, codici di stato HTTP e altro ancora. Per altre informazioni, vedere il [file Readme nella cartella del modello di Resource Manager in GitHub](https://aka.ms/appgwgoaccessreadme).

## <a name="next-steps"></a>Passaggi successivi

* Visualizzare i log contatori ed eventi usando i [log di Monitoraggio di Azure](../azure-monitor/insights/azure-networking-analytics.md).
* Post di blog [Visualize your Azure activity log with Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) (Visualizzare il log attività di Azure con Power BI).
* Post di blog [View and analyze Azure activity logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) (Visualizzare e analizzare i log attività di Azure in Power BI e altre opzioni).

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
