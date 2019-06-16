---
title: Introduzione a web application firewall per il Gateway applicazione di Azure
description: Questo articolo offre una panoramica di Web application firewall (WAF) per il gateway applicazione
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 5/22/2019
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: 9c2759222198f5df682d9e7a5363c0d9679e0fad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65991396"
---
# <a name="web-application-firewall-for-azure-application-gateway"></a>Web application firewall per il Gateway applicazione di Azure

Gateway applicazione di Azure offre un web application firewall (WAF) che offre una protezione centralizzata delle applicazioni web da comuni exploit e vulnerabilità. Le applicazioni Web sono sempre più destinate da attacchi che sfruttano vulnerabilità comunemente note. Attacchi SQL injection e scripting intersito sono tra gli attacchi più comuni.

Impedire attacchi di questo tipo nel codice dell'applicazione è complesso. È possibile richiedere una manutenzione rigorosa, l'applicazione di patch e il monitoraggio a più livelli della topologia dell'applicazione. Un web application firewall centralizzato consente di rendere molto più semplice la gestione di sicurezza. Un WAF offre inoltre agli amministratori dell'applicazione migliori garanzie di protezione contro le minacce e le intrusioni.

Una soluzione WAF può reagire a una minaccia alla sicurezza più veloce tramite l'applicazione di patch in modo centralizzato una vulnerabilità nota, anziché proteggere ogni applicazione web singoli. I gateway applicazione esistente possono essere convertiti facilmente nel gateway applicazione abilitata alla parete incendi.

