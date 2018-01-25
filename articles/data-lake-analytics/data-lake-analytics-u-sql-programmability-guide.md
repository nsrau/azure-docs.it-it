---
title: "Guida alla programmabilità di U-SQL per Azure Data Lake | Microsoft Docs"
description: Informazioni sul set di servizi di Azure Data Lake che consente di creare una piattaforma Big Data basata sul cloud.
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
ms.assetid: 63be271e-7c44-4d19-9897-c2913ee9599d
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/30/2017
ms.author: saveenr
ms.openlocfilehash: 3686cfffd2c29461213b2866665e59336f037fa0
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2018
---
# <a name="u-sql-programmability-guide"></a>Guida alla programmabilità di U-SQL

U-SQL è un linguaggio di query progettato per carichi di lavoro di tipo Big Data. Una delle caratteristiche esclusive di U-SQL è la combinazione tra linguaggio dichiarativo di tipo SQL e funzionalità di estendibilità e programmabilità del linguaggio C#. Questa guida è incentrata sulle funzionalità di estendibilità e programmabilità del linguaggio U-SQL supportate da C#.

## <a name="requirements"></a>Requisiti

Scaricare e installare [Strumenti Azure Data Lake per Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="get-started-with-u-sql"></a>Introduzione a U-SQL  

Si consideri lo script U-SQL seguente:

```
@a  = 
  SELECT * FROM 
    (VALUES
       ("Contoso",   1500.0, "2017-03-39"),
       ("Woodgrove", 2700.0, "2017-04-10")
    ) AS D( customer, amount );

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

```
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

Il frammento di codice seguente analizza una stringa come valore DateTime in un'istruzione DECLARE.

```
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>Usare espressioni C# per conversioni del tipo di dati

L'esempio seguente illustra come effettuare una conversione di dati di tipo datetime usando espressioni C#. In questo particolare scenario, i dati della stringa datetime vengono convertiti in datetime standard con la notazione dell'ora 00:00:00.

```
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

```
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

```
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

Nel codice seguente viene illustrato come referenziare un assembly:

```
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

Consultare la [le istruzioni di registrazione assembly](https://blogs.msdn.microsoft.com/azuredatalake/2016/08/26/how-to-register-u-sql-assemblies-in-your-u-sql-catalog/) che descrivono nel dettaglio questo argomento.


### <a name="use-assembly-versioning"></a>Usare il controllo delle versioni degli assembly
Attualmente, U-SQL usa .NET Framework versione 4.5. Verificare quindi che i propri assembly siano compatibili con tale versione di runtime.

Come indicato in precedenza, U-SQL esegue il codice in un formato a 64 bit (x64). Verificare quindi che il codice venga compilato per l'esecuzione su x64. In caso contrario verrà visualizzato l'errore di formato non corretto riportato sopra.

Ogni DLL di assembly e file di risorse caricato (ad esempio un diverso runtime, un assembly nativo o un file di configurazione) può essere al massimo di 400 MB. Le dimensioni totali delle risorse distribuite, tramite DEPLOY RESOURCE o riferimenti agli assembly e ai relativi file aggiuntivi, non possono superare 3 GB.

Si noti infine che ogni database U-SQL può contenere solo una versione di un determinato assembly. Se sono necessarie entrambe le versioni 7 e 8 della libreria NewtonSoft Json.Net, si devono registrare in due database diversi. Ogni script, inoltre, può fare riferimento a una sola versione di una determinata DLL di assembly. A tale riguardo, U-SQL segue la semantica di gestione e controllo delle versioni degli assembly di C#.

## <a name="use-user-defined-functions-udf"></a>Usare funzioni definite dall'utente (UDF)
Le funzioni definite dall'utente (UDF) di U-SQL sono routine di programmazione che accettano parametri, eseguono un'azione, ad esempio un calcolo complesso, e restituiscono il risultato di tale azione come valore. Il valore restituito della funzione UDF può essere solo un valore scalare singolo. Una funzione UDF di U-SQL può essere chiamata nello script di base di U-SQL come qualsiasi altra funzione scalare di C#.

È consigliabile inizializzare le funzioni definite dall'utente di U-SQL come **public** e **static**.

```
public static string MyFunction(string param1)
{
    return "my result";
}
```

Per prima cosa verrà esaminato il semplice esempio della creazione di una funzione definita dall'utente.

Nello scenario di questo caso d'uso, è necessario determinare il periodo fiscale, ovvero il trimestre fiscale e il mese fiscale, del primo accesso dell'utente specifico. In questo scenario, il primo mese dell'anno fiscale è giugno.

Per calcolare il periodo fiscale, si introduce la funzione C# seguente:

```
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

```
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

```
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

Di seguito è riportato l'effettivo script U-SQL di base:

```
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

```
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

In questo esempio viene illustrato un uso semplificato della funzione UDF inline in U-SQL.

### <a name="keep-state-between-udf-invocations"></a>Mantenere lo stato tra chiamate di funzioni definite dall'utente
Gli oggetti di programmabilità C# in U-SQL possono essere resi più sofisticati introducendo l'interattività tramite variabili code-behind globali. Verrà esaminato lo scenario del caso d'uso aziendale descritto di seguito.

Nelle grandi organizzazioni gli utenti possono accedere a svariate applicazioni interne, ad esempio Microsoft Dynamics CRM, Power BI e così via. I clienti potrebbero voler applicare un'analisi dei dati di telemetria relativamente al modo in cui gli utenti passano da un'applicazione all'altra, alle tendenze di utilizzo e così via. L'obiettivo per l'azienda è ottimizzare l'utilizzo delle applicazioni ed eventualmente combinare anche diverse applicazioni o specifiche routine di accesso.

Per raggiungere tale obiettivo, è necessario determinare gli ID sessione e l'intervallo di tempo rispetto all'ultima sessione eseguita.

È necessario trovare un accesso precedente e quindi assegnare tale accesso a tutte le sessioni generate per la stessa applicazione. Il primo problema è che lo script U-SQL di base non consente di applicare calcoli sulle colonne già calcolate con la funzione LAG. Il secondo problema è che è necessario mantenere la sessione specifica per tutte le sessioni incluse nello stesso periodo di tempo.

Per risolvere questo problema, viene usata una variabile globale all'interno di una sezione code-behind: `static public string globalSession;`.

Questa variabile globale viene applicata all'intero set di righe durante l'esecuzione dello script.

Di seguito è riportata la sezione code-behind del programma U-SQL:

```
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

```
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

```
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

## <a name="use-user-defined-types-udt"></a>Usare tipi definiti dall'utente (UDT)
I tipi definiti dall'utente (UDT) sono un'altra funzionalità di programmabilità di U-SQL. L'UDT U-SQL funziona come un normale tipo definito dall'utente C#. C# è un linguaggio fortemente tipizzato che consente l'uso di tipi incorporati e personalizzati definiti dall'utente.

U-SQL non può serializzare o deserializzare implicitamente tipi definiti dall'utente arbitrari quando il tipo definito dall'utente viene passato tra i vertici nei set di righe. Di conseguenza, l'utente deve specificare un formattatore esplicito usando l'interfaccia IFormatter. Questo fornisce a U-SQL i metodi di serializzazione e deserializzazione per il tipo definito dall'utente.

> [!NOTE]
> Gli outputter e gli estrattori predefiniti di U-SQL non possono attualmente serializzare o deserializzare i dati UDT da o verso i file anche con l'impostazione di IFormatter. Di conseguenza, quando si scrivono dati UDT in un file con l'istruzione OUTPUT o si leggono tali dati con un estrattore, è necessario passare i dati come stringa o matrice di byte. Si chiama quindi in modo esplicito il codice di serializzazione o deserializzazione, ossia il metodo ToString() del tipo definito dall'utente. Gli outputter e gli estrattori definiti dall'utente, invece, possono leggere e scrivere i tipi definiti dall'utente.

Se si prova a usare il tipo definito dall'utente in EXTRACTOR o OUTPUTTER, fuori dall'istruzione SELECT precedente, come illustrato di seguito:

```
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

viene visualizzato l'errore seguente:

```
Error   1   E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
MyNameSpace.Myfunction_Returning_UDT.

Description:

Outputters.Text only supports built-in types.

Resolution:

Implement a custom outputter that knows how to serialize this type, or call a serialization method on the type in
the preceding SELECT.   C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\
USQL-Programmability\Types.usql 52  1   USQL-Programmability
```

Per usare il tipo definito dall'utente nell'outputter, è necessario serializzarlo in stringa con il metodo ToString() e creare un outputter personalizzato.

Al momento non è possibile usare UDT in GROUP BY. Se si usa l'UDT in GROUP BY, viene generato l'errore seguente:

```
Error   1   E_CSC_USER_INVALIDTYPEINCLAUSE: GROUP BY doesn't support type MyNameSpace.Myfunction_Returning_UDT
for column myfield

Description:

GROUP BY doesn't support UDT or Complex types.

Resolution:

Add a SELECT statement where you can project a scalar column that you want to use with GROUP BY.
C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\USQL-Programmability\Types.usql
62  5   USQL-Programmability
```

Per definire un UDT, è necessario:

* Aggiungere gli spazi dei nomi seguenti:

```
using Microsoft.Analytics.Interfaces
using System.IO;
```

* Aggiungere `Microsoft.Analytics.Interfaces`, obbligatorio per le interfacce per i tipi definiti dall'utente. Per definire l'interfaccia IFormatter potrebbe essere necessario anche `System.IO`.

* Definire un tipo definito dall'utente con l'attributo SqlUserDefinedType.

**SqlUserDefinedType** è usato per contrassegnare la definizione di un tipo in un assembly come tipo definito dall'utente (UDT) in U-SQL. Le proprietà dell'attributo corrispondono alle caratteristiche fisiche dell'UDT. Questa classe non può essere ereditata.

SqlUserDefinedType è un attributo obbligatorio per la definizione dell'UDT.

Il costruttore della classe:  

* SqlUserDefinedTypeAttribute (formattatore di tipo)

* Formattatore di tipo: parametro obbligatorio per definire un formattatore UDT. Nello specifico, qui deve essere passato il tipo dell'interfaccia `IFormatter`.

```
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* Un tipo definito dall'utente richiede in genere anche la definizione dell'interfaccia IFormatter, come illustrato nell'esempio seguente:

```
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

L'interfaccia `IFormatter` serializza e deserializza un oggetto grafico con il tipo radice \<typeparamref name="T">.

\<typeparam name="T"> il tipo radice per l'oggetto grafico da serializzare e deserializzare.

* **Deserialize**: deserializza i dati nel flusso fornito e ricostruisce il grafico degli oggetti.

* **Serialize**: serializza un oggetto o un grafico di oggetti con la radice specificata nel flusso fornito.

`MyType` instance: istanza del tipo.  
`IColumnWriter` writer/`IColumnReader` reader: flusso di colonna sottostante.  
`ISerializationContext` context: enumerazione che definisce un set di flag che specifica il contesto di origine o di destinazione per il flusso durante la serializzazione.

* **Intermediate**: specifica che il contesto di origine o di destinazione non è un archivio permanente.

* **Persistence**: specifica che il contesto di origine o di destinazione è un archivio permanente.

Come un normale tipo C#, la definizione di un tipo definito dall'utente di U-SQL può includere override per operatori come +/==/!= e così via. Può anche includere metodi statici. Ad esempio, se si intende usare il tipo definito dall'utente come parametro per una funzione di aggregazione MIN U-SQL, è necessario definire l'override dell'operatore <.

In precedenza in questa guida è stato illustrato un esempio di identificazione del periodo fiscale dalla data specifica nel formato `Qn:Pn (Q1:P10)`. Nell'esempio seguente viene illustrato come definire un tipo personalizzato per i valori del periodo fiscale.

Di seguito è riportato un esempio di sezione code-behind con tipo definito dall'utente e interfaccia IFormatter personalizzati:

```
[SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
public struct FiscalPeriod
{
    public int Quarter { get; private set; }

    public int Month { get; private set; }

    public FiscalPeriod(int quarter, int month):this()
    {
    this.Quarter = quarter;
    this.Month = month;
    }

    public override bool Equals(object obj)
    {
    if (ReferenceEquals(null, obj))
    {
        return false;
    }

    return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
    }

    public bool Equals(FiscalPeriod other)
    {
return this.Quarter.Equals(other.Quarter) && this.Month.Equals(other.Month);
    }

    public bool GreaterThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
    }

    public bool LessThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
    }

    public override int GetHashCode()
    {
    unchecked
    {
        return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
    }
    }

    public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
    {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
    }

    public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.Equals(c2);
    }

    public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
    {
    return !c1.Equals(c2);
    }
    public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.GreaterThan(c2);
    }
    public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.LessThan(c2);
    }
    public override string ToString()
    {
    return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
    }

}

public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
{
    public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
    {
    using (var binaryWriter = new BinaryWriter(writer.BaseStream))
    {
        binaryWriter.Write(instance.Quarter);
        binaryWriter.Write(instance.Month);
        binaryWriter.Flush();
    }
    }

    public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
    {
    using (var binaryReader = new BinaryReader(reader.BaseStream))
    {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
        return result;
    }
    }
}
```

Il tipo definito include due numeri, corrispondenti a trimestre e mese. Qui sono definiti gli operatori `==/!=/>/<` e il metodo statico `ToString()`.

Come indicato in precedenza, il tipo definito dall'utente può essere usato nelle espressioni SELECT, ma non in OUTPUTTER/EXTRACTOR senza serializzazione personalizzata. Deve essere serializzato come stringa con `ToString()` oppure deve essere usato con un elemento OUTPUTTER/EXTRACTOR personalizzato.

Ora esaminiamo l'uso dell'UDT. In una sezione code-behind, la funzione GetFiscalPeriod è stata modificata come segue:

```
public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
{
    int FiscalMonth = 0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter = 0;
    if (FiscalMonth >= 1 && FiscalMonth <= 3)
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

    return new FiscalPeriod(FiscalQuarter, FiscalMonth);
}
```

Come si può notare, restituisce il valore del tipo FiscalPeriod.

Di seguito è riportato un esempio dell'ulteriore uso nello script U-SQL di base. Questo esempio illustra diverse forme di chiamata del tipo definito dall'utente dallo script U-SQL.

```
DECLARE @input_file string = @"c:\work\cosmos\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"c:\work\cosmos\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT 
        guid AS start_id,
        dt,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Quarter AS fiscalquarter,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Month AS fiscalmonth,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt) + new USQL_Programmability.CustomFunctions.FiscalPeriod(1,7) AS fiscalperiod_adjusted,
        user,
        des
    FROM @rs0;

@rs2 =
    SELECT 
           start_id,
           dt,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           fiscalquarter,
           fiscalmonth,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS fiscalperiod,

       // This user-defined type was created in the prior SELECT.  Passing the UDT to this subsequent SELECT would have failed if the UDT was not annotated with an IFormatter.
           fiscalperiod_adjusted.ToString() AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

Ecco un esempio di una sezione code-behind completa:

```
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.IO;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }

        public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
        {
            int FiscalMonth = 0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter = 0;
            if (FiscalMonth >= 1 && FiscalMonth <= 3)
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

            return new FiscalPeriod(FiscalQuarter, FiscalMonth);
        }



        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
        public struct FiscalPeriod
        {
            public int Quarter { get; private set; }

            public int Month { get; private set; }

            public FiscalPeriod(int quarter, int month):this()
            {
                this.Quarter = quarter;
                this.Month = month;
            }

            public override bool Equals(object obj)
            {
                if (ReferenceEquals(null, obj))
                {
                    return false;
                }

                return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
            }

            public bool Equals(FiscalPeriod other)
            {
return this.Quarter.Equals(other.Quarter) &&    this.Month.Equals(other.Month);
            }

            public bool GreaterThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
            }

            public bool LessThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
            }

            public override int GetHashCode()
            {
                unchecked
                {
                    return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
                }
            }

            public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
            {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
            }

            public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.Equals(c2);
            }

            public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
            {
                return !c1.Equals(c2);
            }
            public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.GreaterThan(c2);
            }
            public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.LessThan(c2);
            }
            public override string ToString()
            {
                return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
            }

        }

        public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
        {
public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
            {
                using (var binaryWriter = new BinaryWriter(writer.BaseStream))
                {
                    binaryWriter.Write(instance.Quarter);
                    binaryWriter.Write(instance.Month);
                    binaryWriter.Flush();
                }
            }

public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
            {
                using (var binaryReader = new BinaryReader(reader.BaseStream))
                {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
                    return result;
                }
            }
        }
    }
}
```

## <a name="use-user-defined-aggregates-udagg"></a>Usare aggregazioni definite dall'utente (UDAGG)
Le aggregazioni definite dall'utente sono funzioni correlate all'aggregazione che non sono già incluse in U-SQL. Può trattarsi ad esempio di una funzione di aggregazione per eseguire calcoli matematici personalizzati, concatenazioni di stringa o modifiche con stringhe e così via.

La definizione della classe base delle aggregazioni definite dall'utente è la seguente:

```csharp
    [SqlUserDefinedAggregate]
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
    {
        protected IAggregate();

        public abstract void Accumulate(T1 t1, T2 t2);
        public abstract void Init();
        public abstract TResult Terminate();
    }
