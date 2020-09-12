---
title: Informazioni di riferimento sul linguaggio SQL di accelerazione query
titleSuffix: Azure Storage
description: Informazioni su come usare la sintassi SQL di accelerazione query.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: ereilebr
ms.openlocfilehash: affddf7367f58107106ae07a07b8baedac73e251
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89659557"
---
# <a name="query-acceleration-sql-language-reference"></a>Informazioni di riferimento sul linguaggio SQL di accelerazione query

L'accelerazione delle query supporta un linguaggio simile a SQL ANSI per l'espressione di query sul contenuto del BLOB.  Il sottolinguaggio SQL per l'accelerazione delle query è un subset di ANSI SQL con un set limitato di tipi di dati supportati, operatori e così via, ma si espande anche in ANSI SQL per supportare le query su formati di dati semistrutturati gerarchici come JSON. 

## <a name="select-syntax"></a>Seleziona sintassi

L'unica istruzione SQL supportata da accelerazione query è l'istruzione SELECT. In questo esempio viene restituita ogni riga per la quale Expression restituisce true.

```sql
SELECT * FROM table [WHERE expression] [LIMIT limit]
```

Per i dati in formato CSV, la *tabella* deve essere `BlobStorage` .  Ciò significa che la query verrà eseguita in base a qualsiasi blob specificato nella chiamata REST.
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

<a id="data-types"></a>

## <a name="data-types"></a>Tipi di dati

|Tipo di dati|Descrizione|
|---------|-------------------------------------------|
|INT      |Intero con segno a 64 bit.                     |
|FLOAT    |virgola mobile a 64 bit ("doppia precisione").|
|STRING   |Stringa Unicode a lunghezza variabile.            |
|timestamp|Un momento specifico.                           |
|BOOLEAN  |True o false.                             |

