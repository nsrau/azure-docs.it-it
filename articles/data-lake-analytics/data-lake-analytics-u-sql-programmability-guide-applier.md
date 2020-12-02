---
title: Guida alla programmabilità di applicazione definita dall'utente U-SQL per Azure Data Lake
description: Informazioni sulla guida di programmabilità di U-SQL UDO-oggetto di applicazione definito dall'utente.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 0842a2cfa021ef8ea45c19ec885c7dec371730de
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512572"
---
# <a name="use-user-defined-applier"></a>USA oggetto di applicazione definito dall'utente 

## <a name="u-sql-udo-user-defined-applier"></a>U-SQL UDO: oggetto di applicazione definito dall'utente
Un oggetto di applicazione definito dall'utente di U-SQL consente di richiamare una funzione C# personalizzata per ogni riga restituita dall'espressione di tabella esterna di una query. L'input di destra viene valutato per ogni riga dell'input di sinistra e le righe prodotte vengono combinate per l'output finale. L'elenco delle colonne prodotte dall'operatore APPLY è la combinazione del set di colonne dell'input di destra e di sinistra.


## <a name="how-to-define-and-use-user-defined-applier"></a>Come definire e usare l'oggetto di applicazione definito dall'utente
Un oggetto di applicazione definito dall'utente viene richiamato come parte dell'espressione SELECT di U-SQL.

La chiamata tipica all'oggetto di applicazione definito dall'utente si presenta come segue:

```usql
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Per altre informazioni sull'uso di oggetti di applicazione in un'espressione SELECT, vedere [U-SQL SELECT Selecting from CROSS APPLY and OUTER APPLY](/u-sql/statements-and-expressions/select/from/select-selecting-from-cross-apply-and-outer-apply) (Selezione con SELECT di U-SQL da CROSS APPLY e OUTER APPLY).

La definizione della classe base degli oggetti di applicazione definiti dall'utente è la seguente:

```csharp
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

Per definire un oggetto di applicazione definito dall'utente, è necessario creare l'interfaccia `IApplier` con l'attributo [`SqlUserDefinedApplier`], che per la definizione di un oggetto di applicazione definito dall'utente è facoltativo.

```csharp
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

**SqlUserDefinedApplier** indica che il tipo deve essere registrato come oggetto di applicazione definito dall'utente. La classe non può essere ereditata.

**SqlUserDefinedApplier** è **facoltativo** per la definizione di un oggetto di applicazione definito dall'utente.


I principali oggetti di programmabilità sono i seguenti:

```csharp
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

I set di righe di input vengono passati come input `IRow`. Le righe di output vengono generate come interfaccia di output `IUpdatableRow`.

I nomi delle singole colonne possono essere determinati chiamando il metodo dello schema `IRow`.

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Per ottenere i valori di dati effettivi da `IRow` in ingresso, si usa il metodo Get() dell'interfaccia `IRow`.

```csharp
mycolumn = row.Get<int>("mycolumn")
```

In alternativa, si usa il nome di colonna dello schema:

```csharp
row.Get<int>(row.Schema[0].Name)
```

I valori di output devono essere impostati con l'output di `IUpdatableRow`:

```csharp
output.Set<int>("mycolumn", mycolumn)
```

È importante comprendere che gli oggetti di applicazione personalizzati restituiscono solo le colonne o i valori definiti con la chiamata al metodo `output.Set`.

L'output effettivo viene attivato chiamando `yield return output.AsReadOnly();`.

I parametri dell'oggetto di applicazione definito dall'utente possono essere passati al costruttore. L'oggetto di applicazione può restituire un numero variabile di colonne da definire durante la chiamata all'oggetto di applicazione nello script U-SQL di base.

```csharp
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

Di seguito è riportato un esempio di oggetto di applicazione definito dall'utente:

```csharp
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

```usql
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

```text
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Chevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

Si tratta di un normale file con valori delimitati da tabulazioni (TSV) con una colonna di proprietà contenente le proprietà delle automobili, come marca e modello. Tali proprietà devono essere analizzate rispetto alle colonne della tabella. L'oggetto di applicazione specificato consente di generare un numero dinamico di proprietà nel set di righe, in base al parametro passato. È possibile generare tutte le proprietà o solo uno specifico set di proprietà.

```text
...USQL_Programmability.ParserApplier ("all")
...USQL_Programmability.ParserApplier ("make")
...USQL_Programmability.ParserApplier ("make&model")
```

L'oggetto di applicazione definito dall'utente può essere chiamato come nuova istanza dell'oggetto di applicazione:

```usql
CROSS APPLY new MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```

In alternativa, è possibile usare la chiamata a un metodo factory wrapper:

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```


## <a name="next-steps"></a>Passaggi successivi
* [Guida alla programmabilità di U-SQL-Panoramica](data-lake-analytics-u-sql-programmability-guide.md)
* [Guida alla programmabilità di U-SQL-UDT e UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)