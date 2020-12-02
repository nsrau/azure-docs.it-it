---
title: Guida alla programmabilità di U-SQL per Azure Data Lake
description: Informazioni sulla Panoramica di U-SQL e la programmabilità UDF in Azure Data Lake Analytics per consentire la creazione di uno script USQL valido.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: d263f2616607a96a8aa14f1ad1c06330b1b44644
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510863"
---
# <a name="u-sql-programmability-guide-overview"></a>Panoramica della Guida di programmabilità di U-SQL

U-SQL è un linguaggio di query progettato per carichi di lavoro di tipo Big Data. Una delle caratteristiche esclusive di U-SQL è la combinazione tra linguaggio dichiarativo di tipo SQL e funzionalità di estendibilità e programmabilità del linguaggio C#. Questa guida è incentrata sulle funzionalità di estendibilità e programmabilità del linguaggio U-SQL supportate da C#.

## <a name="requirements"></a>Requisiti

Scaricare e installare [Strumenti Azure Data Lake per Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="get-started-with-u-sql"></a>Introduzione a U-SQL  

Si consideri lo script U-SQL seguente:

```usql
@a  = 
  SELECT * FROM 
    (VALUES
       ("Contoso",   1500.0, "2017-03-39"),
       ("Woodgrove", 2700.0, "2017-04-10")
    ) AS D( customer, amount, date );

@results =
  SELECT
    customer,
    amount,
    date
  FROM @a;    
```

Lo script definisce due set di righe: `@a` e `@results`. La definizione del set di righe `@results` deriva da `@a`.

## <a name="c-types-and-expressions-in-u-sql-script"></a>Tipi ed espressioni C# in uno script U-SQL

Un'espressione U-SQL è un'espressione C# combinata con operazioni logiche U-SQL quali `AND`, `OR` e `NOT`. Le espressioni U-SQL possono essere utilizzate con l'istruzione SELECT, EXTRACT, WHERE, HAVING, GROUP BY e DECLARE. Lo script seguente, ad esempio, analizza una stringa come valore DateTime.

```usql
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

Il frammento di codice seguente analizza una stringa come valore DateTime in un'istruzione DECLARE.

```usql
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>Usare espressioni C# per conversioni del tipo di dati

L'esempio seguente illustra come effettuare una conversione di dati di tipo datetime usando espressioni C#. In questo particolare scenario, i dati della stringa datetime vengono convertiti in datetime standard con la notazione dell'ora 00:00:00.

```usql
DECLARE @dt = "2016-07-06 10:23:15";

@rs1 =
  SELECT 
    Convert.ToDateTime(Convert.ToDateTime(@dt).ToString("yyyy-MM-dd")) AS dt,
    dt AS olddt
  FROM @rs0;

OUTPUT @rs1 
  TO @output_file 
  USING Outputters.Text();
```

### <a name="use-c-expressions-for-todays-date"></a>Usare espressioni C# per la data odierna

Per eseguire il pull della data odierna, è possibile usare l'espressione C# seguente: `DateTime.Now.ToString("M/d/yyyy")`

Di seguito è riportato un esempio di come usare questa espressione in uno script:

```usql
@rs1 =
  SELECT
    MAX(guid) AS start_id,
    MIN(dt) AS start_time,
    MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
    MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
    DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
    user,
    des
  FROM @rs0
  GROUP BY user, des;
```
## <a name="using-net-assemblies"></a>Uso di assembly .NET

Il modello di estendibilità di U-SQL dipende in larga misura dalla possibilità di aggiungere codice personalizzato da assembly .NET. 

### <a name="register-a-net-assembly"></a>Registrare un assembly .NET

Usare l'istruzione `CREATE ASSEMBLY` per inserire un assembly .NET in un database U-SQL. In seguito gli script U-SQL potranno usare tali assembly tramite l'istruzione `REFERENCE ASSEMBLY`. 

Nel codice seguente viene illustrato come registrare un assembly:

```usql
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

Nel codice seguente viene illustrato come referenziare un assembly:

```usql
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

Consultare la [le istruzioni di registrazione assembly](/archive/blogs/azuredatalake/how-to-register-u-sql-assemblies-in-your-u-sql-catalog) che descrivono nel dettaglio questo argomento.


### <a name="use-assembly-versioning"></a>Usare il controllo delle versioni degli assembly
Attualmente, U-SQL usa la versione .NET Framework 4.7.2. Verificare quindi che i propri assembly siano compatibili con tale versione di runtime.

Come indicato in precedenza, U-SQL esegue il codice in un formato a 64 bit (x64). Verificare quindi che il codice venga compilato per l'esecuzione su x64. In caso contrario verrà visualizzato l'errore di formato non corretto riportato sopra.

Ogni DLL di assembly e file di risorse caricato (ad esempio un diverso runtime, un assembly nativo o un file di configurazione) può essere al massimo di 400 MB. Le dimensioni totali delle risorse distribuite, tramite DEPLOY RESOURCE o riferimenti agli assembly e ai relativi file aggiuntivi, non possono superare 3 GB.

Si noti infine che ogni database U-SQL può contenere solo una versione di un determinato assembly. Se, ad esempio, sono necessarie sia la versione 7 che la versione 8 della libreria NewtonSoft Json.NET, è necessario registrarle in due database diversi. Ogni script, inoltre, può fare riferimento a una sola versione di una determinata DLL di assembly. A tale riguardo, U-SQL segue la semantica di gestione e controllo delle versioni degli assembly di C#.

## <a name="use-user-defined-functions-udf"></a>Usare funzioni definite dall'utente (UDF)
Le funzioni definite dall'utente (UDF) di U-SQL sono routine di programmazione che accettano parametri, eseguono un'azione, ad esempio un calcolo complesso, e restituiscono il risultato di tale azione come valore. Il valore restituito della funzione UDF può essere solo un valore scalare singolo. Una funzione UDF di U-SQL può essere chiamata nello script di base di U-SQL come qualsiasi altra funzione scalare di C#.

È consigliabile inizializzare le funzioni definite dall'utente di U-SQL come **public** e **static**.

```usql
public static string MyFunction(string param1)
{
    return "my result";
}
```

Per prima cosa verrà esaminato il semplice esempio della creazione di una funzione definita dall'utente.

Nello scenario di questo caso d'uso, è necessario determinare il periodo fiscale, ovvero il trimestre fiscale e il mese fiscale, del primo accesso dell'utente specifico. In questo scenario, il primo mese dell'anno fiscale è giugno.

Per calcolare il periodo fiscale, si introduce la funzione C# seguente:

```usql
public static string GetFiscalPeriod(DateTime dt)
{
    int FiscalMonth=0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter=0;
    if (FiscalMonth >=1 && FiscalMonth<=3)
    {
    FiscalQuarter = 1;
    }
    if (FiscalMonth >= 4 && FiscalMonth <= 6)
    {
    FiscalQuarter = 2;
    }
    if (FiscalMonth >= 7 && FiscalMonth <= 9)
    {
    FiscalQuarter = 3;
    }
    if (FiscalMonth >= 10 && FiscalMonth <= 12)
    {
    FiscalQuarter = 4;
    }

    return "Q" + FiscalQuarter.ToString() + ":P" + FiscalMonth.ToString();
}
```

In questo modo vengono semplicemente calcolati il mese e il trimestre fiscali e viene restituito un valore stringa. Per giugno (primo mese del primo trimestre fiscale), si usa "Q1:P1", per luglio "Q1:P2" e così via.

Si tratta di una normale funzione C# che verrà usata nel progetto U-SQL.

Di seguito è illustrato l'aspetto della sezione code-behind in questo scenario:

```usql
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        public static string GetFiscalPeriod(DateTime dt)
        {
            int FiscalMonth=0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter=0;
            if (FiscalMonth >=1 && FiscalMonth<=3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return "Q" + FiscalQuarter.ToString() + ":" + FiscalMonth.ToString();
        }
    }
}
```

Ora si procede a chiamare la funzione dallo script U-SQL di base. A tale scopo, è necessario specificare un nome completo per la funzione, includendo lo spazio dei nomi, in questo caso SpazioDeiNomi.Classe.Funzione(parametro).
```usql
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

Di seguito è riportato l'effettivo script U-SQL di base:
```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt DateTime,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");

@rs1 =
    SELECT
        MAX(guid) AS start_id,
    MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Di seguito è riportato il file di output dell'esecuzione dello script:

```output
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

In questo esempio viene illustrato un uso semplificato della funzione UDF inline in U-SQL.

### <a name="keep-state-between-udf-invocations"></a>Mantenere lo stato tra chiamate di funzioni definite dall'utente
Gli oggetti di programmabilità C# in U-SQL possono essere resi più sofisticati introducendo l'interattività tramite variabili code-behind globali. Verrà esaminato lo scenario del caso d'uso aziendale descritto di seguito.

Nelle grandi organizzazioni gli utenti possono accedere a svariate applicazioni interne, Questi possono includere Microsoft Dynamics CRM, Power BI e così via. I clienti potrebbero voler applicare un'analisi dei dati di telemetria relativamente al modo in cui gli utenti passano da un'applicazione all'altra, alle tendenze di utilizzo e così via. L'obiettivo per l'azienda è ottimizzare l'utilizzo delle applicazioni ed eventualmente combinare anche diverse applicazioni o specifiche routine di accesso.

Per raggiungere tale obiettivo, è necessario determinare gli ID sessione e l'intervallo di tempo rispetto all'ultima sessione eseguita.

È necessario trovare un accesso precedente e quindi assegnare tale accesso a tutte le sessioni generate per la stessa applicazione. Il primo problema è che lo script U-SQL di base non consente di applicare calcoli sulle colonne già calcolate con la funzione LAG. Il secondo problema è che è necessario mantenere la sessione specifica per tutte le sessioni incluse nello stesso periodo di tempo.

Per risolvere questo problema, viene usata una variabile globale all'interno di una sezione code-behind: `static public string globalSession;`.

Questa variabile globale viene applicata all'intero set di righe durante l'esecuzione dello script.

Di seguito è riportata la sezione code-behind del programma U-SQL:

```csharp
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQLApplication21
{
    public class UserSession
    {
        static public string globalSession;
        static public string StampUserSession(string eventTime, string PreviousRow, string Session)
        {

            if (!string.IsNullOrEmpty(PreviousRow))
            {
                double timeGap = Convert.ToDateTime(eventTime).Subtract(Convert.ToDateTime(PreviousRow)).TotalMinutes;
                if (timeGap <= 60) {return Session;}
                else {return Guid.NewGuid().ToString();}
            }
            else {return Guid.NewGuid().ToString();}

        }

        static public string getStampUserSession(string Session)
        {
            if (Session != globalSession && !string.IsNullOrEmpty(Session)) { globalSession = Session; }
            return globalSession;
        }

    }
}
```

Questo esempio illustra la variabile globale `static public string globalSession;` usata nella funzione `getStampUserSession` e reinizializzata a ogni modifica del parametro di sessione.

Lo script U-SQL di base è il seguente:

```usql
DECLARE @in string = @"\UserSession\test1.tsv";
DECLARE @out1 string = @"\UserSession\Out1.csv";
DECLARE @out2 string = @"\UserSession\Out2.csv";
DECLARE @out3 string = @"\UserSession\Out3.csv";

@records =
    EXTRACT DataId string,
            EventDateTime string,           
            UserName string,
            UserSessionTimestamp string

    FROM @in
    USING Extractors.Tsv();

@rs1 =
    SELECT 
        EventDateTime,
        UserName,
    LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,          
        string.IsNullOrEmpty(LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,           
        USQLApplication21.UserSession.StampUserSession
           (
            EventDateTime,
            LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC),
            LAG(UserSessionTimestamp, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)
           ) AS UserSessionTimestamp
    FROM @records;

@rs2 =
    SELECT 
        EventDateTime,
        UserName,
        LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,
        string.IsNullOrEmpty( LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,
        USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp) AS UserSessionTimestamp
    FROM @rs1
    WHERE UserName != "UserName";

OUTPUT @rs2
    TO @out2
    ORDER BY UserName, EventDateTime ASC
    USING Outputters.Csv();
```

La funzione `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` viene chiamata durante il secondo calcolo del set di righe della memoria. Passa la colonna `UserSessionTimestamp` e restituisce il valore finché non viene modificato `UserSessionTimestamp`.

Il file di output è il seguente:

```output
"2016-02-19T07:32:36.8420000-08:00","User1",,True,"72a0660e-22df-428e-b672-e0977007177f"
"2016-02-17T11:52:43.6350000-08:00","User2",,True,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-17T11:59:08.8320000-08:00","User2","2016-02-17T11:52:43.6350000-08:00",False,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-11T07:04:17.2630000-08:00","User3",,True,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-11T07:10:33.9720000-08:00","User3","2016-02-11T07:04:17.2630000-08:00",False,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-15T21:27:41.8210000-08:00","User3","2016-02-11T07:10:33.9720000-08:00",False,"4d2bc48d-bdf3-4591-a9c1-7b15ceb8e074"
"2016-02-16T05:48:49.6360000-08:00","User3","2016-02-15T21:27:41.8210000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-16T06:22:43.6390000-08:00","User3","2016-02-16T05:48:49.6360000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-17T16:29:53.2280000-08:00","User3","2016-02-16T06:22:43.6390000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T16:39:07.2430000-08:00","User3","2016-02-17T16:29:53.2280000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T17:20:39.3220000-08:00","User3","2016-02-17T16:39:07.2430000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-19T05:23:54.5710000-08:00","User3","2016-02-17T17:20:39.3220000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T05:48:37.7510000-08:00","User3","2016-02-19T05:23:54.5710000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T06:40:27.4830000-08:00","User3","2016-02-19T05:48:37.7510000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T07:27:37.7550000-08:00","User3","2016-02-19T06:40:27.4830000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T19:35:40.9450000-08:00","User3","2016-02-19T07:27:37.7550000-08:00",False,"3f385f0b-3e68-4456-ac74-ff6cef093674"
"2016-02-20T00:07:37.8250000-08:00","User3","2016-02-19T19:35:40.9450000-08:00",False,"685f76d5-ca48-4c58-b77d-bd3a9ddb33da"
"2016-02-11T09:01:33.9720000-08:00","User4",,True,"9f0cf696-c8ba-449a-8d5f-1ca6ed8f2ee8"
"2016-02-17T06:30:38.6210000-08:00","User4","2016-02-11T09:01:33.9720000-08:00",False,"8b11fd2a-01bf-4a5e-a9af-3c92c4e4382a"
"2016-02-17T22:15:26.4020000-08:00","User4","2016-02-17T06:30:38.6210000-08:00",False,"4e1cb707-3b5f-49c1-90c7-9b33b86ca1f4"
"2016-02-18T14:37:27.6560000-08:00","User4","2016-02-17T22:15:26.4020000-08:00",False,"f4e44400-e837-40ed-8dfd-2ea264d4e338"
"2016-02-19T01:20:31.4800000-08:00","User4","2016-02-18T14:37:27.6560000-08:00",False,"2136f4cf-7c7d-43c1-8ae2-08f4ad6a6e08"
```

Questo esempio illustra lo scenario di un caso d'uso più complesso in cui si usa una variabile globale in una sezione code-behind applicata all'intero set di righe della memoria.

## <a name="next-steps"></a>Passaggi successivi
* [Guida alla programmabilità di U-SQL-UDT e UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)
* [Guida alla programmabilità di U-SQL-UDO](data-lake-analytics-u-sql-programmability-guide-UDO.md)