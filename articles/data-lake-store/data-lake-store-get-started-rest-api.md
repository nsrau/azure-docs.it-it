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
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="08/02/2016"
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
- **Creare un'applicazione di Azure Active Directory**. Esistono due modalità di autenticazione con Azure Active Directory: **interattiva** e **non interattiva**. I prerequisiti sono diversi a seconda della modalità di autenticazione.
	* **Per l'autenticazione interattiva** (usata in questo articolo): in Azure Active Directory è necessario creare un'**applicazione client nativa**. Dopo aver creato l'applicazione, recuperare i valori seguenti correlati.
		- Ottenere l'**ID client** e l'**URI di reindirizzamento** per l'applicazione
		- Impostare autorizzazioni delegate

	* **Per l'autenticazione non interattiva**: in Azure Active Directory è necessario creare un'**applicazione Web**. Dopo aver creato l'applicazione, recuperare i valori seguenti correlati.
		- Ottenere l'**ID client**, il **segreto client** e l'**URI di reindirizzamento** per l'applicazione
		- Impostare autorizzazioni delegate
		- Assegnare l'applicazione Azure Active Directory a un ruolo. Il ruolo può essere al livello dell'ambito in cui si desidera concedere l'autorizzazione per l'applicazione di Azure Active Directory. Ad esempio, è possibile assegnare l'applicazione al livello della sottoscrizione o del gruppo di risorse. Per istruzioni, vedere [Assegnare l'applicazione al ruolo](../resource-group-create-service-principal-portal.md#assign-application-to-role).

	Per istruzioni su come recuperare questi valori, impostare le autorizzazioni e assegnare i ruoli, vedere [Creare un'applicazione e un'entità servizio di Active Directory tramite il portale](../resource-group-create-service-principal-portal.md).

- [cURL](http://curl.haxx.se/). Questo articolo usa cURL per illustrare come effettuare chiamate API REST con un account Archivio Data Lake.

## Come si esegue l'autenticazione tramite Azure Active Directory?

È possibile adottare due approcci per l'autenticazione tramite Azure Active Directory.

### Autenticazione interattiva (autenticazione utente)

In questo scenario, l'applicazione richiede all'utente di accedere e tutte le operazioni vengono eseguite nel contesto utente. Eseguire la procedura seguente per l'autenticazione interattiva.

1. Tramite l'applicazione, reindirizzare l'utente all'URL seguente:

		https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<CLIENT-ID>&response_type=code&redirect_uri=<REDIRECT-URI>

	>[AZURE.NOTE] \<REDIRECT-URI> deve essere codificato per essere usato in un URL. Quindi, per https://localhost usare `https%3A%2F%2Flocalhost`

	Per questa esercitazione, è possibile sostituire i valori segnaposto nell'URL precedente e incollare quest'ultimo nella barra degli indirizzi di un web browser. Si verrà reindirizzati per l'autenticazione tramite l'accesso ad Azure. Dopo aver eseguito correttamente l'accesso, la risposta verrà visualizzata nella barra degli indirizzi del browser. La risposta sarà nel formato seguente:
		
		http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. Acquisire il codice di autorizzazione dalla risposta. Per questa esercitazione, è possibile copiare il codice di autorizzazione dalla barra degli indirizzi del web browser e passarla nella richiesta POST all'endpoint di token come illustrato di seguito:

		curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<CLIENT-ID> \
        -F code=<AUTHORIZATION-CODE>

	>[AZURE.NOTE] In questo caso, \<REDIRECT-URI> non deve essere codificato.

3. La risposta è un oggetto JSON che contiene un token di accesso (ad esempio, `"access_token": "<ACCESS_TOKEN>"`) e un token di aggiornamento (ad esempio, `"refresh_token": "<REFRESH_TOKEN>"`). L'applicazione usa il token di accesso quando si accede all'Archivio Azure Data Lake e il token di aggiornamento quando un token di accesso scade per ottenerne un altro.

		{"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":	"1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4.  Quando il token di accesso scade, è possibile richiederne uno nuovo tramite il token di aggiornamento come illustrato di seguito:

		 curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      		-F grant_type=refresh_token \
      		-F resource=https://management.core.windows.net/ \
      		-F client_id=<CLIENT-ID> \
      		-F refresh_token=<REFRESH-TOKEN>
 
Per altre informazioni sull'autenticazione utente interattiva, vedere l'articolo relativo al [flusso di concessione del codice di autorizzazione](https://msdn.microsoft.com/library/azure/dn645542.aspx).

### Autenticazione non interattiva

In questo scenario, l'applicazione fornisce le proprie credenziali per eseguire le operazioni. A tale scopo, è necessario inviare una richiesta POST come quella riportata di seguito.

	curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

L'output della richiesta include un token di autorizzazione (indicato da `access-token` nell'output riportato di seguito) che verrà passato successivamente con le chiamate API REST. Salvare questo token di autenticazione in un file di testo, che sarà necessario più avanti in questo articolo.

	{"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

Questo articolo usa l'approccio **non interattivo**. Per altre informazioni sull'autenticazione non interattiva (chiamate da servizio a servizio), vedere l'articolo sulle [chiamate da servizio a servizio tramite credenziali](https://msdn.microsoft.com/library/azure/dn645543.aspx).

## Creare un account Archivio Data Lake

Questa operazione si basa sulla chiamata API REST definita [qui](https://msdn.microsoft.com/library/mt694078.aspx).

Usare il comando cURL seguente. Sostituire **<yourstorename>** con il nome dell'Archivio Data Lake.

	curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

Nel comando precedente sostituire <`REDACTED`> con il token di autorizzazione recuperato prima. Il payload della richiesta per questo comando è contenuto nel file **input.json** fornito per il parametro `-d` precedente. Il contenuto del file input.json è simile al seguente:

	{
	"location": "eastus2",
	"tags": {
		"department": "finance"
		},
	"properties": {}
	}	

## Creare delle cartelle in un account Archivio Data Lake

Questa operazione si basa sulla chiamata API REST WebHDFS definita [qui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory).

Usare il comando cURL seguente. Sostituire **<yourstorename>** con il nome dell'Archivio Data Lake.

	curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS

Nel comando precedente sostituire <`REDACTED`> con il token di autorizzazione recuperato prima. Questo comando crea una directory denominata **mytempdir** nella cartella radice del proprio account Archivio Data Lake.

Se l'operazione viene completata correttamente, verrà visualizzata una risposta simile alla seguente:

	{"boolean":true}

## Elencare le cartelle in un account Archivio Data Lake

Questa operazione si basa sulla chiamata API REST WebHDFS definita [qui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory).

Usare il comando cURL seguente. Sostituire **<yourstorename>** con il nome dell'Archivio Data Lake.

	curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS

Nel comando precedente sostituire <`REDACTED`> con il token di autorizzazione recuperato prima.

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

1. Inviare una richiesta HTTP PUT senza inviare i dati dei file da caricare. Nel comando seguente sostituire **<yourstorename>** con il nome dell'Archivio Data Lake.

		curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=CREATE

	L'output del comando conterrà un URL di reindirizzamento temporaneo, come quello riportato di seguito.

		HTTP/1.1 100 Continue

		HTTP/1.1 307 Temporary Redirect
		...
		...
		Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=CREATE&write=true
		...
		...

2. A questo punto è necessario inviare un'altra richiesta HTTP PUT all'URL elencato per la proprietà **Location** nella risposta. Sostituire **<yourstorename>** con il nome dell'Archivio Data Lake.

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

Tuttavia, dal momento che non esiste alcuna differenza nei parametri di input tra il primo e il secondo passaggio, è possibile usare il parametro `-L` per inviare la prima richiesta. L'opzione `-L` combina essenzialmente due richieste in una e fa ripetere a cURL la richiesta nel nuovo percorso. Infine, viene visualizzato l'output di tutte le chiamate di richiesta, come illustrato di seguito. Sostituire **<yourstorename>** con il nome dell'Archivio Data Lake.

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

Per rinominare un file, usare il comando cURL seguente: Sostituire **<yourstorename>** con il nome dell'Archivio Data Lake.

	curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt

L'output dovrebbe essere simile al seguente:

	HTTP/1.1 200 OK
	...
	
	{"boolean":true}

## Eliminare un file da un account Archivio Data Lake

Questa operazione si basa sulla chiamata API REST WebHDFS definita [qui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory).

Utilizzare il comando cURL seguente per eliminare un file. Sostituire **<yourstorename>** con il nome dell'Archivio Data Lake.

	curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE

Verrà visualizzato un output simile al seguente:

	HTTP/1.1 200 OK
	...
	
	{"boolean":true}

## Eliminare un account Archivio Data Lake

Questa operazione si basa sulla chiamata API REST definita [qui](https://msdn.microsoft.com/library/mt694075.aspx).

Usare il comando cURL seguente per eliminare un account Archivio Data Lake. Sostituire **<yourstorename>** con il nome dell'Archivio Data Lake.

	curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

Verrà visualizzato un output simile al seguente:

	HTTP/1.1 200 OK
	...
	...

## Vedere anche

- [Aprire le applicazioni Big Data di origine che funzionano con Archivio Azure Data Lake](data-lake-store-compatible-oss-other-applications.md)
 

<!---HONumber=AcomDC_0803_2016-->