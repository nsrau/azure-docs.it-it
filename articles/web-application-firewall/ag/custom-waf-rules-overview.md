---
title: Azure Web Application Firewall (WAF) v2 custom rules on Application Gateway
description: In questo articolo viene fornita una panoramica delle regole personalizzate WAF (Web Application Firewall) v2 nel gateway applicazione di Azure.This article provides an overview of Web Application Firewall (WAF) v2 custom rules on Azure Application Gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 01/30/2020
ms.author: victorh
ms.openlocfilehash: 072c7bd5b5b292ca4f0e53c59fcb7e9771331a94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77031732"
---
# <a name="custom-rules-for-web-application-firewall-v2-on-azure-application-gateway"></a>Regole personalizzate per Web Application Firewall v2 nel gateway applicazione di AzureCustom rules for Web Application Firewall v2 on Azure Application Gateway

Il firewall applicazione Web (WAF) v2 di Azure Application Gateway viene fornito con un set di regole preconfigurato e gestito dalla piattaforma che offre protezione da molti tipi diversi di attacchi. Questi attacchi includono cross-site scripting, SQL injection, e altri. Se sei un amministratore WAF, potresti voler scrivere regole personalizzate per aumentare le regole del set di regole di base (CRS). Le regole possono bloccare o consentire il traffico richiesto in base ai criteri di corrispondenza.

Le regole personalizzate consentono di creare regole personalizzate che vengono valutate per ogni richiesta che passa attraverso il WAF. Queste regole hanno una priorità più elevata rispetto alle altre dei set di regole gestiti. Le regole personalizzate contengono un nome di regola, una priorità della regola e una matrice di condizioni corrispondenti. Se queste condizioni vengono soddisfatte, viene eseguita un'azione (per consentire o bloccare).

Ad esempio, è possibile bloccare tutte le richieste provenienti da un indirizzo IP nell'intervallo 192.168.5.4/24.For example, you can block all requests from an IP address in the range 192.168.5.4/24. In questa regola, l'operatore è *IPMatch*, matchValues è l'intervallo di indirizzi IP (192.168.5.4/24) e l'azione consiste nel bloccare il traffico. È inoltre possibile impostare il nome e la priorità della regola.

Le regole personalizzate supportano l'utilizzo della logica di composizione per creare regole più avanzate che soddisfano le esigenze di sicurezza. Ad esempio, (Condizione 1 **e** Condizione 2) **o** Condizione 3). Ciò significa che se vengono soddisfatte la condizione 1 **e** la condizione 2 **o** se viene soddisfatta la condizione 3, il WAF deve eseguire l'azione specificata nella regola personalizzata.

Condizioni di corrispondenza diverse all'interno della stessa regola vengono sempre combinate utilizzando **e**. Ad esempio, bloccare il traffico proveniente da un indirizzo IP specifico e solo se utilizzano un determinato browser.

Se si desidera **o** due condizioni diverse, le due condizioni devono essere in regole diverse. Ad esempio, bloccare il traffico proveniente da un indirizzo IP specifico o bloccare il traffico se utilizza un browser specifico.

> [!NOTE]
> Il numero massimo di regole personalizzate WAF è 100. Per altre informazioni sui limiti del gateway applicazione, vedere [Limiti, quote e vincoli](../../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits)di sottoscrizione e servizio di Azure.

Le espressioni regolari sono supportate anche nelle regole personalizzate, proprio come nei set di regole CRS. Per alcuni esempi, vedere gli esempi 3 e 5 in [Creare e utilizzare regole personalizzate](create-custom-waf-rules.md)del firewall delle applicazioni Web .

## <a name="allowing-vs-blocking"></a>Consentire contro il blocco

Consentire e bloccare il traffico è semplice con regole personalizzate. Ad esempio, è possibile bloccare tutto il traffico proveniente da un intervallo di indirizzi IP. È possibile effettuare un'altra regola per consentire il traffico se la richiesta proviene da un browser specifico.

Per consentire un elemento, assicurarsi che il `-Action` parametro sia impostato su **Consenti**. Per bloccare un `-Action` elemento, assicurarsi che il parametro sia impostato su **Blocco**.

```azurepowershell
$AllowRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example1 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Allow

$BlockRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example2 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

Il `$BlockRule` precedente esegue il mapping alla regola personalizzata seguente in Azure Resource Manager:The previous maps to the following custom rule in Azure Resource Manager:

```json
"customRules": [
      {
        "name": "blockEvilBot",
        "priority": 2,
        "ruleType": "MatchRule",
        "action": "Block",
        "matchConditions": [
          {
            "matchVariables": [
              {
                "variableName": "RequestHeaders",
                "selector": "User-Agent"
              }
            ],
            "operator": "Contains",
            "negationConditon": false,
            "matchValues": [
              "evilbot"
            ],
            "transforms": [
              "Lowercase"
            ]
          }
        ]
      }
    ], 