```

**SqlUserDefinedAggregate** indica che il tipo deve essere registrato come aggregazione definita dall'utente. Questa classe non può essere ereditata.

L'attributo SqlUserDefinedType è **facoltativo** per la definizione di aggregazioni definite dall'utente.


La classe base consente di passare tre parametri astratti: due come parametri di input e uno come risultato. I tipi di dati sono variabili e devono essere definiti quando viene ereditata la classe.

```
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    { … }

    public override void Accumulate(string guid, string user)
    { … }

    public override string Terminate()
    { … }
}
```

* **Init** esegue la chiamata una volta per ogni gruppo durante il calcolo. Fornisce la routine di inizializzazione per ogni gruppo di aggregazione.  
* **Accumulate** viene eseguito una volta per ogni valore. Fornisce la funzionalità principale per l'algoritmo di aggregazione. Consente di aggregare valori con vari tipi di dati che vengono definiti quando viene ereditata la classe. Può accettare due parametri di tipi di dati della variabile.
* **Terminate** viene eseguito una volta per ogni gruppo di aggregazione al termine dell'elaborazione per restituire il risultato per ogni gruppo.

Per dichiarare tipi di dati di input e output corretti, usare la definizione di classe come segue:

```
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: primo parametro per Accumulate
* T2: primo parametro per Accumulate
* TResult: tipo restituito di Terminate

