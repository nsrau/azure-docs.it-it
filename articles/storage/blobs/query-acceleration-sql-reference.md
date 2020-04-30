---
title: Guida di riferimento al linguaggio SQL per l'accelerazione delle query (anteprima)
titleSuffix: Azure Storage
description: Informazioni su come usare la sintassi SQL di accelerazione query.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: ereilebr
ms.openlocfilehash: cea5fb507225f063e2d48c56fae254e123a8f72b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81772119"
---
# <a name="query-acceleration-sql-language-reference-preview"></a>Guida di riferimento al linguaggio SQL per l'accelerazione delle query (anteprima)

L'accelerazione delle query supporta un linguaggio simile a SQL ANSI per l'espressione di query sul contenuto del BLOB.  Il sottolinguaggio SQL per l'accelerazione delle query è un subset di ANSI SQL con un set limitato di tipi di dati supportati, operatori e così via, ma si espande anche in ANSI SQL per supportare le query su formati di dati semistrutturati gerarchici come JSON. 

> [!NOTE]
> La funzionalità di accelerazione delle query è in anteprima pubblica ed è disponibile nelle aree Canada centrale e Francia centrale. Per esaminare le limitazioni, vedere l'articolo relativo ai [problemi noti](data-lake-storage-known-issues.md) . Per iscriversi all'anteprima, vedere [questo modulo](https://aka.ms/adls/qa-preview-signup). 

## <a name="select-syntax"></a>Seleziona sintassi

L'unica istruzione SQL supportata da accelerazione query è l'istruzione SELECT. In questo esempio viene restituita ogni riga per la quale Expression restituisce true.

```sql
SELECT * FROM table [WHERE expression] [LIMIT limit]
```

Per i dati in formato CSV *table* , la tabella `BlobStorage`deve essere.  Ciò significa che la query verrà eseguita in base a qualsiasi blob specificato nella chiamata REST.
Per i dati in formato JSON, *Table* è un "descrittore di tabella".   Vedere la sezione [descrittori di tabella](#table-descriptors) di questo articolo.

Nell'esempio seguente, per ogni riga per cui l' *espressione* where restituisce true, questa istruzione restituirà una nuova riga effettuata dalla valutazione di ciascuna espressione di proiezione.


```sql
SELECT expression [, expression …] FROM table [WHERE expression] [LIMIT limit]
```

Nell'esempio seguente viene restituito un calcolo di aggregazione, ad esempio il valore medio di una determinata colonna, su ognuna delle righe per le quali *Expression* restituisce true. 

```sql
SELECT aggregate_expression FROM table [WHERE expression] [LIMIT limit]
```

Nell'esempio seguente vengono restituiti offset appropriati per la suddivisione di un BLOB in formato CSV.  Vedere la sezione [sys. Split](#sys-split) di questo articolo.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

<a id="data-types" />

## <a name="data-types"></a>Tipi di dati

|Tipo di dati|Descrizione|
|---------|-------------------------------------------|
|INT      |Intero con segno a 64 bit.                     |
|FLOAT    |virgola mobile a 64 bit ("doppia precisione").|
|STRING   |Stringa Unicode a lunghezza variabile.            |
|timestamp|Un momento specifico.                           |
|BOOLEAN  |True o false.                             |

Durante la lettura di valori da dati in formato CSV, tutti i valori vengono letti come stringhe.  I valori stringa possono essere convertiti in altri tipi usando le espressioni CAST.  È possibile eseguire il cast implicito dei valori ad altri tipi a seconda del contesto. per altre informazioni, vedere [precedenza dei tipi di dati (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017).

## <a name="expressions"></a>Espressioni

### <a name="referencing-fields"></a>Campi di riferimento

Per i dati in formato JSON o i dati in formato CSV con una riga di intestazione, è possibile fare riferimento ai campi in base al nome.  I nomi dei campi possono essere racchiusi tra virgolette o senza virgolette. I nomi di campo tra virgolette sono racchiusi tra virgolette doppie ("), possono contenere spazi e fanno distinzione tra maiuscole e minuscole.  I nomi di campo non racchiusi tra virgolette non fanno distinzione tra maiuscole e minuscole e non possono contenere caratteri speciali.

Nei dati in formato CSV, è possibile fare riferimento ai campi anche in base all'ordinale, preceduto da un carattere di sottolineatura (_).  Ad esempio, è possibile fare riferimento al primo campo come _1 oppure fare riferimento all'undicesimo campo come _11.  Il riferimento ai campi per ordinale è utile per i dati in formato CSV che non contengono una riga di intestazione, nel qual caso l'unico modo per fare riferimento a un campo specifico è in base al numero ordinale.

### <a name="operators"></a>Operatori

Sono supportati gli operatori SQL standard seguenti:

``=``, ``!=``, ``<>``, ``<``, ``<=``, ``>``, ``>=``, ``+``, ``-``, ``/``, ``*``, ``%``, ``AND``, ``OR``, ``NOT``, ``CAST``, ``BETWEEN``, ``IN``, ``NULLIF``, ``COALESCE``

Se i tipi di dati a sinistra e a destra di un operatore sono diversi, la conversione automatica verrà eseguita in base alle regole specificate qui: [precedenza dei tipi di dati (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017).

Il linguaggio SQL di accelerazione query supporta solo un subset molto piccolo dei tipi di dati descritti in questo articolo.  Vedere la sezione [tipi di dati](#data-types) di questo articolo.

### <a name="casts"></a>Cast

Il linguaggio SQL di accelerazione query supporta l'operatore CAST, in base alle regole seguenti: [conversione del tipo di dati (motore di database)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-conversion-database-engine?view=sql-server-2017).  

Il linguaggio SQL di accelerazione query supporta solo un piccolo subset dei tipi di dati descritti in questo articolo.  Vedere la sezione [tipi di dati](#data-types) di questo articolo.

### <a name="string-functions"></a>Funzioni per i valori stringa

Il linguaggio SQL per l'accelerazione delle query supporta le seguenti funzioni di stringa SQL standard:

``LIKE``, ``CHAR_LENGTH``, ``CHARACTER_LENGTH``, ``LOWER``, ``UPPER``, ``SUBSTRING``, ``TRIM``, ``LEADING``, ``TRAILING``.

Ecco alcuni esempi:

|Funzione|Esempio|Risultato|
|---|---|---|
|CHARACTER_LENGTH|``SELECT CHARACTER_LENGTH('abcdefg') from BlobStorage`` |``7``|
|CHAR_LENGTH|``SELECT CHAR_LENGTH(_1) from BlobStorage``|``1``|
|LOWER|``SELECT LOWER('AbCdEfG') from BlobStorage``|``abcdefg``|
|UPPER|``SELECT UPPER('AbCdEfG') from BlobStorage``|``ABCDEFG``|
|SUBSTRING|``SUBSTRING('123456789', 1, 5)``|``23456``|
|TRIM|``TRIM(BOTH '123' FROM '1112211Microsoft22211122')``|``Microsoft``|

La funzione [like](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15) consente di cercare un modello. Di seguito sono riportati alcuni esempi che usano la funzione [like](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15) per cercare la stringa ``abc,abd,cd\ntest,test2,test3\na_bc,xc%d^e,gh[i ``di dati.

|Query|Esempio|
|--|--|
|``SELECT _1, _2, _3 from BlobStorage where _2 LIKE 'a%'``|``abc,abd,cd\n``|
|``SELECT * from BlobStorage where _1 LIKE 'a[bcd]c``|``abc,abd,cd\n``|
|``SELECT _1 from BlobStorage where _2 LIKE '[^xyz]%'``|``abc\ntest\n``|
|``SELECT * from BlobStorage where _1 LIKE 'a_``|``abc,abd,cd\n``|
|``SELECT _2,_3 from BlobStorage where _3 LIKE '[g-h]_![[a-j]' Escape '!'``|``xc%d^e,gh[i\n``|

### <a name="date-functions"></a>Funzioni di data

Sono supportate le funzioni di data SQL standard seguenti:

``DATE_ADD``, ``DATE_DIFF``, ``EXTRACT``, ``TO_STRING``, ``TO_TIMESTAMP``.

Attualmente si convertono tutti i [formati di data di IS08601 standard](https://www.w3.org/TR/NOTE-datetime). 

#### <a name="date_add-function"></a>Funzione DATE_ADD

Il linguaggio SQL per l'accelerazione delle query supporta anno, mese, giorno, ora, minuto e ``DATE_ADD`` secondo per la funzione.

Esempi:

```sql
DATE_ADD(datepart, quantity, timestamp)
DATE_ADD('minute', 1, CAST('2017-01-02T03:04:05.006Z' AS TIMESTAMP)
```

#### <a name="date_diff-function"></a>Funzione DATE_DIFF

Il linguaggio SQL per l'accelerazione delle query supporta anno, mese, giorno, ora, minuto e ``DATE_DIFF`` secondo per la funzione.

```sql
DATE_DIFF(datepart, timestamp, timestamp)
DATE_DIFF('hour','2018-11-09T00:00+05:30','2018-11-09T01:00:23-08:00') 
```

#### <a name="extract-function"></a>ESTRAi funzione

Per ESTRAi diversi dalla parte della data supportata ``DATE_ADD`` per la funzione, il linguaggio SQL di accelerazione query supporta timezone_hour e timezone_minute come parte della data.

Esempi:

```sql
EXTRACT(datepart FROM timestampstring)
EXTRACT(YEAR FROM '2010-01-01T')
```

#### <a name="to_string-function"></a>Funzione TO_STRING

Esempi:

```sql
TO_STRING(TimeStamp , format)
TO_STRING(CAST('1969-07-20T20:18Z' AS TIMESTAMP),  'MMMM d, y')
```

In questa tabella vengono descritte le stringhe che è possibile utilizzare per specificare il formato ``TO_STRING`` di output della funzione.

|Stringa di formato    |Output                               |
|-----------------|-------------------------------------|
|yy               |Anno in formato a 2 cifre-1999 come '99 '|
|y                |Anno in formato a 4 cifre               |
|aaaa             |Anno in formato a 4 cifre               |
|M                |Mese dell'anno-1                    |
|MM               |Mese con riempimento zero-01               |
|MMM              |Abbr. mese dell'anno-JAN            |
|MMMM             |Mese intero-maggio                      |
|d                |Giorno del mese (1-31)                  |
|gg               |Giorno del mese con riempimento zero (01-31)     |
|a                |AM o PM                             |
|h                |Ora del giorno (1-12)                   |
|hh               |Zero ore riempite o giorno (01-12)     |
|H                |Ora del giorno (0-23)                   |
|HH               |Ora del giorno con riempimento zero (00-23)      |
|m                |Minuto dell'ora (0-59)                |
|MM               |Minuto con riempimento zero (00-59)           |
|s                |Secondi di minuti (0-59)             |
|ss               |Zero secondi riempiti (00-59)          |
|S                |Frazione di secondi (0.1-0,9)        |
|SS               |Frazione di secondi (0,01-0,99)      |
|SSS              |Frazione di secondi (0,001-0,999)    |
|X                |Offset in ore                      |
|XX o XXXX       |Offset in ore e minuti (+ 0430)  |
|XXX o XXXXX     |Offset in ore e minuti (-07:00) |
|x                |Offset in ore (7)                  |
|XX o xxxx       |Offset in ore e minuti (+ 0530)    |
|Xxx o xxxxx     |Offset in ore e minuti (+ 05:30)   |

#### <a name="to_timestamp-function"></a>Funzione TO_TIMESTAMP

Sono supportati solo i formati IS08601.

Esempi:

```sql
TO_TIMESTAMP(string)
TO_TIMESTAMP('2007T')
```

> [!NOTE]
> È anche possibile usare la ``UTCNOW`` funzione per ottenere l'ora di sistema.


## <a name="aggregate-expressions"></a>Espressioni di aggregazione

Un'istruzione SELECT può contenere una o più espressioni di proiezione o una singola espressione di aggregazione.  Sono supportate le espressioni di aggregazione seguenti:

|Expression|Descrizione|
|--|--|
|[CONTEGGIO (\*)](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |Restituisce il numero di record che corrispondono all'espressione del predicato.|
|[CONTEGGIO (espressione)](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |Restituisce il numero di record per i quali Expression è diverso da null.|
|[MEDIA (espressione)](https://docs.microsoft.com/sql/t-sql/functions/avg-transact-sql?view=sql-server-ver15)    |Restituisce la media dei valori non null dell'espressione.|
|[MIN (espressione)](https://docs.microsoft.com/sql/t-sql/functions/min-transact-sql?view=sql-server-ver15)    |Restituisce il valore minimo non null dell'espressione.|
|[Max (espressione](https://docs.microsoft.com/sql/t-sql/functions/max-transact-sql?view=sql-server-ver15))    |Restituisce il valore massimo non null dell'espressione.|
|[SUM (espressione)](https://docs.microsoft.com/sql/t-sql/functions/sum-transact-sql?view=sql-server-ver15)    |Restituisce la somma di tutti i valori non null dell'espressione.|

### <a name="missing"></a>MANCANTE

L' ``IS MISSING`` operatore è l'unico non standard supportato dal linguaggio SQL per l'accelerazione delle query.  Per i dati JSON, se un campo non è presente in un particolare record di input, ``IS MISSING`` il campo dell'espressione restituirà il valore booleano true.

<a id="table-descriptors" />

## <a name="table-descriptors"></a>Descrittori di tabella

Per i dati CSV, il nome della tabella `BlobStorage`è sempre.  Ad esempio:

```sql
SELECT * FROM BlobStorage
```

Per i dati JSON sono disponibili opzioni aggiuntive:

```sql
SELECT * FROM BlobStorage[*].path
```

In questo modo è possibile eseguire query su subset di dati JSON.

Per le query JSON, è possibile menzionare il percorso nella parte della clausola FROM. Questi percorsi consentiranno di analizzare il subset di dati JSON. Questi percorsi possono fare riferimento a valori di matrice e oggetto JSON.

Ecco un esempio per comprenderlo in modo più dettagliato.

Ecco i dati di esempio:

```json
{
  "id": 1,
  "name": "mouse",
  "price": 12.5,
  "tags": [
    "wireless",
    "accessory"
  ],
  "dimensions": {
    "length": 3,
    "width": 2,
    "height": 2
  },
  "weight": 0.2,
  "warehouses": [
    {
      "latitude": 41.8,
      "longitude": -87.6
    }
  ]
}
```

Potrebbe essere interessante solo l' `warehouses` oggetto JSON dei dati precedenti. L' `warehouses` oggetto è un tipo di matrice JSON, pertanto è possibile citarlo nella clausola from. La query di esempio può avere un aspetto simile al seguente.

```sql
SELECT latitude FROM BlobStorage[*].warehouses[*]
```

La query ottiene tutti i campi, ma seleziona solo la latitudine.

Se si desidera accedere solo al valore `dimensions` dell'oggetto JSON, è possibile utilizzare fare riferimento a tale oggetto nella query. Ad esempio:

```sql
SELECT length FROM BlobStorage[*].dimensions
```

Questo limita anche l'accesso ai membri dell' `dimensions` oggetto. Se si vuole accedere ad altri membri dei campi JSON e dei valori interni degli oggetti JSON, è possibile usare una query simile a quella illustrata nell'esempio seguente:

```sql
SELECT weight,warehouses[0].longitude,id,tags[1] FROM BlobStorage[*]
```

> [!NOTE]
> BlobStorage e BlobStorage [\*] fanno entrambi riferimento all'intero oggetto. Tuttavia, se si ha un percorso nella clausola FROM, è necessario usare BlobStorage [\*]. Path

<a id="sys-split" />

## <a name="syssplit"></a>Sys. Split

Si tratta di un formato speciale dell'istruzione SELECT, disponibile solo per i dati in formato CSV.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

Usare questa istruzione nei casi in cui si vuole scaricare e quindi elaborare record di dati CSV in batch. In questo modo è possibile elaborare i record in parallelo anziché dover scaricare tutti i record alla volta. Questa istruzione non restituisce record dal file CSV. Viene invece restituito un insieme di dimensioni batch. È quindi possibile usare ogni dimensione del batch per recuperare un batch di record di dati. 

Utilizzare il parametro *split_size* per specificare il numero di byte che si desidera includere in ogni batch. Se, ad esempio, si desidera elaborare solo 10 MB di dati alla volta, l'istruzione avrà un aspetto simile al seguente: `SELECT sys.split(10485760)FROM BlobStorage` perché 10 MB è uguale a 10.485.760 byte. Ogni batch conterrà tutti i record che possono rientrare in questi 10 MB. 

Nella maggior parte dei casi, la dimensione di ogni batch sarà leggermente superiore al numero specificato. Questo perché un batch non può contenere un record parziale. Se l'ultimo record in un batch viene avviato prima della fine della soglia, il batch sarà più grande, in modo che possa contenere il record completo. La dimensione dell'ultimo batch sarà probabilmente inferiore alla dimensione specificata.

>[!NOTE]
> Il split_size deve essere almeno 10 MB (10485760).

## <a name="see-also"></a>Vedere anche

- [Accelerazione query Azure Data Lake Storage (anteprima)](data-lake-storage-query-acceleration.md)
- [Filtrare i dati usando Azure Data Lake Storage Acceleration query (anteprima)](data-lake-storage-query-acceleration-how-to.md)

