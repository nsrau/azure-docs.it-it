---
title: Date_Bucket (Transact-SQL) - SQL Edge di Azure (anteprima)
description: Informazioni sull'uso di Date_Bucket in SQL Edge di Azure (anteprima)
keywords: Date_Bucket, SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: c2f63abeb9f935236b4c35decb278eb86e0e2a82
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/31/2020
ms.locfileid: "84233304"
---
# <a name="date_bucket-transact-sql"></a>Date_Bucket (Transact-SQL)

Questa funzione restituisce il valore datetime corrispondente all'inizio di ogni bucket datetime, dal valore di origine predefinito di `1900-01-01 00:00:00.000`.

Vedere [Funzioni e tipi di dati di data e ora &#40;Transact-SQL&#41;](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql/) per una panoramica di tutte le funzioni e i tipi di dati di data e ora Transact-SQL.

[Convenzioni della sintassi Transact-SQL](/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql/)

`DATE_BUCKET` usa il valore di data di origine predefinito `1900-01-01 00:00:00.000`, ovvero ore 12:00 di lunedì 1 gennaio 1900.

## <a name="syntax"></a>Sintassi

```sql
DATE_BUCKET (datePart, number, date)
```

## <a name="arguments"></a>Argomenti

*datePart*

Parte di *date* usata con il parametro 'number'. Ex. Anno, mese, minuto, secondo e così via.

> [!NOTE]
> `DATE_BUCKET` non accetta equivalenti di variabili definite dall'utente come argomenti di *datePart*.
  
|*datePart*|Abbreviazioni|  
|---|---|
|**day**|**dd**, **d**|  
|**week**|**wk**, **ww**|  
|**hour**|**hh**|  
|**minute**|**mi**, **n**|  
|**second**|**ss**, **s**|  
|**millisecond**|**ms**|  

*number*

Numero intero che decide la larghezza del bucket combinato con l'argomento *datePart*. Rappresenta la larghezza dei bucket di dataPart dall'ora di origine. **`This argument cannot be a negative integer value`** . 

*date*

Espressione che può risolversi in uno dei valori seguenti:

+ **date**
+ **datetime**
+ **datetimeoffset**
+ **datetime2**
+ **smalldatetime**
+ **time**

Per *date*, `DATE_BUCKET` accetterà un'espressione di colonna, un'espressione o una variabile definita dall'utente se si risolve in uno dei tipi di dati indicati in precedenza.

## <a name="return-type"></a>Tipo restituito

Il tipo di dati del valore restituito per questo metodo è dinamico. Il tipo restituito dipende dall'argomento specificato per `date`. Se viene fornito un tipo di dati di input valido per `date`, `DATE_BUCKET` restituisce lo stesso tipo di dati. `DATE_BUCKET` genera un errore se viene specificato un valore letterale stringa per il parametro `date`.

## <a name="return-values"></a>Valori restituiti

### <a name="understanding-the-output-from-date_bucket"></a>Informazioni sull'output da `DATE_BUCKET`

`Date_Bucket` restituisce il valore di data o ora più recente, corrispondente al parametro datePart e number. Nelle espressioni riportate di seguito, ad esempio, `Date_Bucket` restituirà il valore di output di `2020-04-13 00:00:00.0000000`, poiché l'output viene calcolato in base a bucket di una settimana dalla data/ora di origine predefinita `1900-01-01 00:00:00.000`. Il valore `2020-04-13 00:00:00.0000000` è successivo di 6276 settimane dal valore di origine `1900-01-01 00:00:00.000`. 

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 1, @date)
```

Per tutte le espressioni riportate di seguito, verrà restituito lo stesso valore di output `2020-04-13 00:00:00.0000000`. Questo perché il valore `2020-04-13 00:00:00.0000000` è successivo di 6276 settimane dalla data di origine e 6276 è divisibile per 2, 3, 4 e 6.

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 2, @date)
Select DATE_BUCKET(wk, 3, @date)
Select DATE_BUCKET(wk, 4, @date)
Select DATE_BUCKET(wk, 6, @date)
```