Ad esempio: 

```
public class GuidAggregate : IAggregate<string, int, int>
```

oppure

```
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>Usare aggregazioni definite dall'utente in U-SQL
Per usare un'aggregazione definita dall'utente, per prima cosa è necessario definirla nel code-behind oppure farvi riferimento dalla DLL di programmabilità esistente, come descritto in precedenza.

Usare quindi la sintassi seguente:

```
AGG<UDAGG_functionname>(param1,param2)
```

Di seguito è riportato un esempio di aggregazione definita dall'utente:

```
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    {
        guid_agg = "";
    }

    public override void Accumulate(string guid, string user)
    {
        if (user.ToUpper()== "USER1")
        {
        guid_agg += "{" + guid + "}";
        }
    }

    public override string Terminate()
    {
        return guid_agg;
    }

}
```

Ecco lo script U-SQL di base:

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @" \usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    SELECT
        user,
        AGG<USQL_Programmability.GuidAggregate>(guid,user) AS guid_list
    FROM @rs0
    GROUP BY user;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

Nello scenario di questo caso d'uso, vengono concatenati GUID di classe per gli utenti specifici.

## <a name="use-user-defined-objects-udo"></a>Usare oggetti definiti dall'utente (UDO)
U-SQL consente di definire oggetti di programmabilità personalizzati, denominati oggetti definiti dall'utente (UDO).

Di seguito è riportato un elenco degli oggetti definiti dall'utente in U-SQL:

* Estrattori definiti dall'utente
    * Estrazione riga per riga
    * Vengono usati per implementare l'estrazione di dati da file strutturati personalizzati

* Outputter definiti dall'utente
    * Output riga per riga
    * Vengono usati per tipi di dati di output o formati di file personalizzati

* Elaboratori definiti dall'utente
    * Viene usato per richiedere una riga e produrre una riga
    * Vengono usati per ridurre il numero di colonne o produrre nuove colonne con valori derivati da un set di colonne esistente

* Oggetti di applicazione definiti dall'utente
    * Serve a richiedere una riga e produrre da 0 a n righe
    * Viene usato con OUTER/CROSS APPLY

* Combinatori definiti dall'utente
    * Combinano set di righe (JOIN definiti dall'utente)

* Riduttori definiti dall'utente
    * Serve a richiedere n righe e produrre una riga
    * Vengono usati per ridurre il numero di righe

Un oggetto definito dall'utente viene in genere chiamato in modo esplicito negli script U-SQL come parte delle istruzioni U-SQL seguenti:

* EXTRACT
* OUTPUT
* PROCESS
* COMBINE
* REDUCE

> [!NOTE]  
> Gli oggetti definiti dall'utente sono limitati per occupare 0,5 GB di memoria.  Questa limitazione di memoria non è applicabile alle esecuzioni locali.

## <a name="use-user-defined-extractors"></a>Usare estrattori definiti dall'utente
U-SQL consente di importare dati esterni con un'istruzione EXTRACT. L'istruzione EXTRACT consente di usare estrattori UDO predefiniti.  

* *Extractors.Text()*: consente di estrarre da file di testo delimitati di varie codifiche.

* *Extractors.Csv()*: consente di estrarre da file di testo delimitati da virgole (CSV) di varie codifiche.

* *Extractors.Tsv()*: consente di estrarre da file di testo delimitati da tabulazioni (TSV) di varie codifiche.

Può essere utile per sviluppare un estrattore personalizzato. Questo può essere opportuno durante un'importazione di dati, se si vuole eseguire una o più delle attività seguenti:

* Modificare i dati di input suddividendo le colonne e modificando singoli valori. Per la combinazione di colonne è preferibile la funzionalità PROCESSOR.
* Analizzare dati non strutturati, come pagine Web e messaggi di posta elettronica, o dati parzialmente non strutturati, ad esempio XML/JSON.
* Analizzare dati in una codifica non supportata.

Per definire un estrattore definito dall'utente (UDE), è necessario creare un'interfaccia `IExtractor`. Tutti i parametri di input per l'estrattore, come i delimitatori di riga/colonna e la codifica, devono essere definiti nel costruttore della classe. L'interfaccia `IExtractor` deve contenere anche una definizione per l'override `IEnumerable<IRow>`, come illustrato di seguito:

```
[SqlUserDefinedExtractor]
public class SampleExtractor : IExtractor
{
     public SampleExtractor(string row_delimiter, char col_delimiter)
     { … }

