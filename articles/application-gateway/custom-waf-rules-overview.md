---
title: Regole personalizzate di Web Application Firewall (WAF) di Azure
description: Questo articolo offre una panoramica di web application firewall regole personalizzate (WAF) nel Gateway applicazione di Azure.
services: application-gateway
ms.topic: article
author: vhorne
ms.service: application-gateway
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: d35e1dc96f65e51ab14c4962d1824334cbdb1616
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66752038"
---
# <a name="custom-rules-for-web-application-firewall"></a>Regole personalizzate per Web Application Firewall

Il web application firewall (WAF) Gateway applicazione di Azure include un set di regole preconfigurato e piattaforma gestito che offre protezione da molti tipi diversi di attacchi. Questi attacchi includono cross site scripting, attacchi SQL injection e ad altri utenti. Se sei un amministratore di Web Application firewall, è possibile scrivere è norme per potenziare le regole di base impostare regole (CRS). Le regole possono bloccare o consentire il traffico richiesto in base ai criteri definiti.

Regole personalizzate consentono di creare regole personalizzate che vengono valutati per ogni richiesta che passa attraverso il firewall WAF. Queste regole contengono una priorità più alta rispetto al resto delle regole nei set di regole gestite. Le regole personalizzate contengono un nome di regola, priorità della regola e una matrice di condizioni di corrispondenza. Se vengono soddisfatte queste condizioni, viene eseguita un'azione (per consentire o bloccare).

Ad esempio, è possibile bloccare tutte le richieste da un indirizzo IP in 192.168.5.4/24 l'intervallo. In questa regola è l'operatore *IPMatch*il matchValues è l'intervallo di indirizzi IP (192.168.5.4/24) e l'azione consiste nel bloccare il traffico. È anche possibile impostare nome e la priorità della regola.

Regole personalizzate supportano l'uso di composizione per la logica per verificare le regole più avanzate che indirizzi che i requisiti di sicurezza. Ad esempio, (1 di condizione **e** condizione 2) **o** condizione 3).  In questo esempio significa che se 1 condizione **e** condizione 2 vengono soddisfatte, **o** se 3 condizione viene soddisfatta, il WAF deve eseguire l'azione specificata nella regola personalizzata.

Diverse condizioni di corrispondenza all'interno della stessa regola si complicano sempre usando **e**. Ad esempio, bloccare il traffico da un indirizzo IP specifico, e solo se si sta usando un determinato browser.

Se si desidera **o** due condizioni diverse, le due condizioni devono essere nelle regole diverse. Bloccare il traffico da un traffico specifico blocco o indirizzo IP, ad esempio, se si sta usando un browser specifico.

