---
title: Sintassi di ricerca lambda - API Academic Knowledge
titlesuffix: Azure Cognitive Services
description: Informazioni sulla sintassi di ricerca lambda che è possibile usare nell'API Academic Knowledge.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: a76be5203c7d62ba973993bf6338b7496e2fce80
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55203930"
---
# <a name="lambda-search-syntax"></a>Sintassi di ricerca lambda

Ogni stringa di query di ricerca *lambda* descrive un modello di grafico. Una query deve avere almeno un nodo iniziale, che specifica da quale nodo del grafico iniziare l'attraversamento. Per specificare un nodo iniziale, chiamare il metodo *MAG.StartFrom()* e passare l'ID di uno o più nodi oppure un oggetto query che specifica i vincoli di ricerca. Il metodo *StartFrom()* ha tre overload. Tali overload accettano tutti due argomenti, di cui il secondo è facoltativo. Il primo argomento può essere un tipo long integer, una raccolta enumerabile di tipi long integer o una stringa che rappresenta un oggetto JSON, con la stessa semantica della ricerca *json*:
```
StartFrom(long cellid, IEnumerable<string> select = null)
StartFrom(IEnumerable<long> cellid, IEnumerable<string> select = null)
StartFrom(string queryObject, IEnumerable<string> select = null)
```

Il processo di scrittura di una query di ricerca lambda prevede lo spostamento da un nodo a un altro. Per specificare il tipo di limite da attraversare, usare *FollowEdge()* e passare i tipi di limite desiderati. *FollowEdge()* accetta un numero arbitrario di argomenti stringa:
```
FollowEdge(params string[] edgeTypes)
```
> [!NOTE]
> Se i tipi di limite da seguire non sono importanti, omettere *FollowEdge()* tra due nodi: la query attraverserà tutti i limiti possibili tra questi due nodi.

È possibile specificare le azioni di attraversamento da eseguire su un nodo tramite *VisitNode()*, per indicare se arrestarsi in corrispondenza del nodo e restituire il percorso corrente come risultato oppure se continuare a esplorare il grafico.  Il tipo di enumerazione *Action* definisce due tipi di azioni: *Action.Return* e *Action.Continue*. È possibile passare tale valore di enumerazione direttamente a *VisitNode()* oppure combinare gli elementi con l'operatore AND bit per bit "&". Quando si combinano due azioni, vengono eseguite entrambe le azioni. Nota: non usare l'operatore OR bit per bit "|" per le azioni. Ciò può comportare l'arresto della query senza restituzione di alcun risultato. Se si ignora *VisitNode()* tra due chiamate a *FollowEdge()*, la query esplora in modo incondizionato il grafico dopo aver raggiunto un nodo.

```
VisitNode(Action action, IEnumerable<string> select = null)
```

Per *VisitNode()*, è anche possibile passare un'espressione lambda di tipo *Expression\<Func\<INode, Action\>\>*, che accetta un elemento *INode* e restituisce un'azione di attraversamento:

```
VisitNode(Expression<Func<INode, Action>> action, IEnumerable<string> select = null)
```

## <a name="inode"></a>*INode* 

*INode* fornisce interfacce di accesso ai dati di *sola lettura* e alcune funzioni helper predefinite in un nodo. 

### <a name="basic-data-access-interfaces"></a>Interfacce di accesso ai dati di base

##### <a name="long-cellid"></a>long CellID

ID a 64 bit del nodo. 

##### <a name="t-getfieldtstring-fieldname"></a>T GetField\<T\>(string fieldName)

Ottiene il valore della proprietà specificata. *T* è il tipo desiderato in base a cui si suppone che venga interpretato il campo. Se non è possibile convertire in modo implicito il tipo desiderato dal tipo del campo, viene eseguito un tentativo di cast automatico del tipo. Nota: quando la proprietà ha più valori, *GetField\<string\>* fa in modo che l'elenco venga serializzato in una stringa Json ["val1", "val2", ...]. Se la proprietà non esiste, viene generata un'eccezione e l'esplorazione del grafico corrente viene interrotta.

##### <a name="bool-containsfieldstring-fieldname"></a>bool ContainsField(string fieldName)

Indica se esiste un campo con il nome specificato nel nodo corrente.

##### <a name="string-getstring-fieldname"></a>string get(string fieldName)

Funziona come *GetField\<string\>(fieldName)*. Tuttavia, non genera eccezioni quando il campo non viene trovato, ma restituisce una stringa vuota ("").

##### <a name="bool-hasstring-fieldname"></a>bool has(string fieldName)

Indica se la proprietà specificata esiste nel nodo corrente. Corrisponde a *ContainsField(fieldName)*.

##### <a name="bool-hasstring-fieldname-string-value"></a>bool has(string fieldName, string value)

Indica se la proprietà ha il valore specificato. 

##### <a name="int-countstring-fieldname"></a>int count(string fieldname)

Ottiene il numero di valori di una determinata proprietà. Quando la proprietà non esiste, restituisce 0.

### <a name="built-in-helper-functions"></a>Funzioni helper predefinite

##### <a name="action-returnifbool-condition"></a>Action return_if(bool condition)

Restituisce *Action.Return* se la condizione è *true*. Se la condizione è *false* e l'espressione non è stata unita ad altre azioni da un operatore AND bit per bit, l'esplorazione del grafico viene interrotta.

##### <a name="action-continueifbool-condition"></a>Action continue_if(bool condition)

Restituisce *Action.Continue* se la condizione è *true*. Se la condizione è *false* e l'espressione non è stata unita ad altre azioni da un operatore AND bit per bit, l'esplorazione del grafico viene interrotta.

##### <a name="bool-dicedouble-p"></a>bool dice(double p)

Genera un numero casuale maggiore o uguale a 0,0 e minore di 1,0. Questa funzione restituisce *true* solo se il numero è minore o uguale a *p*.

Rispetto alla ricerca *json*, la ricerca *lambda* è più espressiva: le espressioni lambda C# possono essere usate direttamente per specificare modelli di query. Ecco due esempi.

```
MAG.StartFrom(@"{
    type  : ""ConferenceSeries"",
    match : {
        FullName : ""graph""
    }
}", new List<string>{ "FullName", "ShortName" })
.FollowEdge("ConferenceInstanceIDs")
.VisitNode(v => v.return_if(v.GetField<DateTime>("StartDate").ToString().Contains("2014")),
        new List<string>{ "FullName", "StartDate" })
```

```
MAG.StartFrom(@"{
    type  : ""Affiliation"",
    match : {
        Name : ""microsoft""
    }
}").FollowEdge("PaperIDs")
.VisitNode(v => v.return_if(v.get("NormalizedTitle").Contains("graph") || v.GetField<int>("CitationCount") > 100),
        new List<string>{ "OriginalTitle", "CitationCount" })
```
