---
title: Guida alla programmabilità dell'estrattore definita dall'utente U-SQL per Azure Data Lake
description: Informazioni sulla guida di programmabilità di U-SQL UDO-Extractor definito dall'utente.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: a3613c2b95f13e6bbaaf570f522ad1f7b7edd756
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512590"
---
# <a name="use-user-defined-extractor"></a>USA estrattore definito dall'utente

## <a name="u-sql-udo-user-defined-extractor"></a>U-SQL UDO: Extractor definito dall'utente
U-SQL consente di importare dati esterni con un'istruzione EXTRACT. L'istruzione EXTRACT consente di usare estrattori UDO predefiniti.  

* *Extractors.Text()*: consente di estrarre da file di testo delimitati di varie codifiche.

* *Extractors.Csv()*: consente di estrarre da file di testo delimitati da virgole (CSV) di varie codifiche.

* *Extractors.Tsv()*: consente di estrarre da file di testo delimitati da tabulazioni (TSV) di varie codifiche.

Può essere utile per sviluppare un estrattore personalizzato. Questo può essere opportuno durante un'importazione di dati, se si vuole eseguire una o più delle attività seguenti:

* Modificare i dati di input suddividendo le colonne e modificando singoli valori. Per la combinazione di colonne è preferibile la funzionalità PROCESSOR.
* Analizzare dati non strutturati, come pagine Web e messaggi di posta elettronica, o dati parzialmente non strutturati, ad esempio XML/JSON.
* Analizzare dati in una codifica non supportata.

## <a name="how-to-define-and-use-user-defined-extractor"></a>Come definire e usare un estrattore definito dall'utente
Per definire un estrattore definito dall'utente (UDE), è necessario creare un'interfaccia `IExtractor`. Tutti i parametri di input per l'estrattore, come i delimitatori di riga/colonna e la codifica, devono essere definiti nel costruttore della classe. L'interfaccia `IExtractor` deve contenere anche una definizione per l'override `IEnumerable<IRow>`, come illustrato di seguito:

```csharp
[SqlUserDefinedExtractor]
public class SampleExtractor : IExtractor
{
     public SampleExtractor(string row_delimiter, char col_delimiter)
     { … }

     public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
     { … }
}
```

L'attributo **SqlUserDefinedExtractor** indica che il tipo deve essere registrato come estrattore definito dall'utente. La classe non può essere ereditata.

SqlUserDefinedExtractor è un attributo facoltativo per la definizione di UDE, che consente di definire la proprietà AtomicFileProcessing dell'oggetto UDE.

* bool     AtomicFileProcessing   

* **true** indica che l'estrattore richiede file di input atomici (JSON, XML e così via)
* **false** indica che l'estrattore può gestire file suddivisi/distribuiti (CSV, SEQ e così via)

I principali oggetti di programmabilità UDE sono **input** e **output**. L'oggetto di input viene usato per enumerare i dati di input come `IUnstructuredReader`. L'oggetto di output viene usato per impostare i dati di output come risultato dell'attività di estrazione.

I dati di input sono accessibili tramite `System.IO.Stream` e `System.IO.StreamReader`.

Per l'enumerazione delle colonne di input, per prima cosa si suddivide il flusso di input con un delimitatore di riga.

```csharp
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

Quindi, la riga di input viene ulteriormente suddivisa nelle parti di una colonna.

```csharp
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

```csharp
output.Set<string>(count, part);
```

L'output effettivo dell'estrattore viene attivato chiamando `yield return output.AsReadOnly();`.

Di seguito è riportato l'esempio di estrattore:

```csharp
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

```usql
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


## <a name="next-steps"></a>Passaggi successivi
* [Guida alla programmabilità di U-SQL-Panoramica](data-lake-analytics-u-sql-programmability-guide.md)
* [Guida alla programmabilità di U-SQL-UDT e UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)