     public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
     { … }
}
```

L'attributo **SqlUserDefinedExtractor** indica che il tipo deve essere registrato come estrattore definito dall'utente. Questa classe non può essere ereditata.

SqlUserDefinedExtractor è un attributo facoltativo per la definizione di UDE, che consente di definire la proprietà AtomicFileProcessing dell'oggetto UDE.

* bool     AtomicFileProcessing   

* **true** indica che l'estrattore richiede file di input atomici (JSON, XML e così via)
* **false** indica che l'estrattore può gestire file suddivisi/distribuiti (CSV, SEQ e così via)

I principali oggetti di programmabilità UDE sono **input** e **output**. L'oggetto di input viene usato per enumerare i dati di input come `IUnstructuredReader`. L'oggetto di output viene usato per impostare i dati di output come risultato dell'attività di estrazione.

I dati di input sono accessibili tramite `System.IO.Stream` e `System.IO.StreamReader`.

Per l'enumerazione delle colonne di input, per prima cosa si suddivide il flusso di input con un delimitatore di riga.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

Quindi, la riga di input viene ulteriormente suddivisa nelle parti di una colonna.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

Per impostare i dati di output, si usa il metodo `output.Set`.

È importante comprendere che l'estrattore personalizzato restituisce solo le colonne e i valori definiti con l'output. il metodo di chiamata output.Set.

```
output.Set<string>(count, part);
```

L'output effettivo dell'estrattore viene attivato chiamando `yield return output.AsReadOnly();`.

Di seguito è riportato l'esempio di estrattore:

```
[SqlUserDefinedExtractor(AtomicFileProcessing = true)]
public class FullDescriptionExtractor : IExtractor
{
     private Encoding _encoding;
     private byte[] _row_delim;
     private char _col_delim;

    public FullDescriptionExtractor(Encoding encoding, string row_delim = "\r\n", char col_delim = '\t')
    {
         this._encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
         this._row_delim = this._encoding.GetBytes(row_delim);
         this._col_delim = col_delim;

    }

    public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
    {
         string line;
         //Read the input line by line
         foreach (Stream current in input.Split(_encoding.GetBytes("\r\n")))
         {
        using (System.IO.StreamReader streamReader = new StreamReader(current, this._encoding))
         {
             line = streamReader.ReadToEnd().Trim();
             //Split the input by the column delimiter
             string[] parts = line.Split(this._col_delim);
             int count = 0; // start with first column
             foreach (string part in parts)
             {
    if (count == 0)
             {  // for column “guid”, re-generated guid
                 Guid new_guid = Guid.NewGuid();
                 output.Set<Guid>(count, new_guid);
             }
             else if (count == 2)
             {
                 // for column “user”, convert to UPPER case
                 output.Set<string>(count, part.ToUpper());

             }
             else
             {
                 // keep the rest of the columns as-is
                 output.Set<string>(count, part);
             }
             count += 1;
             }

         }
         yield return output.AsReadOnly();
         }
         yield break;
     }
}
```

Nello scenario di questo caso d'uso, l'estrattore rigenera il GUID per la colonna "guid" e converte i valori della colonna "user" in lettere maiuscole. Gli estrattori personalizzati possono produrre risultati più complessi analizzando e modificando i dati di input.

Di seguito è riportato uno script U-SQL di base che usa un estrattore personalizzato:

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file
        USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 TO @output_file USING Outputters.Text();
```

## <a name="use-user-defined-outputters"></a>Usare outputter definiti dall'utente
L'outputter definito dall'utente è un altro oggetto definito dall'utente di U-SQL che consente di estendere una funzionalità predefinita di U-SQL. Come per l'estrattore, esistono diversi outputter integrati.

* *Outputters.Text()*: scrive i dati in file di testo delimitati di codifiche diverse.
* *Outputters.Csv()*: scrive i dati in file di testo delimitati da virgole (CSV) di codifiche diverse.
* *Outputters.Tsv()*: scrive i dati in file di testo delimitati da tabulazioni (TSV) di codifiche diverse.

L'outputter personalizzato consente di scrivere i dati in un formato definito personalizzato. Questo può essere utile per le attività seguenti:

* Scrittura di i dati in file non strutturati o semistrutturati
* Scrittura di dati in codifiche non supportate
* Modifica dei dati di output o aggiunta di attributi personalizzati

Per definire outputter definiti dall'utente, è necessario creare l'interfaccia `IOutputter`.

Di seguito è riportata l'implementazione della classe `IOutputter` di base:

