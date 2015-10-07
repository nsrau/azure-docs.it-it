### Supporto della compressione  
L'elaborazione di set di dati di grandi dimensioni può causare colli di bottiglia I/O e di rete. Pertanto, i dati compressi negli archivi possono non solo velocizzare il trasferimento dei dati attraverso la rete e risparmiare spazio su disco, ma apportare anche miglioramenti significativi delle prestazioni nell'elaborazione di dati di grandi dimensioni. Attualmente, la compressione è supportata per gli archivi di dati basati su file, ad esempio BLOB di Azure o il file system locale.

Per specificare la compressione per un set di dati, usare la proprietà **compression** nel set di dati JSON come illustrato di seguito:

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
 
Si noti che la sezione **compression** dispone di due proprietà:
  
- **Type:** il codec di compressione, che può essere **GZIP**, **Deflate** o **BZIP2**.  
- **Level:** il rapporto di compressione, che può essere **Optimal** o **Fastest**. 
	- **Fastest:** l'operazione di compressione deve essere completata il più rapidamente possibile, anche se il file risultante non viene compresso in modo ottimale. 
	- **Optimal**: l'operazione di compressione deve comprimere il file in modo ottimale, anche se il completamento richiede più tempo. 
	
	Per altre informazioni, vedere l'argomento relativo al [livello di compressione](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx).

Si supponga che il set di dati di esempio sopra riportato venga usato come output di un'attività di copia, l'attività di copia comprimerà i dati di output con il codec GZIP usando un rapporto ottimale e quindi scriverà i dati compressi in un file denominato pagecounts.csv.gz nell'archiviazione BLOB di Azure.

Quando si specifica una proprietà di compressione in un set di dati di input JSON, la pipeline può leggere i dati compressi dall'origine. Quando si specifica la proprietà in un set di dati di output JSON, l'attività di copia può scrivere i dati compressi nella destinazione. Di seguito vengono forniti alcuni scenari di esempio:

- Leggere i dati compressi GZIP da un BLOB di Azure, decomprimerli e scrivere i dati del risultato in un database SQL di Azure. In questo caso, definire il set di dati di input del BLOB di Azure con la proprietà compression di JSON. 
- Leggere i dati da un file di testo normale dal file system locale, comprimerli usando il formato GZIP e scrivere i dati compressi in un BLOB di Azure. In questo caso, definire il set di dati di output del BLOB di Azure con la proprietà compression di JSON.  
- Leggere i dati compressi GZIP da un BLOB di Azure, decomprimerli, comprimerli usando BZIP2 e scrivere i dati del risultato in un BLOB di Azure. In questo caso, il set di dati di input del BLOB di Azure viene definito con il tipo di compressione impostato su GZIP e il set di dati di output viene definito con il tipo di compressione impostato BZIP2.   

<!----HONumber=Sept15_HO1-->
