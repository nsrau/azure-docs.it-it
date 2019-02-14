---
title: Interpretazione semantica - API Knowledge Exploration Service
titlesuffix: Azure Cognitive Services
description: Informazioni su come usare l'interpretazione semantica nell'API Knowledge Exploration Service (KES).
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 26f8d885f8cf85ab849ba221392df206e492aac4
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55860814"
---
# <a name="semantic-interpretation"></a>Interpretazione semantica

L'interpretazione semantica associa l'output semantico a ogni percorso interpretato tramite la grammatica.  In particolare, il servizio valuta la sequenza di istruzioni negli elementi `tag` attraversati dall'interpretazione per calcolare l'output finale.  

Un'istruzione può essere un'assegnazione di un valore letterale o di una variabile a un'altra variabile.  Può anche assegnare l'output di una funzione con 0 o più parametri a una variabile.  Ogni parametro di funzione può essere specificato usando un valore letterale o una variabile.  Se la funzione non restituisce alcun output, l'assegnazione viene omessa.

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

Una variabile viene specificata usando un identificatore name che inizia con una lettera ed è costituito solo da lettere (A-Z), numeri (0-9) e dal carattere di sottolineatura (\_).  Il tipo viene dedotto in modo implicito dal valore di output della funzione o del valore letterale assegnato. 

Di seguito è riportato un elenco dei tipi di dati attualmente supportati:

|Type|DESCRIZIONE|Esempi|
|----|----|----|
|string|Sequenza di 0 o più caratteri|"Hello World!"<br/>""|
|Bool|Valore booleano|true<br/>false|
|Int32|Intero con segno a 32 bit.  Da -2,1e9 a 2,1e9|123<br/>-321|
|Int64|Intero con segno a 64 bit. -9,2e18 e 9,2e18|9876543210|
|Double|Virgola mobile a precisione doppia. 1,7e+/-308 (15 cifre)|123,456789<br/>1,23456789e2|
|Guid|Identificatore univoco globale|"602DD052-CC47-4B23-A16A-26B52D30C05B"|
|Query|Espressione di query che specifica un subset di oggetti dati nell'indice|All()<br/>And(*q1*, *q2*)|

## <a name="semantic-functions"></a>Funzioni semantiche

Esiste un set predefinito di funzioni semantiche,  che consentono la costruzione di query sofisticate e offrono il controllo sensibile al contesto sulle interpretazioni della grammatica.

### <a name="and-function"></a>Funzione And

`query = And(query1, query2);`

Restituisce una query composta dall'intersezione di due query di input.

### <a name="or-function"></a>Funzione Or

`query = Or(query1, query2);`

Restituisce una query composta dall'unione di due query di input.

### <a name="all-function"></a>Funzione All

`query = All();`

Restituisce una query che include tutti gli oggetti dati.

Nell'esempio seguente la funzione All() viene usata per creare in modo iterativo una query basata sull'intersezione di 1 o più parole chiave.

```
<tag>query = All();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = And(query, keyword);</tag>
</item>
```

### <a name="none-function"></a>Funzione None

`query = None();`

Restituisce una query che non include oggetti dati.

Nell'esempio seguente la funzione None() viene usata per creare in modo iterativo una query basata sull'unione di 1 o più parole chiave.

```
<tag>query = None();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = Or(query, keyword);</tag>
</item>
```

### <a name="query-function"></a>Funzione Query

```
query = Query(attrName, value)
query = Query(attrName, value, op)
```

Restituisce una query che include solo oggetti dati il cui attributo *attrName* corrisponde al valore *value* a seconda dell'operazione *op* specificata, che usa come valore predefinito "eq".  Usare in genere un elemento `attrref` per creare una query basata sulla stringa di query di input corrispondente.  Se un valore viene specificato o ottenuto con altri mezzi, è possibile usare la funzione Query() per creare una query corrispondente a questo valore.

