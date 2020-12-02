---
title: Guida alla programmabilità del processore definita dall'utente U-SQL per Azure Data Lake
description: Informazioni sulla guida di programmabilità di U-SQL UDO-processore definito dall'utente.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 6ff45c577e94a8c63bd7cb1e6603e4d5519af5c6
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512598"
---
# <a name="use-user-defined-processor"></a>Usa processore definito dall'utente

## <a name="u-sql-udo-user-defined-processor"></a>U-SQL UDO: processore definito dall'utente
Un elaboratore definito dall'utente (UDP) è un tipo di oggetto definito dall'utente di U-SQL che consente di elaborare le righe in ingresso applicando funzionalità di programmabilità. Un elaboratore definito dall'utente consente di combinare colonne, modificare valori e aggiungere nuove colonne, se necessario. Essenzialmente, consente di elaborare un set di righe per produrre gli elementi dati necessari.

## <a name="how-to-define-and-use-user-defined-processor"></a>Come definire e usare un processore definito dall'utente
Per definire un elaboratore definito dall'utente, è necessario creare un'interfaccia `IProcessor` con l'attributo `SqlUserDefinedProcessor`, che per gli elaboratori definiti dall'utente è facoltativo.

L'interfaccia deve contenere la definizione per l'override dei set di righe dell'interfaccia `IRow`, come illustrato nell'esempio seguente:

```csharp
[SqlUserDefinedProcessor]
public class MyProcessor: IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
        …
 }
}
```

**SqlUserDefinedProcessor** indica che il tipo deve essere registrato come Processor definito dall'utente. La classe non può essere ereditata.

Per la definizione degli elaboratori definiti dall'utente, l'attributo SqlUserDefinedProcessor è **facoltativo**.

I principali oggetti di programmabilità sono **input** e **output**. L'oggetto di input viene usato per enumerare le colonne di input e l'output e per impostare i dati di output come risultato dell'attività di elaborazione.

Per l'enumerazione delle colonne di input, si usa il metodo `input.Get`.

```csharp
string column_name = input.Get<string>("column_name");
```

Il parametro per il metodo `input.Get` è una colonna passata come parte della clausola `PRODUCE` dell'istruzione `PROCESS` dello script U-SQL di base. In questo caso è necessario usare il tipo di dati corretto.

Per l'output, usare il metodo `output.Set`.

È importante notare che il producer personalizzato restituisce solo le colonne e i valori definiti con la chiamata al metodo `output.Set`.

```csharp
output.Set<string>("mycolumn", mycolumn);
```

L'output effettivo dell'elaboratore viene attivato chiamando `return output.AsReadOnly();`.

Di seguito è riportato un esempio di elaboratore:

```csharp
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

```usql
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


## <a name="next-steps"></a>Passaggi successivi
* [Guida alla programmabilità di U-SQL-Panoramica](data-lake-analytics-u-sql-programmability-guide.md)
* [Guida alla programmabilità di U-SQL-UDT e UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)