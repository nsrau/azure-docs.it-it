---
title: Guida alla programmabilità dell'outputter definito dall'utente U-SQL per Azure Data Lake
description: Informazioni sulla guida di programmabilità di U-SQL UDO definito dall'utente outputter.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 56b104b5cc8f8923445455c71fe2418e39539b8e
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512567"
---
# <a name="use-user-defined-outputter"></a>USA outputter definito dall'utente

## <a name="u-sql-udo-user-defined-outputter"></a>U-SQL UDO: outputter definito dall'utente
L'outputter definito dall'utente è un altro oggetto definito dall'utente di U-SQL che consente di estendere una funzionalità predefinita di U-SQL. Come per l'estrattore, esistono diversi outputter integrati.

* *Outputters.Text()*: scrive i dati in file di testo delimitati di codifiche diverse.
* *Outputters.Csv()*: scrive i dati in file di testo delimitati da virgole (CSV) di codifiche diverse.
* *Outputters.Tsv()*: scrive i dati in file di testo delimitati da tabulazioni (TSV) di codifiche diverse.

L'outputter personalizzato consente di scrivere i dati in un formato definito personalizzato. Questo può essere utile per le attività seguenti:

* Scrittura di i dati in file non strutturati o semistrutturati
* Scrittura di dati in codifiche non supportate
* Modifica dei dati di output o aggiunta di attributi personalizzati

## <a name="how-to-define-and-use-user-defined-outputter"></a>Come definire e usare outputter definito dall'utente
Per definire outputter definiti dall'utente, è necessario creare l'interfaccia `IOutputter`.

Di seguito è riportata l'implementazione della classe `IOutputter` di base:

```csharp
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

Tutti i parametri di input per l'outputter, come i delimitatori di riga/colonna, la codifica e così via, devono essere definiti nel costruttore della classe. L'interfaccia `IOutputter` deve contenere anche una definizione per l'override `void Output`. L'attributo `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` può essere facoltativamente impostato per l'elaborazione di file atomici. Per altre informazioni, vedere i dettagli riportati di seguito.

```csharp
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

L'attributo **SqlUserDefinedOutputter** indica che il tipo deve essere registrato come outputter definito dall'utente. La classe non può essere ereditata.

SqlUserDefinedOutputter è un attributo facoltativo per la definizione di un outputter definito dall'utente. Viene usato per definire la proprietà AtomicFileProcessing.

* bool     AtomicFileProcessing   

* **true** indica che l'outputter richiede file di output atomici (JSON, XML e così via)
* **false** indica che l'outputter può gestire file suddivisi/distribuiti (CSV, SEQ e così via)

I principali oggetti di programmabilità sono **row** e **output**. L'oggetto **row** viene usato per enumerare i dati di output come interfaccia `IRow`. **Output** viene usato per impostare i dati di output sul file di destinazione.

I dati di output sono accessibili tramite l'interfaccia `IRow`. I dati di output vengono trasmessi una riga alla volta.

I singoli valori vengono enumerati chiamando il metodo Get dell'interfaccia IRow:

```csharp
row.Get<string>("column_name")
```

I nomi delle singole colonne possono essere determinati chiamando `row.Schema`:

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Questo approccio consente di compilare un outputter flessibile per qualsiasi schema di metadati.

I dati di output vengono scritti in un file usando `System.IO.StreamWriter`. Il parametro di flusso viene impostato su `output.BaseStream` come parte di `IUnstructuredWriter output`.

Si noti che è importante scaricare il buffer dei dati nel file dopo ogni iterazione di riga. L'oggetto `StreamWriter`, inoltre, deve essere usato con l'attributo Disposable abilitato (impostazione predefinita) e con la parola chiave **using**:

```csharp
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

In alternativa, chiamare il metodo Flush() in modo esplicito dopo ogni iterazione, come illustrato nell'esempio riportato di seguito.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>Impostare intestazioni e piè di pagina per l'outputter definito dall'utente
Per impostare un'intestazione, usare il flusso di esecuzione dell'iterazione singola.

```csharp
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

```csharp
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
    public override void Close()
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

```usql
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

```usql
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="next-steps"></a>Passaggi successivi
* [Guida alla programmabilità di U-SQL-Panoramica](data-lake-analytics-u-sql-programmability-guide.md)
* [Guida alla programmabilità di U-SQL-UDT e UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)