```
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

Tutti i parametri di input per l'outputter, come i delimitatori di riga/colonna, la codifica e così via, devono essere definiti nel costruttore della classe. L'interfaccia `IOutputter` deve contenere anche una definizione per l'override `void Output`. L'attributo `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` può essere facoltativamente impostato per l'elaborazione di file atomici. Per altre informazioni, vedere i dettagli riportati di seguito.

```
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class MyOutputter : IOutputter
{

    public MyOutputter(myparam1, myparam2)
    {
      …
    }

    public override void Close()
    {
      …
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
      …
    }
}
```

* `Output` viene chiamato per ogni riga di input. Restituisce il set di righe `IUnstructuredWriter output`.
* La classe del costruttore viene usata per passare parametri all'outputter definito dall'utente.
* `Close` viene usato per eseguire facoltativamente l'override per rilasciare uno stato dispendioso o determinare quando è stata scritta l'ultima riga.

L'attributo **SqlUserDefinedOutputter** indica che il tipo deve essere registrato come outputter definito dall'utente. Questa classe non può essere ereditata.

SqlUserDefinedOutputter è un attributo facoltativo per la definizione di un outputter definito dall'utente. Viene usato per definire la proprietà AtomicFileProcessing.

* bool     AtomicFileProcessing   

* **true** indica che l'outputter richiede file di output atomici (JSON, XML e così via)
* **false** indica che l'outputter può gestire file suddivisi/distribuiti (CSV, SEQ e così via)

I principali oggetti di programmabilità sono **row** e **output**. L'oggetto **row** viene usato per enumerare i dati di output come interfaccia `IRow`. **Output** viene usato per impostare i dati di output sul file di destinazione.

I dati di output sono accessibili tramite l'interfaccia `IRow`. I dati di output vengono trasmessi una riga alla volta.

I singoli valori vengono enumerati chiamando il metodo Get dell'interfaccia IRow:

```
row.Get<string>("column_name")
```

I nomi delle singole colonne possono essere determinati chiamando `row.Schema`:

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Questo approccio consente di compilare un outputter flessibile per qualsiasi schema di metadati.

I dati di output vengono scritti in un file usando `System.IO.StreamWriter`. Il parametro di flusso viene impostato su `output.BaseStrea` come parte di `IUnstructuredWriter output`.

Si noti che è importante scaricare il buffer dei dati nel file dopo ogni iterazione di riga. L'oggetto `StreamWriter`, inoltre, deve essere usato con l'attributo Disposable abilitato (impostazione predefinita) e con la parola chiave **using**:

```
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

In alternativa, chiamare il metodo Flush() in modo esplicito dopo ogni iterazione, come illustrato nell'esempio riportato di seguito.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>Impostare intestazioni e piè di pagina per l'outputter definito dall'utente
Per impostare un'intestazione, usare il flusso di esecuzione dell'iterazione singola.

```
public override void Output(IRow row, IUnstructuredWriter output)
{
 …
if (isHeaderRow)
{
    …                
}

 …
if (isHeaderRow)
{
    isHeaderRow = false;
}
 …
}
}
```

Il codice nel primo blocco `if (isHeaderRow)` viene eseguito una sola volta.

Per il piè di pagina, usare il riferimento all'istanza dell'oggetto `System.IO.Stream` (`output.BaseStream`). Scrivere il piè di pagina nel metodo Close() dell'interfaccia `IOutputter`.  Per altre informazioni, vedere l'esempio seguente.

Di seguito è riportato un esempio di outputter definito dall'utente:

```
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class HTMLOutputter : IOutputter
{
    // Local variables initialization
    private string row_delimiter;
    private char col_delimiter;
    private bool isHeaderRow;
    private Encoding encoding;
    private bool IsTableHeader = true;
    private Stream g_writer;

    // Parameters definition            
    public HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    this.isHeaderRow = isHeader;
    this.encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
    }

    // The Close method is used to write the footer to the file. It's executed only once, after all rows
    public override void Close().
    {
    //Reference to IO.Stream object - g_writer
    StreamWriter streamWriter = new StreamWriter(g_writer, this.encoding);
    streamWriter.Write("</table>");
    streamWriter.Flush();
    streamWriter.Close();
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
    System.IO.StreamWriter streamWriter = new StreamWriter(output.BaseStream, this.encoding);

    // Metadata schema initialization to enumerate column names
    ISchema schema = row.Schema;

    // This is a data-independent header--HTML table definition
    if (IsTableHeader)
    {
        streamWriter.Write("<table border=1>");
        IsTableHeader = false;
    }

    // HTML table attributes
    string header_wrapper_on = "<th>";
    string header_wrapper_off = "</th>";
    string data_wrapper_on = "<td>";
    string data_wrapper_off = "</td>";

    // Header row output--runs only once
    if (isHeaderRow)
    {
        streamWriter.Write("<tr>");
        for (int i = 0; i < schema.Count(); i++)
        {
        var col = schema[i];
        streamWriter.Write(header_wrapper_on + col.Name + header_wrapper_off);
        }
        streamWriter.Write("</tr>");
    }

    // Data row output
    streamWriter.Write("<tr>");                
    for (int i = 0; i < schema.Count(); i++)
    {
        var col = schema[i];
        string val = "";
        try
        {
        // Data type enumeration--required to match the distinct list of types from OUTPUT statement
        switch (col.Type.Name.ToString().ToLower())
        {
            case "string": val = row.Get<string>(col.Name).ToString(); break;
            case "guid": val = row.Get<Guid>(col.Name).ToString(); break;
            default: break;
        }
        }
        // Handling NULL values--keeping them empty
        catch (System.NullReferenceException)
        {
        }
        streamWriter.Write(data_wrapper_on + val + data_wrapper_off);
    }
    streamWriter.Write("</tr>");

    if (isHeaderRow)
    {
        isHeaderRow = false;
    }
    // Reference to the instance of the IO.Stream object for footer generation
    g_writer = output.BaseStream;
    streamWriter.Flush();
    }
}

// Define the factory classes
public static class Factory
{
    public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    return new HTMLOutputter(isHeader, encoding);
    }
}
```

Ecco lo script U-SQL di base:

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.html";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
         FROM @input_file
         USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 
    TO @output_file 
    USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Questo è un outputter HTML che crea un file HTML con dati di tabella.

### <a name="call-outputter-from-u-sql-base-script"></a>Chiamare l'outputter dallo script U-SQL di base
Per chiamare un outputter personalizzato dallo script U-SQL di base, è necessario creare la nuova istanza dell'oggetto outputter.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Per evitare di creare un'istanza dell'oggetto nello script di base, è possibile creare un wrapper di funzione, come illustrato nell'esempio precedente:

```csharp
        // Define the factory classes
        public static class Factory
        {
            public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                return new HTMLOutputter(isHeader, encoding);
            }
        }
```

In questo caso, la chiamata originale si presenta come segue:

