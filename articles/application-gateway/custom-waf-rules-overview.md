---
title: Regole personalizzate di Azure web application firewall (WAF) V2
description: Questo articolo fornisce una panoramica delle regole personalizzate web application firewall (WAF) V2 in applicazione Azure gateway.
services: application-gateway
ms.topic: article
author: vhorne
ms.service: application-gateway
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: 2e96a2a2dd5504c906b5fb84b643467a83518f21
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027572"
---
# <a name="overview-custom-rules-for-web-application-firewall-v2"></a>Panoramica: regole personalizzate per web application firewall V2

Applicazione Azure gateway web application firewall (WAF) v2 include un set di regole preconfigurato, gestito dalla piattaforma che offre protezione da molti tipi diversi di attacchi. Questi attacchi includono lo scripting tra siti, SQL injection e altri. Se si è un amministratore di WAF, potrebbe essere necessario scrivere regole personalizzate per aumentare le regole del set di regole di base. Le regole possono bloccare o consentire il traffico richiesto in base ai criteri di corrispondenza.

Con le regole personalizzate è possibile creare regole personalizzate, che vengono valutate per ogni richiesta che passa attraverso WAF. Queste regole hanno una priorità più elevata rispetto alle altre regole nei set di regole gestiti. Le regole personalizzate contengono un nome di regola, una priorità della regola e una matrice di condizioni di corrispondenza. Se queste condizioni vengono soddisfatte, viene eseguita un'azione per consentire o bloccare.

Ad esempio, è possibile creare una regola per bloccare tutte le richieste da un indirizzo IP compreso nell'intervallo 192.168.5.4/24. In questa regola, l'operatore è *IPMatch*, *matchValues* è l'intervallo di indirizzi IP (192.168.5.4/24) e l' *azione* consiste nel bloccare il traffico. È anche possibile impostare il nome e la priorità della regola.

Le regole personalizzate supportano l'uso della logica di composizione per apportare regole più avanzate per soddisfare le esigenze di sicurezza. Ad esempio, "(condizione 1 *e* condizione 2) *o* condizione 3)" significa che se la condizione 1 *e* la condizione 2 sono soddisfatte *oppure* se viene soddisfatta la condizione 3, WAF deve eseguire l'azione specificata nella regola personalizzata.

Condizioni di corrispondenza diverse all'interno della stessa regola vengono sempre composte tramite *e*. Ad esempio, una regola che usa *e* potrebbe specificare di bloccare il traffico da un determinato indirizzo IP e solo se viene usato un determinato browser.

Se si desidera utilizzare *o* per due condizioni diverse, è necessario che le due condizioni siano in regole diverse. Ad esempio, la regola che usa *o* potrebbe specificare per bloccare il traffico da un determinato indirizzo IP o bloccare il traffico se viene usato un determinato browser.

