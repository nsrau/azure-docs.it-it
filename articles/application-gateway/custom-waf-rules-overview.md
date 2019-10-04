---
title: Regole personalizzate di Azure Web Application Firewall (WAF) V2
description: Questo articolo fornisce una panoramica delle regole personalizzate di Web Application Firewall (WAF) V2 in applicazione Azure gateway.
services: application-gateway
ms.topic: article
author: vhorne
ms.service: application-gateway
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: 154317e558c2c9a22f569f569684cced467900d5
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937472"
---
# <a name="custom-rules-for-web-application-firewall-v2"></a>Regole personalizzate per il Web Application Firewall V2

Il applicazione Azure gateway Web Application Firewall (WAF) v2 include un set di regole preconfigurato, gestito dalla piattaforma che offre protezione da molti tipi diversi di attacchi. Questi attacchi includono lo scripting tra siti, SQL injection e altri. Se si è un amministratore di WAF, è consigliabile scrivere regole personalizzate per aumentare le regole del set di regole di base (CRS). Le regole possono bloccare o consentire il traffico richiesto in base ai criteri di corrispondenza.

Le regole personalizzate consentono di creare regole personalizzate che vengono valutate per ogni richiesta che passa attraverso il WAF. Queste regole hanno una priorità più elevata rispetto alle altre regole nei set di regole gestiti. Le regole personalizzate contengono un nome di regola, una priorità della regola e una matrice di condizioni di corrispondenza. Se queste condizioni vengono soddisfatte, viene eseguita un'azione (per consentire o bloccare).

Ad esempio, è possibile bloccare tutte le richieste da un indirizzo IP compreso nell'intervallo 192.168.5.4/24. In questa regola, l'operatore è *IPMatch*, matchValues è l'intervallo di indirizzi IP (192.168.5.4/24) e l'azione consiste nel bloccare il traffico. È anche possibile impostare il nome e la priorità della regola.

Le regole personalizzate supportano l'uso della logica di composizione per apportare regole più avanzate per soddisfare le esigenze di sicurezza. Ad esempio, (condizione 1 **e** condizione 2) **o** condizione 3).  Questo esempio indica che se la condizione 1 **e** la condizione 2 sono soddisfatte **o** se viene soddisfatta la condizione 3, il WAF deve eseguire l'azione specificata nella regola personalizzata.

Condizioni di corrispondenza diverse all'interno della stessa regola vengono sempre composte mediante **e**. Ad esempio, bloccare il traffico da un indirizzo IP specifico e solo se viene usato un determinato browser.

Se si desiderano **o** due condizioni diverse, le due condizioni devono essere in regole diverse. È ad esempio possibile bloccare il traffico da un indirizzo IP specifico o bloccare il traffico se utilizzano un browser specifico.

