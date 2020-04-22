---
title: Riferimento al linguaggio SQL per l'accelerazione delle query (anteprima)Query acceleration SQL language reference (preview)
titleSuffix: Azure Storage
description: Informazioni su come usare la sintassi sql per l'accelerazione delle query.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: ereilebr
ms.openlocfilehash: cea5fb507225f063e2d48c56fae254e123a8f72b
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81772119"
---
# <a name="query-acceleration-sql-language-reference-preview"></a>Riferimento al linguaggio SQL per l'accelerazione delle query (anteprima)Query acceleration SQL language reference (preview)

L'accelerazione delle query supporta un linguaggio simile a SQL ANSI per esprimere query sul contenuto BLOB.  Il dialetto SQL ad accelerazione query è un sottoinsieme di SQL ANSI, con un set limitato di tipi di dati supportati, operatori e così via, ma si espande anche su ANSI SQL per supportare query su formati di dati semistrutturati gerarchici come JSON. 

> [!NOTE]
> La funzionalità di accelerazione delle query è disponibile nell'anteprima pubblica ed è disponibile nelle aree centrali canadesi e francesi. Per esaminare le limitazioni, vedere l'articolo [Problemi noti.](data-lake-storage-known-issues.md) Per iscriversi all'anteprima, vedere [questo modulo.](https://aka.ms/adls/qa-preview-signup) 

## <a name="select-syntax"></a>Sintassi SELECT

L'unica istruzione SQL supportata dall'accelerazione delle query è l'istruzione SELECT. In questo esempio vengono restituite tutte le righe per le quali expression restituisce true.

```sql
SELECT * FROM table [WHERE expression] [LIMIT limit]
```

