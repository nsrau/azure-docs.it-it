---
title: Guida alla programmabilità del riduttore definito dall'utente U-SQL per Azure Data Lake
description: Informazioni sulla guida di programmabilità di U-SQL UDO-riduttore definito dall'utente.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 52d44685678c3e89dc820042a7925d284500cef8
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512566"
---
# <a name="use-user-defined-reducer"></a>USA riduttore definito dall'utente

## <a name="u-sql-udo-user-defined-reducer"></a>U-SQL UDO: Reducer definito dall'utente

U-SQL consente di scrivere riduttori di set di righe personalizzati in C# usando il framework di estendibilità degli operatori definito dall'utente e implementando un'interfaccia IReducer.

Un riduttore definito dall'utente (UDR) può essere usato per eliminare le righe non necessarie durante l'estrazione (importazione) di dati, nonché per modificare e valutare righe e colonne. In base alla logica di programmabilità, può anche definire le righe da estrarre.

## <a name="how-to-define-and-use-user-defined-reducer"></a>Come definire e usare il riduttore definito dall'utente
Per definire una classe di riduttori definiti dall'utente, è necessario creare un'interfaccia `IReducer` con l'attributo `SqlUserDefinedReducer` facoltativo.

Questa interfaccia di classe deve contenere una definizione per l'override dei set di righe dell'interfaccia `IEnumerable`.

```csharp
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
        …
    }

}
```

L'attributo **SqlUserDefinedReducer** indica che il tipo deve essere registrato come riduttore definito dall'utente. La classe non può essere ereditata.
**SqlUserDefinedReducer** è un attributo facoltativo per la definizione di un riduttore definito dall'utente. Viene usato per definire la proprietà IsRecursive.

* bool     IsRecursive    
* **true** = indica se questo Reducer è associativo e commutativo

I principali oggetti di programmabilità sono **input** e **output**. L'oggetto di input viene usato per enumerare le righe di input. L'output viene usato per impostare le righe di output come risultato dell'attività di riduzione.

Per l'enumerazione delle righe di input, si usa il metodo `Row.Get`.

```csharp
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

Il parametro per il metodo `Row.Get` è una colonna passata come parte della classe `PRODUCE` dell'istruzione `REDUCE` dello script U-SQL di base. Anche qui occorre usare il tipo di dati corretto.

Per l'output, usare il metodo `output.Set`.

È importante comprendere che il riduttore personalizzato restituisce solo i valori definiti con la chiamata al metodo `output.Set`.

```csharp
output.Set<string>("mycolumn", guid);
```

L'output effettivo del riduttore viene attivato chiamando `yield return output.AsReadOnly();`.

Di seguito è riportato un esempio di riduttore:

```csharp
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

```usql
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

## <a name="next-steps"></a>Passaggi successivi
* [Guida alla programmabilità di U-SQL-Panoramica](data-lake-analytics-u-sql-programmability-guide.md)
* [Guida alla programmabilità di U-SQL-UDT e UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)