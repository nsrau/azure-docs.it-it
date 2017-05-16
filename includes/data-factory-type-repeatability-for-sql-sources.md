## <a name="repeatability-during-copy"></a>Ripetibilità durante la copia
Quando si copiano dati in un database di SQL Server/SQL di Azure da altri archivi dati, è necessario definire criteri di ripetibilità per evitare risultati imprevisti. 

Quando si copiano dati in un database SQL Server/SQL di Azure, per impostazione predefinita l'attività di copia ACCODA il set di dati alla tabella di sink predefinita. Ad esempio, quando si copiano dati da un'origine file con estensione csv (valori delimitati da virgole) composta da due record in un database di SQL Server/SQL di Azure, la tabella assume l'aspetto seguente:

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Si supponga di aver trovato degli errori nel file di origine e di aver aggiornato la quantità di Down Tube da 2 a 4 nel file di origine. Se si esegue nuovamente la sezione di dati per quel periodo, saranno disponibili due nuovi record accodati al database di SQL Server/SQL di Azure. L'esempio seguente presuppone che in nessuna delle colonne della tabella sia presente il vincolo di chiave primaria.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Per evitare questa situazione, è necessario specificare la semantica UPSERT usando uno dei due meccanismi illustrati di seguito.

> [!NOTE]
> In base ai criteri di ripetizione dei tentativi specificati, è possibile ripetere automaticamente l'esecuzione di una sezione anche in Data Factory di Azure.
> 
> 

### <a name="mechanism-1"></a>Meccanismo 1
È possibile usare la proprietà **sqlWriterCleanupScript** per eseguire un'azione di pulizia prima dell'esecuzione di una sezione. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

In questo caso, durante la copia di una sezione viene prima eseguito uno script di pulizia che elimina i dati dalla tabella SQL corrispondente alla sezione. L'attività di copia inserirà i dati nella tabella SQL in un momento successivo. 

Se la sezione viene eseguita nuovamente, la quantità risulterà aggiornata come desiderato.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Si supponga, ad esempio, che il record Flat Washer venga rimosso dal file con estensione csv originale. Se la sezione viene eseguita nuovamente, si ottiene il risultato seguente: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```
Nessuna nuova operazione è stata eseguita. L'attività di copia ha eseguito lo script di pulizia per eliminare i dati corrispondenti alla sezione. Quindi, ha letto l'input dal CSV (che conteneva solo 1 record) e lo ha inserito nella tabella. 

### <a name="mechanism-2"></a>Meccanismo 2
> [!IMPORTANT]
> Attualmente sliceIdentifierColumnName non è supportato per SQL Data Warehouse di Azure. 

Un altro meccanismo per ottenere la ripetibilità prevede la disponibilità di una colonna dedicata (**sliceIdentifierColumnName**) nella tabella di destinazione. Questa colonna viene usata da Data factory di Azure per garantire che l'origine e la destinazione rimangano sincronizzate. Questo approccio può essere usato solo quando è disponibile una certa flessibilità nella modifica o nella definizione dello schema della tabella SQL di destinazione. 

La colonna viene usata da Data factory di Azure per scopi di ripetibilità e nel corso del processo Data factory di Azure non apporterà alcuna modifica allo schema della tabella. Per applicare questo approccio, è possibile seguire questa procedura:

1. Definire una colonna di tipo binario (32) nella tabella SQL di destinazione, in cui non sia presente alcun vincolo. Ai fini di questo esempio, la colonna viene denominata "ColumnForADFuseOnly".
2. Usarla nell'attività di copia come segue:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "ColumnForADFuseOnly"
    }
    ```

Data factory di Azure popolerà la colonna in modo che l'origine e la destinazione risultino sincronizzate. L'utente non potrà usare i valori della colonna al di fuori di questo contesto. 

Analogamente al meccanismo 1, l'attività di copia pulisce prima i dati della sezione specificata dalla tabella SQL di destinazione, quindi esegue normalmente l'attività di copia per inserire i dati dall'origine alla destinazione della sezione. 

