---
title: Informazioni di riferimento sulla sintassi di SQLRuleAction in Azure | Documentazione Microsoft
description: Informazioni dettagliate sulla grammatica di SQLRuleAction.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2017
ms.author: sethm
ms.openlocfilehash: 83b4f76a171cd8a860e7ab43462c976bf4df941a
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2017
---
# <a name="sqlruleaction-syntax"></a>Sintassi di SQLRuleAction

*SqlRuleAction* è un'istanza della [classe SqlRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction) e rappresenta un set di azioni scritto in una sintassi basata sul linguaggio SQL ed eseguito su una [classe BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).   
  
Questo articolo offre informazioni dettagliate sulla grammatica delle azioni regola SQL.  
  
```  
<statements> ::=
    <statement> [, ...n]  
  
```  
  
```  
<statement> ::=
    <action> [;]
    Remarks
    -------
    Semicolon is optional.  
  
```  
  
```  
<action> ::=
    SET <property> = <expression>
    REMOVE <property>  
```

```
<expression> ::=
    <constant>
    | <function>
    | <property>
    | <expression> { + | - | * | / | % } <expression>
    | { + | - } <expression>
    | ( <expression> )
``` 

```
<property> := 
    [<scope> .] <property_name>
``` 
  
## <a name="arguments"></a>Argomenti  
  
-   `<scope>` è una stringa facoltativa che indica l'ambito di `<property_name>`. I valori validi sono `sys` o `user`. Il valore `sys` indica l'ambito del sistema. In questo caso, `<property_name>` sarà il nome di una proprietà pubblica della [classe BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user` indica l'ambito dell'utente. In questo caso, `<property_name>` sarà una chiave del dizionario della [classe BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). Se l'argomento `<scope>` non è specificato, l'ambito predefinito è `user`.  
  
### <a name="remarks"></a>Osservazioni  

Il tentativo di accedere a una proprietà di sistema inesistente costituisce un errore, mentre il tentativo di accedere a una proprietà utente inesistente non è un errore. Una proprietà utente inesistente viene invece valutata internamente come valore sconosciuto. Un valore sconosciuto viene gestito in modo speciale durante la valutazione degli operatori.  
  
## <a name="propertyname"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Argomenti  
 `<regular_identifier>` è una stringa rappresentata dall'espressione regolare seguente:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
 L'espressione corrisponde a qualsiasi stringa che inizia con una lettera seguita da uno o più caratteri di sottolineatura/lettere/cifre.  
  
 `[:IsLetter:]` indica qualsiasi carattere Unicode classificato come lettera Unicode. `System.Char.IsLetter(c)` restituisce `true` se `c` è una lettera Unicode.  
  
 `[:IsDigit:]` indica qualsiasi carattere Unicode classificato come cifra decimale. `System.Char.IsDigit(c)` restituisce `true` se `c` è una cifra Unicode.  
  
 `<regular_identifier>` non può essere una parola chiave riservata.  
  
 `<delimited_identifier>` è qualsiasi stringa racchiusa tra parentesi quadra aperta e parentesi quadra chiusa ([]). Una parentesi quadra chiusa è rappresentata con due parentesi quadre chiuse. Di seguito sono riportati esempi di `<delimited_identifier>`:  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
 `<quoted_identifier>` è qualsiasi stringa racchiusa tra virgolette doppie. Le virgolette doppie nell'identificatore sono rappresentate con due virgolette doppie. Non è consigliabile usare identificatori racchiusi tra virgolette perché possono essere facilmente confusi con una costante di tipo stringa. Usare se possibile un identificatore delimitato. Di seguito è riportato un esempio di `<quoted_identifier>`:  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>pattern  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Osservazioni
  
 `<pattern>` deve essere un'espressione valutata come stringa. Viene usato come modello per l'operatore LIKE      e può contenere i caratteri jolly seguenti.  
  
-   `%`: qualsiasi stringa di zero o più caratteri.  
  
-   `_`: qualsiasi carattere singolo.  
  
## <a name="escapechar"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Osservazioni
  
 `<escape_char>` deve essere un'espressione valutata come stringa di lunghezza 1. Viene usato come carattere di escape per l'operatore LIKE.  
  
 Ad esempio, `property LIKE 'ABC\%' ESCAPE '\'` corrisponde a `ABC%` anziché a una stringa che inizia con `ABC`.  
  
## <a name="constant"></a>costante  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argomenti  
  
-   `<integer_constant>` è una stringa di numeri non racchiusi tra virgolette e non contenenti separatori decimali. I valori sono archiviati internamente come `System.Int64` e seguono lo stesso intervallo.  
  
     Di seguito sono riportati esempi di costanti di tipo long:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` è una stringa di numeri non racchiusi tra virgolette e contenente un separatore decimale. I valori sono archiviati internamente come `System.Double` e seguono lo stesso intervallo e la stessa precisione.  
  
     In una versione futura, questo numero potrebbe essere archiviato in un tipo di dati diverso per supportare una semantica numerica esatta e non dover quindi fare affidamento sul fatto che il tipo di dati sottostante per `<decimal_constant>` sia `System.Double`.  
  
     Di seguito sono riportati esempi di costanti decimali:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>` è un numero scritto in notazione scientifica. I valori sono archiviati internamente come `System.Double` e seguono lo stesso intervallo e la stessa precisione. Di seguito sono riportati esempi di costanti numeriche approssimative:  
  
    ```  
    101.5E5  
    0.5E-2  
    ```  
  
## <a name="booleanconstant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>Osservazioni
  
Le costanti booleane sono rappresentate dalle parole chiave `TRUE` e `FALSE`. I valori sono archiviati come `System.Boolean`.  
  
## <a name="stringconstant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Osservazioni
  
Le costanti di tipo stringa sono racchiuse tra virgolette singole e includono qualsiasi carattere Unicode valido. Le virgolette singole incorporate in una costante di tipo stringa sono rappresentate con due virgolette singole.  
  
## <a name="function"></a>funzione  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Osservazioni  

La funzione `newid()` restituisce un **System.Guid** generato dal metodo `System.Guid.NewGuid()`.  
  
La funzione `property(name)` restituisce il valore della proprietà a cui viene fatto riferimento con `name`. Il valore di `name` può essere qualsiasi espressione valida che restituisce un valore stringa.  
  
## <a name="considerations"></a>Considerazioni

- L'azione SET viene usata per creare una nuova proprietà o aggiornare il valore di una proprietà esistente.
- L'azione REMOVE viene usata per rimuovere una proprietà.
- SET esegue la conversione implicita, se possibile, quando il tipo dell'espressione e il tipo della proprietà esistente sono diversi.
- Se viene fatto riferimento a proprietà di sistema inesistenti, l'azione ha esito negativo.
- L'azione non ha invece esito negativo se viene fatto riferimento a proprietà utente inesistenti.
- Una proprietà utente inesistente viene valutata internamente come valore sconosciuto, seguendo la stessa semantica di [SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) nella valutazione degli operatori.

## <a name="next-steps"></a>Passaggi successivi

- [Classe SQLRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
- [Classe SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
