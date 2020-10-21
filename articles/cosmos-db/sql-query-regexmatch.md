---
title: RegexMatch in linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema RegexMatch SQL in Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 620ffcb379f9fa1402e422c13bcadf8da4047409
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284201"
---
# <a name="regexmatch-azure-cosmos-db"></a>REGEXMATCH (Azure Cosmos DB)

Fornisce funzionalità di espressioni regolari. Le espressioni regolari sono una notazione concisa e flessibile per la ricerca di modelli di testo. Azure Cosmos DB usa le [espressioni regolari compatibili con Perl (PCRE)](http://www.pcre.org/). 

## <a name="syntax"></a>Sintassi
  
```sql
RegexMatch(<str_expr1>, <str_expr2>, [, <str_expr3>])  
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr1*  
   Espressione stringa in cui eseguire la ricerca.  
  
*str_expr2*  
   Espressione regolare.

*str_expr3*  
   Stringa dei modificatori selezionati da utilizzare con l'espressione regolare. Questo valore stringa è facoltativo. Se si vuole eseguire RegexMatch senza modificatori, è possibile aggiungere una stringa vuota oppure omettere completamente. 

È possibile ottenere informazioni sulla [sintassi per la creazione di espressioni regolari in Perl](https://perldoc.perl.org/perlre). 

Azure Cosmos DB supporta i quattro modificatori seguenti:

| Modificatore | Descrizione |
| ------ | ----------- |
| `m` | Considera l'espressione stringa da cercare come più righe. Senza questa opzione, "^" e "$" corrisponderanno all'inizio o alla fine della stringa e non a ogni singola riga. |
| `s` | Consente a "." di trovare la corrispondenza con qualsiasi carattere, incluso un carattere di nuova riga. | 
| `i` | Ignora maiuscole/minuscole durante i criteri di ricerca. |
| `x` | Ignorare tutti gli spazi vuoti. |

## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione booleana. Restituisce undefined se l'espressione stringa da cercare, l'espressione regolare o i modificatori selezionati non sono validi.
  
## <a name="examples"></a>Esempi
  
L'esempio di RegexMatch semplice seguente controlla la stringa "abcd" per la corrispondenza di espressioni regolari usando alcuni modificatori diversi.
  
```sql
SELECT RegexMatch ("abcd", "ABC", "") AS NoModifiers, 
RegexMatch ("abcd", "ABC", "i") AS CaseInsensitive, 
RegexMatch ("abcd", "ab.", "") AS WildcardCharacter,
RegexMatch ("abcd", "ab c", "x") AS IgnoreWhiteSpace, 
RegexMatch ("abcd", "aB c", "ix") AS CaseInsensitiveAndIgnoreWhiteSpace 
```  
  
 Questo è il set di risultati.  
  
```json
[
    {
        "NoModifiers": false,
        "CaseInsensitive": true,
        "WildcardCharacter": true,
        "IgnoreWhiteSpace": true,
        "CaseInsensitiveAndIgnoreWhiteSpace": true
    }
]
```

Con RegexMatch, è possibile usare metacaratteri per eseguire ricerche di stringhe più complesse che altrimenti non sarebbero possibili con le funzioni di sistema StartsWith, EndsWith, Contains o StringEquals. Di seguito sono riportati alcuni esempi aggiuntivi che è possibile eseguire usando il set di dati nutrizionali disponibile tramite il [Azure Cosmos DB Playground per le query](https://www.documentdb.com/sql/demo). 

> [!NOTE] 
> Se è necessario usare un metacarattere in un'espressione regolare e non si vuole che abbia un significato speciale, è consigliabile eseguire l'escape del metacarattere usando `\` .

**Controllare gli elementi che contengono una descrizione che contiene la parola "Salt" esattamente una volta:**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, "salt{1}","")
```

**Controllare gli elementi che contengono una descrizione contenente un numero compreso tra 0 e 99:**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, "[0-99]","")
```

**Controllare gli elementi con una descrizione che contiene quattro parole che iniziano con "S" o "s":**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, " s... ","i")
```

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema trarrà vantaggio da un [indice di intervallo](index-policy.md#includeexclude-strategy) se l'espressione regolare può essere suddivisa in funzioni di sistema StartsWith, EndsWith, Contains o StringEquals.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni stringa in Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
