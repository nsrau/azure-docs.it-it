---
title: Informazioni di riferimento sulla sintassi di SQLFilter nel bus di servizio di Azure | Microsoft Docs
description: Informazioni dettagliate sulla grammatica di SQLFilter.
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
ms.date: 06/27/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 3aaec8f9b6a3bbcf814f771405c3b589de6f7ae0
ms.contentlocale: it-it
ms.lasthandoff: 06/28/2017

---

# <a name="sqlfilter-syntax"></a>Sintassi di SQLFilter

*SqlFilter* è un'istanza della [classe SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) e rappresenta un'espressione filtro basata sul linguaggio SQL che viene valutata su una [classe BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). Un SqlFilter supporta un sottoinsieme dello standard SQL-92.  
  
 Questo argomento offre informazioni dettagliate sulla grammatica di SqlFilter.  
  
```  
<predicate ::=  
      { NOT <predicate> }  
      | <predicate> AND <predicate>  
      | <predicate> OR <predicate>  
      | <expression> { = | <> | != | > | >= | < | <= } <expression>  
      | <property> IS [NOT] NULL  
      | <expression> [NOT] IN ( <expression> [, ...n] )  
      | <expression> [NOT] LIKE <pattern> [ESCAPE <escape_char>]  
      | EXISTS ( <property> )  
      | ( <predicate> )  
  
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
  
## <a name="remarks"></a>Osservazioni

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
  
     Questi sono esempi di costanti di tipo long:  
  
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

Le costanti booleane sono rappresentate dalle parole chiave **TRUE** e **FALSE**. I valori sono archiviati come `System.Boolean`.  
  
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
  
Tenere presente la semantica di [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) seguente:  
  
-   Nei nomi delle proprietà non viene fatta distinzione tra maiuscole e minuscole.  
  
-   Gli operatori seguono la semantica di conversione implicita C#, quando possibile.  
  
-   Le proprietà di sistema sono proprietà pubbliche esposte in istanze di [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).  
  
    Tenere presente la semantica di `IS [NOT] NULL` seguente:  
  
    -   `property IS NULL` viene valutato come `true` se la proprietà non esiste o se il valore della proprietà è `null`.  
  
### <a name="property-evaluation-semantics"></a>Semantica di valutazione delle proprietà  
  
-   Il tentativo di valutare una proprietà di sistema inesistente genera un'eccezione [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception).  
  
-   Una proprietà non esistente verrà valutata internamente come **valore sconosciuto**.  
  
 Valutazione dei valori sconosciuti negli operatori aritmetici:  
  
-   Per gli operatori binari, se il lato sinistro e/o destro degli operandi viene valutato come **valore sconosciuto**, il risultato è un **valore sconosciuto**.  
  
-   Per gli operatori unari, se un operando viene valutato come **valore sconosciuto**, il risultato è un **valore sconosciuto**.  
  
 Valutazione dei valori sconosciuti negli operatori di confronto binari:  
  
-   Se il lato sinistro e/o destro degli operandi viene valutato come **valore sconosciuto**, il risultato è un **valore sconosciuto**.  
  
 Valutazione dei valori sconosciuti in `[NOT] LIKE`:  
  
-   Se qualsiasi operando viene valutato come **valore sconosciuto**, il risultato è un **valore sconosciuto**.  
  
 Valutazione dei valori sconosciuti in `[NOT] IN`:  
  
-   Se l'operando di sinistra viene valutato come **valore sconosciuto**, il risultato è un **valore sconosciuto**.  
  
 Valutazione dei valori sconosciuti nell'operatore **AND**:  
  
```  
+---+---+---+---+  
|AND| T | F | U |  
+---+---+---+---+  
| T | T | F | U |  
+---+---+---+---+  
| F | F | F | F |  
+---+---+---+---+  
| U | U | F | U |  
+---+---+---+---+  
```  
  
 Valutazione dei valori sconosciuti nell'operatore **OR**:  
  
```  
+---+---+---+---+  
|OR | T | F | U |  
+---+---+---+---+  
| T | T | T | T |  
+---+---+---+---+  
| F | T | F | U |  
+---+---+---+---+  
| U | T | U | U |  
+---+---+---+---+  
```  
  
### <a name="operator-binding-semantics"></a>Semantica di binding degli operatori
  
-   Gli operatori di confronto come `>`, `>=`, `<`, `<=`, `!=` e `=` seguono la stessa semantica dell'associazione di operatori C# nelle conversioni implicite e nelle promozioni del tipo di dati.  
  
-   Gli operatori aritmetici come `+`, `-`, `*`, `/` e `%` seguono la stessa semantica dell'associazione di operatori C# nelle conversioni implicite e nelle promozioni del tipo di dati.

## <a name="next-steps"></a>Passaggi successivi

- [Classe SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [Classe SQLRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
