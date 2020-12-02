---
title: U-SQL UDT e guida alla programmabilità di UDAGG per Azure Data Lake
description: Informazioni sul tipo definito dall'utente U-SQL e la programmabilità UDAGG in Azure Data Lake Analytics per consentire la creazione di uno script USQL efficace.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: bf2e1e1bc30aeb3306d0a643eca4725d8765edac
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512614"
---
# <a name="u-sql-programmability-guide---udt-and-udagg"></a>Guida alla programmabilità di U-SQL-UDT e UDAGG



## <a name="use-user-defined-types-udt"></a>Usare tipi definiti dall'utente (UDT)
I tipi definiti dall'utente (UDT) sono un'altra funzionalità di programmabilità di U-SQL. L'UDT U-SQL funziona come un normale tipo definito dall'utente C#. C# è un linguaggio fortemente tipizzato che consente l'uso di tipi incorporati e personalizzati definiti dall'utente.

U-SQL non può serializzare o deserializzare implicitamente tipi definiti dall'utente arbitrari quando il tipo definito dall'utente viene passato tra i vertici nei set di righe. Di conseguenza, l'utente deve specificare un formattatore esplicito usando l'interfaccia IFormatter. Questo fornisce a U-SQL i metodi di serializzazione e deserializzazione per il tipo definito dall'utente.

> [!NOTE]
> Gli outputter e gli estrattori predefiniti di U-SQL non possono attualmente serializzare o deserializzare i dati UDT da o verso i file anche con l'impostazione di IFormatter. Di conseguenza, quando si scrivono dati UDT in un file con l'istruzione OUTPUT o si leggono tali dati con un estrattore, è necessario passare i dati come stringa o matrice di byte. Si chiama quindi in modo esplicito il codice di serializzazione o deserializzazione, ossia il metodo ToString() del tipo definito dall'utente. Gli outputter e gli estrattori definiti dall'utente, invece, possono leggere e scrivere i tipi definiti dall'utente.

Se si prova a usare il tipo definito dall'utente in EXTRACTOR o OUTPUTTER, fuori dall'istruzione SELECT precedente, come illustrato di seguito:

```usql
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

viene visualizzato l'errore seguente:

```output
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

```output
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

1. Aggiungere gli spazi dei nomi seguenti:

```csharp
using Microsoft.Analytics.Interfaces
using System.IO;
```

2. Aggiungere `Microsoft.Analytics.Interfaces`, obbligatorio per le interfacce per i tipi definiti dall'utente. Per definire l'interfaccia IFormatter potrebbe essere necessario anche `System.IO`.

3. Definire un tipo definito dall'utente con l'attributo SqlUserDefinedType.

**SqlUserDefinedType** è usato per contrassegnare la definizione di un tipo in un assembly come tipo definito dall'utente (UDT) in U-SQL. Le proprietà dell'attributo corrispondono alle caratteristiche fisiche dell'UDT. La classe non può essere ereditata.

SqlUserDefinedType è un attributo obbligatorio per la definizione dell'UDT.

Il costruttore della classe:  

* SqlUserDefinedTypeAttribute (formattatore di tipo)

* Formattatore di tipo: parametro obbligatorio per definire un formattatore UDT. Nello specifico, qui deve essere passato il tipo dell'interfaccia `IFormatter`.

```csharp
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* Un tipo definito dall'utente richiede in genere anche la definizione dell'interfaccia IFormatter, come illustrato nell'esempio seguente:

```csharp
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

L' `IFormatter` interfaccia serializza e deserializza un oggetto grafico con il tipo radice di \<typeparamref name="T"> .

\<typeparam name="T">Tipo radice per l'oggetto grafico da serializzare e deserializzare.

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

```csharp
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

```csharp
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

```usql
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

```csharp
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
        }        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
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

**SqlUserDefinedAggregate** indica che il tipo deve essere registrato come aggregazione definita dall'utente. La classe non può essere ereditata.

L'attributo SqlUserDefinedType è **facoltativo** per la definizione UDAGG.


La classe base consente di passare tre parametri astratti: due come parametri di input e uno come risultato. I tipi di dati sono variabili e devono essere definiti quando viene ereditata la classe.

```csharp
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

```csharp
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: primo parametro per Accumulate
* T2: secondo parametro da accumulare
* TResult: tipo restituito di Terminate

Ad esempio:

```csharp
public class GuidAggregate : IAggregate<string, int, int>
```

o

```csharp
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>Usare aggregazioni definite dall'utente in U-SQL
Per usare un'aggregazione definita dall'utente, per prima cosa è necessario definirla nel code-behind oppure farvi riferimento dalla DLL di programmabilità esistente, come descritto in precedenza.

Usare quindi la sintassi seguente:

```csharp
AGG<UDAGG_functionname>(param1,param2)
```

Di seguito è riportato un esempio di aggregazione definita dall'utente:

```csharp
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

```usql
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

## <a name="next-steps"></a>Passaggi successivi
* [Guida alla programmabilità di U-SQL-Panoramica](data-lake-analytics-u-sql-programmability-guide.md)
* [Guida alla programmabilità di U-SQL-UDO](data-lake-analytics-u-sql-programmability-guide-UDO.md)
