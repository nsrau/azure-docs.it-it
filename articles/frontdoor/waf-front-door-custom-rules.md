---
title: Regola personalizzata del firewall applicazione Web per l'ingresso principale di Azure
description: Informazioni su come usare web application firewall (WAF) regole personalizzate la protezione delle applicazioni web da attacchi dannosi.
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/07/2019
ms.author: kumud;tyao
ms.openlocfilehash: 744c6fb9235c9daa2d5239ef9fd13679db943650
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61459709"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Regole personalizzate per web application firewall con l'ingresso principale di Azure
Firewall (WAF) di applicazione web di Azure con il servizio di ingresso principale consente di controllare l'accesso alle applicazioni web in base alle condizioni definite. Una regola personalizzata di Web Application firewall è costituito da un numero di priorità, un tipo di regola, le condizioni di corrispondenza e un'azione. Esistono due tipi di regole personalizzate: corrispondono alle regole e regole dei limiti di frequenza. Una regola di corrispondenza controlla l'accesso in base alle condizioni di corrispondenza, mentre una regola di limite di frequenza controlla l'accesso in base alle condizioni e la frequenza delle richieste in ingresso corrispondenti. È possibile disabilitare una regola personalizzata per evitare che venga valutata, ma continuare a mantenere la configurazione. Questo articolo illustra le regole di corrispondenza basati su parametri http.

## <a name="priority-match-conditions-and-action-types"></a>Priorità, le condizioni di corrispondenza e i tipi di azione
È possibile controllare l'accesso con una regola WAf personalizzata che definisce un numero di priorità, un tipo di regola, le condizioni di corrispondenza e un'azione. 

- **Priorità:** è un integer univoco che descrive l'ordine di valutazione di regole WAF. Le regole con i valori più bassi vengono valutate prima delle regole con i valori più alti

- **Azione:** definisce la modalità indirizzare una richiesta se esiste una corrispondenza per una regola WAF. È possibile scegliere uno del sotto le azioni da applicare quando una richiesta corrisponde a una regola personalizzata.

    - *Consenti* -WAF inoltra la ricerca al back-end, registra una voce nel log di Web Application firewall e viene chiusa.
    - *Blocco* -richiesta è bloccata, WAF Invia risposta al client senza inoltrare le richieste al back-end. Web Application firewall registra una voce nei log del WAF.
    - *Log* -i log WAF una voce in Web Application firewall registra e continua valutazione della regola successiva.
    - *Reindirizzare* -WAF reindirizza una richiesta a un URI specificato, registra una voce nei log del WAF e viene chiuso.

- **Condizione di corrispondenza:** definisce una variabile di corrispondenza, un operatore e valore corrispondano. Ogni regola può contenere più condizioni di corrispondenza. Una condizione di corrispondenza può essere basata sul seguito *corrispondere variabili*:
    - IndirizzoRemoto (indirizzo IP del client)
    - RequestMethod
    - QueryString
    - PostArgs
    - RequestUri
    - RequestHeader
    - RequestBody

- **Operatore:** elenco include quanto segue:
    - Qualsiasi: viene spesso utilizzato per definire l'azione predefinita se nessuna regola viene confrontata. Qualsiasi viene trovata una corrispondenza operatore all.
    - IPMatch: definire restrizioni IP per la variabile IndirizzoRemoto
    - GeoMatch: definire geografica filtro per la variabile IndirizzoRemoto
    - Uguale
    - Contiene
    - LessThan: vincolo delle dimensioni
    - GreaterThan: vincolo delle dimensioni
    - LessThanOrEqual: vincolo delle dimensioni
    - GreaterThanOrEqual: vincolo delle dimensioni
    - BeginsWith
     - EndsWith

È possibile impostare *negare* condizione true se il risultato di una condizione deve essere negato.

*Corrispondenza valore* definisce l'elenco di valori possibili corrispondenze.
Supporta il metodo di richiesta HTTP i valori includono:
- GET
- POST
- PUT
- HEAD
- DELETE
- BLOCCO
- LO SBLOCCO
- PROFILO
- OPTIONS
- PROPFIND
- PROPPATCH
- MKCOL
- COPIA
- SPOSTARE

## <a name="examples"></a>Esempi

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>Esempio di regole personalizzate di Web Application firewall in base a parametri http

Ecco un esempio che illustra la configurazione di una regola personalizzata con due condizioni di corrispondenza. Le richieste provengono da un sito specifico come definito dal referrer e stringa di query non contiene "password".

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
Viene illustrato un esempio di configurazione per il blocco "PUT" metodo come indicato di seguito:

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

### <a name="size-constraint"></a>Vincolo delle dimensioni

È possibile creare una regola personalizzata che specifica il vincolo delle dimensioni nella parte di una richiesta in ingresso. Ad esempio, di sotto di regola consente di bloccare un Url di lunghezza superiore a 100 caratteri.

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
- Informazioni su [firewall applicazione web](waf-overview.md)
- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).

