---
title: Risoluzione dei problemi - Firewall applicazione Web di AzureTroubleshoot - Azure Web Application Firewall
description: In questo articolo vengono fornite informazioni sulla risoluzione dei problemi per Web Application Firewall (WAF) per Il gateway applicazione di Azure
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: ant
ms.topic: conceptual
ms.openlocfilehash: 33c85752903edd618044ccbab06aff7df9a791da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74046201"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>Risolvere i problemi relativi a Web Application Firewall (WAF) per il gateway applicazione di AzureTroubleshoot Web Application Firewall (WAF) for Azure Application Gateway

Esistono alcune operazioni che è possibile eseguire se le richieste che devono passare attraverso il Firewall applicazione Web (WAF) sono bloccate.

Innanzitutto, assicurati di aver letto la [panoramica di WAF](ag-overview.md) e i documenti di [configurazione WAF.](application-gateway-waf-configuration.md) Assicurarsi inoltre di aver abilitato il [monitoraggio WAF](../../application-gateway/application-gateway-diagnostics.md) Questi articoli illustrano come funzionano i set di regole WAF, come funzionano i set di regole WAF e come accedere ai registri WAF.

## <a name="understanding-waf-logs"></a>Informazioni sui registri WAF

Lo scopo dei registri WAF è quello di mostrare ogni richiesta che viene abbinata o bloccata dal WAF. Si tratta di un registro di tutte le richieste valutate che vengono abbinate o bloccate. Se si nota che il WAF blocca una richiesta che non dovrebbe (un falso positivo), si può fare un paio di cose. In primo luogo, restringere e trovare la richiesta specifica. Esaminare i log per trovare l'URI, il timestamp o l'ID transazione specifico della richiesta. Quando si trovano le voci di log associate, è possibile iniziare ad agire sui falsi positivi.

Si supponga, ad esempio, di disporre di un traffico legittimo che contiene la stringa *1/1* che si desidera passare attraverso il WAF. Se si tenta la richiesta, il WAF blocca il traffico che contiene la stringa *1/1* in qualsiasi parametro o campo. Si tratta di una stringa spesso associata a un attacco SQL injection. È possibile esaminare i registri e visualizzare il timestamp della richiesta e le regole che sono bloccate/corrispondenti.

