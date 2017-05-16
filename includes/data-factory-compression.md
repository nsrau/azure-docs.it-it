## <a name="specifying-compression"></a>Specificare la compressione
L'elaborazione di set di dati di grandi dimensioni può causare colli di bottiglia I/O e di rete. Pertanto, i dati compressi negli archivi possono non solo velocizzare il trasferimento dei dati attraverso la rete e risparmiare spazio su disco, ma apportare anche miglioramenti significativi delle prestazioni nell'elaborazione di dati di grandi dimensioni. Attualmente, la compressione è supportata per gli archivi di dati basati su file, ad esempio BLOB di Azure o il file system locale.  

> [!NOTE]
> Le impostazioni di compressione non sono attualmente supportate per i dati in **AvroFormat**, **OrcFormat** o **ParquetFormat**. Durante la lettura di file in questo formato, Data Factory rileva e usa il codec di compressione nei metadati. Durante la scrittura di file in questi formati, Data Factory sceglie il codec di compressione predefinito per tale formato, ad esempio ZLIB per OrcFormat e SNAPPY per ParquetFormat.
>
>

Per specificare la compressione per un set di dati, usare la proprietà **compression** nel set di dati JSON come illustrato di seguito:   

```json
{  
    "name": "AzureBlobDataSet",  
    "properties": {  
        "availability": {  
            "frequency": "Day",  
              "interval": 1  
        },  
        "type": "AzureBlob",  
        "linkedServiceName": "StorageLinkedService",  
        "typeProperties": {  
            "fileName": "pagecounts.csv.gz",  
            "folderPath": "compression/file/",  
            "compression": {  
                "type": "GZip",  
                "level": "Optimal"  
            }  
        }  
    }  
}  
```
Si supponga che il set di dati di esempio sopra riportato venga usato come output di un'attività di copia, l'attività di copia comprime i dati di output con il codec GZIP usando un rapporto ottimale e quindi scrive i dati compressi in un file denominato pagecounts.csv.gz nell'archivio BLOB di Azure.   

La sezione **compression** ha due proprietà:  

* **Type:** codec di compressione, che può essere **GZIP**, **Deflate**, **BZIP2** o **ZipDeflate**.  
* **Level:** rapporto di compressione, che può essere **Optimal** o **Fastest**.

  * **Fastest:** l'operazione di compressione deve essere completata il più rapidamente possibile, anche se il file risultante non viene compresso in modo ottimale.
  * **Optimal**: l'operazione di compressione deve comprimere il file in modo ottimale, anche se il completamento richiede più tempo.

    Per maggiori informazioni, vedere l'argomento relativo al [livello di compressione](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) .

Quando si specifica una proprietà `compression` in un set di dati di input JSON, la pipeline può leggere i dati compressi dall'origine. Quando si specifica la proprietà in un set di dati di output JSON, l'attività di copia può scrivere i dati compressi nella destinazione. Di seguito vengono forniti alcuni scenari di esempio:

* Leggere i dati compressi GZIP da un BLOB di Azure, decomprimerli e scrivere i dati del risultato in un database SQL di Azure. Definire il set di dati di input del BLOB di Azure con la proprietà JSON `compression` `type` impostata su GZIP.
* Leggere i dati da un file di testo normale dal file system locale, comprimerli usando il formato GZIP e scrivere i dati compressi in un BLOB di Azure. Definire un set di dati di output del BLOB di Azure con la proprietà JSON `compression` `type` impostata su GZIP.
* Leggere il file ZIP dal server FTP, decomprimerlo per ottenere i file all'interno e inserire i file in Azure Data Lake Store. Definire un set di dati FTP di input con la proprietà JSON `compression` `type` impostata su ZipDeflate.
* Leggere i dati compressi GZIP da un BLOB di Azure, decomprimerli, comprimerli usando BZIP2 e scrivere i dati del risultato in un BLOB di Azure. In questo caso, il set di dati di input del BLOB di Azure viene definito con `compression` `type` impostato su GZIP e il set di dati di output viene definito con `compression` `type` impostato su BZIP2.   