Il WAF del Gateway applicazione si basa sul [impostare regole principali (CRS)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 o 2.2.9 dal Open Web Application Security Project (OWASP). Il WAF viene aggiornato automaticamente in modo da includere la protezione contro le vulnerabilità di nuovo, senza alcuna configurazione aggiuntiva.

![Diagramma di WAF del Gateway applicazione](./media/waf-overview/WAF1.png)

Il Gateway applicazione funziona come un application delivery controller (ADC). Offre la terminazione Secure Sockets Layer (SSL), l'affinità di sessione basata su cookie, distribuzione del carico round robin, routing basato sul contenuto, possibilità di ospitare più siti Web e miglioramenti della sicurezza.

Miglioramenti della sicurezza Gateway applicazione includono la gestione dei criteri SSL e supporto di SSL end-to-end. Sicurezza delle applicazioni viene potenziata dall'integrazione WAF nel Gateway applicazione. La combinazione consente di proteggere le applicazioni web dalle vulnerabilità comuni. E fornisce una posizione centrale per la gestione facile da configurare.

## <a name="benefits"></a>Vantaggi

Questa sezione descrive i vantaggi di base che forniscono il Gateway applicazione e del firewall WAF.

### <a name="protection"></a>Protezione

* Proteggere le applicazioni web da attacchi senza alcuna modifica al codice di back-end e vulnerabilità web.

* Proteggere più applicazioni web nello stesso momento. Un'istanza del Gateway applicazione può ospitare fino a 100 siti Web protetti da un web application firewall.

### <a name="monitoring"></a>Monitoraggio

* Monitorare gli attacchi contro le applicazioni web con un log WAF in tempo reale. Il log è integrato con [monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-overview.md) per tenere traccia degli avvisi WAF e monitorare facilmente le tendenze.

* Il WAF del Gateway applicazione è integrato con il Centro sicurezza di Azure. Il Centro sicurezza offre una visualizzazione centralizzata dello stato della protezione di tutte le risorse di Azure.

### <a name="customization"></a>Personalizzazione

* È possibile personalizzare le regole di Web Application firewall e gruppi di regole per esigenze dell'applicazione ed eliminare i falsi positivi.

## <a name="features"></a>Funzionalità

- Protezione di SQL injection.
- Protezione di scripting intersito.
- Protezione dagli altri attacchi web comuni, ad esempio inserimento di comandi, tipo HTTP request smuggling, nella suddivisione della risposta HTTP e inclusione di file remota.
- Protezione da violazioni del protocollo HTTP.
- Protezione contro eventuali anomalie del protocollo HTTP, ad esempio mancanti user agent host e intestazioni accept.
- Protezione contro robot, crawler e scanner.
- Rilevamento di comuni errori di configurazione dell'applicazione (ad esempio, Apache e IIS).
- Consente di limitare le dimensioni delle richieste può essere configurato con limiti inferiore e superiore.
- Gli elenchi di esclusione consentono di omettere determinati attributi di richiesta da una valutazione di Web Application firewall. Un esempio comune sono i token inserito Active Directory usati per l'autenticazione o campi password.

### <a name="core-rule-sets"></a>Set di regole principali

Il Gateway applicazione supporta due set di regole CRS 3.0 e CRS 2.2.9. Queste regole proteggono le applicazioni web da attività dannose.

Il WAF del Gateway applicazione è preconfigurato con CRS 3.0 per impostazione predefinita. Ma è possibile scegliere di usare invece CRS 2.2.9. CRS 3.0 offre meno falsi positivi rispetto a CRS 2.2.9. È anche possibile [personalizzare le regole in base alle esigenze](application-gateway-customize-waf-rules-portal.md).

Il WAF protegge contro le vulnerabilità web seguente:

- Attacchi SQL injection
- Attacchi di scripting intersito
- Altri attacchi comuni, ad esempio inserimento di comandi, HTTP request smuggling, HTTP response splitting e inclusione di file remota
- Violazioni del protocollo HTTP
- User agent host di eventuali anomalie del protocollo HTTP, ad esempio mancante e accettano Header
- BOT, crawler e scanner
- Comuni errori di configurazione dell'applicazione (ad esempio, Apache e IIS)

#### <a name="owasp-crs-30"></a>OWASP CRS 3.0

CRS 3.0 include 13 gruppi di regole, come illustrato nella tabella seguente. Ogni gruppo contiene più regole, che possono essere disabilitate.

|Gruppo di regole|Descrizione|
|---|---|
|**[REQUEST-911-METHOD-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs911)**|Bloccare i metodi (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](application-gateway-crs-rulegroups-rules.md#crs913)**|Protezione da scanner di porta e l'ambiente|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920)**|La protezione contro protocollo e i problemi di codifica|
|**[REQUEST-921-PROTOCOL-ATTACK](application-gateway-crs-rulegroups-rules.md#crs921)**|Protezione da attacchi injection di intestazione, richiesta smuggling e response splitting|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](application-gateway-crs-rulegroups-rules.md#crs930)**|Protezione da attacchi e il percorso file|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](application-gateway-crs-rulegroups-rules.md#crs931)**|Protezione da attacchi di remote file inclusion (RFI)|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](application-gateway-crs-rulegroups-rules.md#crs932)**|Proteggere di nuovo gli attacchi di esecuzione remota del codice|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](application-gateway-crs-rulegroups-rules.md#crs933)**|Protezione da attacchi PHP injection|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](application-gateway-crs-rulegroups-rules.md#crs941)**|Protezione contro gli attacchi di scripting intersito|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](application-gateway-crs-rulegroups-rules.md#crs942)**|Protezione da attacchi SQL injection|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](application-gateway-crs-rulegroups-rules.md#crs943)**|Protezione da attacchi ovvero session fixation|

#### <a name="owasp-crs-229"></a>OWASP CRS 2.2.9

CRS 2.2.9 include 10 gruppi di regole, come illustrato nella tabella seguente. Ogni gruppo contiene più regole, che possono essere disabilitate.

|Gruppo di regole|Descrizione|
|---|---|
|**[crs_20_protocol_violations](application-gateway-crs-rulegroups-rules.md#crs20)**|Protezione da violazioni del protocollo (ad esempio caratteri non validi o un'operazione GET con un corpo della richiesta)|
|**[crs_21_protocol_anomalies](application-gateway-crs-rulegroups-rules.md#crs21)**|Protezione da informazioni di intestazione errate|
|**[crs_23_request_limits](application-gateway-crs-rulegroups-rules.md#crs23)**|Protezione da argomenti o file che superano i limiti|
|**[crs_30_http_policy](application-gateway-crs-rulegroups-rules.md#crs30)**|Protezione da metodi con restrizioni, le intestazioni e tipi di file|
|**[crs_35_bad_robots](application-gateway-crs-rulegroups-rules.md#crs35)**|Protezione da web e scanner|
|**[crs_40_generic_attacks](application-gateway-crs-rulegroups-rules.md#crs40)**|Protezione da attacchi generici (ad esempio ovvero session fixation, inclusione di file remoto e PHP injection)|
|**[crs_41_sql_injection_attacks](application-gateway-crs-rulegroups-rules.md#crs41sql)**|Protezione da attacchi SQL injection|
|**[crs_41_xss_attacks](application-gateway-crs-rulegroups-rules.md#crs41xss)**|Protezione contro gli attacchi di scripting intersito|
|**[crs_42_tight_security](application-gateway-crs-rulegroups-rules.md#crs42)**|Protezione da attacchi path traversal|
|**[crs_45_trojans](application-gateway-crs-rulegroups-rules.md#crs45)**|Protezione da backdoor Trojan|

### <a name="waf-modes"></a>Modalità del WAF

Il WAF del Gateway applicazione può essere configurato per l'esecuzione in due modalità seguenti:

* **Modalità di rilevamento**: Monitora e registra tutti gli avvisi di minaccia. Attiva la registrazione diagnostica per il Gateway applicazione nel **diagnostica** sezione. È anche necessario assicurarsi che il log del WAF sia selezionato e attivato. Firewall applicazione Web non blocca le richieste in ingresso quando opera in modalità di rilevamento.
* **Modalità di prevenzione**: Attacchi di rilevare le regole e le intrusioni di blocchi. L'autore dell'attacco riceve un'eccezione "accesso non autorizzato 403" e la connessione viene terminata. Modalità di prevenzione registra tali attacchi nei registri del WAF.

### <a name="anomaly-scoring-mode"></a>Modalità di punteggio delle anomalie

OWASP più diffuse è disponibili due modalità per decidere se bloccare il traffico: Modalità tradizionale e quella di assegnazione dei punteggi delle anomalie.

Attiva la modalità tradizionale, il traffico che corrisponde a qualsiasi regola è considerato indipendentemente dalle altre corrispondenze regola. Questa modalità è facile da comprendere. Ma la mancanza di informazioni su quante regole corrispondono a una richiesta specifica è una limitazione. Pertanto, è stata introdotta la modalità di assegnazione dei punteggi delle anomalie. È il valore predefinito di 3 OWASP. *x*.

In modalità di assegnazione dei punteggi delle anomalie, il traffico che corrisponde a qualsiasi regola non viene immediatamente bloccato quando il firewall è in modalità di prevenzione. Le regole hanno un livello di gravità specifico: *Critici*, *errore*, *avviso*, oppure *preavviso*. Che livello di gravità influisce su un valore numerico per la richiesta, che viene chiamato il punteggio dell'anomalia. Ad esempio, uno *avviso* regola di corrispondenza contribuisce al punteggio di 3. Uno *critico* regola di corrispondenza contribuisce 5.

|Severity  |Value  |
|---------|---------|
|Critico     |5|
|Tipi di errore        |4|
|Avviso      |3|
|Si noti che       |2|

È una soglia pari a 5 per il punteggio delle anomalie per bloccare il traffico. Pertanto, un singolo *critico* corrispondenza regola è sufficiente per il WAF del Gateway applicazione bloccare una richiesta, anche in modalità di prevenzione. Ma uno *avviso* regola di corrispondenza aumenta solo l'anomalia punteggio per 3, che non è sufficiente da sola per bloccare il traffico.

> [!NOTE]
> Il messaggio che viene registrato quando il traffico corrisponde a una regola WAF include il valore dell'azione "Bloccato". Ma il traffico è effettivamente solo bloccate per un punteggio di anomalia pari a 5 o versione successiva.  

### <a name="waf-monitoring"></a>Monitoraggio WAF

Il monitoraggio dello stato del gateway applicazione è un'attività importante. Il monitoraggio dell'integrità del Web Application firewall e le applicazioni protette è supportato dall'integrazione con il Centro sicurezza di Azure, monitoraggio di Azure e log di monitoraggio di Azure.

![Diagramma della diagnostica WAF del Gateway applicazione](./media/waf-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Monitoraggio di Azure

Log del Gateway applicazione sono integrate con [monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-overview.md). In questo modo è possibile tenere traccia delle informazioni di diagnostica, inclusi i log e gli avvisi WAF. È possibile accedere a questa funzionalità nel **diagnostica** scheda nella risorsa Gateway applicazione nel portale o direttamente tramite Monitoraggio di Azure. Per altre informazioni sull'abilitazione dei log, vedere [diagnostica del Gateway applicazione](application-gateway-diagnostics.md).

#### <a name="azure-security-center"></a>Centro sicurezza di Azure

[Il Centro sicurezza](../security-center/security-center-intro.md) consente di prevenire, rilevare e rispondere alle minacce. Offre una maggiore visibilità e controllo avanzati della sicurezza delle risorse di Azure. Il Gateway applicazione è [integrato con il Centro sicurezza](application-gateway-integration-security-center.md). Il Centro sicurezza analizza l'ambiente per rilevare le applicazioni web non protette. Può consigliare il WAF del Gateway applicazione per proteggere le risorse vulnerabili. Si crea il firewall direttamente dal Centro sicurezza. Queste istanze WAF sono integrate con il Centro sicurezza. Inviano avvisi e informazioni sull'integrità al Centro sicurezza per la creazione di report.

![Finestra Panoramica del Centro sicurezza](./media/waf-overview/figure1.png)

#### <a name="logging"></a>Registrazione

Il WAF del Gateway applicazione fornisce rapporti dettagliati su ogni minaccia rilevato. La registrazione è integrata con i log di diagnostica di Azure. Gli avvisi vengono registrati in formato JSON. Questi log possono essere integrati con i [log di Monitoraggio di Azure](../azure-monitor/insights/azure-networking-analytics.md).

![Log di diagnostica del Gateway applicazione windows](./media/waf-overview/waf2.png)

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupId}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{appGatewayName}",
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
  }
} 

```

## <a name="application-gateway-waf-sku-pricing"></a>Prezzi dello SKU WAF del gateway applicazione

Il WAF del Gateway applicazione è disponibile in un nuovo uno SKU. Questo SKU è disponibile solo nel modello di provisioning di Azure Resource Manager, non nel modello di distribuzione classica. Inoltre, lo SKU WAF è disponibile solo in dimensioni di istanze di Gateway applicazione di medie e grandi dimensioni. Tutti i limiti per il Gateway applicazione si applicano anche per lo SKU WAF.

Prezzi sono basati su una tariffa oraria in istanza gateway e un costo di elaborazione dei dati. [Prezzi del Gateway applicazione](https://azure.microsoft.com/pricing/details/application-gateway/) per lo SKU WAF sono differenti dalle tariffe SKU standard. I costi di elaborazione dei dati sono uguali. Non sono previsti addebiti per ogni regola o un gruppo di regole. È possibile proteggere più applicazioni web dietro il firewall applicazione web stessa. Non vengono effettuati addebiti per il supporto di più applicazioni.

## <a name="next-steps"></a>Passaggi successivi

Visualizzare [come configurare web application firewall nel Gateway applicazione](tutorial-restrict-web-traffic-powershell.md).
