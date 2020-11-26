---
title: Colmare i gap temporali e imputare i valori mancanti-Azure SQL Edge
description: Informazioni sugli intervalli di tempo di riempimento e sull'imputazione dei valori mancanti in Azure SQL Edge
keywords: SQL Edge, timeseries
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: c444732a497d595235ac337d7f5c71fb84f17cca
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185590"
---
# <a name="filling-time-gaps-and-imputing-missing-values"></a>Colmare i gap temporali e imputare i valori mancanti 

Quando si gestiscono i dati delle serie temporali, è spesso possibile che nei dati delle serie temporali siano presenti valori mancanti per gli attributi. È anche possibile che, a causa della natura dei dati, o a causa di interruzioni nella raccolta dei dati, siano presenti *Gap* temporali nel set di dati.

Ad esempio, quando si raccolgono statistiche sull'utilizzo energetico per un Smart Device, ogni volta che il dispositivo non è operativo, le statistiche di utilizzo comporteranno Gap. Analogamente, in uno scenario di raccolta dei dati di telemetria del computer, è possibile che i diversi sensori siano configurati per emettere dati a frequenze diverse, ottenendo valori mancanti per i sensori. Se, ad esempio, sono presenti due sensori, tensione e pressione, configurati rispettivamente a 100 Hz e frequenza di 10 Hz, il sensore di tensione emetterà i dati ogni centesimo di secondo, mentre il sensore di pressione emetterà i dati solo ogni decimo di secondo.

La tabella seguente descrive un set di dati di telemetria del computer, che è stato raccolto in un intervallo di un secondo. 

```
timestamp               VoltageReading  PressureReading
----------------------- --------------- ----------------
2020-09-07 06:14:41.000 164.990400      97.223600
2020-09-07 06:14:42.000 162.241300      93.992800
2020-09-07 06:14:43.000 163.271200      NULL
2020-09-07 06:14:44.000 161.368100      93.403700
2020-09-07 06:14:45.000 NULL            NULL
2020-09-07 06:14:46.000 NULL            98.364800
2020-09-07 06:14:49.000 NULL            94.098300
2020-09-07 06:14:51.000 157.695700      103.359100
2020-09-07 06:14:52.000 157.019200      NULL
2020-09-07 06:14:54.000 NULL            95.352000
2020-09-07 06:14:56.000 159.183500      100.748200

```

Esistono due importanti caratteristiche del set di dati precedente. 

- Il set di dati non contiene punti dati correlati a diversi timestamp `2020-09-07 06:14:47.000` ,,, `2020-09-07 06:14:48.000` `2020-09-07 06:14:50.000` `2020-09-07 06:14:53.000` e `2020-09-07 06:14:55.000` . Questi timestamp sono *Gap* nel set di dati.  
- Sono presenti valori mancanti, rappresentati come `null` , per le letture di tensione e pressione. 

## <a name="gap-filling"></a>Riempimento Gap 

Il riempimento Gap è una tecnica che consente di creare set di timestamp contigui e ordinati per semplificare l'analisi dei dati delle serie temporali. In Azure SQL Edge, il modo più semplice per colmare gap nel set di dati di serie temporali consiste nel definire una tabella temporanea con la distribuzione temporale desiderata, quindi eseguire un' `LEFT OUTER JOIN` `RIGHT OUTER JOIN` operazione o nella tabella del set di dati. 

Prendendo i `MachineTelemetry` dati rappresentati sopra come esempio, è possibile usare la query seguente per generare set di timestamp contigui e ordinati per l'analisi. 

> [!NOTE]
> La query seguente genera le righe mancanti, con i valori di timestamp e i `null` valori per gli attributi. 

```sql
Create Table #SeriesGenerate(dt datetime Primary key Clustered)
GO

Declare @startdate datetime = '2020-09-07 06:14:41.000', @endtime datetime = '2020-09-07 06:14:56.000'
While (@startdate <= @endtime)
BEGIN
Insert into #SeriesGenerate values (@startdate)
set @startdate = DATEADD(SECOND, 1, @startdate)
END

Select a.dt as timestamp, b.VoltageReading, b.PressureReading 
From 
#SeriesGenerate a LEFT OUTER JOIN MachineTelemetry b 
    on a.dt = b.[timestamp]
```
La query precedente produce l'output seguente che contiene tutti i timestamp di *un secondo* nell'intervallo specificato.

Set di risultati

```

timestamp               VoltageReading    PressureReading
----------------------- ----------------- ----------------
2020-09-07 06:14:41.000 164.990400        97.223600
2020-09-07 06:14:42.000 162.241300        93.992800
2020-09-07 06:14:43.000 163.271200        NULL
2020-09-07 06:14:44.000 161.368100        93.403700
2020-09-07 06:14:45.000 NULL              NULL
2020-09-07 06:14:46.000 NULL              98.364800
2020-09-07 06:14:47.000 NULL              NULL
2020-09-07 06:14:48.000 NULL              NULL
2020-09-07 06:14:49.000 NULL              94.098300
2020-09-07 06:14:50.000 NULL              NULL
2020-09-07 06:14:51.000 157.695700        103.359100
2020-09-07 06:14:52.000 157.019200        NULL
2020-09-07 06:14:53.000 NULL              NULL
2020-09-07 06:14:54.000 NULL              95.352000
2020-09-07 06:14:55.000 NULL              NULL
2020-09-07 06:14:56.000 159.183500        100.748200
```

## <a name="imputing-missing-values"></a>Inserimento di valori mancanti

La query precedente ha generato i timestamp mancanti per l'analisi dei dati, ma non ha sostituito alcuno dei valori mancanti (rappresentati come null) per le `voltage` `pressure` letture e. In Azure SQL Edge è stata aggiunta una nuova sintassi a T-SQL `LAST_VALUE()` e `FIRST_VALUE()` funzioni, che forniscono i meccanismi per imputare i valori mancanti, in base ai valori precedenti o successivi del set di dati. 

