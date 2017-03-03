---
title: "Monitorare i log di accesso e delle prestazioni, l&quot;integrità back-end e le metriche per il gateway applicazione | Documentazione Microsoft"
description: Informazioni su come abilitare e gestire i log di accesso e delle prestazioni per il gateway applicazione
services: application-gateway
documentationcenter: na
author: amitsriva
manager: rossort
editor: tysonn
tags: azure-resource-manager
ms.assetid: 300628b8-8e3d-40ab-b294-3ecc5e48ef98
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/17/2017
ms.author: amitsriva
translationtype: Human Translation
ms.sourcegitcommit: d65b354bc972c8268f1b4f072843b5bf4977a7c4
ms.openlocfilehash: 2b37bf92ce8945996eb64477c28bea845b7df516
ms.lasthandoff: 02/09/2017


---
# <a name="backend-health-diagnostics-logging-and-metrics-for-application-gateway"></a>Integrità back-end, registrazione diagnostica e metriche per il gateway applicazione

Azure consente di monitorare le risorse tramite la registrazione e le metriche. Il gateway applicazione offre queste funzionalità con integrità back-end, registrazione e metriche.

[**Integrità back-end** ](#backend-health): il gateway applicazione consente di monitorare l'integrità dei server nei pool back-end tramite il portale e PowerShell. L'integrità dei pool back-end è disponibile anche tramite i log di diagnostica delle prestazioni.

[**Registrazione**](#enable-logging-with-powershell): la registrazione consente di salvare o usare i log delle prestazioni, di accesso e di altre tipologie relativi a una risorsa per il monitoraggio.

[**Metrica**](#metrics) : attualmente, il gateway applicazione dispone di una metrica. Questa metrica misura la velocità effettiva del gateway applicazione in byte al secondo.

## <a name="backend-health"></a>Integrità back-end

Il gateway applicazione consente di monitorare l'integrità dei singoli membri dei pool back-end tramite il portale, PowerShell e l'interfaccia della riga di comando. Le informazioni di riepilogo dell'integrità dei pool back-end sono disponibili anche tramite i log di diagnostica delle prestazioni. Il report sull'integrità back-end riflette l'output del probe di integrità del gateway applicazione per le istanze di back-end. Se il probing ha esito positivo ed è possibile inviare traffico al back-end, questo verrà considerato integro, altrimenti verrà considerato danneggiato.

> [!important]
> Se è presente un gruppo di sicurezza di rete nella subnet del gateway applicazione, è necessario aprire gli intervalli di porte 65503-65534 sulle istanze del gateway applicazione.

### <a name="view-backend-health-through-the-portal"></a>Visualizzare l'integrità back-end tramite il portale

Non sono necessarie operazioni per visualizzare l'integrità back-end. In un gateway applicazione esistente, passare a **Monitoraggio** > **Integrità back-end**. Ogni membro del pool back-end è elencato in questa pagina, a prescindere che si tratti di schede di interfaccia di rete, IP o FQDN. Vengono visualizzati il nome del pool back-end, la porta, le impostazioni HTTP back-end e lo stato di integrità. I valori validi per lo stato di integrità sono "Integro", "Danneggiato" e "Sconosciuto".

> [!WARNING]
> Se viene visualizzato lo stato di integrità back-end **Sconosciuto**, assicurarsi che l'accesso al back-end non sia bloccato da una regola del gruppo di sicurezza di rete o da un DNS personalizzato nella rete virtuale.

![Integrità back-end][10]

### <a name="view-backend-health-with-powershell"></a>Visualizzare l'integrità back-end con PowerShell

L'integrità back-end può anche essere recuperata tramite PowerShell. Il codice di PowerShell seguente illustra come eseguire il pull dell'integrità back-end con il cmdlet `Get-AzureRmApplicationGatewayBackendHealth`.

```powershell
Get-AzureRmApplicationGatewayBackendHealth -Name ApplicationGateway1 -ResourceGroupName Contoso
```

Un esempio della risposta restituita è indicato nel frammento seguente.

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

## <a name="diagnostic-logging"></a>Registrazione diagnostica

In Azure è possibile usare diversi tipi di log per gestire e risolvere i problemi dei gateway applicazione. Alcuni di questi log sono accessibili dal portale e tutti i log possono essere estratti da un archivio BLOB di Azure e visualizzati in diversi strumenti, ad esempio [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md), Excel e PowerBI. L'elenco seguente contiene altre informazioni sui diversi tipi di log:

* **Log attività:** è possibile usare [Log attività di Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (in precedenza Log operativi e Log di controllo) per visualizzare tutte le operazioni inviate alla sottoscrizione di Azure e il relativo stato. Le voci di Log di controllo vengono raccolte per impostazione predefinita e possono essere visualizzate nel portale di Azure.
* **Log di accesso:** è possibile usare questo log per visualizzare il modello di accesso del gateway applicazione e analizzare informazioni importanti, inclusi IP del chiamante, URL richiesto, latenza della risposta, codice restituito, byte in ingresso e in uscita. Il log di accesso viene raccolto ogni 300 secondi. Il log contiene un record per ogni istanza del gateway applicazione. L'istanza del gateway applicazione può essere identificata dalla proprietà 'instanceId'.
* **Log delle prestazioni:** è possibile usare questo log per visualizzare le prestazioni delle istanze del gateway applicazione. Questo log acquisisce le informazioni sulle prestazioni di ogni singola istanza, come il totale delle richieste servite, la velocità effettiva in byte, il numero delle richieste non riuscite e il numero delle istanze back-end integre e non integre. Il log delle prestazioni viene raccolto ogni 60 secondi.
* **Log del firewall:** è possibile usare questo log per visualizzare le richieste registrate tramite la modalità di rilevamento o prevenzione di un gateway applicazione configurato con il firewall applicazione Web.

> [!WARNING]
> I log sono disponibili solo per le risorse distribuite nel modello di distribuzione di Gestione risorse. Non è possibile usare i log per le risorse nel modello di distribuzione classica. Per altre informazioni sui due modelli, vedere l'articolo [Comprendere la distribuzione di Gestione risorse e distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md) .

Per l'archiviazione dei log sono disponibili tre diverse opzioni.

* Account di archiviazione: ideali quando i log vengono archiviati per un periodo più lungo ed esaminati quando necessario.
* Hub eventi: ottima opzione per l'integrazione con altri strumenti SEIM per ricevere avvisi sulle risorse
* Log Analytics: questa soluzione è più adatta per il monitoraggio generale in tempo reale dell'applicazione o l'analisi delle tendenze.

### <a name="enable-logging-with-powershell"></a>Abilitare la registrazione con PowerShell

Registrazione attività viene abilitata automaticamente per tutte le risorse di Resource Manager. È necessario abilitare la registrazione degli accessi e delle prestazioni per iniziare a raccogliere i dati disponibili tramite tali log. Per abilitare la registrazione, seguire questa procedura:

1. Prendere nota dell'ID risorsa dell'account di archiviazione in cui vengono archiviati i dati dei log. Il valore sarà nel formato: /subscriptions/\<subscriptionId\>/resourceGroups/\<nume gruppo di risorse\>/providers/Microsoft.Storage/storageAccounts/\<nome account di archiviazione\>. Può essere usato qualsiasi account di archiviazione della sottoscrizione. Per reperire queste informazioni è possibile usare il portale di anteprima.

    ![Portale di anteprima: Diagnostica del gateway applicazione](./media/application-gateway-diagnostics/diagnostics1.png)

2. Prendere nota dell'ID di risorsa del gateway applicazione per cui abilitare la registrazione. Il valore sarà nel formato: /subscriptions/\<subscriptionId\>/resourceGroups/\<nome gruppo di risorse\>/providers/Microsoft.Network/applicationGateways/\<nome gateway applicazione\>. Per reperire queste informazioni è possibile usare il portale di anteprima.

    ![Portale di anteprima: Diagnostica del gateway applicazione](./media/application-gateway-diagnostics/diagnostics2.png)

3. Abilitare la registrazione diagnostica usando il cmdlet di PowerShell seguente:

    ```powershell
    Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```
    
> [!TIP] 
>I log attività non richiedono un account di archiviazione separato. Per l'uso del servizio di archiviazione per la registrazione degli accessi e delle prestazioni è previsto un addebito.

### <a name="enable-logging-with-azure-portal"></a>Abilitare la registrazione con il portale di Azure

#### <a name="step-1"></a>Passaggio 1

Nel portale di Azure passare alla risorsa. Fare clic su **Log diagnostici**. Se è la prima volta che si esegue la configurazione della diagnostica, il pannello sarà simile al seguente:

Per il gateway applicazione, sono disponibili 3 log.

* Log di accesso
* Log delle prestazioni
* Log del firewall

Fare clic su **Abilita diagnostica** per avviare la raccolta dei dati.

![pannello impostazioni di diagnostica][1]

#### <a name="step-2"></a>Passaggio 2

Nel pannello **Impostazioni diagnostica** vengono configurate le impostazioni per i log di diagnostica. In questo esempio viene usato Log Analytics per archiviare i log. Fare clic su **Configura** in **Log Analytics** per configurare l'area di lavoro. Per salvare i log di diagnostica, è possibile anche usare l'hub eventi e un account di archiviazione.

![pannello Diagnostica][2]

#### <a name="step-3"></a>Passaggio 3

Scegliere un'area di lavoro di OMS oppure crearne una nuova. In questo esempio viene usata un'area di lavoro esistente.

![area di lavoro oms][3]

#### <a name="step-4"></a>Passaggio 4

Al termine, verificare le impostazioni e fare clic su **Salva** per salvarle.

![confermare la selezione][4]

### <a name="activity-log"></a>Log attività

Questi log (noti in precedenza come "log operativi") vengono generati da Azure per impostazione predefinita.  I log vengono conservati per 90 giorni nell'archivio dei registri eventi di Azure. Per altre informazioni su questi log, consultare l'articolo [Visualizzare eventi e log attività](../monitoring-and-diagnostics/insights-debugging-with-events.md).

### <a name="access-log"></a>Log di accesso

Questo log viene generato solo se è stato abilitato per il singolo gateway applicazione come descritto nei passaggi precedenti. I dati vengono archiviati nell'account di archiviazione specificato quando è stata abilitata la registrazione. Ogni accesso del gateway applicazione viene registrato in formato JSON, come illustrato nell'esempio seguente:

```json
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
```

### <a name="performance-log"></a>Log delle prestazioni

Questo log viene generato solo se è stato abilitato per il singolo gateway applicazione come descritto nei passaggi precedenti. I dati vengono archiviati nell'account di archiviazione specificato quando è stata abilitata la registrazione. Vengono registrati i dati seguenti:

```json
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
```

> [!NOTE]
> La latenza è calcolata dall'ora di ricezione del primo byte della richiesta HTTP al momento dell'invio dell'ultimo byte della risposta HTTP. Si tratta della somma del tempo di elaborazione del gateway applicazione più il costo di rete per il back-end e il tempo impiegato dal back-end per elaborare la richiesta.

### <a name="firewall-log"></a>Log del firewall

Questo log viene generato solo se è stato abilitato per il singolo gateway applicazione come descritto nei passaggi precedenti. Questo log richiede anche che il firewall applicazione Web sia configurato in un gateway applicazione. I dati vengono archiviati nell'account di archiviazione specificato quando è stata abilitata la registrazione. Vengono registrati i dati seguenti:

```json
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
```

### <a name="view-and-analyze-the-activity-log"></a>Visualizzare e analizzare Log attività

È possibile visualizzare e analizzare i dati di Log attività con uno dei metodi seguenti:

* **Strumenti di Azure:** recuperare le informazioni da Log attività con Azure PowerShell, l'interfaccia della riga di comando di Azure (CLI), l'API REST di Azure o il portale di anteprima di Azure.  Le istruzioni dettagliate di ciascun metodo sono fornite nell'articolo [Operazioni attività con Resource Manager](../azure-resource-manager/resource-group-audit.md).
* **Power BI:** se non esiste ancora un account [Power BI](https://powerbi.microsoft.com/pricing) , è possibile crearne uno di prova gratuitamente. Usando il [pacchetto di contenuto dei log attività di Azure per Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/) è possibile analizzare i dati con i dashboard preconfigurati, che possono anche essere personalizzati.

## <a name="view-and-analyze-the-access-performance-and-firewall-log"></a>Visualizzare e analizzare il log di accesso, delle prestazioni e del firewall

Azure [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) consente di raccogliere i file del contatore e del registro eventi dall'account di archiviazione BLOB e include visualizzazioni e funzionalità di ricerca avanzate per analizzare i log.

È anche possibile connettersi all'account di archiviazione e recuperare le voci del log JSON per i log di accesso e delle prestazioni. Dopo avere scaricato i file JSON, è possibile convertirli in CSV e visualizzarli in Excel, PowerBI o un altro strumento di visualizzazione dei dati.

> [!TIP]
> Se si ha familiarità con Visual Studio e i concetti di base della modifica dei valori di costanti e variabili in C#, è possibile usare i [convertitori di log](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponibili in Github.
> 
> 

## <a name="metrics"></a>Metrica

Metrica è una funzionalità di alcune risorse di Azure che consente di visualizzare i contatori delle prestazioni nel portale. Per il gateway applicazione è disponibile una metrica al momento della stesura di questo articolo. Questa metrica è la velocità effettiva e può essere visualizzata nel portale. Passare a un gateway applicazione e fare clic su **Metrics**(Metriche). Per visualizzare i valori, selezionare Velocità effettiva nella sezione **Metriche disponibili**. Nella figura seguente è possibile vedere un esempio dei filtri che possono essere applicati per visualizzare i dati in diversi intervalli di tempo.

Per un elenco delle metriche attualmente supportate, visitare [Supported metrics with Azure Monitor](../monitoring-and-diagnostics/monitoring-supported-metrics.md)

![visualizzazione della metrica][5]

### <a name="alert-rules"></a>Regole di avviso

Le regole di avviso possono essere avviate in base alle metriche su una risorsa. Per il gateway applicazione, questo significa che un avviso può chiamare un webhook o inviare un messaggio di posta elettronica a un amministratore se la velocità effettiva del gateway applicazione è al di sopra, al di sotto o sul limite per un periodo di tempo specificato.

L'esempio seguente illustra la creazione di una regola di avviso per l'invio di un messaggio di posta elettronica a un amministratore al superamento del limite della velocità effettiva.

#### <a name="step-1"></a>Passaggio 1

Per iniziare, fare clic su **Add metric alert** (Aggiungi avviso sulla metrica). Questo può essere visualizzato anche dal pannello delle metriche.

![pannello Regole di avviso][6]

#### <a name="step-2"></a>Passaggio 2

Nel pannello **Add rule** (Aggiungi regola) compilare le sezioni relative a nome, condizione e notifica, quindi fare clic su **OK** al termine.

Lo strumento di selezione **Condition** (Condizione) accetta 4 valori: **Greater than** (Maggiore di), **Greater than or equal** (Maggiore o uguale a), **Less than** (Minore di) o **Less than or equal to** (Minore o uguale a).

Lo strumento di selezione **Period** (Periodo) è possibile inserire valori compresi tra 5 minuti e 6 ore.

Selezionando **Invia messaggio di posta elettronica a proprietari, collaboratori e lettori**, il messaggio di posta elettronica può essere dinamicamente basato sugli utenti che dispongono di accesso a questa risorsa. In alternativa, è possibile inserire un elenco di utenti separato da virgole nella casella di testo **Additional administrator email(s)** (Indirizzi di posta elettronica aggiuntivi dell'amministratore).

![aggiungere regole di avviso][7]

Se il limite viene superato, l'utente riceve un messaggio di posta elettronica simile al seguente:

![messaggio di posta elettronica sul superamento della soglia][8]

Dopo la creazione dell'avviso sulla metrica, viene visualizzato un elenco degli avvisi e viene fornita una panoramica su tutte le regole di avviso.

![visualizzazione della regola di avviso][9]

Per altre informazioni sulle notifiche di avviso, visitare [Receive alert notifications](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

Per maggiori informazioni sui webhook e su come usarli con gli avvisi, visitare [Come configurare i webhook per gli avvisi](../monitoring-and-diagnostics/insights-webhooks-alerts.md)

## <a name="next-steps"></a>Passaggi successivi

* Visualizzare il registro contatori ed eventi con [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)
* Post di blog [Visualizzare il Log attività di Azure con Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx).
* Post di blog [Visualizzare e analizzare Log attività di Azure in Power BI e altro ancora](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).

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