```
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="use-user-defined-processors"></a>Usare elaboratori definiti dall'utente
Un elaboratore definito dall'utente (UDP) è un tipo di oggetto definito dall'utente di U-SQL che consente di elaborare le righe in ingresso applicando funzionalità di programmabilità. Un elaboratore definito dall'utente consente di combinare colonne, modificare valori e aggiungere nuove colonne, se necessario. Essenzialmente, consente di elaborare un set di righe per produrre gli elementi dati necessari.

Per definire un elaboratore definito dall'utente, è necessario creare un'interfaccia `IProcessor` con l'attributo `SqlUserDefinedProcessor`, che per gli elaboratori definiti dall'utente è facoltativo.

L'interfaccia deve contenere la definizione per l'override dei set di righe dell'interfaccia `IRow`, come illustrato nell'esempio seguente:

```
[SqlUserDefinedProcessor]
public class MyProcessor: IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
        …
 }
}
```

**SqlUserDefinedProcessor** indica che il tipo deve essere registrato come Processor definito dall'utente. Questa classe non può essere ereditata.

Per la definizione degli elaboratori definiti dall'utente, l'attributo SqlUserDefinedProcessor è **facoltativo**.

I principali oggetti di programmabilità sono **input** e **output**. L'oggetto di input viene usato per enumerare le colonne di input e l'output e per impostare i dati di output come risultato dell'attività di elaborazione.

Per l'enumerazione delle colonne di input, si usa il metodo `input.Get`.

```
string column_name = input.Get<string>("column_name");
```

Il parametro per il metodo `input.Get` è una colonna passata come parte della clausola `PRODUCE` dell'istruzione `PROCESS` dello script U-SQL di base. In questo caso è necessario usare il tipo di dati corretto.

Per l'output, usare il metodo `output.Set`.

È importante notare che il producer personalizzato restituisce solo le colonne e i valori definiti con la chiamata al metodo `output.Set`.

```
output.Set<string>("mycolumn", mycolumn);
```

L'output effettivo dell'elaboratore viene attivato chiamando `return output.AsReadOnly();`.

Di seguito è riportato un esempio di elaboratore:

```
[SqlUserDefinedProcessor]
public class FullDescriptionProcessor : IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
     string user = input.Get<string>("user");
     string des = input.Get<string>("des");
     string full_description = user.ToUpper() + "=>" + des;
     output.Set<string>("dt", input.Get<string>("dt"));
     output.Set<string>("full_description", full_description);
     output.Set<Guid>("new_guid", Guid.NewGuid());
     output.Set<Guid>("guid", input.Get<Guid>("guid"));
     return output.AsReadOnly();
 }
}
```

Nello scenario di questo caso d'uso, l'elaboratore genera una nuova colonna denominata "full_description" combinando le colonne esistenti, in questo caso "user" in lettere maiuscole e "des". Rigenera anche un GUID e restituisce il nuovo valore GUID e quello originale.

Come si può notare nell'esempio precedente, è possibile chiamare metodi C# durante la chiamata al metodo `output.Set`.

Di seguito è riportato un esempio di script U-SQL di base che usa un elaboratore personalizzato:

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
     PROCESS @rs0
     PRODUCE dt String,
             full_description String,
             guid Guid,
             new_guid Guid
     USING new USQL_Programmability.FullDescriptionProcessor();

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

## <a name="use-user-defined-appliers"></a>Usare oggetti di applicazione definiti dall'utente
Un oggetto di applicazione definito dall'utente di U-SQL consente di richiamare una funzione C# personalizzata per ogni riga restituita dall'espressione di tabella esterna di una query. L'input di destra viene valutato per ogni riga dell'input di sinistra e le righe prodotte vengono combinate per l'output finale. L'elenco delle colonne prodotte dall'operatore APPLY è la combinazione del set di colonne dell'input di destra e di sinistra.

Un oggetto di applicazione definito dall'utente viene richiamato come parte dell'espressione SELECT di U-SQL.

La chiamata tipica all'oggetto di applicazione definito dall'utente si presenta come segue:

```
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Per altre informazioni sull'uso di oggetti di applicazione in un'espressione SELECT, vedere [U-SQL SELECT Selecting from CROSS APPLY and OUTER APPLY](https://msdn.microsoft.com/library/azure/mt621307.aspx) (Selezione con SELECT di U-SQL da CROSS APPLY e OUTER APPLY).

La definizione della classe base degli oggetti di applicazione definiti dall'utente è la seguente:

```
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

Per definire un oggetto di applicazione definito dall'utente, è necessario creare l'interfaccia `IApplier` con l'attributo [`SqlUserDefinedApplier`], che per la definizione di un oggetto di applicazione definito dall'utente è facoltativo.

```
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
    public ParserApplier()
    {
        …
    }

    public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
    {
        …
    }
}
```

* Apply viene chiamato per ogni riga della tabella esterna. Restituisce il set di righe di output di `IUpdatableRow`.
* La classe del costruttore viene usata per passare parametri all'oggetto di applicazione definito dall'utente.

**SqlUserDefinedApplier** indica che il tipo deve essere registrato come oggetto di applicazione definito dall'utente. Questa classe non può essere ereditata.

**SqlUserDefinedApplier** è **facoltativo** per la definizione di un oggetto di applicazione definito dall'utente.


I principali oggetti di programmabilità sono i seguenti:

```
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

I set di righe di input vengono passati come input `IRow`. Le righe di output vengono generate come interfaccia di output `IUpdatableRow`.

I nomi delle singole colonne possono essere determinati chiamando il metodo dello schema `IRow`.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Per ottenere i valori di dati effettivi da `IRow` in ingresso, si usa il metodo Get() dell'interfaccia `IRow`.

```
mycolumn = row.Get<int>("mycolumn")
```

In alternativa, si usa il nome di colonna dello schema:

```
row.Get<int>(row.Schema[0].Name)
```

I valori di output devono essere impostati con l'output di `IUpdatableRow`:

```
output.Set<int>("mycolumn", mycolumn)
```

È importante comprendere che gli oggetti di applicazione personalizzati restituiscono solo le colonne o i valori definiti con la chiamata al metodo `output.Set`.

L'output effettivo viene attivato chiamando `yield return output.AsReadOnly();`.

I parametri dell'oggetto di applicazione definito dall'utente possono essere passati al costruttore. L'oggetto di applicazione può restituire un numero variabile di colonne da definire durante la chiamata all'oggetto di applicazione nello script U-SQL di base.

```
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

Di seguito è riportato un esempio di oggetto di applicazione definito dall'utente:

```
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
private string parsingPart;

public ParserApplier(string parsingPart)
{
    if (parsingPart.ToUpper().Contains("ALL")
    || parsingPart.ToUpper().Contains("MAKE")
    || parsingPart.ToUpper().Contains("MODEL")
    || parsingPart.ToUpper().Contains("YEAR")
    || parsingPart.ToUpper().Contains("TYPE")
    || parsingPart.ToUpper().Contains("MILLAGE")
    )
    {
    this.parsingPart = parsingPart;
    }
    else
    {
    throw new ArgumentException("Incorrect parameter. Please use: 'ALL[MAKE|MODEL|TYPE|MILLAGE]'");
    }
}

