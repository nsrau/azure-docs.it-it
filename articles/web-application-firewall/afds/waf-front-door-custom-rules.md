---
title: Web application firewall custom rule for Azure Front Door
description: Informazioni su come utilizzare le regole personalizzate WAF (Web Application Firewall) che proteggono le applicazioni Web da attacchi dannosi.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 158bfe30bf48ee420be8efb9ff32fff0e555d9e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475825"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Regole personalizzate per il firewall dell'applicazione Web con il portetno anteriore di AzureCustom rules for Web Application Firewall with Azure Front Door

Firewall applicazione Web di Azure (WAF) con porte anteriori consente di controllare l'accesso alle applicazioni Web in base alle condizioni definite. Una regola WAF personalizzata è costituita da un numero di priorità, un tipo di regola, condizioni di corrispondenza e un'azione. Esistono due tipi di regole personalizzate: regole di corrispondenza e regole limite di frequenza. Una regola di corrispondenza controlla l'accesso in base a un set di condizioni corrispondenti, mentre una regola di limite di frequenza controlla l'accesso in base alle condizioni di corrispondenza e alle frequenze delle richieste in ingresso. È possibile disabilitare una regola personalizzata per impedirne la valutazione, pur conservando la configurazione. 

## <a name="priority-match-conditions-and-action-types"></a>Priorità, condizioni di corrispondenza e tipi di azione

È possibile controllare l'accesso con una regola WAf personalizzata che definisce un numero di priorità, un tipo di regola, una matrice di condizioni di corrispondenza e un'azione. 

- **Priorità:** è un numero intero univoco che descrive l'ordine di valutazione delle regole WAF. Le regole con valori di priorità inferiori vengono valutate prima delle regole con valori più alti. I numeri di priorità devono essere univoci tra tutte le regole personalizzate.

- **Azione:** definisce come instradare una richiesta se viene soddisfatta una regola WAF. È possibile scegliere una delle seguenti azioni da applicare quando una richiesta corrisponde a una regola personalizzata.

    - *Consenti* - WAF inoltra la missione al back-end, registra una voce nei registri WAF e esce.
    - *Blocca:* la richiesta è bloccata, WAF invia la risposta al client senza inoltrare la richiesta al back-end. WAF registra una voce nei registri WAF.
    - *Registro* - WAF registra una voce nei registri WAF e continua a valutare la regola successiva.
    - *Reindirizzamento:* WAF reindirizza la richiesta a un URI specificato, registra una voce nei registri WAF e esce.

- **Condizione di corrispondenza:** definisce una variabile di corrispondenza, un operatore e un valore di corrispondenza. Ogni regola può contenere più condizioni di corrispondenza. Una condizione di corrispondenza può essere basata sulla posizione geografica, sugli indirizzi IP del client (CIDR), sulle dimensioni o sulla corrispondenza delle stringhe. La corrispondenza delle stringhe può essere confrontata con un elenco di variabili di corrispondenza.
  - **Variabile di corrispondenza:**
    - Metodo Request
    - QueryString
    - PostArgs (informazioni in stato di comando
    - Requesturi
    - RequestHeader
    - RequestBody
    - Cookie
  - **Operator:**
    - Qualsiasi: viene spesso utilizzato per definire l'azione predefinita se non viene trovata alcuna corrispondenza con le regole. Any è una corrispondenza all operatore.
    - Uguale
    - Contiene
    - LessThan: vincolo di dimensione
    - GreaterThan: vincolo di dimensione
    - LessThanOrEqual: vincolo di dimensione
    - GreaterThanOrEqual: vincolo di dimensione
    - BeginsWith
    - EndsWith
    - Regex (Espressione regolare)
  
  - **Regex** non supporta le seguenti operazioni: 
    - Backreference e acquisizione di sottoespressioni
    - Asserzioni arbitrarie di larghezza zero
    - Riferimenti di subroutine e modelli ricorsiviSubroutine references and recursive patterns
    - Modelli condizionali
    - Verbi di controllo di backtracking
    - Direttiva a byte singolo c
    - La direttiva di corrispondenza della nuova riga di riga di tipo R
    - L'inizio di .K della direttiva match reset
    - Callout e codice incorporato
    - Raggruppamento atomico e quantificatori possessivi

  - **Nega [opzionale]:** È possibile impostare la condizione *di negazione su* true se il risultato di una condizione deve essere negato.
      
  - **Trasforma [opzionale]:** Elenco di stringhe con nomi di trasformazioni da eseguire prima che venga tentata la corrispondenza. Queste possono essere le seguenti trasformazioni:
     - Maiuscolo 
     - Lettere minuscole
     - Trim
     - RemoveNulls
     - UrlDecode
     - Urlencode
     
   - **Valore di corrispondenza:** I valori del metodo di richiesta HTTP supportati includono:Supported HTTP request method values include:
     - GET
     - POST
     - PUT
     - HEAD
     - Elimina
     - LOCK
     - UNLOCK
     - PROFILE
     - OPTIONS
     - PROPFIND
     - Proppatch
     - MKCOL
     - COPIA
     - MOVE

## <a name="examples"></a>Esempi

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>Esempio di regole personalizzate WAF basate su parametri http

Di seguito è riportato un esempio che illustra la configurazione di una regola personalizzata con due condizioni di corrispondenza. Le richieste provengono da un sito specificato come definito dal referrer e la stringa di query non contiene "password".

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
Di seguito è riportato un esempio di configurazione per il blocco del metodo "PUT":An example configuration for blocking "PUT" method is shown as below:

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

### <a name="size-constraint"></a>Vincolo di dimensione

È possibile creare una regola personalizzata che specifica il vincolo di dimensione in una parte di una richiesta in ingresso. Ad esempio, la regola seguente blocca un URL di lunghezza superiore a 100 caratteri.

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
- [Configurare un criterio di Firewall applicazione Web con Azure PowerShellConfigure a Web Application Firewall policy using Azure PowerShell](waf-front-door-custom-rules-powershell.md) 
- Informazioni su [Web Application Firewall con front Door](afds-overview.md)
- Informazioni su come [creare una Frontdoor](../../frontdoor/quickstart-create-front-door.md).

