### <a name="type-conversion-sample"></a>Esempio di conversione di tipo
L'esempio seguente riguarda la copia dei dati da un Blob in SQL Azure con conversioni del tipo.

Si supponga che il set di dati Blob sia in formato CSV e contenga 3 colonne. Una di esse è una colonna datetime con un formato datetime personalizzato che utilizza nomi abbreviati francesi per il giorno della settimana.

Definire il set di dati di origine Blob come indicato di seguito e le definizioni di tipo per le colonne.

```json
{
    "name": "AzureBlobTypeSystemInput",
    "properties":
    {
         "structure": 
          [
                { "name": "userid", "type": "Int64"},
                { "name": "name", "type": "String"},
                { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
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
        "external": true,
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Dato il precedente tipo SQL per la tabella di mapping di tipo .NET, è possibile definire la tabella di SQL Azure con lo schema seguente.

| Nome colonna | Tipo SQL |
| --- | --- |
| userid |bigint |
| name |text |
| lastlogindate |datetime |

Quindi si definirà il set di dati di SQL Azure come indicato di seguito. 

> [!NOTE]
> Non è necessario specificare la sezione **struttura** con informazioni sul tipo perché le informazioni sul tipo sono già state specificate nell'archivio dati sottostante.

```json
{
    "name": "AzureSQLOutput",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

In questo caso Data Factory eseguirà automaticamente la conversione del tipo incluso il campo Datetime con il formato data personalizzato fr-fr quando si spostano dati da Blob a SQL Azure.



<!--HONumber=Dec16_HO3-->


