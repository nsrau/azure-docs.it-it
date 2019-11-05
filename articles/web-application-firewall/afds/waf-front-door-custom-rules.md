---
title: Regola personalizzata del firewall applicazione Web per il front-end di Azure
description: Informazioni su come usare le regole personalizzate del Web Application Firewall (WAF) per proteggere le applicazioni Web da attacchi dannosi.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 516e327cca1aa6a691a1d932c5f48c9108d818b4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512522"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Regole personalizzate per il Web Application Firewall con sportello anteriore di Azure

Il servizio Web Application Firewall (WAF) di Azure con il servizio front door consente di controllare l'accesso alle applicazioni Web in base alle condizioni definite. Una regola WAF personalizzata è costituita da un numero di priorità, da un tipo di regola, da condizioni di corrispondenza e da un'azione. Esistono due tipi di regole personalizzate: regole di corrispondenza e regole relative ai limiti di frequenza. Una regola di corrispondenza controlla l'accesso in base a un set di condizioni di corrispondenza mentre una regola limite di velocità controlla l'accesso in base alle condizioni di corrispondenza e alle frequenze delle richieste in ingresso. È possibile disabilitare una regola personalizzata per impedirne la valutazione, mantenendo comunque la configurazione. 

## <a name="priority-match-conditions-and-action-types"></a>Priorità, condizioni di corrispondenza e tipi di azione

È possibile controllare l'accesso con una regola WAf personalizzata che definisce un numero di priorità, un tipo di regola, una matrice di condizioni di corrispondenza e un'azione. 

- **Priority:** valore integer univoco che descrive l'ordine di valutazione delle regole WAF. Le regole con valori di priorità inferiore vengono valutate prima delle regole con valori più elevati. I numeri di priorità devono essere univoci tra tutte le regole personalizzate.

- **Action:** definisce la modalità di indirizzamento di una richiesta in caso di corrispondenza di una regola WAF. È possibile scegliere una delle azioni seguenti da applicare quando una richiesta corrisponde a una regola personalizzata.

    - *Allow* -WAF Invia la ricerca al back-end, registra una voce nei log WAF e viene chiusa.
    - La richiesta di *blocco* è bloccata, WAF Invia la risposta al client senza inoltrare la richiesta al back-end. WAF registra una voce nei log di WAF.
    - *Log* -WAF registra una voce nei log WAF e continua a valutare la regola successiva.
    - *Redirect-WAF reindirizza la* richiesta a un URI specificato, registra una voce nei log WAF e viene chiusa.

- **Condizione di corrispondenza:** definisce una variabile di corrispondenza, un operatore e un valore di corrispondenza. Ogni regola può contenere più condizioni di corrispondenza. Una condizione di corrispondenza può essere basata sulla posizione geografica, sugli indirizzi IP del client (CIDR), sulle dimensioni o sulla corrispondenza di stringa. La corrispondenza della stringa può essere confrontata con un elenco di variabili di corrispondenza.
  - **Variabile di corrispondenza:**
    - requestMethod
    - QueryString
    - Postargs
    - requestUri
    - RequestHeader
    - RequestBody
    - Cookie
  - **Operatore**
    - Any: viene spesso usato per definire l'azione predefinita se non sono presenti regole corrispondenti. Any è un operatore match all.
    - Uguale
    - Contiene
    - LessThan: vincolo size
    - GreaterThan: vincolo size
    - LessThanOrEqual: vincolo size
    - GreaterThanOrEqual: vincolo size
    - Inizia con
    - EndsWith
    - Regex (Espressione regolare)
  
  - **Regex** non supporta le operazioni seguenti: 
    - Backreference e acquisizione di sottoespressioni
    - Asserzioni di larghezza zero arbitrarie
    - Riferimenti a subroutine e modelli ricorsivi
    - Modelli condizionali
    - Verbi di controllo backtracking
    - Direttiva a byte singolo \c
    - Direttiva di corrispondenza nuova riga
    - La direttiva \k Start of match Reset
    - Callout e codice incorporato
    - Raggruppamento atomico e quantificatori possessivi

  - **Negazione [facoltativo]:** È possibile impostare la condizione *negate* su true se il risultato di una condizione deve essere negato.
      
  - **Transform [facoltativo]:** Elenco di stringhe con i nomi delle trasformazioni da eseguire prima che venga tentata la corrispondenza. Queste possono essere le trasformazioni seguenti:
     - Maiuscolo 
     - Lettere minuscole
     - Trim
     - RemoveNulls
     - UrlDecode
     - UrlEncode
     
   - **Valore corrispondenza:** I valori del metodo di richiesta HTTP supportati includono:
     - GET
     - POST
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
- [Configurare un criterio di Web Application Firewall usando Azure PowerShell](waf-front-door-custom-rules-powershell.md) 
- Informazioni su [Web Application Firewall con sportello anteriore](afds-overview.md)
- Informazioni su [come creare una Frontdoor](../../frontdoor/quickstart-create-front-door.md).