Nell'esempio seguente, è possibile vedere che quattro regole vengono attivate durante la stessa richiesta (utilizzando il TransactionId campo). Il primo dice che corrisponde perché l'utente ha utilizzato un URL numerico / IP per la richiesta, che aumenta il punteggio di anomalia di tre dal momento che si tratta di un avviso. La regola successiva che corrisponde è 942130, che è quella che stai cercando. Nel `details.data` campo è possibile visualizzare l'1/1. *1=1* Questo aumenta ulteriormente il punteggio di anomalia di tre di nuovo, in quanto è anche un avvertimento. Generalmente, ogni regola che ha l'azione **Matched** aumenta il punteggio di anomalia, e a questo punto il punteggio di anomalia sarebbe sei. Per ulteriori informazioni, consultate [Modalità punteggio anomalia](ag-overview.md#anomaly-scoring-mode).

Le ultime due voci di registro mostrano che la richiesta è stata bloccata perché il punteggio di anomalia era sufficientemente alto. Queste voci hanno un'azione diversa rispetto alle altre due. Dimostrano di aver *effettivamente bloccato* la richiesta. Queste regole sono obbligatorie e non possono essere disabilitate. Non dovrebbero essere considerati come regole, ma più come infrastruttura di base degli interni WAF.

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

Con queste informazioni e la conoscenza che la regola 942130 è quella che corrisponde alla stringa *1/1,* è possibile eseguire alcune operazioni per impedire che questo bloccare il traffico:

- Utilizzare un elenco di esclusione

   Per ulteriori informazioni sugli elenchi di esclusione, vedere [Configurazione WAF.](application-gateway-waf-configuration.md#waf-exclusion-lists)
- Disabilitare la regola.

### <a name="using-an-exclusion-list"></a>Utilizzo di un elenco di esclusione

Per prendere una decisione informata sulla gestione di un falso positivo, è importante acquisire familiarità con le tecnologie utilizzate dall'applicazione. Si supponga, ad esempio, che non vi sia un server SQL nello stack di tecnologia e che si ottenete falsi positivi correlati a tali regole. La disabilitazione di queste regole non indebolisce necessariamente la sicurezza.

Uno dei vantaggi dell'utilizzo di un elenco di esclusione è la disabilitazione solo di una parte specifica di una richiesta. Tuttavia, ciò significa che un'esclusione specifica è applicabile a tutto il traffico che passa attraverso il WAF perché è un'impostazione globale. Ad esempio, questo potrebbe causare un problema se *1 : 1* è una richiesta valida nel corpo per una determinata app, ma non per altri. Un altro vantaggio è che è possibile scegliere tra corpo, intestazioni e cookie da escludere se viene soddisfatta una determinata condizione, invece di escludere l'intera richiesta.

Occasionalmente, ci sono casi in cui parametri specifici vengono passati al WAF in un modo che potrebbe non essere intuitivo. Ad esempio, è un token che viene passato durante l'autenticazione tramite Azure Active Directory.For example, there is a token that gets passed when authenticating using Azure Active Directory. Questo token, *__RequestVerificationToken*, in genere vengono passati come cookie di richiesta. Tuttavia, in alcuni casi in cui i cookie sono disabilitati, questo token viene passato anche come attributo di richiesta o "arg". In questo caso, è necessario assicurarsi che *__RequestVerificationToken* viene aggiunto all'elenco di esclusione anche come **nome dell'attributo Request.**

![Esclusioni](../media/web-application-firewall-troubleshoot/exclusion-list.png)

In questo esempio, si desidera escludere il **nome dell'attributo Request** uguale a *text1*. Questo è evidente perché è possibile visualizzare il nome dell'attributo nei registri del firewall: **dati: Dati corrispondenti: 1 , 1 trovato all'interno di ARGS:text1: 1 .** L'attributo è **text1**. È inoltre possibile trovare questo nome di attributo in altri modi, vedere [Ricerca dei nomi degli attributi](#finding-request-attribute-names)di richiesta .

![Elenchi di esclusione di WAF](../media/web-application-firewall-troubleshoot/waf-config.png)

### <a name="disabling-rules"></a>Disabilitazione delle regole

Un altro modo per aggirare un falso positivo è quello di disabilitare la regola che corrispondeva all'ingresso che il WAF pensava fosse dannoso. Poiché i log WAF sono stati analizzati e la regola è stata ridotta a 942130, è possibile disabilitarla nel portale di Azure.Since you've parseed the WAF logs and have narrowed the rule down to 942130, you can disable it in the Azure portal. Vedere Personalizzare le regole del firewall delle [applicazioni Web tramite il portale](application-gateway-customize-waf-rules-portal.md)di Azure.

Uno dei vantaggi della disabilitazione di una regola è che se si conosce tutto il traffico che contiene una determinata condizione che verrà normalmente bloccata è il traffico valido, è possibile disabilitare tale regola per l'intero WAF. Tuttavia, se si tratta solo di traffico valido in un caso di utilizzo specifico, si apre una vulnerabilità disabilitando tale regola per l'intero WAF poiché si tratta di un'impostazione globale.

Se si vuole usare Azure PowerShell, vedere Personalizzare le regole del firewall delle [applicazioni Web tramite PowerShell.](application-gateway-customize-waf-rules-powershell.md) Se si vuole usare l'interfaccia della riga di comando di Azure, vedere [Personalizzare le regole del firewall dell'applicazione Web tramite l'interfaccia della riga di comando](application-gateway-customize-waf-rules-cli.md)di Azure.If you want to use Azure CLI, see Customize web application firewall rules through the Azure CLI .

![Regole di WAF](../media/web-application-firewall-troubleshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>Ricerca dei nomi degli attributi delle richieste

Con l'aiuto di [Fiddler](https://www.telerik.com/fiddler), è possibile controllare le singole richieste e determinare quali campi specifici di una pagina web vengono chiamati. Ciò consente di escludere determinati campi dall'ispezione tramite elenchi di esclusione.

In questo esempio, è possibile vedere che il campo in cui è stata immessa la stringa *11* è denominato **text1**.

![Fiddler](../media/web-application-firewall-troubleshoot/fiddler-1.png)

Questo è un campo che puoi escludere. Per ulteriori informazioni sugli elenchi di esclusione, vedere Limiti delle richieste di richieste di [Web application firewall e elenchi di esclusione](application-gateway-waf-configuration.md#waf-exclusion-lists). In questo caso è possibile escludere la valutazione configurando l'esclusione seguente:You can exclude the evaluation in this case by configuring the following exclusion:

![Esclusione WAF](../media/web-application-firewall-troubleshoot/waf-exclusion-02.png)

È inoltre possibile esaminare i registri del firewall per ottenere le informazioni per vedere cosa è necessario aggiungere all'elenco di esclusione. Per abilitare la registrazione, vedere [Integrità back-end, log di diagnostica e metriche per il gateway applicazione](../../application-gateway/application-gateway-diagnostics.md).

Esaminare il registro del firewall e visualizzare il file PT1H.json per l'ora in cui si è verificata la richiesta che si desidera controllare.

In questo esempio, è possibile vedere che si dispone di quattro regole con lo stesso TransactionID e che si sono verificate tutte contemporaneamente:In this example, you can see that you have four rules with the same TransactionID, and that they all occurred at the exact same time:

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

Conoscendo il funzionamento degli insiemi di regole CRS e il funzionamento del set di regole CRS 3.0 con un sistema di punteggio di anomalie (vedere Web Application Firewall per Il gateway applicazione di [Azure)](ag-overview.md)si sa che le due regole più in basso con **l'azione: proprietà bloccata** vengono bloccate in base al punteggio di anomalia totale. Le regole su cui concentrarsi sono le prime due.

La prima voce viene registrata perché l'utente ha utilizzato un indirizzo IP numerico per passare al gateway applicazione, che può essere ignorato in questo caso.

La seconda (regola 942130) è quella interessante. Si può vedere nei dettagli che corrisponde a un modello (1 - 1) e il campo è denominato **text1**. Seguire gli stessi passaggi precedenti per escludere il **nome dell'attributo** di richiesta **che è uguale** a **1 .**

## <a name="finding-request-header-names"></a>Ricerca dei nomi delle intestazioni delle richieste

Fiddler è uno strumento utile ancora una volta per trovare i nomi delle intestazioni di richiesta. Nella schermata seguente è possibile visualizzare le intestazioni per questa richiesta GET, che includono *Content-Type*, *User-Agent*e così via.

![Fiddler](../media/web-application-firewall-troubleshoot/fiddler-2.png)

Un altro modo per visualizzare le intestazioni di richiesta e risposta è guardare all'interno degli strumenti di sviluppo di Chrome. È possibile premere F12 o fare clic con il pulsante destro del mouse su -> **Controlla** -> strumenti di**sviluppo**e selezionare la scheda **Rete.**

![Chrome F12](../media/web-application-firewall-troubleshoot/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>Ricerca dei nomi dei cookie delle richieste

Se la richiesta contiene cookie, è possibile selezionare la scheda **Cookie** per visualizzarli in Fiddler.

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>Limitare i parametri globali per eliminare i falsi positivi

- Disabilitare l'ispezione del corpo della richiesta

   Impostando **Inspect request body** su off, i corpi di richiesta di tutto il traffico non verranno valutati dal WAF. Ciò può essere utile se si sa che i corpi della richiesta non sono dannosi per l'applicazione.

   Disabilitando questa opzione, solo il corpo della richiesta non viene controllato. Le intestazioni e i cookie rimangono controllati, a meno che i singoli elementi non vengano esclusi utilizzando la funzionalità dell'elenco di esclusione.

- Limiti delle dimensioni dei file

   Limitando le dimensioni del file per il tuo WAF, stai limitando la possibilità che un attacco accada ai tuoi server web. Consentendo il caricamento di file di grandi dimensioni, aumenta il rischio che il back-end venga sovraccaricato. Limitare le dimensioni del file a un normale caso di utilizzo per l'applicazione è solo un altro modo per prevenire gli attacchi.

   > [!NOTE]
   > Se sai che la tua app non avrà mai bisogno di alcun caricamento di file superiore a una determinata dimensione, puoi limitarlo impostando un limite.

## <a name="firewall-metrics-waf_v1-only"></a>Metriche del firewall (solo WAF_v1)

Per i firewall delle applicazioni Web v1, nel portale sono ora disponibili le metriche seguenti: 

1. Conteggio richieste bloccate di Web Application Firewall Numero di richieste bloccate Numero di richieste bloccate
2. Conteggio regole regole bloccate di Web Application Firewall Tutte le regole che sono state soddisfatte **e** la richiesta è stata bloccata
3. Distribuzione totale delle regole del firewall dell'applicazione Web Tutte le regole corrispondenti durante la valutazione
     
Per abilitare le metriche, selezionare la scheda **Metriche** nel portale e selezionare una delle tre metriche.

## <a name="next-steps"></a>Passaggi successivi

Vedere [Come configurare il firewall dell'applicazione Web nel gateway applicazione](tutorial-restrict-web-traffic-powershell.md).
