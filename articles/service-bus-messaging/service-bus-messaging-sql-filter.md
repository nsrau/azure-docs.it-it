---
title: Sintassi del filtro SQL per la regola di sottoscrizione del bus di servizio di Azure | Microsoft Docs
description: Questo articolo fornisce informazioni dettagliate sulla grammatica del filtro SQL. Un filtro SQL supporta un subset dello standard SQL-92.
ms.topic: article
ms.date: 11/24/2020
ms.openlocfilehash: 9bff18b2161e419d728c360c9ed950ac2867fea8
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498677"
---
# <a name="subscription-rule-sql-filter-syntax"></a>Sintassi del filtro SQL per la regola di sottoscrizione

Un *filtro SQL* è uno dei tipi di filtro disponibili per le sottoscrizioni dell'argomento del bus di servizio. Si tratta di un'espressione di testo che si basa su un subset dello standard SQL-92. Le espressioni di filtro vengono usate con l' `sqlExpression` elemento della proprietà' SqlFilter ' di un bus di servizio `Rule` in un [modello di Azure Resource Manager](service-bus-resource-manager-namespace-topic-with-rule.md)o dell'argomento del comando dell'interfaccia della riga di comando di Azure `az servicebus topic subscription rule create` [`--filter-sql-expression`](/cli/azure/servicebus/topic/subscription/rule?preserve-view=true&view=azure-cli-latest#az_servicebus_topic_subscription_rule_create) e di diverse funzioni SDK che consentono di gestire le regole di sottoscrizione.

