## <a name="column-mapping-with-translator-rules"></a>Mapping di colonne con regole di conversione
Mapping di colonne utilizzabile per specificare come le colonne specificate nella "struttura" di mapping della tabella di origine alle colonne specificate nella "struttura" della tabella di sink. La proprietà **columnMapping** è disponibile nella sezione **typeProperties** dell'attività di copia.

Il mapping di colonne supporta gli scenari seguenti:

* Tutte le colonne nella tabella di origine “struttura” vengono mappate a tutte le colonne nella tabella di sink “struttura”.
* Un subset delle colonne nella tabella di origine "struttura" viene mappato a tutte le colonne alla tabella di sink "struttura".

Le seguenti sono condizioni di errore e genereranno un'eccezione:

* Un numero inferiore o superiore di colonne nella "struttura" della tabella di sink di quanto specificato nel mapping.
* Mapping duplicato.
* Il risultato della query SQL non ha un nome colonna specificato nel mapping.

## <a name="column-mapping-samples"></a>Esempi di mapping di colonne
> [!NOTE]
> Gli esempi che seguono sono per SQL Azure e Azure Blob, ma sono applicabili per qualsiasi archivio dati che supporti set di dati rettangolari. È necessario modificare i set di dati e le definizioni dei servizi collegati negli esempi riportati di seguito per puntare a dati nell'origine dati pertinente. 
> 
> 

### <a name="sample-1-column-mapping-from-azure-sql-to-azure-blob"></a>Esempio 1: mapping di colonne da Azure SQL al BLOB di Azure
In questo esempio, la tabella di input ha una struttura che punta a una tabella SQL in un database SQL di Azure.

    {
        "name": "AzureSQLInput",
        "properties": {
            "structure": 
             [
               { "name": "userid"},
               { "name": "name"},
               { "name": "group"}
             ],
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }

In questo esempio, la tabella di output ha una struttura che punta a un BLOB in un'archiviazione BLOB di Azure.

    {
        "name": "AzureBlobOutput",
        "properties":
        {
             "structure": 
              [
                    { "name": "myuserid"},
                    { "name": "myname" },
                    { "name": "mygroup"}
              ],
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/myfolder",
                "fileName":"myfile.csv",
                "format":
                {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability":
            {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }

Il JSON per l'attività è illustrato di seguito. Le colonne dell'origine vengono mappate alle colonne nel sink (**columnMappings**) usando la proprietà **Translator**.

    {
        "name": "CopyActivity",
        "description": "description", 
        "type": "Copy",
        "inputs":  [ { "name": "AzureSQLInput"  } ],
        "outputs":  [ { "name": "AzureBlobOutput" } ],
        "typeProperties":    {
            "source":
            {
                "type": "SqlSource"
            },
            "sink":
            {
                "type": "BlobSink"
            },
            "translator": 
            {
                "type": "TabularTranslator",
                "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
            }
        },
       "scheduler": {
              "frequency": "Hour",
              "interval": 1
            }
    }

**Flusso del mapping di colonne:**

![Flusso del mapping di colonne](./media/data-factory-data-stores-with-rectangular-tables/column-mapping-flow.png)

### <a name="sample-2-column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>Esempio 2: mapping di colonne con query SQL da Azure SQL al BLOB di Azure
In questo esempio, una query SQL viene utilizzata per estrarre dati da Azure SQL invece di specificare semplicemente il nome della tabella e i nomi delle colonne nella sezione "struttura". 

    {
        "name": "CopyActivity",
        "description": "description", 
        "type": "CopyActivity",
        "inputs":  [ { "name": " AzureSQLInput"  } ],
        "outputs":  [ { "name": " AzureBlobOutput" } ],
        "typeProperties":
        {
            "source":
            {
                "type": "SqlSource",
                "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
            },
            "sink":
            {
                "type": "BlobSink"
            },
            "Translator": 
            {
                "type": "TabularTranslator",
                "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
            }
        },
        "scheduler": {
              "frequency": "Hour",
              "interval": 1
            }
    }

In questo caso, i risultati della query vengono prima mappati alle colonne specificate in "struttura" di origine. Successivamente, le colonne di origine "struttura" vengono mappate alle colonne nel sink "struttura" con le regole specificate nel columnMappings.  Si supponga che la query restituisca 5 colonne, altre due colonne, poi quelle specificati nella "struttura" di origine.

**Flusso del mapping di colonne**

![Flusso del mapping di colonne-2](./media/data-factory-data-stores-with-rectangular-tables/column-mapping-flow-2.png)



<!--HONumber=Nov16_HO3-->