public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
{

    string[] properties = input.Get<string>("properties").Split(',');

    //  only process with correct number of properties
    if (properties.Count() == 5)
    {

    string make = properties[0];
    string model = properties[1];
    string year = properties[2];
    string type = properties[3];
    int millage = -1;

    // Only return millage if it is number, otherwise, -1
    if (!int.TryParse(properties[4], out millage))
    {
        millage = -1;
    }

    if (parsingPart.ToUpper().Contains("MAKE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("make", make);
    if (parsingPart.ToUpper().Contains("MODEL") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("model", model);
    if (parsingPart.ToUpper().Contains("YEAR") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("year", year);
    if (parsingPart.ToUpper().Contains("TYPE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("type", type);
    if (parsingPart.ToUpper().Contains("MILLAGE") || parsingPart.ToUpper().Contains("ALL")) output.Set<int>("millage", millage);
    }
    yield return output.AsReadOnly();            
}
}
```

Di seguito è riportato lo script U-SQL di base per questo oggetto di applicazione definito dall'utente:

```
DECLARE @input_file string = @"c:\usql-programmability\car_fleet.tsv";
DECLARE @output_file string = @"c:\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        stocknumber int,
        vin String,
        properties String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        r.stocknumber,
        r.vin,
        properties.make,
        properties.model,
        properties.year,
        properties.type,
        properties.millage
    FROM @rs0 AS r
    CROSS APPLY
    new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

Nello scenario di questo caso d'uso, l'oggetto di applicazione definito dall'utente funge da parser di valori delimitati da virgole per le proprietà del parco auto. Le righe del file di input si presentano come segue:

```
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Shevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

Si tratta di un normale file con valori delimitati da tabulazioni (TSV) con una colonna di proprietà contenente le proprietà delle automobili, come marca e modello. Tali proprietà devono essere analizzate rispetto alle colonne della tabella. L'oggetto di applicazione specificato consente di generare un numero dinamico di proprietà nel set di righe, in base al parametro passato. È possibile generare tutte le proprietà o solo uno specifico set di proprietà.

    …USQL_Programmability.ParserApplier ("all")
    …USQL_Programmability.ParserApplier ("make")
    …USQL_Programmability.ParserApplier ("make&model")

L'oggetto di applicazione definito dall'utente può essere chiamato come nuova istanza dell'oggetto di applicazione:

```
CROSS APPLY new MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

In alternativa, è possibile usare la chiamata a un metodo factory wrapper:

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

## <a name="use-user-defined-combiners"></a>Usare combinatori definiti dall'utente
Un combinatore definito dall'utente (UDC) consente di combinare le righe dei set di sinistra e di destra, in base a logica personalizzata. Il combinatore definito dall'utente viene usato con l'espressione COMBINE.

Un combinatore viene richiamato con l'espressione COMBINE, che specifica le informazioni necessarie su entrambi i set di righe di input, le colonne di raggruppamento e lo schema dei risultati previsto e informazioni aggiuntive.

Per chiamare un combinatore in uno script U-SQL di base, si usa la sintassi seguente:

```
Combine_Expression :=
    'COMBINE' Combine_Input
    'WITH' Combine_Input
    Join_On_Clause
    Produce_Clause
    [Readonly_Clause]
    [Required_Clause]
    USING_Clause.
```

Per altre informazioni, vedere [COMBINE Expression (U-SQL)](https://msdn.microsoft.com/library/azure/mt621339.aspx) (Espressione COMBINE (U-SQL)).

Per definire un combinatore definito dall'utente, è necessario creare l'interfaccia `ICombiner` con l'attributo [`SqlUserDefinedCombiner`], che per la definizione di un combinatore definito dall'utente è facoltativo.

Definizione della classe `ICombiner` di base:

```
public abstract class ICombiner : IUserDefinedOperator
{
protected ICombiner();
public virtual IEnumerable<IRow> Combine(List<IRowset> inputs,
       IUpdatableRow output);
public abstract IEnumerable<IRow> Combine(IRowset left, IRowset right,
       IUpdatableRow output);
}
```

L'implementazione personalizzata di un'interfaccia `ICombiner` deve contenere una definizione per un override `IEnumerable<IRow>` Combine.

```
[SqlUserDefinedCombiner]
public class MyCombiner : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    …
}
}
```

L'attributo **SqlUserDefinedCombiner** indica che il tipo deve essere registrato come combinatore definito dall'utente. Questa classe non può essere ereditata.

**SqlUserDefinedCombiner** viene usato per definire la proprietà della modalità di combinazione. È un attributo facoltativo per la definizione di un combinatore definito dall'utente.

Modalità     CombinerMode

L'enumerazione CombinerMode può accettare i valori seguenti:

* Full (0): ogni riga di output dipende potenzialmente da tutte le righe di input di sinistra e di destra con lo stesso valore chiave.

* Left (1): ogni riga di output dipende da una singola riga di input di sinistra e potenzialmente da tutte le righe di destra con lo stesso valore chiave.

* Right (2): ogni riga di output dipende da una singola riga di input di destra e potenzialmente da tutte le righe di sinistra con lo stesso valore chiave.

* Inner (3): ogni riga di output dipende da una singola riga di input di sinistra e di destra con lo stesso valore.

Esempio:     [`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`]


I principali oggetti di programmabilità sono i seguenti:

```csharp
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

I set di righe di input vengono passati come tipo di interfaccia `IRowset` a **sinistra** e a **destra**. Entrambi i set di righe devono essere enumerati per l'elaborazione. È possibile enumerare ogni interfaccia una sola volta, quindi deve essere enumerata e memorizzata nella cache, se necessario.

Per la memorizzazione nella cache, è possibile creare un tipo di struttura di memoria List\<T\> come risultato dell'esecuzione di una query LINQ, specificamente List<`IRow`>. Durante l'enumerazione è possibile usare anche il tipo di dati anonimo.

Per altre informazioni su tali query, vedere l'[introduzione alle query LINQ (C#)](https://msdn.microsoft.com/library/bb397906.aspx). Per altre informazioni sull'interfaccia IEnumerable\<T\>, vedere [Interfaccia IEnumerable\<T\>](https://msdn.microsoft.com/library/9eekhta0(v=vs.110).aspx).

Per ottenere i valori di dati effettivi da `IRowset` in ingresso, si usa il metodo Get() dell'interfaccia `IRow`.

```
mycolumn = row.Get<int>("mycolumn")
```

I nomi delle singole colonne possono essere determinati chiamando il metodo dello schema `IRow`.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

In alternativa, è possibile usare il nome di colonna dello schema:

```
c# row.Get<int>(row.Schema[0].Name)
```

L'enumerazione generale con LINQ si presenta come segue:

```
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

Al termine dell'enumerazione di entrambi i set di righe, si scorreranno in ciclo tutte le righe. Per ogni riga del set di sinistra si troveranno tutte le righe che soddisfano la condizione del combinatore.

I valori di output devono essere impostati con l'output di `IUpdatableRow`.

```
output.Set<int>("mycolumn", mycolumn)
```

L'output effettivo viene attivato chiamando `yield return output.AsReadOnly();`.

Di seguito è riportato un esempio di combinatore:

```
[SqlUserDefinedCombiner]
public class CombineSales : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    var internetSales =
    (from row in left.Rows
          select new
          {
              ProductKey = row.Get<int>("ProductKey"),
              OrderDateKey = row.Get<int>("OrderDateKey"),
              SalesAmount = row.Get<decimal>("SalesAmount"),
              TaxAmt = row.Get<decimal>("TaxAmt")
          }).ToList();

    var resellerSales =
    (from row in right.Rows
     select new
     {
     ProductKey = row.Get<int>("ProductKey"),
     OrderDateKey = row.Get<int>("OrderDateKey"),
     SalesAmount = row.Get<decimal>("SalesAmount"),
     TaxAmt = row.Get<decimal>("TaxAmt")
     }).ToList();

    foreach (var row_i in internetSales)
    {
    foreach (var row_r in resellerSales)
    {

        if (
        row_i.OrderDateKey > 0
        && row_i.OrderDateKey < row_r.OrderDateKey
        && row_i.OrderDateKey == 20010701
        && (row_r.SalesAmount + row_r.TaxAmt) > 20000)
        {
        output.Set<int>("OrderDateKey", row_i.OrderDateKey);
        output.Set<int>("ProductKey", row_i.ProductKey);
        output.Set<decimal>("Internet_Sales_Amount", row_i.SalesAmount + row_i.TaxAmt);
        output.Set<decimal>("Reseller_Sales_Amount", row_r.SalesAmount + row_r.TaxAmt);
        }

    }
    }
    yield return output.AsReadOnly();
}
}
```

Nello scenario di questo caso d'uso, si crea un report analitico per il rivenditore. L'obiettivo è trovare tutti i prodotti che costano più di 20.000 dollari e che in un dato intervallo di tempo vengono venduti più velocemente tramite il sito Web che non tramite il normale rivenditore.

Di seguito è riportato lo script U-SQL di base, in cui è possibile confrontare la logica di un JOIN regolare e di un combinatore:

```sql
DECLARE @LocalURI string = @"\usql-programmability\";

DECLARE @input_file_internet_sales string = @LocalURI+"FactInternetSales.txt";
DECLARE @input_file_reseller_sales string = @LocalURI+"FactResellerSales.txt";
DECLARE @output_file1 string = @LocalURI+"output_file1.tsv";
DECLARE @output_file2 string = @LocalURI+"output_file2.tsv";

@fact_internet_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    CustomerKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_internet_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@fact_reseller_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    ResellerKey int ,
    EmployeeKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_reseller_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@rs1 =
SELECT
    fis.OrderDateKey,
    fis.ProductKey,
    fis.SalesAmount+fis.TaxAmt AS Internet_Sales_Amount,
    frs.SalesAmount+frs.TaxAmt AS Reseller_Sales_Amount
FROM @fact_internet_sales AS fis
     INNER JOIN @fact_reseller_sales AS frs
     ON fis.ProductKey == frs.ProductKey
WHERE
    fis.OrderDateKey < frs.OrderDateKey
    AND fis.OrderDateKey == 20010701
    AND frs.SalesAmount+frs.TaxAmt > 20000;

@rs2 =
COMBINE @fact_internet_sales AS fis
WITH @fact_reseller_sales AS frs
ON fis.ProductKey == frs.ProductKey
PRODUCE OrderDateKey int,
        ProductKey int,
        Internet_Sales_Amount decimal,
        Reseller_Sales_Amount decimal
USING new USQL_Programmability.CombineSales();

OUTPUT @rs1 TO @output_file1 USING Outputters.Tsv();
OUTPUT @rs2 TO @output_file2 USING Outputters.Tsv();
```

Un combinatore definito dall'utente può essere chiamato come nuova istanza dell'oggetto di applicazione:

```
USING new MyNameSpace.MyCombiner();
```


In alternativa, è possibile usare la chiamata a un metodo factory wrapper:

```
USING MyNameSpace.MyCombiner();
```

## <a name="use-user-defined-reducers"></a>Usare riduttori definiti dall'utente

U-SQL consente di scrivere riduttori di set di righe personalizzati in C# usando il framework di estendibilità degli operatori definito dall'utente e implementando un'interfaccia IReducer.

Un riduttore definito dall'utente (UDR) può essere usato per eliminare le righe non necessarie durante l'estrazione (importazione) di dati, nonché per modificare e valutare righe e colonne. In base alla logica di programmabilità, può anche definire le righe da estrarre.

Per definire una classe di riduttori definiti dall'utente, è necessario creare un'interfaccia `IReducer` con l'attributo `SqlUserDefinedReducer` facoltativo.

Questa interfaccia di classe deve contenere una definizione per l'override dei set di righe dell'interfaccia `IEnumerable`.

```
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
        …
    }

}
```

L'attributo **SqlUserDefinedReducer** indica che il tipo deve essere registrato come riduttore definito dall'utente. Questa classe non può essere ereditata.
**SqlUserDefinedReducer** è un attributo facoltativo per la definizione di un riduttore definito dall'utente. Viene usato per definire la proprietà IsRecursive.

* bool     IsRecursive    
* **true** = indica se questo Reducer è associativo e commutativo

I principali oggetti di programmabilità sono **input** e **output**. L'oggetto di input viene usato per enumerare le righe di input. L'output viene usato per impostare le righe di output come risultato dell'attività di riduzione.

Per l'enumerazione delle righe di input, si usa il metodo `Row.Get`.

```
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

Il parametro per il metodo `Row.Get` è una colonna passata come parte della classe `PRODUCE` dell'istruzione `REDUCE` dello script U-SQL di base. Anche qui occorre usare il tipo di dati corretto.

Per l'output, usare il metodo `output.Set`.

È importante comprendere che il riduttore personalizzato restituisce solo i valori definiti con la chiamata al metodo `output.Set`.

```
output.Set<string>("mycolumn", guid);
```

L'output effettivo del riduttore viene attivato chiamando `yield return output.AsReadOnly();`.

Di seguito è riportato un esempio di riduttore:

```
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
    string guid;
    DateTime dt;
    string user;
    string des;

    foreach (IRow row in input.Rows)
    {
        guid = row.Get<string>("guid");
        dt = row.Get<DateTime>("dt");
        user = row.Get<string>("user");
        des = row.Get<string>("des");

        if (user.Length > 0)
        {
        output.Set<string>("guid", guid);
        output.Set<DateTime>("dt", dt);
        output.Set<string>("user", user);
        output.Set<string>("des", des);

        yield return output.AsReadOnly();
        }
    }
    }

}
```

Nello scenario di questo caso d'uso, il riduttore ignora le righe con nome utente vuoto. Per ogni riga nel set, legge ogni colonna obbligatoria e quindi valuta la lunghezza del nome utente. Restituisce la riga effettiva solo se il valore del nome utente ha una lunghezza superiore a 0.

Di seguito è riportato uno script U-SQL di base che usa un riduttore personalizzato:

```
DECLARE @input_file string = @"\usql-programmability\input_file_reducer.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    REDUCE @rs0 PRESORT guid
    ON guid  
    PRODUCE guid string, dt DateTime, user String, des String
    USING new USQL_Programmability.EmptyUserReducer();

@rs2 =
    SELECT guid AS start_id,
           dt AS start_time,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS start_fiscalperiod,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```