Il bus di servizio Premium supporta anche la [sintassi del selettore dei messaggi JMS SQL](https://docs.oracle.com/javaee/7/api/javax/jms/Message.html) tramite l'API JMS 2,0.

  
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
  
-   `<scope>` è una stringa facoltativa che indica l'ambito di `<property_name>`. I valori validi sono `sys` o `user`. Il `sys` valore indica l'ambito del sistema in cui `<property_name>` è il nome di una proprietà pubblica della [classe BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user` indica l'ambito dell'utente in cui `<property_name>` è una chiave del dizionario della [classe BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) . `user` l'ambito è l'ambito predefinito se `<scope>` non è specificato.  
  
## <a name="remarks"></a>Commenti

Un tentativo di accedere a una proprietà di sistema inesistente è un errore, mentre un tentativo di accedere a una proprietà utente inesistente non è un errore. Una proprietà utente inesistente viene invece valutata internamente come valore sconosciuto. Un valore sconosciuto viene gestito in modo speciale durante la valutazione degli operatori.  
  
## <a name="property_name"></a>property_name  
  
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
  
Un `<regular_identifier>` non può essere una parola chiave riservata.  
  
`<delimited_identifier>` è qualsiasi stringa racchiusa tra parentesi quadra aperta e parentesi quadra chiusa ([]). Una parentesi quadra chiusa è rappresentata con due parentesi quadre chiuse. Di seguito sono riportati esempi di `<delimited_identifier>`:  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
`<quoted_identifier>` è qualsiasi stringa racchiusa tra virgolette doppie. Le virgolette doppie nell'identificatore sono rappresentate con due virgolette doppie. Non è consigliabile usare gli identificatori delimitati perché può essere facilmente confuso con una costante di stringa. Usare se possibile un identificatore delimitato. Di seguito è riportato un esempio di `<quoted_identifier>` :  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>pattern  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Commenti
  
`<pattern>` deve essere un'espressione valutata come stringa. Viene usato come modello per l'operatore LIKE.      e può contenere i caratteri jolly seguenti.  
  
-   `%`: qualsiasi stringa di zero o più caratteri.  
  
-   `_`: qualsiasi carattere singolo.  
  
## <a name="escape_char"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Commenti  

`<escape_char>` deve essere un'espressione valutata come stringa di lunghezza 1. Viene usato come carattere di escape per l'operatore LIKE.  
  
 Ad esempio, `property LIKE 'ABC\%' ESCAPE '\'` corrisponde a `ABC%` anziché a una stringa che inizia con `ABC`.  
  
## <a name="constant"></a>constant  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argomenti  
  
-   `<integer_constant>` è una stringa di numeri che non sono racchiusi tra virgolette e non contengono punti decimali. I valori sono archiviati internamente come `System.Int64` e seguono lo stesso intervallo.  
  
     Di seguito sono riportati esempi di costanti lunghe:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` è una stringa di numeri che non sono racchiusi tra virgolette e contengono una virgola decimale. I valori sono archiviati internamente come `System.Double` e seguono lo stesso intervallo e la stessa precisione.  
  
     In una versione futura, questo numero potrebbe essere archiviato in un tipo di dati diverso per supportare la semantica esatta dei numeri, pertanto non è consigliabile fare affidamento sul fatto che il tipo di dati sottostante è `System.Double` per `<decimal_constant>` .  
  
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
  
## <a name="boolean_constant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>Commenti  

Le costanti booleane sono rappresentate dalle parole chiave **TRUE** e **FALSE**. I valori sono archiviati come `System.Boolean`.  
  
## <a name="string_constant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Commenti  

Le costanti di tipo stringa sono racchiuse tra virgolette singole e includono qualsiasi carattere Unicode valido. Le virgolette singole incorporate in una costante di tipo stringa sono rappresentate con due virgolette singole.  
  
## <a name="function"></a>function  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Commenti
  
La `newid()` funzione restituisce un oggetto `System.Guid` generato dal `System.Guid.NewGuid()` metodo.  
  
La funzione `property(name)` restituisce il valore della proprietà a cui viene fatto riferimento con `name`. Il valore di `name` può essere qualsiasi espressione valida che restituisce un valore stringa.  
  
## <a name="considerations"></a>Considerazioni
  
Tenere presente la semantica di [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) seguente:  
  
-   Nei nomi delle proprietà non viene fatta distinzione tra maiuscole e minuscole.  
  
-   Gli operatori seguono la semantica di conversione implicita C#, quando possibile.  
  
-   Le proprietà di sistema sono proprietà pubbliche esposte in istanze di [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).  
  
    Tenere presente la semantica di `IS [NOT] NULL` seguente:  
  
    -   `property IS NULL` viene valutato come `true` se la proprietà non esiste o se il valore della proprietà è `null`.  
  
### <a name="property-evaluation-semantics"></a>Semantica di valutazione delle proprietà  
  
- Il tentativo di valutare una proprietà di sistema inesistente genera un'eccezione [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception).  
  
- Una proprietà che non esiste viene valutata internamente come **sconosciuta**.  
  
  Valutazione dei valori sconosciuti negli operatori aritmetici:  
  
- Per gli operatori binari, se il lato sinistro o destro degli operandi viene valutato come **sconosciuto**, il risultato è **sconosciuto**.  
  
- Per gli operatori unari, se un operando viene valutato come **valore sconosciuto**, il risultato è un **valore sconosciuto**.  
  
  Valutazione dei valori sconosciuti negli operatori di confronto binari:  
  
- Se il lato sinistro o destro degli operandi viene valutato come **sconosciuto**, il risultato è **sconosciuto**.  
  
  Valutazione dei valori sconosciuti in `[NOT] LIKE`:  
  
- Se un operando viene valutato come **sconosciuto**, il risultato è **sconosciuto**.  
  
  Valutazione dei valori sconosciuti in `[NOT] IN`:  
  
- Se l'operando sinistro viene valutato come **sconosciuto**, il risultato è **sconosciuto**.  
  
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


## <a name="examples"></a>Esempi

### <a name="set-rule-action-for-a-sql-filter"></a>Imposta azione regola per un filtro SQL

```csharp
// instantiate the ManagementClient
this.mgmtClient = new ManagementClient(connectionString);

// create the SQL filter
var sqlFilter = new SqlFilter("source = @stringParam");

// assign value for the parameter
sqlFilter.Parameters.Add("@stringParam", "orders");

// instantiate the Rule = Filter + Action
var filterActionRule = new RuleDescription
{
    Name = "filterActionRule",
    Filter = sqlFilter,
    Action = new SqlRuleAction("SET source='routedOrders'")
};

// create the rule on Service Bus
await this.mgmtClient.CreateRuleAsync(topicName, subscriptionName, filterActionRule);
```

### <a name="sql-filter-on-a-system-property"></a>Filtro SQL su una proprietà di sistema

```csharp
sys.Label LIKE '%bus%'`
```

### <a name="using-or"></a>Utilizzando o 

```csharp
 sys.Label LIKE '%bus%'` OR `user.tag IN ('queue', 'topic', 'subscription')
```

### <a name="using-in-and-not-in"></a>Uso di IN e NOT IN

```csharp
StoreId IN('Store1', 'Store2', 'Store3')"

sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'

sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')
```

Per un esempio C#, vedere l' [argomento relativo ai filtri di esempio su GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters).


## <a name="next-steps"></a>Passaggi successivi

- [Classe SQLFilter (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [Classe SQLFilter (.NET Standard)](/dotnet/api/microsoft.azure.servicebus.sqlfilter)
- [Classe SqlFilter (Java)](/java/api/com.microsoft.azure.servicebus.rules.SqlFilter)
- [SqlRuleFilter (JavaScript)](/javascript/api/@azure/service-bus/sqlrulefilter)
- [AZ ServiceBus topic Subscription Rule](/cli/azure/servicebus/topic/subscription/rule)
- [New-AzServiceBusRule](/powershell/module/az.servicebus/new-azservicebusrule)