Nell'esempio seguente la funzione Query() viene usata per implementare il supporto per specificare le pubblicazioni accademiche di un particolare decennio.

```xml
written in the 90s
<tag>
  beginYear = Query("academic#Year", 1990, "ge");
  endYear = Query("academic#Year", 2000, "lt");
  year = And(beginYear, endYear);
</tag>
```

### <a name="composite-function"></a>Funzione Composite

`query = Composite(innerQuery);`

Restituisce una query che incapsula un elemento *innerQuery* costituito da corrispondenze con gli attributi secondari di un comune attributo composito *attr*.  Per l'incapsulamento è necessario che l'attributo composito *attr* di qualsiasi oggetto dati corrispondente abbia almeno un valore che soddisfa singolarmente *innerQuery*.  Si noti che una query sugli attributi secondari di un attributo composito deve essere incapsulata usando la funzione Composite() prima di poter essere combinata con altre query.

Ad esempio, la query seguente restituisce le pubblicazioni accademiche di "harry shum" mentre era in "microsoft":
```
Composite(And(Query("academic#Author.Name", "harry shum"), 
              Query("academic#Author.Affiliation", "microsoft")));
```

La query seguente restituisce invece le pubblicazioni accademiche in cui uno degli autori è "harry shum" e una delle affiliazioni è "microsoft":
```
And(Composite(Query("academic#Author.Name", "harry shum"), 
    Composite(Query("academic#Author.Affiliation", "microsoft")));
```

### <a name="getvariable-function"></a>Funzione GetVariable

`value = GetVariable(name, scope);`

Restituisce il valore della variabile *name* definito nell'elemento *scope* specificato.  *name* è un identificatore che inizia con una lettera ed è costituito solo da lettere (A-Z), numeri (0-9) e dal carattere di sottolineatura ().  *scope* può essere impostato su "request" o "system".  Si noti che le variabili definite in ambiti diversi sono diverse tra loro, incluse quelle definite tramite l'output delle funzioni semantiche.

Le variabili dell'ambito della richiesta vengono condivise da tutte le interpretazioni all'interno della richiesta di interpretazione corrente.  Possono essere usate per controllare la ricerca di interpretazioni tramite la grammatica.

Le variabili di sistema sono predefinite dal servizio e possono essere usate per recuperare diverse statistiche sullo stato corrente del sistema.  Di seguito è riportato il set di variabili di sistema attualmente supportate:

|NOME|Type|DESCRIZIONE|
|----|----|----|
|IsAtEndOfQuery|Bool|true se l'interpretazione corrente ha confrontato tutto il testo della query di input|
|IsBeyondEndOfQuery|Bool|true se l'interpretazione corrente ha suggerito completamenti oltre al testo della query di input|

### <a name="setvariable-function"></a>Funzione SetVariable

`SetVariable(name, value, scope);`

Assegna *value* alla variabile *name* nell'elemento *scope* specificato.  *name* è un identificatore che inizia con una lettera ed è costituito solo da lettere (A-Z), numeri (0-9) e dal carattere di sottolineatura ().  Al momento l'unico valore valido per *scope* è "request".  Non sono disponibili variabili sistema impostabili.

Le variabili dell'ambito della richiesta vengono condivise da tutte le interpretazioni all'interno della richiesta di interpretazione corrente.  Possono essere usate per controllare la ricerca di interpretazioni tramite la grammatica.

### <a name="assertequals-function"></a>Funzione AssertEquals

`AssertEquals(value1, value2);`

Se *value1* e *value2* sono equivalenti, la funzione ha esito positivo e nessun effetto collaterale.  In caso contrario, la funzione ha esito negativo e rifiuta l'interpretazione.

### <a name="assertnotequals-function"></a>Funzione AssertNotEquals

`AssertNotEquals(value1, value2);`

Se *value1* e *value2* non sono equivalenti, la funzione ha esito positivo e nessun effetto collaterale.  In caso contrario, la funzione ha esito negativo e rifiuta l'interpretazione.