> [!NOTE]
> Il numero massimo di regole personalizzate di Web Application firewall è 100. Per altre informazioni sui limiti del Gateway applicazione, vedere [sottoscrizione di Azure e limiti, quote e vincoli](../azure-subscription-service-limits.md#application-gateway-limits).

Le espressioni regolari sono supportate anche nelle regole personalizzate, come nel set di regole CRS. Per esempi di queste, vedere gli esempi 3 e 5 [regole del firewall applicazione web personalizzato crea e Usa](create-custom-waf-rules.md).

## <a name="allowing-vs-blocking"></a>Consentire e bloccare

Consentire e bloccare il traffico è semplice con regole personalizzate. Ad esempio, è possibile bloccare tutto il traffico proveniente da un intervallo di indirizzi IP. È possibile apportare un'altra regola per consentire il traffico, se la richiesta proviene da un browser specifico.

Per consentire a un elemento, assicurarsi che il `-Action` parametro è impostato su **Consenti**. Per bloccare un elemento, verificare che il `-Action` parametro è impostato su **blocco**.

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

Il precedente `$BlockRule` esegue il mapping alla regola personalizzata in Azure Resource Manager seguente:

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

Questa regola personalizzata contiene un nome, priorità, un'azione e la matrice di condizioni che devono essere soddisfatti per l'azione da eseguire sul posto di corrispondenza. Per un'ulteriore spiegazione di questi campi, vedere le descrizioni dei campi seguenti. Ad esempio regole personalizzate, vedere [regole del firewall applicazione web personalizzato crea e Usa](create-custom-waf-rules.md).

## <a name="fields-for-custom-rules"></a>Campi per le regole personalizzate

### <a name="name-optional"></a>[Facoltativo] nome

Si tratta del nome della regola. Questo nome viene visualizzato nei log.

### <a name="priority-required"></a>Priorità [obbligatorio]

- Determina l'ordine in cui le regole vengono valutate in. Minore sarà il valore, meno la valutazione della regola.
-Deve essere univoco tra tutte le regole personalizzate. Una regola con priorità 100 verrà valutata prima di una regola con priorità 200.

### <a name="rule-type-required"></a>Tipo di regola [obbligatorio]

Attualmente, deve essere **MatchRule**.

### <a name="match-variable-required"></a>Variabile di corrispondenza [obbligatorio]

Deve essere una delle variabili:

- IndirizzoRemoto: indirizzo IP/nome host della connessione di computer remoto
- RequestMethod: metodo di richiesta HTTP (GET, POST, PUT, DELETE e così via.)
- Stringa di query: variabile nell'URI
- PostArgs: gli argomenti inviati nel corpo del POST
- RequestUri: URI della richiesta
- RequestHeaders: intestazioni della richiesta
- RequestBody: corpo della richiesta
- RequestCookies: i cookie della richiesta

### <a name="selector-optional"></a>Selettore [facoltativo]

Descrive il campo della raccolta matchVariable. Ad esempio, se il matchVariable RequestHeaders, il selettore potrebbe essere nel *User-Agent* intestazione.

### <a name="operator-required"></a>Operatore [obbligatorio]

Deve essere uno dei seguenti operatori:

- IPMatch - usata solo quando è variabile corrispondenza *IndirizzoRemoto*
- Uguale a-input è lo stesso come il MatchValue
- Contiene
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- EndsWith
- Regex (Espressione regolare)

### <a name="negate-condition-optional"></a>Negare condizione [facoltativo]

Nega la condizione corrente.

### <a name="transform-optional"></a>Trasformazione [facoltativo]

Viene tentato un elenco di stringhe con nomi di trasformazioni da eseguire prima la corrispondenza. Possono essere le trasformazioni seguenti:

- Lettere minuscole
- Trim
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>Corrispondono ai valori [obbligatorio]

Elenco di valori da confrontare, che può essere considerato in corso *o*' ed. È possibile, ad esempio, gli indirizzi IP o altre stringhe. Il formato del valore dipende dall'operatore precedente.

### <a name="action-required"></a>Azione [obbligatorio]

- Consenti: autorizza la transazione, ignorando tutte le successive regole. Ciò significa che la richiesta specificata viene aggiunto all'elenco consentiti e quando esiste una corrispondenza, la richiesta non prosegue più ulteriore valutazione e viene inviato al pool back-end. Le regole nell'elenco indirizzi consentiti non vengono valutate per qualsiasi ulteriore regole personalizzate o le regole gestite.
- Blocco: blocca la transazione in base alla *SecDefaultAction* (modalità di rilevamento/prevenzione). Proprio come l'azione di assenso, dopo che la richiesta viene valutata e aggiunto all'elenco bloccati, la valutazione viene arrestata e la richiesta è bloccata. Qualsiasi richiesta dopo che soddisfa i criteri stessi non verrà valutato e verrà semplicemente bloccato. 
- Log: consente la regola di scrivere nel log, ma consente l'esecuzione per la valutazione del resto delle regole. Le successive regole personalizzate vengono valutate nell'ordine di priorità, seguite dalle regole gestite.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso informazioni sulle regole personalizzate, [creare regole personalizzate](create-custom-waf-rules.md).
