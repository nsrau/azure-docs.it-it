---
title: Applicare la trasformazione SQL
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Apply SQL Transformation in Azure Machine Learning per eseguire una query SQLite sui set di dati di input per trasformare i dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/12/2020
ms.openlocfilehash: c66fbe59fd5b2660d02bfca285f78666d64569fe
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94555601"
---
# <a name="apply-sql-transformation"></a>Applicare la trasformazione SQL

Questo articolo descrive un modulo di Azure Machine Learning Designer.

Con il modulo Apply SQL Transformation è possibile:
  
-   Creare tabelle per i risultati e salvare i set di dati in un database portabile.  
  
-   Eseguire trasformazioni personalizzate sui tipi di dati oppure creare aggregazioni.  
  
-   Eseguire le istruzioni di query SQL per filtrare o modificare i dati e restituire i risultati della query come tabella dati.  

> [!IMPORTANT]
> Il motore SQL usato in questo modulo è **SQLite**. Per ulteriori informazioni sulla sintassi di SQLite, vedere [SQL come riconosciuto da SQLite](https://www.sqlite.org/index.html).
> Questo modulo rileverà i dati in SQLite, che si trova nel database di memoria, di conseguenza l'esecuzione del modulo richiede molto più memoria e potrebbe verificarsi un `Out of memory` errore. Verificare che il computer disponga di RAM sufficiente.

## <a name="how-to-configure-apply-sql-transformation"></a>Come configurare Apply SQL Transformation  

Il modulo può accettare come input fino a tre set di dati. Quando si fa riferimento ai set di dati connessi a ogni porta di input, è necessario utilizzare i nomi `t1` , `t2` e `t3` . Il numero di tabella indica l'indice della porta di input.  

Di seguito è riportato il codice di esempio per illustrare come unire in join due tabelle. T1 e T2 sono due set di dati connessi alle porte di input Left e Middle della **trasformazione Apply SQL** :

```sql
SELECT t1.*
    , t3.Average_Rating
FROM t1 join
    (SELECT placeID
        , AVG(rating) AS Average_Rating
    FROM t2
    GROUP BY placeID
    ) as t3
on t1.placeID = t3.placeID
```
  
Il parametro rimanente è una query SQL che usa la sintassi SQLite. Quando si digitano più righe nella casella di testo **script SQL** , utilizzare un punto e virgola per terminare ciascuna istruzione. In caso contrario, le interruzioni di riga vengono convertite in spazi.  

Il modulo supporta tutte le istruzioni standard della sintassi SQLite. Per un elenco di istruzioni non supportate, vedere la sezione [Note tecniche](#technical-notes) .

##  <a name="technical-notes"></a>Note tecniche  

Questa sezione contiene informazioni dettagliate sull'implementazione, suggerimenti e risposte alle domande più frequenti.

-   Un input è sempre necessario sulla porta 1.  
  
-   Per gli identificatori di colonna che contengono uno spazio o altri caratteri speciali, racchiudere sempre l'identificatore di colonna tra parentesi quadre o virgolette doppie quando si fa riferimento alla colonna `SELECT` nelle `WHERE` clausole o.  
  
### <a name="unsupported-statements"></a>Istruzioni non supportate  

Anche se supporta gran parte dello standard ANSI SQL, SQLite non include molte funzionalità supportate dai sistemi di database relazionale in commercio. Per ulteriori informazioni, vedere [SQL come riconosciuto da SQLite](http://www.sqlite.org/lang.html). Quando si creano istruzioni SQL, è inoltre necessario tenere presenti le restrizioni seguenti:  
  
- SQLite usa la tipizzazione dinamica dei valori anziché assegnare un tipo a una colonna, come avviene nella maggior parte dei sistemi di database relazionali. È scarsamente tipizzato e consente la conversione implicita del tipo.  
  
- `LEFT OUTER JOIN` viene implementato, ma non `RIGHT OUTER JOIN` o `FULL OUTER JOIN` .  

- È possibile usare istruzioni `RENAME TABLE` e `ADD COLUMN` con il comando `ALTER TABLE`, ma altre clausole non sono supportate, tra cui `DROP COLUMN`, `ALTER COLUMN` e `ADD CONSTRAINT`.  
  
- È possibile creare una vista in SQLite, ma successivamente le viste sono di sola lettura. Non è possibile eseguire un'istruzione `DELETE`, `INSERT` o `UPDATE` su una vista. È tuttavia possibile creare un trigger che viene attivato al tentativo di eseguire operazioni `DELETE`, `INSERT` o `UPDATE` su una vista ed eseguire altre operazioni nel corpo del trigger.  
  

Oltre all'elenco delle funzioni non supportate disponibili nel sito SQLite ufficiale, il wiki seguente fornisce un elenco di altre funzionalità non supportate: [SQLite-SQL](http://www2.sqlite.org/cvstrac/wiki?p=UnsupportedSql) non supportato  
    
## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 
