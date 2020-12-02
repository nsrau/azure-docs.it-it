---
title: Guida alla programmabilità del combinatore definito dall'utente U-SQL per Azure Data Lake
description: Informazioni sulla guida di programmabilità di U-SQL UDO-combinatore definito dall'utente.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: a6c560cf4ec11197183711656d69024591e7008c
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512601"
---
# <a name="use-user-defined-combiner"></a>USA combinatore definito dall'utente

## <a name="u-sql-udo-user-defined-combiner"></a>UDO U-SQL: combinatore definito dall'utente
Un combinatore definito dall'utente (UDC) consente di combinare le righe dei set di sinistra e di destra, in base a logica personalizzata. Il combinatore definito dall'utente viene usato con l'espressione COMBINE.

## <a name="how-to-define-and-use-user-defined-combiner"></a>Come definire e usare il combinatore definito dall'utente

Un combinatore viene richiamato con l'espressione COMBINE, che specifica le informazioni necessarie su entrambi i set di righe di input, le colonne di raggruppamento e lo schema dei risultati previsto e informazioni aggiuntive.

Per chiamare un combinatore in uno script U-SQL di base, si usa la sintassi seguente:

```usql
Combine_Expression :=
    'COMBINE' Combine_Input
    'WITH' Combine_Input
    Join_On_Clause
    Produce_Clause
    [Readonly_Clause]
    [Required_Clause]
    USING_Clause.
```

Per altre informazioni, vedere [COMBINE Expression (U-SQL)](/u-sql/statements-and-expressions/combine-expression) (Espressione COMBINE (U-SQL)).

Per definire un combinatore definito dall'utente, è necessario creare l'interfaccia `ICombiner` con l'attributo [`SqlUserDefinedCombiner`], che per la definizione di un combinatore definito dall'utente è facoltativo.

Definizione della classe `ICombiner` di base:

```csharp
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

```csharp
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

L'attributo **SqlUserDefinedCombiner** indica che il tipo deve essere registrato come combinatore definito dall'utente. La classe non può essere ereditata.

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

I set di righe di input **left** vengono passati come **right** `IRowset` tipo di interfaccia a sinistra e a destra. Entrambi i set di righe devono essere enumerati per l'elaborazione. È possibile enumerare ogni interfaccia una sola volta, quindi deve essere enumerata e memorizzata nella cache, se necessario.

Ai fini della memorizzazione nella cache, è possibile creare un \<T\> tipo di elenco di struttura di memoria come risultato dell'esecuzione di una query LINQ, in particolare l'elenco<`IRow`>. Durante l'enumerazione è possibile usare anche il tipo di dati anonimo.

Per ulteriori informazioni sulle query LINQ e sull' [ \<T\> interfaccia IEnumerable](/dotnet/api/system.collections.generic.ienumerable-1) , vedere [Introduzione alle query LINQ (C#)](/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries) \<T\> .

Per ottenere i valori di dati effettivi da `IRowset` in ingresso, si usa il metodo Get() dell'interfaccia `IRow`.

```csharp
mycolumn = row.Get<int>("mycolumn")
```

I nomi delle singole colonne possono essere determinati chiamando il metodo dello schema `IRow`.

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

In alternativa, è possibile usare il nome di colonna dello schema:

```csharp
c# row.Get<int>(row.Schema[0].Name)
```

L'enumerazione generale con LINQ si presenta come segue:

```csharp
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

Al termine dell'enumerazione di entrambi i set di righe, si scorreranno in ciclo tutte le righe. Per ogni riga del set di sinistra si troveranno tutte le righe che soddisfano la condizione del combinatore.

I valori di output devono essere impostati con l'output di `IUpdatableRow`.

```csharp
output.Set<int>("mycolumn", mycolumn)
```

L'output effettivo viene attivato chiamando `yield return output.AsReadOnly();`.

Di seguito è riportato un esempio di combinatore:

```csharp
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

```csharp
USING new MyNameSpace.MyCombiner();
```


In alternativa, è possibile usare la chiamata a un metodo factory wrapper:

```csharp
USING MyNameSpace.MyCombiner();
```

## <a name="next-steps"></a>Passaggi successivi
* [Guida alla programmabilità di U-SQL-Panoramica](data-lake-analytics-u-sql-programmability-guide.md)
* [Guida alla programmabilità di U-SQL-UDT e UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)