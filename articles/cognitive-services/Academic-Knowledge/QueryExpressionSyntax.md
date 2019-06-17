---
title: Sintassi delle espressioni di query - API Academic Knowledge
titlesuffix: Azure Cognitive Services
description: Informazioni su come usare la sintassi delle espressioni di query nell'API Academic Knowledge.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: 95c2e9d3f54f967b3ebb434c742f69251b80573e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61336757"
---
# <a name="query-expression-syntax"></a>Sintassi delle espressioni di query

Come osservato in precedenza, la risposta a una richiesta di **interpretazione** include un'espressione di query. La grammatica che ha interpretato la query dell'utente ha creato un'espressione di query per ogni interpretazione. Un'espressione di query può quindi essere usata per rilasciare una richiesta di **valutazione** per recuperare i risultati della ricerca di entità.

È anche possibile costruire le proprie espressione di query e usarle in una richiesta di **valutazione**. Questo può essere utile se si sta compilando un'interfaccia utente personalizzata che crea un'espressione di query in risposta alle azioni dell'utente. A tale scopo, è necessario conoscere la sintassi per le espressioni di query.  

Ogni attributo di entità che può essere incluso in un'espressione di query ha un tipo di dati specifico e un set di operatori query possibili. Il set di attributi di entità e operatori supportati per ogni attributo è specificato in [Attributi di entità](EntityAttributes.md). Una query a valore singolo richiede l'attributo per supportare l'operazione *Equals*. Una query con prefisso richiede l'attributo per supportare l'operazione *StartsWith*. Le query di intervallo numerico richiedono l'attributo per supportare l'operazione *IsBetween*.

Alcuni dei dati delle entità vengono archiviati come attributi compositi, come indicato dal punto "." nel nome dell'attributo. Ad esempio, le informazioni su autore/affiliazione vengono rappresentate come attributo composito. Contiene 4 componenti: AuN, AuId, AfN, AfId. Questi componenti sono dati separati che costituiscono un singolo valore dell'attributo di entità.


**Attributo stringa: valore singolo** (include le corrispondenze ai sinonimi)  
Ti='indexing by latent semantic analysis'  
Composite(AA.AuN='sue dumais')

**Attributo stringa: valore singolo esatto** (corrisponde solo ai valori canonici)  
Ti=='indexing by latent semantic analysis'  
Composite(AA.AuN=='susan t dumais')
     
**Attributo stringa: valore del prefisso**   
Ti='indexing by latent seman'...  
Composite(AA.AuN='sue du'...)

**Attributo numerico: valore singolo**  
Y=2010
 
**Attributo numerico: valore intervallo**  
Y>2005  
Y>=2005  
Y<2010  
Y<=2010  
Y=\[2010, 2012\) (include solo il valore limite sinistro: 2010, 2011)  
Y=\[2010, 2012\] (include entrambi i valori limite: 2010, 2011, 2012)
 
**Attributo numerico: valore del prefisso**  
Y='19'... (qualsiasi valore numerico che inizia con 19) 
 
**Attributo di data: valore singolo**  
D='2010-02-04'

**Attributo di data: valore intervallo**  
D>'2010-02-03'  
D=['2010-02-03','2010-02-05']

**Query And/Or:**  
And(Y=1985, Ti='disordered electronic systems')  
Or(Ti='disordered electronic systems', Ti='fault tolerance principles and practice')  
And(Or(Y=1985,Y=2008), Ti='disordered electronic systems')
 
**Query composite:**  
Per eseguire una query dei componenti di un attributo composito, è necessario racchiudere la parte dell'espressione di query che fa riferimento all'attributo composito nella funzione Composite(). 

Ad esempio, per eseguire una query dei documenti in base al nome dell'autore, usare la query seguente:
```
Composite(AA.AuN='mike smith')
```
<br>Per eseguire una query dei documenti in base a un autore particolare durante la permanenza dell'autore presso un istituto specifico, usare la query seguente:
```
Composite(And(AA.AuN='mike smith',AA.AfN='harvard university'))
```
<br>La funzione Composite() collega le due parti dell'attributo composito. Ciò significa che si ottengono solo i documenti in cui uno degli autori è "Mike Smith" mentre era a Harvard. 

Per eseguire una query dei documenti in base a un autore particolare in affiliazione con (altri) autori di un istituto specifico, usare la query seguente:
```
And(Composite(AA.AuN='mike smith'),Composite(AA.AfN='harvard university'))
```
<br>In questa versione, poiché Composite() viene applicata singolarmente all'autore e all'affiliazione prima di And(), si ottengono tutti i documenti in cui uno degli autori è "Mike Smith" e una delle affiliazioni degli autori è "Harvard". Questa operazione sembra simile all'esempio di query precedente, ma non è la stessa cosa.

In generale, si consideri l'esempio seguente: è presente un attributo composito C che include due componenti A e B. Un'entità può avere più valori per C. Queste sono le entità:
```
E1: C={A=1, B=1}  C={A=1,B=2}  C={A=2,B=3}
E2: C={A=1, B=3}  C={A=3,B=2}
```

<br>La query 
```
Composite(And(C.A=1, C.B=2))
```

<br>cerca solo le entità che hanno un valore per C, in cui il componente C.A è 1 e il componente C.B è 2. Solo E1 corrisponde a questa query.

La query 
```
And(Composite(C.A=1), Composite(C.B=2))
```
<br>cerca le entità che hanno un valore per C, in cui C.A è 1, e hanno anche un valore per C, in cui C.B è 2. Sia E1 che E2 corrispondono a questa query.

Nota bene:  
- Non è possibile fare riferimento a una parte di un attributo composito al di fuori di una funzione Composite().
- Non è possibile fare riferimento a parti di due diversi attributi compositi nella stessa funzione Composite().
- Non è possibile fare riferimento a un attributo che non fa parte di un attributo composito in una funzione Composite().
