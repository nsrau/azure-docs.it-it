---
title: Risoluzione dei problemi-firewall applicazione Web di Azure
description: Questo articolo fornisce informazioni sulla risoluzione dei problemi per il Web Application Firewall (WAF) per applicazione Azure gateway
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: ant
ms.topic: conceptual
ms.openlocfilehash: 33c85752903edd618044ccbab06aff7df9a791da
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74046201"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>Risolvere i problemi di Web Application Firewall (WAF) per il gateway applicazione Azure

Se le richieste che devono passare attraverso il Web Application Firewall (WAF) sono bloccate, è possibile eseguire alcune operazioni.

Prima di tutto, assicurarsi di aver letto la [Panoramica di WAF](ag-overview.md) e i documenti di [configurazione di WAF](application-gateway-waf-configuration.md) . Assicurarsi anche di aver abilitato il [monitoraggio di WAF](../../application-gateway/application-gateway-diagnostics.md) . questi articoli spiegano come funzionano le funzioni WAF, il funzionamento dei set di regole WAF e come accedere ai log WAF.

## <a name="understanding-waf-logs"></a>Informazioni sui log di WAF

Lo scopo dei log WAF è mostrare ogni richiesta corrispondente o bloccata dal WAF. Si tratta di un Ledger di tutte le richieste valutate corrispondenti o bloccate. Se si nota che WAF blocca una richiesta che non dovrebbe (un falso positivo), è possibile eseguire alcune operazioni. Per prima cosa, limitare e trovare la richiesta specifica. Esaminare i log per trovare l'URI, il timestamp o l'ID transazione specifici della richiesta. Quando si trovano le voci di log associate, è possibile iniziare a agire sui falsi positivi.

Si immagini, ad esempio, di avere un traffico legittimo contenente la stringa *1 = 1* che si vuole passare attraverso la WAF. Se si prova la richiesta, il WAF blocca il traffico che contiene la stringa *1 = 1* in qualsiasi parametro o campo. Si tratta di una stringa spesso associata a un attacco SQL injection. È possibile esaminare i log e visualizzare il timestamp della richiesta e le regole bloccate o corrispondenti.

Nell'esempio seguente è possibile notare che vengono attivate quattro regole durante la stessa richiesta (usando il campo ID transazione). Il primo dice che corrisponde a perché l'utente ha usato un URL numerico/IP per la richiesta, che aumenta il Punteggio di anomalia di tre poiché è un avviso. La regola successiva corrispondente è 942130, ovvero quella che si sta cercando. È possibile visualizzare *1 = 1* nel campo `details.data`. Questo aumenta ulteriormente il Punteggio di anomalie di tre volte, in quanto è anche un avviso. In genere, ogni regola con l'azione **corrispondente** aumenta il Punteggio di anomalie e a questo punto il Punteggio di anomalia sarebbe sei. Per altre informazioni, vedere modalità di assegnazione dei [punteggi delle anomalie](ag-overview.md#anomaly-scoring-mode).

Le ultime due voci di log mostrano che la richiesta è stata bloccata perché il punteggio delle anomalie era sufficientemente elevato. Queste voci hanno un'azione diversa rispetto alle altre due. Mostrano *effettivamente la* richiesta. Queste regole sono obbligatorie e non possono essere disabilitate. Non devono essere considerati come regole, ma come infrastruttura di base degli elementi interni di WAF.

```json
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "920350", 
        "message": "Host header is a numeric IP address", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ", 
            "data": "40.90.218.160", 
            "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"", 
            "line": "791" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "942130", 
        "message": "SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ", 
            "data": "Matched Data: 1=1 found within ARGS:text1: 1=1", 
            "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"", 
            "line": "554" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ", 
            "data": "", 
            "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"", 
            "line": "57" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ", 
            "data": "", 
            "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"", 
            "line": "73" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    }
}
```

## <a name="fixing-false-positives"></a>Correzione di falsi positivi

Con queste informazioni e la conoscenza della regola 942130 che corrisponde alla stringa *1 = 1* , è possibile eseguire alcune operazioni per impedire il blocco del traffico:

