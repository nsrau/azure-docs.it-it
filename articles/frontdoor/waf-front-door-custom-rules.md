---
title: Regola personalizzata del firewall applicazione Web per il front-end di Azure
description: Informazioni su come usare le regole personalizzate web application firewall (WAF) per proteggere le applicazioni Web da attacchi dannosi.
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/07/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 02b335de7f105d768168d5f798ec9109136d7430
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846268"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Regole personalizzate per web application firewall con lo sportello anteriore di Azure
Azure web application firewall (WAF) con il servizio front door consente di controllare l'accesso alle applicazioni Web in base alle condizioni definite. Una regola WAF personalizzata è costituita da un numero di priorità, un tipo di regola, condizioni di corrispondenza e un'azione. Esistono due tipi di regole personalizzate: regole di corrispondenza e regole relative ai limiti di frequenza. Una regola di corrispondenza controlla l'accesso in base alle condizioni di corrispondenza mentre una regola limite di velocità controlla l'accesso in base alle condizioni di corrispondenza e alle frequenze delle richieste in ingresso. È possibile disabilitare una regola personalizzata per impedirne la valutazione, mantenendo comunque la configurazione. Questo articolo illustra le regole di corrispondenza basate sui parametri http.

## <a name="priority-match-conditions-and-action-types"></a>Priorità, condizioni di corrispondenza e tipi di azione
È possibile controllare l'accesso con una regola WAf personalizzata che definisce un numero di priorità, un tipo di regola, le condizioni di corrispondenza e un'azione. 

- **Priority:** valore integer univoco che descrive l'ordine di valutazione delle regole WAF. Le regole con valori inferiori vengono valutate prima delle regole con valori più elevati

- **Action:** definisce la modalità di indirizzamento di una richiesta in caso di corrispondenza di una regola WAF. È possibile scegliere una delle azioni seguenti da applicare quando una richiesta corrisponde a una regola personalizzata.

    - *Allow* -WAF Invia la ricerca al back-end, registra una voce nei log WAF e viene chiusa.
    - La richiesta di *blocco* è bloccata, WAF Invia la risposta al client senza inoltrare la richiesta al back-end. WAF registra una voce nei log di WAF.
    - *Log* -WAF registra una voce nei log WAF e continua a valutare la regola successiva.
    - *Redirect-WAF* reindirizza la richiesta a un URI specificato, registra una voce nei log WAF e viene chiusa.

- **Condizione di corrispondenza:** definisce una variabile di corrispondenza, un operatore e un valore di corrispondenza. Ogni regola può contenere più condizioni di corrispondenza. Una condizione di corrispondenza può essere basata sulle *variabili di corrispondenza*seguenti:
    - IndirizzoRemoto (IP client)
    - RequestMethod
    - QueryString
    - PostArgs
    - RequestUri
    - RequestHeader
    - RequestBody

- **Operator:** List include quanto segue:
    - Any: viene spesso usato per definire l'azione predefinita se non sono presenti regole corrispondenti. Any è un operatore match all.
    - IPMatch: definire la restrizione IP per la variabile IndirizzoRemoto
    - Geomatch: definire il filtro geografico per la variabile IndirizzoRemoto
    - Uguale
    - Contiene
    - LessThan: vincolo size
    - GreaterThan: vincolo size
    - LessThanOrEqual: vincolo size
    - GreaterThanOrEqual: vincolo size
    - Inizia con
     - EndsWith

È possibile impostare  la condizione negate su true se il risultato di una condizione deve essere negato.

Il *valore di corrispondenza* definisce l'elenco dei valori possibili delle corrispondenze.
I valori del metodo di richiesta HTTP supportati includono:
- GET
- INSERISCI
- PUT
- HEAD
- DELETE
- BLOCCO
- SBLOCCARE
- PROFILO
- OPTIONS
- PROPFIND
- PROPPATCH
- MKCOL
- COPIA
- SPOSTARE

## <a name="examples"></a>Esempi

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>Esempio di regole personalizzate di WAF basate su parametri http

Di seguito è riportato un esempio in cui viene illustrata la configurazione di una regola personalizzata con due condizioni di corrispondenza. Le richieste provengano da un sito specificato come definito dal referrer e la stringa di query non contiene "password".

```
# http rules example
{
  "name": "AllowFromTrustedSites",
  "priority": 1,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestHeader",
      "selector": "Referer",
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "www.mytrustedsites.com/referpage.html"
      ]
    },
    {
      "matchVariable": "QueryString",
      "operator": "Contains",
      "matchValue": [
        "password"
      ],
      "negateCondition": true
    }
  ],
  "action": "Allow",
  "transforms": []
}

```
Di seguito è riportata una configurazione di esempio per il blocco del metodo "PUT":

``` 
# http Request Method custom rules
{
  "name": "BlockPUT",
  "priority": 2,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestMethod",
      "selector": null,
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "PUT"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

### <a name="size-constraint"></a>Vincolo size

È possibile compilare una regola personalizzata che specifica il vincolo di dimensione per una parte di una richiesta in ingresso. La regola seguente, ad esempio, blocca un URL composto da più di 100 caratteri.

```
# http parameters size constraint
{
  "name": "URLOver100",
  "priority": 5,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestUri",
      "selector": null,
      "operator": "GreaterThanOrEqual",
      "negateCondition": false,
      "matchValue": [
        "100"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

## <a name="next-steps"></a>Passaggi successivi
- Informazioni sulle [Web Application Firewall](waf-overview.md)
- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).

