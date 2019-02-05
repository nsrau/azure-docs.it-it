---
title: Utilizzo dell'API di Language Integrated Query JavaScript in Azure Cosmos DB
description: Questo articolo illustra i concetti per l'API di Language Integrated Query JavaScript per la creazione di stored procedure e trigger in Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 101b5382eaa01ed87f05d83c82002fa1b93144b7
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55463940"
---
# <a name="javascript-query-api-in-azure-cosmos-db"></a>API di query JavaScript in Azure Cosmos DB

Oltre a eseguire query usando L'API SQL di Azure Cosmos DB, l'[SDK di Cosmos DB lato server](https://azure.github.io/azure-cosmosdb-js-server/) consente di eseguire query ottimizzate tramite un'interfaccia JavaScript. Non è necessario conoscere il linguaggio SQL per usare questa interfaccia JavaScript. L'API di query JavaScript consente di creare query a livello di programmazione passando funzioni di predicato in una sequenza di chiamate di funzione, con una sintassi familiare alle librerie JavaScript predefinite e integrate della matrice ECMAScript5 come Lodash. Le query vengono analizzate dal runtime JavaScript ed eseguite in modo efficiente usando gli indici di Azure Cosmos DB.

## <a name="supported-javascript-functions"></a>Funzioni JavaScript supportate

| **Funzione** | **Descrizione** |
|---------|---------|
|`chain() ... .value([callback] [, options])`|Avvia una chiamata concatenata che deve terminare con value().|
|`filter(predicateFunction [, options] [, callback])`|Filtra l'input usando una funzione di predicato che restituisce true o false per includere o escludere i documenti di input dal set risultante. Il comportamento di questa funzione è simile a quello di una clausola WHERE in SQL.|
|`flatten([isShallow] [, options] [, callback])`|Combina e rende flat le matrici da ogni elemento di input in un'unica matrice. Il comportamento di questa funzione è simile a quello di SelectMany in LINQ.|
|`map(transformationFunction [, options] [, callback])`|Applica una proiezione a partire da una funzione di trasformazione che esegue il mapping di ogni elemento di input a un valore o oggetto JavaScript. Il comportamento di questa funzione è simile a quello di una clausola SELECT in SQL.|
|`pluck([propertyName] [, options] [, callback])`|Questa funzione è un collegamento a una mappa che estrae il valore di una singola proprietà da ogni elemento di input.|
|`sortBy([predicate] [, options] [, callback])`|Produce un nuovo set di documenti ordinandoli nel flusso di documenti di input in ordine crescente usando il predicato specificato. Il comportamento di questa funzione è simile a quello di una clausola ORDER BY in SQL.|
|`sortByDescending([predicate] [, options] [, callback])`|Produce un nuovo set di documenti ordinandoli nel flusso di documenti di input in ordine decrescente usando il predicato specificato. Il comportamento di questa funzione è simile a quello di una clausola ORDER BY x DESC in SQL.|
|`unwind(collectionSelector, [resultSelector], [options], [callback])`|Esegue un self join con inner array e aggiunge i risultati da entrambi i lati come di tuple alla proiezione dei risultati. Ad esempio, l'aggiunta di un documento Person con person.pets produrrebbe tuple [person, pet]. Il comportamento è simile a quello di SelectMany in .NET LINK.|

Quando inclusi all'interno delle funzioni predicato e/o selettore, i seguenti costrutti JavaScript vengono automaticamente ottimizzati per l'esecuzione diretta sugli indici di Azure Cosmos DB:

- Operatori semplici: `=` `+` `-` `*` `/` `%` `|` `^` `&` `==` `!=` `===` `!===` `<` `>` `<=` `>=` `||` `&&` `<<` `>>` `>>>!` `~`
- Valori letterali, incluso il valore letterale dell'oggetto: {}
- var, return

I seguenti costrutti JavaScript non vengono ottimizzati per gli indici di Azure Cosmos DB:

- Flusso di controllo (ad esempio, if, for, while)
- Chiamate di funzione

Per altre informazioni, vedere la [documentazione relativa a JavaScript lato server di Azure Cosmos DB](https://azure.github.io/azure-cosmosdb-js-server/).

## <a name="sql-to-javascript-cheat-sheet"></a>Foglio riassuntivo di SQL a JavaScript

Nella tabella seguente vengono presentate varie query SQL e le query JavaScript corrispondenti. Come le query SQL, le proprietà (ad esempio item.id) fanno distinzione tra maiuscole e minuscole.

> [!NOTE]
> `__` (doppio carattere di sottolineatura) è un alias per `getContext().getCollection()` quando si usa l'API di query JavaScript.

|**SQL**|**API di query JavaScript**|**Descrizione**|
|---|---|---|
|SELECT *<br>FROM docs| __.map(function(doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;return doc;<br>});|Viene restituita così com'è in tutti i documenti, impaginati con token di continuazione.|
|SELECT <br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs.message AS msg,<br>&nbsp;&nbsp;&nbsp;docs.actions <br>FROM docs|__.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;id: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;msg: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;actions:doc.actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|Proietta l'ID, il messaggio (con aliasing effettuato a msg) e l'azione da tutti i documenti.|
|SELECT *<br>FROM docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return doc.id ==="X998_Y998";<br>});|Esegue una query sui documenti con il predicato: id = "X998_Y998".|
|SELECT *<br>FROM docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;ARRAY_CONTAINS(docs.Tags, 123)|__.filter(function(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return x.Tags && x.Tags.indexOf(123) > -1;<br>});|Esegue una query sui documenti che hanno una proprietà Tags e Tags è una matrice che contiene il valore 123.|
|SELECT<br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs.message AS msg<br>FROM docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return doc.id ==="X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;id: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;msg: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.value();|Esegue una query sui documenti con un predicato id = "X998_Y998" e quindi proietta l'ID e il messaggio (con aliasing effettuato a msg).|
|SELECT VALUE tag<br>FROM docs<br>JOIN tag IN docs.Tags<br>ORDER BY docs._ts|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>
  &nbsp;
  &nbsp;
  &nbsp;
  &nbsp;
  &nbsp;
  &nbsp;
  &nbsp;
  &nbsp;return doc.Tags &&amp;amp; Array.isArray(doc.Tags);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("Tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.value()|Filtra i documenti che hanno la proprietà di matrice Tags, ordina i documenti risultanti in base alla proprietà di sistema timestamp _ts e quindi proietta e appiattisce la matrice Tags.|

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come scrivere e usare stored procedure, trigger e funzioni definite dall'utente in Azure Cosmos DB:

- [Come scrivere stored procedure e trigger usando l'API di query di Javascript](how-to-write-javascript-query-api.md)
- [Uso delle stored procedure, dei trigger e delle funzioni definite dall'utente di Azure Cosmos DB](stored-procedures-triggers-udfs.md)
- [Come usare stored procedure, trigger e funzioni definite dall'utente in Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)
- [Riferimento API di lato server JavaScript di Azure Cosmos DB](https://azure.github.io/azure-cosmosdb-js-server)
- [JavaScript ES6 (ECMA 2015)](https://www.ecma-international.org/ecma-262/6.0/)