> [!NOTE]
> Il numero massimo di regole personalizzate di WAF è 100. Per altre informazioni sui limiti del gateway applicazione, vedere [sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md#application-gateway-limits).

Le espressioni regolari sono supportate anche nelle regole personalizzate, così come sono nei set di regole di base. Per esempi di queste regole, vedere l'argomento "esempio 3" e "esempio 5" in [creare e usare regole Web Application Firewall personalizzate](create-custom-waf-rules.md).

> [!NOTE]
> Le regole personalizzate non sono disponibili nello SKU V1 WAF.

## <a name="allowing-or-blocking-traffic"></a>Consentire o bloccare il traffico

Consentire o bloccare il traffico è semplice con regole personalizzate. Ad esempio, è possibile bloccare tutto il traffico che deriva da un intervallo di indirizzi IP. È possibile creare un'altra regola per consentire il traffico se la richiesta deriva da un determinato browser.

Per consentire un elemento, verificare che il parametro `-Action` sia impostato su **Consenti**. Per bloccare un elemento, verificare che il parametro `-Action` sia impostato su **Block**, come illustrato nel codice seguente:

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

Il `$BlockRule` precedente esegue il mapping alla seguente regola personalizzata in Azure Resource Manager:

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

Questa regola personalizzata contiene un nome, una priorità, un'azione e una matrice di condizioni di corrispondenza che devono essere soddisfatte per l'azione da eseguire. Per le descrizioni dei campi della regola personalizzata, vedere le sezioni seguenti. Per esempi di regole personalizzate, vedere [creare e usare regole Web Application Firewall personalizzate](create-custom-waf-rules.md).

## <a name="custom-rule-fields"></a>Campi di regole personalizzate

### <a name="name-optional"></a>Nome (facoltativo)

Si tratta del nome della regola. Il nome viene visualizzato nei log.

### <a name="priority-required"></a>Priorità (obbligatoria)

- La priorità determina l'ordine in cui vengono valutate le regole. Più basso è il valore, precedente è la valutazione della regola. L'intervallo consentito è compreso tra 1 e 100.
- La priorità deve essere univoca tra tutte le regole personalizzate. Una regola con una priorità di 40 viene valutata prima di una regola con una priorità pari a 80.

### <a name="rule-type-required"></a>Tipo di regola (obbligatorio)

Attualmente, il tipo di regola deve essere **MatchRule**.

### <a name="match-variable-required"></a>Variabile di corrispondenza (obbligatoria)

La variabile di corrispondenza deve essere una delle seguenti:

- IndirizzoRemoto: indirizzo IP o nome host della connessione al computer remoto
- RequestMethod: metodo di richiesta HTTP (GET, POST, PUT, DELETE e così via).
- QueryString: variabile nell'URI.
- Postargs: gli argomenti inviati nel corpo del POST. Le regole personalizzate che usano questa variabile di corrispondenza vengono applicate solo se l'intestazione Content-Type è impostata su "Application/x-www-form-urlencoded" e "multipart/form-data".
- RequestUri: URI della richiesta.
- RequestHeaders: intestazioni della richiesta.
- RequestBody: variabile che contiene l'intero corpo della richiesta nel suo complesso. Le regole personalizzate che usano questa variabile di corrispondenza vengono applicate solo se l'intestazione Content-Type è impostata su "Application/x-www-form-urlencoded". 
- RequestCookies: cookie della richiesta.

### <a name="selector-optional"></a>Selettore (facoltativo)

Il selettore descrive il campo della raccolta matchVariable. Ad esempio, se matchVariable è "RequestHeaders", il selettore potrebbe trovarsi nell'intestazione User-Agent.

### <a name="operator-required"></a>Operatore (obbligatorio)

L'operatore deve essere uno dei seguenti:

- IPMatch: questo operatore viene usato solo quando la variabile di corrispondenza è *IndirizzoRemoto*.
- Equals: l'input è uguale a quello di MatchValue.
- Contiene
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- Inizia con
- EndsWith
- Regex (Espressione regolare)

### <a name="negate-condition-optional"></a>Condizione negate (facoltativo)

Nega la condizione corrente.

### <a name="transform-optional"></a>Transform (facoltativo)

Elenco di stringhe con i nomi delle trasformazioni da completare prima che venga tentata la corrispondenza. Le trasformazioni includono:

- Lettere minuscole
- Trim
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>Valori di corrispondenza (obbligatorio)

Il campo matchValues è un elenco di valori di cui trovare una corrispondenza, che può essere considerato come *o*' ed. Ad esempio, i valori possono essere indirizzi IP o altre stringhe. Il formato del valore dipende dall'operatore precedente.

### <a name="action-required"></a>Azione (obbligatoria)

Il campo azione offre le opzioni seguenti: 

- Allow: autorizza la transazione e ignora tutte le regole successive. Ciò significa che la richiesta specificata viene aggiunta all'elenco Consenti e, dopo la corrispondenza, la richiesta interrompe un'ulteriore valutazione e viene inviata al pool back-end. Le regole presenti nell'elenco Consenti non vengono valutate per altre regole personalizzate o regole gestite.

- Block: blocca la transazione in base a *SecDefaultAction* (modalità di rilevamento/prevenzione). Analogamente all'azione Consenti, dopo che la richiesta viene valutata e aggiunta all'elenco dei blocchi, la valutazione viene arrestata e la richiesta viene bloccata. Le richieste successive che soddisfano le stesse condizioni non vengono valutate. Sono bloccate. 

- Log: consente alla regola di scrivere nel log e consente di eseguire le altre regole per la valutazione. Le regole personalizzate successive vengono valutate in ordine di priorità, seguite dalle regole gestite.

## <a name="next-steps"></a>Passaggi successivi

Ora che sono state apprese le regole personalizzate, è possibile [creare regole personalizzate](create-custom-waf-rules.md).