Output per l'espressione seguente, corrispondente a 6275 settimane dall'ora di origine.

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 5, @date)
```

## <a name="datepart-argument"></a>Argomento datepart

**dayofyear**, **day**, e **weekday** restituiscono lo stesso valore. Ogni elemento *datepart* e le relative abbreviazioni restituiscono lo stesso valore.
  
## <a name="number-argument"></a>Argomento number

L'argomento *number* non può superare l'intervallo di valori **int** positivi. Nelle istruzioni seguenti l'argomento per il parametro *number* supera l'intervallo di **int** di una unità. L'istruzione seguente restituisce il messaggio di errore seguente: "`Msg 8115, Level 16, State 2, Line 2. Arithmetic overflow error converting expression to data type int."`
  
```sql
declare @date datetime2 = '2020-04-30 00:00:00'
Select DATE_BUCKET(dd, 2147483648, @date)
```  

Se alla funzione `Date_Bucket` viene passato un valore negativo per number, viene restituito l'errore seguente. 

```sql
Msg 9834, Level 16, State 1, Line 1
Invalid bucket width value passed to date_bucket function. Only positive values are allowed.
````

## <a name="date-argument"></a>Argomento date  

`DATE_BUCKET` restituisce il valore di base corrispondente al tipo di dati dell'argomento `date`. Nell'esempio seguente viene restituito un valore di output con tipo di dati datetime2. 

```sql
Select DATE_BUCKET(dd, 10, SYSUTCDATETIME())
```

## <a name="remarks"></a>Osservazioni

Usare `DATE_BUCKET` nelle clausole seguenti:

+ GROUP BY
+ HAVING
+ ORDER BY
+ SELECT \<list>
+ WHERE

## <a name="examples"></a>Esempi

### <a name="a-calculating-date_bucket-with-a-bucket-width-of-1-from-the-origin-time"></a>R. Calcolo di Date_Bucket con una larghezza del bucket pari a 1 dall'ora di origine

Ognuna di queste istruzioni incrementa *date_bucket* con una larghezza del bucket di 1 dall'ora di origine:

```sql
declare @date datetime2 = '2020-04-30 21:21:21'
Select 'Week',  DATE_BUCKET(wk, 1, @date)
Union All
Select 'Day',  DATE_BUCKET(dd, 1, @date)
Union All
Select 'Hour',  DATE_BUCKET(hh, 1, @date)
Union All
Select 'Minutes',  DATE_BUCKET(mi, 1, @date)
Union All
Select 'Seconds',  DATE_BUCKET(ss, 1, @date)
```

Questo è il set di risultati.

```sql
Week    2020-04-27 00:00:00.0000000
Day     2020-04-30 00:00:00.0000000
Hour    2020-04-30 21:00:00.0000000
Minutes 2020-04-30 21:21:00.0000000
Seconds 2020-04-30 21:21:21.0000000
```

### <a name="b-using-expressions-as-arguments-for-the-number-and-date-parameters"></a>B. Utilizzo di espressioni come argomenti per i parametri number e date

In questi esempi vengono usati tipi diversi di espressioni come argomenti per i parametri *number* e *date*. Questi esempi vengono creati usando il database 'AdventureWorksDW2017'.
  
#### <a name="specifying-user-defined-variables-as-number-and-date"></a>Indicazione di variabili definite dall'utente come argomenti number e date  

In questo esempio vengono specificate variabili definite dall'utente come argomenti per i parametri *number* e *date*:
  
```sql
DECLARE @days int = 365,
        @datetime datetime2 = '2000-01-01 01:01:01.1110000'; /* 2000 was a leap year */;  
SELECT Date_Bucket(day, @days, @datetime);
```

Questo è il set di risultati.

```sql
---------------------------
1999-12-08 00:00:00.0000000