- Usa un elenco di esclusione

   Vedere [configurazione di WAF](application-gateway-waf-configuration.md#waf-exclusion-lists) per altre informazioni sugli elenchi di esclusione.
- Disabilitare la regola.

### <a name="using-an-exclusion-list"></a>Uso di un elenco di esclusione

Per prendere una decisione consapevole sulla gestione di un falso positivo, è importante acquisire familiarità con le tecnologie utilizzate dall'applicazione. Ad esempio, supponiamo che non esista un server SQL nello stack della tecnologia e che si stiano ottenendo falsi positivi correlati a tali regole. La disabilitazione di tali regole non indebolisce necessariamente la sicurezza.

Un vantaggio dell'uso di un elenco di esclusione è che solo una parte specifica di una richiesta viene disabilitata. Ciò significa tuttavia che un'esclusione specifica è applicabile a tutto il traffico che passa attraverso il WAF perché si tratta di un'impostazione globale. Questo potrebbe, ad esempio, causare un problema se *1 = 1* è una richiesta valida nel corpo di una determinata app, ma non per altri. Un altro vantaggio è che è possibile scegliere tra corpo, intestazioni e cookie da escludere se viene soddisfatta una determinata condizione, anziché escludere l'intera richiesta.

Occasionalmente, esistono casi in cui i parametri specifici vengono passati in WAF in modo che potrebbero non essere intuitivi. Ad esempio, è presente un token che viene passato quando si esegue l'autenticazione con Azure Active Directory. Questo token, *__RequestVerificationToken*, viene in genere passato come cookie di richiesta. Tuttavia, in alcuni casi in cui i cookie sono disabilitati, questo token viene anche passato come attributo della richiesta o "arg". In tal caso, è necessario assicurarsi che *__RequestVerificationToken* venga aggiunto anche all'elenco di esclusione come **nome dell'attributo della richiesta** .

![Esclusioni](../media/web-application-firewall-troubleshoot/exclusion-list.png)

In questo esempio si vuole escludere il **nome dell'attributo della richiesta** che è uguale a *Text1*. Questa operazione è evidente perché è possibile visualizzare il nome dell'attributo nei log del firewall: **dati: dati corrispondenti: 1 = 1 trovato in args: Text1:1 = 1**. L'attributo è **Text1**. È anche possibile trovare il nome di questo attributo in altri modi, vedere [ricerca di nomi di attributi di richiesta](#finding-request-attribute-names).

![Elenchi di esclusione di WAF](../media/web-application-firewall-troubleshoot/waf-config.png)

### <a name="disabling-rules"></a>Disabilitazione di regole

Un altro modo per aggirare un falso positivo è disabilitare la regola corrispondente all'input considerato dannoso dal WAF. Poiché i log WAF sono stati analizzati e la regola è stata limitata a 942130, è possibile disabilitarla nel portale di Azure. Vedere [personalizzare le regole di Web Application Firewall tramite l'portale di Azure](application-gateway-customize-waf-rules-portal.md).

Un vantaggio derivante dalla disabilitazione di una regola è che se si conosce tutto il traffico che contiene una determinata condizione che in genere verrà bloccata è il traffico valido, è possibile disabilitare la regola per l'intero WAF. Tuttavia, se si tratta solo di traffico valido in un caso d'uso specifico, si apre una vulnerabilità disabilitando la regola per l'intero WAF poiché si tratta di un'impostazione globale.

Se si vuole usare Azure PowerShell, vedere [personalizzare Web Application Firewall regole tramite PowerShell](application-gateway-customize-waf-rules-powershell.md). Per usare l'interfaccia della riga di comando di Azure, vedere [personalizzare Web Application Firewall regole tramite l'interfaccia della riga di comando di Azure](application-gateway-customize-waf-rules-cli.md).