> [!NOTE]
> Il numero massimo di regole personalizzate di WAF è 100. Per altre informazioni sui limiti del gateway applicazione, vedere [sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md#application-gateway-limits).

Le espressioni regolari sono supportate anche nelle regole personalizzate, esattamente come nei RuleSet di CRS. Per esempi, vedere gli esempi 3 e 5 in [creare e usare regole Web Application Firewall personalizzate](create-custom-waf-rules.md).

## <a name="allowing-vs-blocking"></a>Consentire il blocco di Visual Studio

Consentire e bloccare il traffico è semplice con regole personalizzate. Ad esempio, è possibile bloccare tutto il traffico proveniente da un intervallo di indirizzi IP. È possibile creare un'altra regola per consentire il traffico se la richiesta deriva da un browser specifico.

Per consentire un elemento, verificare che il parametro `-Action` sia impostato su **Consenti**. Per bloccare un elemento, verificare che il parametro `-Action` sia impostato su **Block**.

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

Questa regola personalizzata contiene un nome, una priorità, un'azione e la matrice di condizioni di corrispondenza che devono essere soddisfatte per l'azione da eseguire. Per una spiegazione più approfondita di questi campi, vedere le descrizioni dei campi seguenti. Per le regole personalizzate, ad esempio, vedere [creare e usare regole Web Application Firewall personalizzate](create-custom-waf-rules.md).

## <a name="fields-for-custom-rules"></a>Campi per le regole personalizzate

### <a name="name-optional"></a>Nome [facoltativo]

Si tratta del nome della regola. Questo nome viene visualizzato nei log.

### <a name="priority-required"></a>Priority [obbligatorio]

- Determina l'ordine in cui vengono valutate le regole. Più basso è il valore, precedente è la valutazione della regola. L'intervallo consentito è compreso tra 1-100. 
- Deve essere univoco tra tutte le regole personalizzate. Una regola con priorità 40 verrà valutata prima di una regola con priorità 80.

### <a name="rule-type-required"></a>Tipo di regola [obbligatorio]

Attualmente, deve essere **MatchRule**.

### <a name="match-variable-required"></a>Match (variabile) [obbligatorio]

Deve essere una delle variabili:

- IndirizzoRemoto: indirizzo IP/nome host della connessione al computer remoto
- RequestMethod: metodo di richiesta HTTP (GET, POST, PUT, DELETE e così via).
- QueryString: variabile nell'URI
- Postargs: argomenti inviati nel corpo del POST. Le regole personalizzate che usano questa variabile di corrispondenza vengono applicate solo se l'intestazione ' Content-Type ' è impostata su' Application/x-www-form-urlencoded ' è multipart/form-data '.
- RequestUri: URI della richiesta
- RequestHeaders: intestazioni della richiesta
- RequestBody: contiene l'intero corpo della richiesta nel suo complesso. Le regole personalizzate che usano questa variabile di corrispondenza vengono applicate solo se l'intestazione ' Content-Type ' è impostata su' Application/x-www-form-urlencoded '. 
- RequestCookies: cookie della richiesta

### <a name="selector-optional"></a>Selettore [facoltativo]

Descrive il campo della raccolta matchVariable. Ad esempio, se matchVariable è RequestHeaders, il selettore potrebbe trovarsi nell'intestazione *User-Agent* .

### <a name="operator-required"></a>Operator [obbligatorio]

Deve essere uno degli operatori seguenti:

- IPMatch: usato solo quando la variabile match è *IndirizzoRemoto*
- Uguale a: l'input corrisponde a MatchValue
- Contiene
- MinoreDi
- MaggioreDi
- LessThanOrEqual
- GreaterThanOrEqual
- Inizia con
- EndsWith
- Regex (Espressione regolare)

### <a name="negate-condition-optional"></a>Negazione di condizione [facoltativo]

Nega la condizione corrente.

### <a name="transform-optional"></a>Transform [facoltativo]

Elenco di stringhe con i nomi delle trasformazioni da eseguire prima che venga tentata la corrispondenza. Queste possono essere le trasformazioni seguenti:

- Minuscolo
- Taglia
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>Valori di corrispondenza [obbligatorio]

Elenco di valori da confrontare, che può essere considerato come *o*' ed. Ad esempio, potrebbe trattarsi di indirizzi IP o di altre stringhe. Il formato del valore dipende dall'operatore precedente.

### <a name="action-required"></a>Azione [obbligatorio]

- Consenti: autorizza la transazione, ignorando tutte le regole successive. Ciò significa che la richiesta specificata viene aggiunta all'elenco Consenti e una volta corrispondente, la richiesta interrompe un'ulteriore valutazione e viene inviata al pool back-end. Le regole presenti nell'elenco Consenti non vengono valutate per altre regole personalizzate o regole gestite.
- Block: blocca la transazione in base a *SecDefaultAction* (modalità di rilevamento/prevenzione). Proprio come l'azione Consenti, una volta che la richiesta viene valutata e aggiunta all'elenco dei blocchi, la valutazione viene arrestata e la richiesta viene bloccata. Eventuali richieste successive a quelle che soddisfano le stesse condizioni non verranno valutate e verranno bloccate. 
- Log: consente alla regola di scrivere nel log, ma consente di eseguire le altre regole per la valutazione. Le regole personalizzate successive vengono valutate in ordine di priorità, seguite dalle regole gestite.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver apprese le regole personalizzate, [creare regole personalizzate](create-custom-waf-rules.md).
