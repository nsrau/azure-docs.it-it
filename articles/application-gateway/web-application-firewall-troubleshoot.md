---
title: Risolvere i problemi il Web Application Firewall per il Gateway applicazione di Azure
description: Questo articolo fornisce informazioni sulla risoluzione dei problemi per Web Application Firewall (WAF) per il Gateway applicazione di Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 6/11/2019
ms.author: ant
ms.topic: conceptual
ms.openlocfilehash: b81ffc5769fa5521094734da5d00aab77aa7cd35
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082443"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>Risolvere i problemi di Web Application Firewall (WAF) per il Gateway applicazione di Azure

Esistono alcune operazioni da eseguire se le richieste che devono passare tramite il Web Application Firewall (WAF) vengono bloccate.

In primo luogo, assicurarsi di aver letto la [Panoramica di Web Application firewall](waf-overview.md) e il [configurazione WAF](application-gateway-waf-configuration.md) documenti. Assicurarsi inoltre che è stato abilitato [monitoraggio WAF](application-gateway-diagnostics.md) questi articoli illustrano come le funzioni il WAF, come la regola WAF set di lavoro e su come accedere ai log del WAF.

## <a name="understanding-waf-logs"></a>Informazioni sui Web Application firewall log

Lo scopo dei log di Web Application firewall è per mostrare tutte le richieste corrispondenti o bloccata dal firewall WAF. È un ledger di tutte le richieste valutate matched o bloccato. Se si nota che il firewall WAF blocca una richiesta che consigliabile non (un falso positivo), è possibile eseguire alcune operazioni. In primo luogo, limitare e trovare la richiesta specifica. Esaminare i log per trovare l'ID specifico di transazioni, timestamp o URI della richiesta. Dopo aver individuato le voci di log associato, è possibile iniziare ad agire per i falsi positivi.

Ad esempio, è necessario un traffico legittimo che contiene la stringa *1 = 1* che si desidera passare attraverso il firewall WAF. Se si tenta la richiesta, il Web Application firewall blocca il traffico che contiene il *1 = 1* stringa in qualsiasi parametro o del campo. Si tratta di una stringa spesso associata a un attacco SQL injection. È possibile esaminare i log e visualizzare il timestamp della richiesta e le regole bloccano/corrispondente.

Nell'esempio seguente, si noterà che quattro regole vengono attivate durante la stessa richiesta (usando il campo ID transazione). Primo afferma corrispondente perché l'utente ha usato un indirizzo IP/numerici URL per la richiesta, che aumenta il punteggio dell'anomalia di tre, poiché si tratta di un avviso. La regola successiva corrispondente è 942130, ovvero quello che sta cercando. È possibile vedere le *1 = 1* nel `details.data` campo. Ciò aumenta ulteriormente il punteggio dell'anomalia di tre anche in questo caso, perché è anche un avviso. In generale, ogni regola che ha l'azione **Matched** punteggio dell'anomalia aumenta e a questo punto il punteggio dell'anomalia sarebbe sei. Per altre informazioni, vedere [modalità di assegnazione dei punteggi delle anomalie](waf-overview.md#anomaly-scoring-mode).

Le voci del due registro finale mostrano che la richiesta è stata bloccata perché il punteggio delle anomalie è stato sufficientemente elevato. Queste voci includono un'azione diversa rispetto gli altri due. Tali esempi mostrano in realtà *bloccata* la richiesta. Queste regole sono obbligatorie e non possono essere disabilitate. Essi non deve essere considerati come regole, ma più come infrastruttura di base dei componenti interni WAF.

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

## <a name="fixing-false-positives"></a>Correzione dei falsi positivi

Con queste informazioni e le conoscenze che la regola 942130 è quello che soddisfano le *1 = 1* stringa, è possibile eseguire alcune operazioni per evitare che si bloccano il traffico:

- Usare un elenco di esclusione

   Visualizzare [configurazione WAF](application-gateway-waf-configuration.md#waf-exclusion-lists) per altre informazioni sull'esclusione Elenca.
- Disabilitare la regola.

### <a name="using-an-exclusion-list"></a>Utilizzo di un elenco di esclusione

Per prendere una decisione consapevole sulla gestione di un falso positivo, è importante acquisire familiarità con le tecnologie che nell'applicazione viene utilizzato. Ad esempio, non c'è un server SQL nello stack di tecnologie e si ricevono i falsi positivi correlati a tali regole. La disabilitazione di queste regole non necessariamente indebolire la sicurezza.

Un vantaggio dell'uso di un elenco di esclusione è che solo una parte specifica di una richiesta è stata disabilitata. Tuttavia, ciò significa che un'esclusione specifica è applicabile a tutto il traffico passa attraverso il firewall WAF perché è un'impostazione globale. Ad esempio, questo potrebbe causare un problema se *1 = 1* è una richiesta valida nel corpo per una determinata app, ma non per altri utenti. Un altro vantaggio è che è possibile scegliere tra i cookie, intestazioni e corpo da escludere se viene soddisfatta una determinata condizione, anziché di semplice esclusione l'intera richiesta.

In alcuni casi, vi sono casi in cui parametri specifici ottengano passati nel firewall WAF in modo che potrebbe non essere intuitivo. Ad esempio, è presente un token che viene passato quando si esegue l'autenticazione con Azure Active Directory. Questo token *requestverificationtoken*, in genere passati come un Cookie di richiesta. Tuttavia, in alcuni casi in cui i cookie sono disabilitati, questo token viene passato anche come attributo della richiesta o "arg". In questo caso, è necessario assicurarsi che *requestverificationtoken* viene aggiunto all'elenco di esclusione come una **il nome di attributo richiesta** anche.

![Esclusioni](media/waf-tshoot/exclusion-list.png)

In questo esempio si desidera escludere il **nome dell'attributo richiesta** che uguaglia *text1*. Questo è evidente perché è possibile visualizzare il nome dell'attributo nei log del firewall: **dati: Dati corrispondenti: 1 = 1 viene trovato all'interno di ARGS:text1: 1=1**. L'attributo è **text1**. È anche possibile trovare il nome dell'attributo alcuni altri modi, vedere [individuazione richiesta di nomi di attributo](#finding-request-attribute-names).

![Elenchi di esclusione di WAF](media/waf-tshoot/waf-config.png)

### <a name="disabling-rules"></a>Disabilitazione delle regole

Un altro modo per evitare un falso positivo è per disabilitare la regola che soddisfano il pensiero di input del WAF è dannoso. Dopo aver analizzato i log WAF e sono state limitate la regola down 942130, è possibile disabilitarlo nel portale di Azure. Visualizzare [personalizzare le regole di web application firewall tramite il portale di Azure](application-gateway-customize-waf-rules-portal.md).

Un vantaggio di disabilitare una regola è che se si conosce il traffico valido tutto il traffico che contiene una determinata condizione che verrà bloccata in genere, è possibile disabilitare tale regola per l'intero WAF. Tuttavia, se è solo il traffico valido in un caso d'uso specifico, si apre una vulnerabilità disabilitando tale regola per l'intero WAF, poiché si tratta di un'impostazione globale.

Se si vuole usare Azure PowerShell, vedere [personalizzare le regole di web application firewall tramite PowerShell](application-gateway-customize-waf-rules-powershell.md). Se si vuole usare Azure CLI, vedere [personalizzare le regole del firewall applicazione web tramite la CLI di Azure](application-gateway-customize-waf-rules-cli.md).

![Regole del WAF](media/waf-tshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>Nomi degli attributi di richiesta di ricerca

Con l'ausilio delle [Fiddler](https://www.telerik.com/fiddler), è possibile controllare le singole richieste e determinare quali campi specifici di una pagina web vengono chiamati. Ciò consente di escludere determinati campi dalla consultazione tramite elenchi di esclusione.

In questo esempio, si noterà che il campo in cui il *1 = 1* stringa è stato immesso viene chiamato **text1**.

![Fiddler](media/waf-tshoot/fiddler-1.png)

Questo è un campo che non è possibile escludere. Per altre informazioni sugli elenchi di esclusione, vedere [limiti di dimensioni di richiesta di Web application firewall ed elenchi di esclusione](application-gateway-waf-configuration.md#waf-exclusion-lists). È possibile escludere in questo caso la versione di valutazione configurando le esclusioni seguenti:

![Esclusione di Web Application firewall](media/waf-tshoot/waf-exclusion-02.png)

È anche possibile esaminare i log del firewall per ottenere le informazioni per vedere cosa è necessario aggiungere all'elenco di esclusione. Per abilitare la registrazione, vedere [integrità Back-end, log di diagnostica e metriche per il Gateway applicazione](application-gateway-diagnostics.md).

Esaminare il log del firewall e visualizzare il file PT1H.json per l'ora in cui è avvenuta la richiesta che si desidera esaminare.

In questo esempio, si noterà che sono presenti quattro regole con l'ID di transazione stessa, e che tutti durante l'esecuzione nello stesso momento esatto:

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

Con la conoscenza di come la regola di CRS imposta lavoro e che il set di regole CRS 3.0 funziona con un'anomalia di assegnazione dei punteggi del sistema (vedere [firewall applicazione Web per il Gateway applicazione di Azure](waf-overview.md)) sapere che le ultime due regole con il  **azione: Bloccato** proprietà siano bloccate in base al punteggio di anomalia totale. Le regole per concentrarsi su sono due, la parte superiore.

La prima voce viene registrata perché l'utente consente di passare al Gateway applicazione, che può essere ignorato in questo caso un indirizzo IP numerico.

Il secondo uno (regola 942130) è quello interessante. È possibile visualizzare i dettagli che corrispondente a un modello (1 = 1) e il campo viene denominato **text1**. Seguire la stessa procedura precedente per escludere le **richiedere nome dell'attributo** che **è uguale a** **1 = 1**.

## <a name="finding-request-header-names"></a>Nomi di intestazioni di richiesta di ricerca

Fiddler è uno strumento utile ancora una volta per trovare i nomi di intestazioni di richiesta. Nello screenshot seguente, è possibile visualizzare le intestazioni per questa richiesta di recupero, tra cui *Content-Type*, *User-Agent*e così via.

![Fiddler](media/waf-tshoot/fiddler-2.png)

Un altro modo per visualizzare le intestazioni di richiesta e risposta consiste esamina gli strumenti di sviluppo di Chrome. È possibile premere F12 o -> pulsante destro del mouse **Inspect** -> **strumenti di sviluppo**e selezionare il **rete** scheda. Caricare una pagina web e fare clic su richiesta che si desidera esaminare.

![F12 di Chrome](media/waf-overview/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>Nomi di cookie di richiesta di ricerca

Se la richiesta contiene i cookie, il **cookie** scheda può essere selezionata per visualizzarli in Fiddler.

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>Limitare i parametri globali per eliminare i falsi positivi

- Disabilitare l'ispezione del corpo della richiesta

   Impostando **controllare il corpo della richiesta** su off, i corpi delle richieste di tutto il traffico non verrà valutati dal firewall WAF. Ciò può essere utile se si sa che i corpi delle richieste non siano dannose per l'applicazione.

   Disattivando questa opzione, non viene controllato solo il corpo della richiesta. Le intestazioni e i cookie rimangono ispezionati, a meno che non sono esclusi quelli singoli utilizzando la funzionalità elenco di esclusione.

- Limiti delle dimensioni dei file

   Limitando le dimensioni del file per il firewall WAF, si sta limitando la possibilità di un attacco che accade ai server web. Consentendo a file di grandi dimensioni da caricare, aumenta il rischio di back-end sovraccarico. Limitare le dimensioni del file per un caso d'uso normale per l'applicazione è semplicemente un altro modo per impedire gli attacchi.

   > [!NOTE]
   > Se si sa che l'app non sarà mai necessario caricare qualsiasi file oltre una determinata dimensione, è possibile limitare che impostando un limite.

## <a name="next-steps"></a>Passaggi successivi

Visualizzare [come configurare web application firewall nel Gateway applicazione](tutorial-restrict-web-traffic-powershell.md).