![Regole di WAF](../media/web-application-firewall-troubleshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>Ricerca dei nomi degli attributi di richiesta

Con l'ausilio di [Fiddler](https://www.telerik.com/fiddler), si esaminano le singole richieste e si determinano i campi specifici di una pagina Web che vengono chiamati. Questo può aiutare a escludere determinati campi dall'ispezione usando elenchi di esclusione.

In questo esempio, è possibile notare che il campo in cui è stata immessa la stringa *1 = 1* è denominato **Text1**.

![Fiddler](../media/web-application-firewall-troubleshoot/fiddler-1.png)

Si tratta di un campo che è possibile escludere. Per ulteriori informazioni sugli elenchi di esclusione, vedere l'articolo relativo ai [limiti e alle esclusioni delle dimensioni delle richieste del Web Application Firewall](application-gateway-waf-configuration.md#waf-exclusion-lists). È possibile escludere la valutazione in questo caso configurando l'esclusione seguente:

![Esclusione WAF](../media/web-application-firewall-troubleshoot/waf-exclusion-02.png)

È anche possibile esaminare i log del firewall per ottenere le informazioni necessarie per visualizzare gli elementi da aggiungere all'elenco di esclusione. Per abilitare la registrazione, vedere [integrità back-end, log di diagnostica e metriche per il gateway applicazione](../../application-gateway/application-gateway-diagnostics.md).

Esaminare il registro del firewall e visualizzare il file PT1H. JSON per l'ora in cui si è verificata la richiesta da ispezionare.

In questo esempio si può notare che sono presenti quattro regole con lo stesso ID transazione e che si sono verificate tutte nello stesso momento:

```json
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "920350",
-           "message": "Host header is a numeric IP address",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ",
-               "data": "40.90.218.160",
-               "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"",
-               "line": "791"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "942130",
-           "message": "SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ",
-               "data": "Matched Data: 1=1 found within ARGS:text1: 1=1",
-               "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"",
-               "line": "554"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ",
-               "data": "",
-               "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"",
-               "line": "57"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ",
-               "data": "",
-               "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"",
-               "line": "73"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
```

Con la conoscenza del funzionamento dei set di regole CRS e che il set di regole CRS 3,0 funziona con un sistema di assegnazione dei punteggi delle anomalie (vedere [Web Application Firewall for applicazione Azure gateway](ag-overview.md)) si sa che le due regole più in basso con la proprietà **Action: blocked** si bloccano in base al punteggio totale delle anomalie. Le regole su cui concentrarsi sono le prime due.

La prima voce viene registrata perché l'utente ha usato un indirizzo IP numerico per passare al gateway applicazione, che in questo caso può essere ignorato.

Il secondo (regola 942130) è quello interessante. È possibile vedere nei dettagli che corrisponde a un modello (1 = 1) e il campo è denominato **Text1**. Seguire gli stessi passaggi precedenti per escludere il **nome dell'attributo della richiesta** **uguale** a **1 = 1**.

## <a name="finding-request-header-names"></a>Ricerca dei nomi delle intestazioni di richiesta

Fiddler è uno strumento utile ancora una volta per trovare i nomi delle intestazioni di richiesta. Nello screenshot seguente è possibile visualizzare le intestazioni per questa richiesta GET, che includono *Content-Type*, *User-Agent*e così via.

![Fiddler](../media/web-application-firewall-troubleshoot/fiddler-2.png)

Un altro modo per visualizzare le intestazioni di richiesta e risposta consiste nell'esaminare gli strumenti di sviluppo di Chrome. È possibile premere F12 o fare clic con il pulsante destro del mouse su > **controllare** -> **strumenti di sviluppo**, quindi selezionare la scheda **rete** . caricare una pagina Web, quindi fare clic sulla richiesta che si desidera controllare.

![F12 Chrome](../media/web-application-firewall-troubleshoot/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>Ricerca dei nomi dei cookie di richiesta

Se la richiesta contiene cookie, è possibile selezionare la scheda **cookie** per visualizzarli in Fiddler.

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>Limitare i parametri globali per eliminare i falsi positivi

- Disabilitare l'ispezione del corpo della richiesta

   Impostando **Controlla il corpo della richiesta** su disattivato, i corpi della richiesta di tutto il traffico non verranno valutati dal WAF. Questo può essere utile se si è certi che i corpi della richiesta non sono dannosi per l'applicazione.

   Se si disabilita questa opzione, solo il corpo della richiesta non viene controllato. Le intestazioni e i cookie rimangono controllati, a meno che non vengano esclusi singolarmente utilizzando la funzionalità elenco di esclusione.

- Limiti delle dimensioni dei file

   Limitando le dimensioni del file per la WAF, si sta limitando la possibilità di un attacco ai server Web. Consentendo il caricamento di file di grandi dimensioni, aumenta il rischio di sovraccarico del back-end. Limitare le dimensioni del file a un normale caso d'uso per l'applicazione è solo un altro modo per prevenire gli attacchi.

   > [!NOTE]
   > Se si è certi che l'app non richiede mai il caricamento di file oltre una determinata dimensione, è possibile limitarlo impostando un limite.

## <a name="firewall-metrics-waf_v1-only"></a>Metriche firewall (solo WAF_v1)

Per i firewall applicazione Web V1, le metriche seguenti sono ora disponibili nel portale: 

1. Il numero di richieste bloccate dal firewall dell'applicazione Web è stato bloccato
2. Il numero di regole bloccate dal firewall dell'applicazione Web è dovuto a tutte le regole corrispondenti **e** la richiesta è stata bloccata
3. Distribuzione regola totale del firewall applicazione Web tutte le regole corrispondenti durante la valutazione
     
Per abilitare le metriche, selezionare la scheda **metriche** nel portale e selezionare una delle tre metriche.

## <a name="next-steps"></a>Passaggi successivi

Vedere [come configurare Web Application Firewall nel gateway applicazione](tutorial-restrict-web-traffic-powershell.md).