Per i dati in formato `BlobStorage`CSV, la *tabella* deve essere .  Ciò significa che la query verrà eseguita sul BLOB specificato nella chiamata REST.
Per i dati in formato JSON, *la tabella* è un "descrittore di tabella".   Vedere la sezione [Descrittori](#table-descriptors) di tabella di questo articolo.

Nell'esempio seguente, per ogni riga per la quale *l'espressione* WHERE restituisce true, questa istruzione restituirà una nuova riga costituita dalla valutazione di ognuna delle espressioni di proiezione.


```sql
SELECT expression [, expression …] FROM table [WHERE expression] [LIMIT limit]
```

L'esempio seguente restituisce un calcolo di aggregazione (ad esempio: il valore medio di una determinata colonna) su ognuna delle righe per le quali *expression* restituisce true. 

```sql
SELECT aggregate_expression FROM table [WHERE expression] [LIMIT limit]
```

L'esempio seguente restituisce offset adatti per la divisione di un BLOB in formato CSV.  Vedere la sezione [Sys.Split](#sys-split) di questo articolo.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

<a id="data-types" />

## <a name="data-types"></a>Tipi di dati

|Tipo di dati|Descrizione|
|---------|-------------------------------------------|
|INT      |Intero con segno a 64 bit.                     |
|FLOAT    |a virgola mobile a 64 bit ("precisiona doppia").|
|STRING   |Stringa Unicode a lunghezza variabile.            |
|timestamp|Un punto nel tempo.                           |
|BOOLEAN  |True o false.                             |

Quando si leggono valori da dati in formato CSV, tutti i valori vengono letti come stringhe.  I valori stringa possono essere convertiti in altri tipi utilizzando espressioni CAST.  I valori possono essere implicitamente cast ad altri tipi a seconda del contesto. Per altre info, vedere Precedenza dei tipi di dati [(Transact-SQL)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017).

## <a name="expressions"></a>Espressioni

### <a name="referencing-fields"></a>Riferimento ai campi

Per i dati in formato JSON o i dati in formato CSV con una riga di intestazione, è possibile fare riferimento ai campi in base al nome.  I nomi dei campi possono essere racchiusi tra virgolette o senza virgolette. I nomi dei campi racchiusi tra virgolette doppie ("), possono contenere spazi e fanno distinzione tra maiuscole e minuscole.  I nomi di campo senza virgolette non fanno distinzione tra maiuscole e minuscole e non possono contenere caratteri speciali.

Nei dati in formato CSV, i campi possono anche fare riferimento da ordinale, preceduto da un carattere di sottolineatura (_).  Ad esempio, è possibile fare riferimento al primo campo come _1 oppure all'undicesimo campo riferimento come _11.  Il riferimento ai campi tramite ordinale è utile per i dati in formato CSV che non contengono una riga di intestazione, nel qual caso l'unico modo per fare riferimento a un determinato campo è per ordine ordinale.

### <a name="operators"></a>Operatori

Sono supportati i seguenti operatori SQL standard:

``=``, ``!=``, ``<>``, ``<``, ``<=``, ``>``, ``>=``, ``+``, ``-``, ``/``, ``*``, ``%``, ``AND``, ``OR``, ``NOT``, ``CAST``, ``BETWEEN``, ``IN``, ``NULLIF``, ``COALESCE``

Se i tipi di dati a sinistra e a destra di un operatore sono diversi, la conversione automatica verrà eseguita in base alle regole specificate qui: Precedenza dei tipi di dati [(Transact-SQL)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017).

Il linguaggio SQL di accelerazione delle query supporta solo un sottoinsieme molto piccolo dei tipi di dati descritti in questo articolo.  Vedere la sezione [Tipi di dati](#data-types) di questo articolo.

### <a name="casts"></a>Cast

Il linguaggio SQL di accelerazione delle query supporta l'operatore CAST, in base alle regole seguenti: Conversione del tipo di [dati (Motore di database)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-conversion-database-engine?view=sql-server-2017).  

Il linguaggio SQL di accelerazione delle query supporta solo un piccolo sottoinsieme dei tipi di dati descritti in questo articolo.  Vedere la sezione [Tipi di dati](#data-types) di questo articolo.

### <a name="string-functions"></a>Funzioni per i valori stringa

Il linguaggio SQL di accelerazione delle query supporta le seguenti funzioni di stringa SQL standard:

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

La funzione [LIKE](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15) consente di cercare un modello. Ecco alcuni esempi che usano la funzione [LIKE](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15) ``abc,abd,cd\ntest,test2,test3\na_bc,xc%d^e,gh[i ``per cercare la stringa di dati.

|Query|Esempio|
|--|--|
|``SELECT _1, _2, _3 from BlobStorage where _2 LIKE 'a%'``|``abc,abd,cd\n``|
|``SELECT * from BlobStorage where _1 LIKE 'a[bcd]c``|``abc,abd,cd\n``|
|``SELECT _1 from BlobStorage where _2 LIKE '[^xyz]%'``|``abc\ntest\n``|
|``SELECT * from BlobStorage where _1 LIKE 'a_``|``abc,abd,cd\n``|
|``SELECT _2,_3 from BlobStorage where _3 LIKE '[g-h]_![[a-j]' Escape '!'``|``xc%d^e,gh[i\n``|

### <a name="date-functions"></a>Funzioni di data

Sono supportate le seguenti funzioni di data SQL standard:

``DATE_ADD``, ``DATE_DIFF``, ``EXTRACT``, ``TO_STRING``, ``TO_TIMESTAMP``.

Attualmente convertiamo tutti i [formati di data standard IS08601](https://www.w3.org/TR/NOTE-datetime). 

#### <a name="date_add-function"></a>DATE_ADD (funzione)

Il linguaggio SQL di accelerazione delle query supporta anno, ``DATE_ADD`` mese, giorno, ora, minuto, secondo per la funzione.

Esempi:

```sql
DATE_ADD(datepart, quantity, timestamp)
DATE_ADD('minute', 1, CAST('2017-01-02T03:04:05.006Z' AS TIMESTAMP)
```

#### <a name="date_diff-function"></a>DATE_DIFF (funzione)

Il linguaggio SQL di accelerazione delle query supporta anno, ``DATE_DIFF`` mese, giorno, ora, minuto, secondo per la funzione.

```sql
DATE_DIFF(datepart, timestamp, timestamp)
DATE_DIFF('hour','2018-11-09T00:00+05:30','2018-11-09T01:00:23-08:00') 
```

#### <a name="extract-function"></a>ESE

Per EXTRACT parte diversa da ``DATE_ADD`` quella supportata per la funzione, il linguaggio SQL di accelerazione delle query supporta timezone_hour e timezone_minute come parte di data.

Esempi:

```sql
EXTRACT(datepart FROM timestampstring)
EXTRACT(YEAR FROM '2010-01-01T')
```

#### <a name="to_string-function"></a>TO_STRING (funzione)

Esempi:

```sql
TO_STRING(TimeStamp , format)
TO_STRING(CAST('1969-07-20T20:18Z' AS TIMESTAMP),  'MMMM d, y')
```

In questa tabella vengono descritte le stringhe che ``TO_STRING`` è possibile utilizzare per specificare il formato di output della funzione.

|Stringa di formato    |Output                               |
|-----------------|-------------------------------------|
|yy               |Anno in formato a 2 cifre – 1999 come '99'|
|y                |Anno in formato a 4 cifre               |
|aaaa             |Anno in formato a 4 cifre               |
|M                |Mese dell'anno – 1                    |
|MM               |Mese imbottito zero – 01               |
|MMM              |Abbr. mese dell'anno -JAN            |
|MMMM             |Mese intero – Maggio                      |
|d                |Giorno del mese (1-31)                  |
|gg               |Giorno del mese imbottita zero (01-31)     |
|a                |AM o PM                             |
|h                |Ora del giorno (1-12)                   |
|hh               |Ore imbottite zero giorno (01-12)     |
|H                |Ora del giorno (0-23)                   |
|HH               |Ora del giorno imbottita zero (00-23)      |
|m                |Minuti dell'ora (0-59)                |
|MM               |minuto imbottito zero (00-59)           |
|s                |Secondo di minuti (0-59)             |
|ss               |Zero secondi imbottiti (00-59)          |
|S                |Frazione di secondi (0,1-0,9)        |
|SS               |Frazione di secondi (0,01-0,99)      |
|Sss              |Frazione di secondi (0,001-0,999)    |
|X                |Offset in ore                      |
|XX o XXXX       |Scostamento in ore e minuti (0430 USD)  |
|XXX o XXXXX     |Offset in ore e minuti (-07:00) |
|x                |Offset in ore (7)                  |
|xx o xxxx       |Scostamento in ore e minuti (0530 USD)    |
|Xxx o xxxxx     |Scostamento in ore e minuti (05:30 USD)   |

#### <a name="to_timestamp-function"></a>TO_TIMESTAMP (funzione)

Sono supportati solo i formati IS08601.

Esempi:

```sql
TO_TIMESTAMP(string)
TO_TIMESTAMP('2007T')
```

> [!NOTE]
> È inoltre possibile ``UTCNOW`` utilizzare la funzione per ottenere l'ora di sistema.


## <a name="aggregate-expressions"></a>Espressioni di aggregazioneAggregate Expressions

Un'istruzione SELECT può contenere una o più espressioni di proiezione o una singola espressione di aggregazione.  Sono supportate le espressioni di aggregazione seguenti:The following aggregate expressions are supported:

|Expression|Descrizione|
|--|--|
|[Conteggio(\*)](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |Restituisce il numero di record che corrispondono all'espressione del predicato.|
|[CONTA.](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |Restituisce il numero di record per i quali expression è diverso da null.|
|[MEDIA(espressione)](https://docs.microsoft.com/sql/t-sql/functions/avg-transact-sql?view=sql-server-ver15)    |Restituisce la media dei valori non null di expression.|
|[MIN(espressione)](https://docs.microsoft.com/sql/t-sql/functions/min-transact-sql?view=sql-server-ver15)    |Restituisce il valore minimo non Null di expression.|
|[MAX(espressione](https://docs.microsoft.com/sql/t-sql/functions/max-transact-sql?view=sql-server-ver15))    |Restituisce il valore massimo non null di expression.|
|[SOMMA(espressione)](https://docs.microsoft.com/sql/t-sql/functions/sum-transact-sql?view=sql-server-ver15)    |Restituisce la somma di tutti i valori non Null di expression.|

### <a name="missing"></a>Mancante

L'operatore ``IS MISSING`` è l'unico non standard supportato dal linguaggio SQL per l'accelerazione delle query.  Per i dati JSON, se un campo non è ``IS MISSING`` presente in un particolare record di input, il campo espressione restituirà il valore booleano true.

<a id="table-descriptors" />

## <a name="table-descriptors"></a>Descrittori di tabellaTable Descriptors

Per i dati CSV, `BlobStorage`il nome della tabella è sempre .  Ad esempio:

```sql
SELECT * FROM BlobStorage
```

Per i dati JSON, sono disponibili opzioni aggiuntive:For JSON data, additional options are available:

```sql
SELECT * FROM BlobStorage[*].path
```

Ciò consente query su sottoinsiemi di dati JSON.

Per le query JSON, è possibile menzionare il percorso in parte della clausola FROM. Questi percorsi consentono di analizzare il subset di dati JSON. Questi percorsi possono fare riferimento ai valori JSON Array e Object.

Facciamo un esempio per capire questo in modo più dettagliato.

Questi sono i nostri dati di esempio:This is our sample data:

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

Si potrebbe essere interessati solo all'oggetto `warehouses` JSON dai dati precedenti. L'oggetto `warehouses` è un tipo di matrice JSON, pertanto è possibile menzionarlo nella clausola FROM. La query di esempio può essere simile alla seguente.

```sql
SELECT latitude FROM BlobStorage[*].warehouses[*]
```

La query ottiene tutti i campi ma seleziona solo la latitudine.

Se si desidera accedere `dimensions` solo al valore dell'oggetto JSON, è possibile usare riferimento a tale oggetto nella query. Ad esempio:

```sql
SELECT length FROM BlobStorage[*].dimensions
```

Questo limita anche l'accesso `dimensions` ai membri dell'oggetto. Se si desidera accedere ad altri membri dei campi JSON e ai valori interni degli oggetti JSON, è possibile usare una query come illustrato nell'esempio seguente:If you want to access other members of JSON fields and inner values of JSON objects, then you might use a queries such as shown in the following example:

```sql
SELECT weight,warehouses[0].longitude,id,tags[1] FROM BlobStorage[*]
```

> [!NOTE]
> BlobStorage e BlobStorage[\*] fanno entrambi riferimento all'intero oggetto. Tuttavia, se si dispone di un percorso nella clausola FROM, sarà necessario usare BlobStorage[\*].path

<a id="sys-split" />

## <a name="syssplit"></a>Sys.Split (dividi)

Si tratta di una forma speciale dell'istruzione SELECT, disponibile solo per i dati in formato CSV.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

Utilizzare questa istruzione nei casi in cui si desidera scaricare e quindi elaborare i record di dati CSV in batch. In questo modo è possibile elaborare i record in parallelo invece di dover scaricare tutti i record contemporaneamente. Questa istruzione non restituisce i record dal file CSV. Al contrario, restituisce una raccolta di dimensioni batch. È quindi possibile utilizzare ogni dimensione batch per recuperare un batch di record di dati. 

Utilizzare il parametro *split_size* per specificare il numero di byte che ogni batch deve contenere. Ad esempio, se si desidera elaborare solo 10 MB di dati alla `SELECT sys.split(10485760)FROM BlobStorage` volta, l'istruzione sarà simile alla seguente: poiché 10 MB è uguale a 10.485.760 byte. Ogni batch conterrà tutti i record che possono rientrare in tali 10 MB. 

Nella maggior parte dei casi, la dimensione di ogni batch sarà leggermente superiore al numero specificato. Questo perché un batch non può contenere un record parziale. Se l'ultimo record di un batch inizia prima della fine della soglia, il batch sarà più grande in modo che possa contenere il record completo. Le dimensioni dell'ultimo batch saranno probabilmente inferiori a quelle specificate.

>[!NOTE]
> Il split_size deve essere di almeno 10 MB (10485760).

## <a name="see-also"></a>Vedere anche

- [Accelerazione delle query di Azure Data Lake Storage (anteprima)Azure Data Lake Storage query acceleration (preview)](data-lake-storage-query-acceleration.md)
- [Filtrare i dati usando l'accelerazione delle query di Azure Data Lake Storage (anteprima)Filter data by using Azure Data Lake Storage query acceleration (preview)](data-lake-storage-query-acceleration-how-to.md)