Durante la lettura di valori da dati in formato CSV, tutti i valori vengono letti come stringhe.  I valori stringa possono essere convertiti in altri tipi usando le espressioni CAST.  È possibile eseguire il cast implicito dei valori ad altri tipi a seconda del contesto. per altre informazioni, vedere [precedenza dei tipi di dati (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql).

## <a name="expressions"></a>Espressioni

### <a name="referencing-fields"></a>Campi di riferimento

Per i dati in formato JSON o i dati in formato CSV con una riga di intestazione, è possibile fare riferimento ai campi in base al nome.  I nomi dei campi possono essere racchiusi tra virgolette o senza virgolette. I nomi di campo tra virgolette sono racchiusi tra virgolette doppie ("), possono contenere spazi e fanno distinzione tra maiuscole e minuscole.  I nomi di campo non racchiusi tra virgolette non fanno distinzione tra maiuscole e minuscole e non possono contenere caratteri speciali.

Nei dati in formato CSV, è possibile fare riferimento ai campi anche in base all'ordinale, preceduto da un carattere di sottolineatura (_).  Ad esempio, è possibile fare riferimento al primo campo come _1 oppure fare riferimento all'undicesimo campo come _11.  Il riferimento ai campi per ordinale è utile per i dati in formato CSV che non contengono una riga di intestazione, nel qual caso l'unico modo per fare riferimento a un campo specifico è in base al numero ordinale.

### <a name="operators"></a>Operatori

Sono supportati gli operatori SQL standard seguenti:

|Operatore|Descrizione|
|--|--|
|[=](https://docs.microsoft.com/sql/t-sql/language-elements/equals-transact-sql)    |Esegue un confronto per determinare se due espressioni sono uguali (operatore di confronto).|
|[!=](https://docs.microsoft.com/sql/t-sql/language-elements/not-equal-to-transact-sql-exclamation)    |Verifica se un'espressione è diversa da un'altra espressione (operatore di confronto).|
|[<>](https://docs.microsoft.com/sql/t-sql/language-elements/not-equal-to-transact-sql-traditional)    |Confronta due espressioni non uguali a (operatore di confronto).|
|[<](https://docs.microsoft.com/sql/t-sql/language-elements/less-than-transact-sql)    |Confronta due espressioni per un valore minore di (operatore di confronto).|
|[<=](https://docs.microsoft.com/sql/t-sql/language-elements/less-than-or-equal-to-transact-sql)    |Confronta due espressioni per un valore minore o uguale a (operatore di confronto).|
|[>](https://docs.microsoft.com/sql/t-sql/language-elements/greater-than-transact-sql)    |Confronta due espressioni per maggiore di (un operatore di confronto). |
|[>=](https://docs.microsoft.com/sql/t-sql/language-elements/greater-than-or-equal-to-transact-sql)    |Confronta due espressioni e verifica la presenza di valori maggiori o uguali (operatore di confronto).|
|[+](https://docs.microsoft.com/sql/t-sql/language-elements/add-transact-sql)    |Esegue la somma di due numeri. Questo operatore aritmetico di addizione consente inoltre di aggiungere un numero, espresso in giorni, a una data.|
|[-](https://docs.microsoft.com/sql/t-sql/language-elements/subtract-transact-sql)    |Sottrae due numeri (operatore aritmetico di sottrazione). |
|[/](https://docs.microsoft.com/sql/t-sql/language-elements/divide-transact-sql)    |Divide un numero per un altro (operatore aritmetico di divisione).|
|[*](https://docs.microsoft.com/sql/t-sql/language-elements/multiply-transact-sql)    |Moltiplica due espressioni (operatore aritmetico di moltiplicazione).|
|[%](https://docs.microsoft.com/sql/t-sql/language-elements/modulo-transact-sql)    |Viene restituito il resto di una divisione.|
|[AND](https://docs.microsoft.com/sql/t-sql/language-elements/bitwise-and-transact-sql)    |Esegue un'operazione con AND logico bit per bit tra due valori integer.|
|[OR](https://docs.microsoft.com/sql/t-sql/language-elements/bitwise-or-transact-sql)    |Esegue un'operazione con OR logico bit per bit tra due valori integer specificati convertiti in espressioni binarie in istruzioni Transact-SQL.|
|[NOT](https://docs.microsoft.com/sql/t-sql/language-elements/not-transact-sql)    |Nega un input booleano.|
|[CAST](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql)    |Consente di convertire un'espressione da un tipo di dati a un altro.|
|[BETWEEN](https://docs.microsoft.com/sql/t-sql/language-elements/between-transact-sql)    |Specifica un intervallo da testare.|
|[IN](https://docs.microsoft.com/sql/t-sql/language-elements/in-transact-sql)    |Determina se il valore specificato corrisponde a un valore in una sottoquery o in un elenco.|
|[NULLIF](https://docs.microsoft.com/sql/t-sql/language-elements/nullif-transact-sql)    |Restituisce un valore Null se le due espressioni specificate sono uguali.|
|[COALESCE](https://docs.microsoft.com/sql/t-sql/language-elements/coalesce-transact-sql)    |Valuta gli argomenti nell'ordine e restituisce il valore corrente della prima espressione che inizialmente non restituisce NULL.|

Se i tipi di dati a sinistra e a destra di un operatore sono diversi, la conversione automatica verrà eseguita in base alle regole specificate qui: [precedenza dei tipi di dati (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql).

Il linguaggio SQL di accelerazione query supporta solo un subset molto piccolo dei tipi di dati descritti in questo articolo.  Vedere la sezione [tipi di dati](#data-types) di questo articolo.

### <a name="casts"></a>Cast

Il linguaggio SQL di accelerazione query supporta l'operatore CAST, in base alle regole seguenti: [conversione del tipo di dati (motore di database)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-conversion-database-engine).  

Il linguaggio SQL di accelerazione query supporta solo un piccolo subset dei tipi di dati descritti in questo articolo.  Vedere la sezione [tipi di dati](#data-types) di questo articolo.

### <a name="string-functions"></a>Funzioni per i valori stringa

Il linguaggio SQL per l'accelerazione delle query supporta le seguenti funzioni di stringa SQL standard:

|Funzione|Descrizione|
|--|--|
|CHAR_LENGTH    | Restituisce la lunghezza in caratteri dell'espressione stringa, se l'espressione stringa è di un tipo di dati character. in caso contrario, restituisce la lunghezza in byte dell'espressione stringa (il valore integer più piccolo non inferiore al numero di bit diviso per 8). Questa funzione corrisponde alla funzione CHARACTER_LENGTH.|
|CHARACTER_LENGTH    |Restituisce la lunghezza in caratteri dell'espressione stringa, se l'espressione stringa è di un tipo di dati character. in caso contrario, restituisce la lunghezza in byte dell'espressione stringa (il valore integer più piccolo non inferiore al numero di bit diviso per 8). Questa funzione corrisponde alla funzione CHAR_LENGTH|
|[LOWER](https://docs.microsoft.com/sql/t-sql/functions/lower-transact-sql)    |Restituisce un'espressione di caratteri dopo aver convertito i caratteri maiuscoli in caratteri minuscoli.|
|[UPPER](https://docs.microsoft.com/sql/t-sql/functions/upper-transact-sql)    |Restituisce un'espressione di caratteri con dati di tipo carattere minuscoli convertiti in maiuscolo.|
|[SUBSTRING](https://docs.microsoft.com/sql/t-sql/functions/substring-transact-sql)    |Restituisce parte di un'espressione di tipo carattere, binario, testo o immagine in SQL Server.|
|[TRIM](https://docs.microsoft.com/sql/t-sql/functions/trim-transact-sql)    |Rimuove il carattere spazio (32) o altri caratteri specificati dall'inizio e dalla fine di una stringa.|
|LEADING    |Descrizione|
|TRAILING    |Descrizione|

Ecco alcuni esempi:

|Funzione|Esempio|Risultato|
|---|---|---|
|CHARACTER_LENGTH|``SELECT CHARACTER_LENGTH('abcdefg') from BlobStorage`` |``7``|
|CHAR_LENGTH|``SELECT CHAR_LENGTH(_1) from BlobStorage``|``1``|
|LOWER|``SELECT LOWER('AbCdEfG') from BlobStorage``|``abcdefg``|
|UPPER|``SELECT UPPER('AbCdEfG') from BlobStorage``|``ABCDEFG``|
|SUBSTRING|``SUBSTRING('123456789', 1, 5)``|``23456``|
|TRIM|``TRIM(BOTH '123' FROM '1112211Microsoft22211122')``|``Microsoft``|

### <a name="date-functions"></a>Funzioni di data

Sono supportate le funzioni di data SQL standard seguenti:

``DATE_ADD``, ``DATE_DIFF``, ``EXTRACT``, ``TO_STRING``, ``TO_TIMESTAMP``.

Attualmente si convertono tutti i [formati di data di IS08601 standard](https://www.w3.org/TR/NOTE-datetime). 

#### <a name="date_add-function"></a>Funzione DATE_ADD

Il linguaggio SQL per l'accelerazione delle query supporta anno, mese, giorno, ora, minuto e secondo per la ``DATE_ADD`` funzione.

Esempi:

'' SQL DATE_ADD (DatePart, Quantity, timestamp) DATE_ADD (' minute ', 1, CAST (' 2017-01-02T03:04:05.006 Z ' come TIMESTAMP)
```

#### DATE_DIFF function

The query acceleration SQL language supports year, month, day, hour, minute, second for the ``DATE_DIFF`` function.

```sql
DATE_DIFF(datepart, timestamp, timestamp)
DATE_DIFF('hour','2018-11-09T00:00+05:30','2018-11-09T01:00:23-08:00') 
```

#### <a name="extract-function"></a>ESTRAi funzione

Per ESTRAi diversi dalla parte della data supportata per la ``DATE_ADD`` funzione, il linguaggio SQL di accelerazione query supporta timezone_hour e timezone_minute come parte della data.

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

In questa tabella vengono descritte le stringhe che è possibile utilizzare per specificare il formato di output della ``TO_STRING`` funzione.

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
|[CONTEGGIO ( \* )](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql)    |Restituisce il numero di record che corrispondono all'espressione del predicato.|
|[CONTEGGIO (espressione)](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql)    |Restituisce il numero di record per i quali Expression è diverso da null.|
|[MEDIA (espressione)](https://docs.microsoft.com/sql/t-sql/functions/avg-transact-sql)    |Restituisce la media dei valori non null dell'espressione.|
|[MIN (espressione)](https://docs.microsoft.com/sql/t-sql/functions/min-transact-sql)    |Restituisce il valore minimo non null dell'espressione.|
|[MAX (espressione](https://docs.microsoft.com/sql/t-sql/functions/max-transact-sql)    |Restituisce il valore massimo non null dell'espressione.|
|[SUM (espressione)](https://docs.microsoft.com/sql/t-sql/functions/sum-transact-sql)    |Restituisce la somma di tutti i valori non null dell'espressione.|

### <a name="missing"></a>MANCANTE

L' ``IS MISSING`` operatore è l'unico non standard supportato dal linguaggio SQL per l'accelerazione delle query.  Per i dati JSON, se un campo non è presente in un particolare record di input, il campo dell'espressione ``IS MISSING`` restituirà il valore booleano true.

<a id="table-descriptors"></a>

## <a name="table-descriptors"></a>Descrittori di tabella

Per i dati CSV, il nome della tabella è sempre `BlobStorage` .  Ad esempio:

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

Se si desidera accedere solo al `dimensions` valore dell'oggetto JSON, è possibile utilizzare fare riferimento a tale oggetto nella query. Ad esempio:

```sql
SELECT length FROM BlobStorage[*].dimensions
```

Questo limita anche l'accesso ai membri dell' `dimensions` oggetto. Se si vuole accedere ad altri membri dei campi JSON e dei valori interni degli oggetti JSON, è possibile usare una query simile a quella illustrata nell'esempio seguente:

```sql
SELECT weight,warehouses[0].longitude,id,tags[1] FROM BlobStorage[*]
```

> [!NOTE]
> BlobStorage e BlobStorage [ \* ] fanno entrambi riferimento all'intero oggetto. Tuttavia, se si ha un percorso nella clausola FROM, è necessario usare BlobStorage [ \* ]. Path

<a id="sys-split"></a>

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

## <a name="see-also"></a>Vedi anche

- [Accelerazione query Azure Data Lake Storage](data-lake-storage-query-acceleration.md)
- [Filtrare i dati tramite Azure Data Lake Storage accelerazione query](data-lake-storage-query-acceleration-how-to.md)