La nuova sintassi aggiunge `IGNORE NULLS` la `RESPECT NULLS` clausola and alle `LAST_VALUE()` `FIRST_VALUE()` funzioni e. Una query seguente sul `MachineTelemetry` set di dati calcola i valori mancanti utilizzando la funzione LAST_VALUE, in cui i valori mancanti vengono sostituiti con l'ultimo valore osservato nel set di dati.

```sql
Select 
    timestamp,
    VoltageReading As OriginalVoltageValues,
    LAST_VALUE(VoltageReading) IGNORE NULLS OVER (ORDER BY timestamp) As ImputedUsingLastValue, 
    PressureReading As OriginalPressureValues,
    LAST_VALUE(PressureReading) IGNORE NULLS OVER (ORDER BY timestamp) As ImputedUsingLastValue
From 
MachineTelemetry 
order by timestamp 
```
Set di risultati

```

timestamp               OrigVoltageVals  ImputedVoltage OrigPressureVals  ImputedPressure
----------------------- ---------------- -------------- ----------------- ----------------
2020-09-07 06:14:41.000 164.990400       164.990400     97.223600         97.223600
2020-09-07 06:14:42.000 162.241300       162.241300     93.992800         93.992800
2020-09-07 06:14:43.000 163.271200       163.271200     NULL              93.992800
2020-09-07 06:14:44.000 161.368100       161.368100     93.403700         93.403700
2020-09-07 06:14:45.000 NULL             161.368100     NULL              93.403700
2020-09-07 06:14:46.000 NULL             161.368100     98.364800         98.364800
2020-09-07 06:14:49.000 NULL             161.368100     94.098300         94.098300
2020-09-07 06:14:51.000 157.695700       157.695700     103.359100        103.359100
2020-09-07 06:14:52.000 157.019200       157.019200     NULL              103.359100
2020-09-07 06:14:54.000 NULL             157.019200     95.352000         95.352000
2020-09-07 06:14:56.000 159.183500       159.183500     100.748200        100.748200

```

Nella query seguente vengono importati i valori mancanti utilizzando sia la `LAST_VALUE()` funzione che la `FIRST_VALUE` funzione. Per, la colonna di output `ImputedVoltage` i valori mancanti vengono sostituiti dall'ultimo valore osservato, mentre per la colonna di output `ImputedPressure` i valori mancanti vengono sostituiti dal valore osservato successivo nel set di dati. 

```sql
Select 
    dt as timestamp, 
    VoltageReading As OrigVoltageVals,
    LAST_VALUE(VoltageReading) IGNORE NULLS OVER (ORDER BY dt) As ImputedVoltage, 
    PressureReading As OrigPressureVals,
    First_VALUE(PressureReading) IGNORE NULLS OVER (ORDER BY dt ROWS 
                    BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING) As ImputedPressure
From 
(Select a.dt, b.VoltageReading,b.PressureReading  from 
    #SeriesGenerate a 
        LEFT OUTER JOIN 
    MachineTelemetry b 
        on a.dt = b.[timestamp]) A
order by timestamp
```
Set di risultati

```

timestamp               OrigVoltageVals  ImputedVoltage  OrigPressureVals  ImputedPressure
----------------------- ---------------- --------------- ----------------- ---------------
2020-09-07 06:14:41.000 164.990400       164.990400      97.223600         97.223600
2020-09-07 06:14:42.000 162.241300       162.241300      93.992800         93.992800
2020-09-07 06:14:43.000 163.271200       163.271200      NULL              93.403700
2020-09-07 06:14:44.000 161.368100       161.368100      93.403700         93.403700
2020-09-07 06:14:45.000 NULL             161.368100      NULL              98.364800
2020-09-07 06:14:46.000 NULL             161.368100      98.364800         98.364800
2020-09-07 06:14:47.000 NULL             161.368100      NULL              94.098300
2020-09-07 06:14:48.000 NULL             161.368100      NULL              94.098300
2020-09-07 06:14:49.000 NULL             161.368100      94.098300         94.098300
2020-09-07 06:14:50.000 NULL             161.368100      NULL              103.359100
2020-09-07 06:14:51.000 157.695700       157.695700      103.359100        103.359100
2020-09-07 06:14:52.000 157.019200       157.019200      NULL              95.352000
2020-09-07 06:14:53.000 NULL             157.019200      NULL              95.352000
2020-09-07 06:14:54.000 NULL             157.019200      95.352000         95.352000
2020-09-07 06:14:55.000 NULL             157.019200      NULL              100.748200
2020-09-07 06:14:56.000 159.183500       159.183500      100.748200        100.748200
```

> [!NOTE]
> La query precedente usa la `FIRST_VALUE()` funzione per sostituire i valori mancanti con il valore osservato successivo. Lo stesso risultato può essere ottenuto utilizzando la `LAST_VALUE()` funzione con una `ORDER BY <ordering_column> DESC` clausola.

## <a name="next-steps"></a>Passaggi successivi 

- [FIRST_VALUE (Transact-SQL)](/sql/t-sql/functions/first-value-transact-sql?toc=%2fazure%2fazure-sql-edge%2ftoc.json)
- [LAST_VALUE (Transact-SQL)](/sql/t-sql/functions/last-value-transact-sql?toc=%2fazure%2fazure-sql-edge%2ftoc.json)
- [DATE_BUCKET (Transact-SQL)](date-bucket-tsql.md)
- [Funzioni di aggregazione (Transact-SQL)](/sql/t-sql/functions/aggregate-functions-transact-sql)