(1 row affected)
```  

#### <a name="specifying-a-column-as-date"></a>Indicazione di una colonna come data

Nell'esempio seguente viene calcolata la somma di OrderQuantity e la somma di UnitPrice con raggruppamento in base a bucket di data settimanali.
  
```sql
SELECT
    Date_Bucket(week, 1 ,cast(Shipdate as datetime2)) AS ShippedDateBucket
    ,Sum(OrderQuantity)  As SumOrderQuantity
    ,Sum(UnitPrice) As SumUnitPrice
FROM dbo.FactInternetSales FIS
where Shipdate between '2011-01-03 00:00:00.000' and '2011-02-28 00:00:00.000'
Group by Date_Bucket(week, 1 ,cast(Shipdate as datetime2))
order by 1
```  

Questo è il set di risultati.
  
```sql
ShippedDateBucket           SumOrderQuantity SumUnitPrice
--------------------------- ---------------- ---------------------
2011-01-03 00:00:00.0000000 21               65589.7546
2011-01-10 00:00:00.0000000 27               89938.5464
2011-01-17 00:00:00.0000000 31               104404.9064
2011-01-24 00:00:00.0000000 36               118525.6846
2011-01-31 00:00:00.0000000 39               123555.431
2011-02-07 00:00:00.0000000 35               109342.351
2011-02-14 00:00:00.0000000 32               107804.8964
2011-02-21 00:00:00.0000000 37               119456.3428
2011-02-28 00:00:00.0000000 9                28968.6982
```  

#### <a name="specifying-scalar-system-function-as-date"></a>Indicazione di una funzione di sistema scalare come valore date

Questo esempio specifica `SYSDATETIME` per *date*. Il valore esatto restituito dipende dal giorno e dall'ora di esecuzione dell'istruzione:
  
```sql
SELECT Date_Bucket(wk, 10, SYSDATETIME());  
```  

Questo è il set di risultati.

```sql
---------------------------
2020-03-02 00:00:00.0000000

(1 row affected)
```  

#### <a name="specifying-scalar-subqueries-and-scalar-functions-as-number-and-date"></a>Indicazione di sottoquery scalari e funzioni scalari come valori number e date

In questo esempio vengono usate sottoquery scalari, `MAX(OrderDate)`, come argomenti per *number* e *date*. `(SELECT top 1 CustomerKey FROM dbo.DimCustomer where GeographyKey > 100)` funge da argomento fittizio perché il parametro number illustri come selezionare un argomento *number* da un elenco di valori.
  
```sql
SELECT DATE_BUCKET(week,(SELECT top 1 CustomerKey FROM dbo.DimCustomer where GeographyKey > 100),  
    (SELECT MAX(OrderDate) FROM dbo.FactInternetSales));  
```  
  
#### <a name="specifying-numeric-expressions-and-scalar-system-functions-as-number-and-date"></a>Indicazione di espressioni numeriche e funzioni di sistema scalari come valori number e date

Questo esempio usa un'espressione numerica, ovvero (10/2), e funzioni di sistema scalari (SYSDATETIME) come argomenti per numero e data.
  
```sql
SELECT Date_Bucket(week,(10/2), SYSDATETIME());
```

#### <a name="specifying-an-aggregate-window-function-as-number"></a>Indicazione di una funzione finestra di aggregazione come argomento number

Questo esempio usa una funzione finestra di aggregazione come argomento per un parametro *number*.
  
```sql
Select 
    DISTINCT DATE_BUCKET(day, 30, Cast([shipdate] as datetime2)) as DateBucket,
    First_Value([SalesOrderNumber]) OVER (Order by DATE_BUCKET(day, 30, Cast([shipdate] as datetime2))) as First_Value_In_Bucket,
    Last_Value([SalesOrderNumber]) OVER (Order by DATE_BUCKET(day, 30, Cast([shipdate] as datetime2))) as Last_Value_In_Bucket
    from [dbo].[FactInternetSales]
Where ShipDate between '2011-01-03 00:00:00.000' and '2011-02-28 00:00:00.000'
order by DateBucket
GO  
``` 

## <a name="see-also"></a>Vedere anche

[CAST e CONVERT &#40;Transact-SQL&#41;](/sql/t-sql/functions/cast-and-convert-transact-sql/)
