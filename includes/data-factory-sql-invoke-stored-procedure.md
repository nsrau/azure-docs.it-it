## Richiamo delle stored procedure per SQL Sink
Quando si copiano dati in SQL Server o Azure SQL/SQL Server Database, una stored procedure di un utente specificato può essere configurata e richiamata con parametri aggiuntivi.

Una stored procedure può essere utilizzata quando i meccanismi di copia predefiniti non possono essere utilizzati. In genere viene utilizzato quando un'elaborazione supplementare (unione colonne, ricerca di valori aggiuntivi, l'inserimento in più tabelle...), deve essere eseguita prima dell'inserimento finale dei dati di origine nella tabella di destinazione.

È possibile richiamare una stored procedure di scelta. Nell'esempio seguente viene illustrato come utilizzare una stored procedure per eseguire un semplice inserimento in una tabella nel database.

**Set di dati di output**

In questo esempio, l’elemento tipo è impostato su: SqlServerTable. Impostarlo su AzureSqlTable da utilizzare con un database SQL Azure.

    {
      "name": "SqlOutput",
      "properties": {
        "type": "SqlServerTable",
        "linkedServiceName": "SqlLinkedService",
        "typeProperties": {
          "tableName": "Marketing"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

Definire la sezione SqlSink nel file JSON dell'attività di copia come indicato di seguito. Per chiamare una stored procedure durante l'inserimento dei dati sono necessarie entrambe le proprietà SqlWriterStoredProcedureName e SqlWriterTableType.

    "sink":
    {
        "type": "SqlSink",
        "SqlWriterTableType": "MarketingType",
        "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
        "storedProcedureParameters":
                {
                    "stringData": 
                    {
                        "value": "str1"     
                    }
                }
    }

Nel database definire la stored procedure con lo stesso nome di SqlWriterStoredProcedureName, che gestisce i dati di input dell'origine specificata e li inserisce nella tabella di output. Si noti che il nome di parametro della stored procedure deve essere identico al tableName definito nel file JSON della tabella.

    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
    AS
    BEGIN
        DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
        INSERT [dbo].[Marketing](ProfileID, State)
        SELECT * FROM @Marketing
    END

Nel database definire il tipo di tabella con lo stesso nome di SqlWriterTableType. Si noti che lo schema del tipo di tabella deve essere identico allo schema restituito dai dati di input.

    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL
    )

La funzionalità di stored procedure sfrutta i [parametri valutati a livello di tabella](https://msdn.microsoft.com/library/bb675163.aspx).

<!---HONumber=AcomDC_0803_2016-->