```

Questa regola personalizzata contiene un nome, una priorità, un'azione e la matrice di condizioni corrispondenti che devono essere soddisfatte affinché l'azione venga eseguita. Per ulteriori informazioni su questi campi, vedere le descrizioni dei campi seguenti. Ad esempio regole personalizzate, vedere [Creare e utilizzare regole del firewall delle applicazioni Web personalizzate.](create-custom-waf-rules.md)

## <a name="fields-for-custom-rules"></a>Campi per le regole personalizzate

### <a name="name-optional"></a>Nome [facoltativo]

Nome della regola.  Appare nei registri.

### <a name="priority-required"></a>Priorità [obbligatorio]

- Determina l'ordine di valutazione delle regole. Più basso è il valore, prima la valutazione della regola. L'intervallo consentito è compreso tra 1 e 100. 
- Deve essere univoco in tutte le regole personalizzate. Una regola con priorità 40 viene valutata prima di una regola con priorità 80.A rule with priority 40 is evaluated before a rule with priority 80.

### <a name="rule-type-required"></a>Tipo di regola [obbligatorio]

Attualmente, deve essere **MatchRule**.

### <a name="match-variable-required"></a>Variabile di corrispondenza [obbligatorio]

Deve essere una delle variabili:

- RemoteAddr – Indirizzo IP/nome host della connessione al computer remoto
- RequestMethod: metodo di richiesta HTTP (GET, POST, PUT, DELETE e così via).
- QueryString: variabile nell'URI
- PostArgs: argomenti inviati nel corpo POST. Le regole personalizzate che utilizzano questa variabile di corrispondenza vengono applicate solo se l'intestazione 'Content-Type' è impostata su 'application/x-www-form-urlencoded' e 'multipart/form-data'.
- RequestUri – URI della richiesta
- RequestHeaders – Intestazioni della richiesta
- RequestBody: contiene l'intero corpo della richiesta nel suo complesso. Le regole personalizzate che utilizzano questa variabile di corrispondenza vengono applicate solo se l'intestazione 'Content-Type' è impostata su 'application/x-www-form-urlencoded'. 
- RequestCookies – Cookie della richiesta

### <a name="selector-optional"></a>Selettore [opzionale]

Descrive il campo della raccolta matchVariable. Ad esempio, se matchVariable è RequestHeaders, il selettore potrebbe trovarsi nell'intestazione *User-Agent.*

### <a name="operator-required"></a>Operatore [obbligatorio]

Deve essere uno dei seguenti operatori:

- IPMatch - utilizzato solo quando Match Variable è *RemoteAddr*
- Uguale a: l'input è uguale a MatchValue
- Contiene
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- EndsWith
- Regex (Espressione regolare)
- Geomatch (anteprima)Geomatch (preview)

### <a name="negate-condition-optional"></a>Condizione di negate [opzionale]

Nega la condizione attuale.

### <a name="transform-optional"></a>Trasformazione [facoltativo]

Elenco di stringhe con nomi di trasformazioni da eseguire prima che venga tentata la corrispondenza. Queste possono essere le seguenti trasformazioni:

- Lettere minuscole
- Trim
- UrlDecode
- Urlencode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>Valori di corrispondenza [obbligatorio]

Elenco di valori da confrontare, che possono essere considerati *OR*'ed. Ad esempio, potrebbe essere indirizzi IP o altre stringhe. Il formato del valore dipende dall'operatore precedente.

### <a name="action-required"></a>Azione [obbligatorio]

- Consenti: autorizza la transazione, ignorando tutte le altre regole. La richiesta specificata viene aggiunta all'elenco Consenti e, una volta trovata una corrispondenza, la richiesta interrompe un'ulteriore valutazione e viene inviata al pool back-end. Le regole presenti nell'elenco Consenti non vengono valutate per altre regole personalizzate o gestite.
- Blocca: blocca la transazione in base a *SecDefaultAction* (modalità di rilevamento/prevenzione). Proprio come l'azione Consenti, una volta che la richiesta viene valutata e aggiunta all'elenco di blocco, la valutazione viene interrotta e la richiesta viene bloccata. Qualsiasi richiesta successiva soddisfa le stesse condizioni non verrà valutata e verrà bloccata. 
- Log: consente alla regola di scrivere nel log, ma consente l'esecuzione del resto delle regole per la valutazione. Le altre regole personalizzate vengono valutate in ordine di priorità, seguite dalle regole gestite.

## <a name="geomatch-custom-rules-preview"></a>Regole personalizzate di geocorrispondenza (anteprima)Geomatch custom rules (preview)

Le regole personalizzate consentono di creare regole personalizzate in base alle esigenze esatte delle applicazioni e dei criteri di sicurezza. È possibile limitare l'accesso alle applicazioni Web in base al paese. Per ulteriori informazioni, consultate [Regole personalizzate di geocorrispondenza (anteprima)](geomatch-custom-rules.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le regole personalizzate, [creare regole personalizzate.](create-custom-waf-rules.md)
