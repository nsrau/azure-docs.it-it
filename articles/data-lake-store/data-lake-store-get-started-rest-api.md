<properties 
   pageTitle="Introduzione ad Archivio Data Lake con API REST| Microsoft Azure" 
   description="Usare API REST WebHDFS per eseguire operazioni su Archivio Data Lake" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="04/08/2016"
   ms.author="nitinme"/>

# Introduzione ad Archivio Azure Data Lake con API REST

> [AZURE.SELECTOR]
- [Portale](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [SDK per Java](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [Interfaccia della riga di comando di Azure](data-lake-store-get-started-cli.md)
- [Node.JS](data-lake-store-manage-use-nodejs.md)

Questo articolo descrive come usare le API REST WebHDFS e le API REST di Archivio Data Lake per eseguire la gestione dell'account e le operazioni del file system su Archivio Azure Data Lake. Archivio Azure Data Lake espone le proprie API REST per operazioni di gestione account. Tuttavia, dal momento che Archivio Data Lake è compatibile con l'ecosistema Hadoop e HDFS, supporta le operazioni del file system tramite le API REST WebHDFS.

>[AZURE.NOTE] Per informazioni dettagliate sul supporto delle API REST per Archivio Data Lake, vedere [Informazioni di riferimento sulle API REST di Archivio Azure Data Lake](https://msdn.microsoft.com/library/mt693424.aspx).

## Prerequisiti

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Abilitare la sottoscrizione di Azure** per l'anteprima pubblica di Data Lake Store. Vedere le [istruzioni](data-lake-store-get-started-portal.md#signup).
- **Creare un'applicazione di Azure Active Directory**. Vedere [Creare un'applicazione e un'entità servizio di Active Directory tramite il portale](../resource-group-create-service-principal-portal.md). Dopo aver creato l'applicazione, recuperare i valori seguenti correlati.
	- Ottenere l'ID client e l'ID tenant per l'applicazione.
	- Crea una chiave di autenticazione
	- Impostare autorizzazioni delegate

	Le istruzioni per il recupero di questi valori sono disponibili nel collegamento fornito in precedenza.
- **Assegnare l'applicazione di Azure Active Directory a un ruolo**. Il ruolo può essere al livello dell'ambito in cui si desidera concedere l'autorizzazione per l'applicazione di Azure Active Directory. Ad esempio, è possibile assegnare l'applicazione al livello della sottoscrizione o del gruppo di risorse. Per istruzioni, vedere [Assegnare l'applicazione al ruolo](../resource-group-create-service-principal-portal.md#assign-application-to-role).
- [cURL](http://curl.haxx.se/). Questo articolo usa cURL per illustrare come effettuare chiamate API REST con un account Archivio Data Lake.

## Come si esegue l'autenticazione tramite Azure Active Directory?

È possibile adottare due approcci per l'autenticazione tramite Azure Active Directory.

* **Interattivo**, in cui l'applicazione richiede agli utenti di effettuare l'accesso. Per ulteriori informazioni, vedere [Flusso di concessione del codice di autorizzazione](https://msdn.microsoft.com/library/azure/dn645542.aspx).

* **Non interattivo**, in cui l'applicazione fornisce le proprie credenziali. Per altre informazioni, vedere [Chiamate da servizio a servizio tramite le credenziali](https://msdn.microsoft.com/library/azure/dn645543.aspx).

Questo articolo usa l'approccio **non interattivo**. A tale scopo, è necessario inviare una richiesta POST come quella riportata di seguito.

	curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

L'output della richiesta include un token di autorizzazione (indicato da `access-token` nell'output riportato di seguito) che verrà passato successivamente con le chiamate API REST. Salvare questo token di autenticazione in un file di testo, che sarà necessario più avanti in questo articolo.

	{"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

## Creare un account Archivio Data Lake

Questa operazione si basa sulla chiamata API REST definita [qui](https://msdn.microsoft.com/library/mt694078.aspx).

Usare il comando cURL seguente. Sostituire **<yourstorename>** con il nome di Archivio Data Lake.

	curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@C:\temp\input.json

Nel comando precedente sostituire <`REDACTED`> con il token di autorizzazione recuperato in precedenza. Il payload della richiesta per questo comando è contenuto nel file **input.json** fornito per il parametro `-d` precedente. Il contenuto del file input.json è simile al seguente:

	{
	"location": "eastus2",
	"tags": {
		"department": "finance"
		},
	"properties": {}
	}	

## Creare delle cartelle in un account Archivio Data Lake

Questa operazione si basa sulla chiamata API REST WebHDFS definita [qui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory).

Usare il comando cURL seguente. Sostituire **<yourstorename>** con il nome di Archivio Data Lake.

	curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS

Nel comando precedente sostituire <`REDACTED`> con il token di autorizzazione recuperato in precedenza. Questo comando crea una directory denominata **mytempdir** nella cartella radice del proprio account Archivio Data Lake.

Se l'operazione viene completata correttamente, verrà visualizzata una risposta simile alla seguente:

	{"boolean":true}

## Elencare le cartelle in un account Archivio Data Lake

Questa operazione si basa sulla chiamata API REST WebHDFS definita [qui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory).

Usare il comando cURL seguente. Sostituire **<yourstorename>** con il nome di Archivio Data Lake.

	curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS

Nel comando precedente sostituire <`REDACTED`> con il token di autorizzazione recuperato in precedenza.

Se l'operazione viene completata correttamente, verrà visualizzata una risposta simile alla seguente:

	{
	"FileStatuses": {
		"FileStatus": [{
			"length": 0,
			"pathSuffix": "mytempdir",
			"type": "DIRECTORY",
			"blockSize": 268435456,
			"accessTime": 1458324719512,
			"modificationTime": 1458324719512,
			"replication": 0,
			"permission": "777",
			"owner": "NotSupportYet",
			"group": "NotSupportYet"
		}]
	}
	}

## Caricare dati in un account Archivio Data Lake

Questa operazione si basa sulla chiamata API REST WebHDFS definita [qui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File).

Il caricamento di dati tramite API REST WebHDFS è un processo in due fasi, come illustrato di seguito.

1. Inviare una richiesta HTTP PUT senza inviare i dati dei file da caricare. Nel comando seguente sostituire **<yourstorename>** con il nome di Archivio Data Lake.

		curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=CREATE

	L'output del comando conterrà un URL di reindirizzamento temporaneo, come quello riportato di seguito.

		HTTP/1.1 100 Continue

		HTTP/1.1 307 Temporary Redirect
		...
		...
		Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=CREATE&write=true
		...
		...

2. A questo punto è necessario inviare un'altra richiesta HTTP PUT verso l'URL elencato per la proprietà **Location** nella risposta. Sostituire **<yourstorename>** con il nome di Archivio Data Lake.

		curl -i -X PUT -T myinputfile.txt -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=CREATE&write=true

	L'output sarà simile al seguente:

		HTTP/1.1 100 Continue

		HTTP/1.1 201 Created
		...
		...

## Leggere i dati da un account Archivio Data Lake

Questa operazione si basa sulla chiamata API REST WebHDFS definita [qui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File).

La lettura dei dati da un account Archivio Data Lake è un processo in due fasi.

* È prima necessario inviare una richiesta GET all'endpoint `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`. Verrà restituito un percorso a cui inviare la richiesta GET successiva.
* È quindi necessario inviare la richiesta GET all'endpoint `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. Verrà visualizzato il contenuto del file.

Tuttavia, dal momento che non esiste alcuna differenza nei parametri di input tra il primo e il secondo passaggio, è possibile utilizzare il parametro `-L` per inviare la prima richiesta. L'opzione `-L` combina essenzialmente due richieste in una e fa ripetere a cURL la richiesta nel nuovo percorso. Infine, viene visualizzato l'output di tutte le chiamate di richiesta, come illustrato di seguito. Sostituire **<yourstorename>** con il nome di Archivio Data Lake.

	curl -i -L GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN

L'output dovrebbe essere simile al seguente:

	HTTP/1.1 307 Temporary Redirect
	...
	Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
	...
	
	HTTP/1.1 200 OK
	...
	
	Hello, Data Lake Store user!

## Rinominare un file in un account Archivio Data Lake

Questa operazione si basa sulla chiamata API REST WebHDFS definita [qui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory).

Per rinominare un file, usare il comando cURL seguente: Sostituire **<yourstorename>** con il nome di Archivio Data Lake.

	curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt

L'output dovrebbe essere simile al seguente:

	HTTP/1.1 200 OK
	...
	
	{"boolean":true}

## Eliminare un file da un account Archivio Data Lake

Questa operazione si basa sulla chiamata API REST WebHDFS definita [qui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory).

Utilizzare il comando cURL seguente per eliminare un file. Sostituire **<yourstorename>** con il nome di Archivio Data Lake.

	curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE

Verrà visualizzato un output simile al seguente:

	HTTP/1.1 200 OK
	...
	
	{"boolean":true}

## Eliminare un account Archivio Data Lake

Questa operazione si basa sulla chiamata API REST definita [qui](https://msdn.microsoft.com/library/mt694075.aspx).

Usare il comando cURL seguente per eliminare un account Archivio Data Lake. Sostituire **<yourstorename>** con il nome di Archivio Data Lake.

	curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

Verrà visualizzato un output simile al seguente:

	HTTP/1.1 200 OK
	...
	...

## Vedere anche

- [Aprire le applicazioni Big Data di origine che funzionano con Archivio Azure Data Lake](data-lake-store-compatible-oss-other-applications.md)
 

<!---HONumber=AcomDC_0413_2016-->