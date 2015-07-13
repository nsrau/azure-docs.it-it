Viene registrato un nuovo script insert che genera una firma di accesso condiviso quando viene inserito un nuovo elemento Todo.

0. Se non è ancora stato creato un account di archiviazione, vedere [Come creare un account di archiviazione].

1. Nel portale di gestione fare clic su **Archiviazione**, sull'account di archiviazione e quindi su **Gestisci chiavi**.

2. Prendere nota dei valori di **Nome account di archiviazione** e **Chiave di accesso**.

   	![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-account-keys.png)

3. Nel servizio mobile fare clic sulla scheda **Configura**, scorrere fino a **Impostazioni app** e immettere una coppia **Nome** e **Valore** per ognuno degli elementi seguenti ottenuti dall'account di archiviazione e quindi fare clic su **Salva**.

	+ `STORAGE_ACCOUNT_NAME`
	+ `STORAGE_ACCOUNT_ACCESS_KEY`

	![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-app-settings.png)

	La chiave di accesso all'account di archiviazione è archiviata in formato crittografato nelle impostazioni dell'app. È possibile accedere a questa chiave da qualsiasi script del server in fase di esecuzione. Per altre informazioni, vedere [Impostazioni app].

4. Fare clic sulla scheda **Dati** e quindi sulla tabella **TodoItem**.

5.  In **todoitem** fare clic sulla scheda **Script** e selezionare **Inserisci**. Sostituire la funzione insert con il codice seguente, quindi fare clic su **Salva**:

		var azure = require('azure');
		var qs = require('querystring');
		var appSettings = require('mobileservice-config').appSettings;
		
		function insert(item, user, request) {
		    // Get storage account settings from app settings. 
		    var accountName = appSettings.STORAGE_ACCOUNT_NAME;
		    var accountKey = appSettings.STORAGE_ACCOUNT_ACCESS_KEY;
		    var host = accountName + '.blob.core.windows.net';
		
		    if ((typeof item.containerName !== "undefined") && (
		    item.containerName !== null)) {
		        // Set the BLOB store container name on the item, which must be lowercase.
		        item.containerName = item.containerName.toLowerCase();
		
		        // If it does not already exist, create the container 
		        // with public read access for blobs.        
		        var blobService = azure.createBlobService(accountName, accountKey, host);
		        blobService.createContainerIfNotExists(item.containerName, {
		            publicAccessLevel: 'blob'
		        }, function(error) {
		            if (!error) {
		
		                // Provide write access to the container for the next 5 mins.        
		                var sharedAccessPolicy = {
		                    AccessPolicy: {
		                        Permissions: azure.Constants.BlobConstants.SharedAccessPermissions.WRITE,
		                        Expiry: new Date(new Date().getTime() + 5 * 60 * 1000)
		                    }
		                };
		
		                // Generate the upload URL with SAS for the new image.
		                var sasQueryUrl = 
		                blobService.generateSharedAccessSignature(item.containerName, 
		                item.resourceName, sharedAccessPolicy);
		
		                // Set the query string.
		                item.sasQueryString = qs.stringify(sasQueryUrl.queryString);
		
		                // Set the full path on the new new item, 
		                // which is used for data binding on the client. 
		                item.imageUri = sasQueryUrl.baseUrl + sasQueryUrl.path;
		
		            } else {
		                console.error(error);
		            }
		            request.execute();
		        });
		    } else {
		        request.execute();
		    }
		}

   	La funzione richiamata quando si verifica un inserimento nella tabella TodoItem verrà sostituita da un nuovo script. Il nuovo script genera una nuova firma di accesso condiviso per l'inserimento, valida per 5 minuti, quindi assegna il valore della firma generata alla proprietà `sasQueryString` dell'elemento restituito. La proprietà `imageUri` viene impostata anche sul percorso risorse del nuovo BLOB, per abilitare la visualizzazione di immagini durante l'associazione nell'interfaccia utente client.

	>[AZURE.NOTE]Questo codice consente di creare una firma di accesso condiviso per un singolo BLOB. Per caricare più BLOB in un contenitore usando la stessa firma di accesso condiviso, chiamare il metodo <a href="http://go.microsoft.com/fwlink/?LinkId=390455" target="_blank">generateSharedAccessSignature</a> con un nome risorse BLOB vuoto, ad esempio: <pre><code>blobService.generateSharedAccessSignature(containerName, '', sharedAccessPolicy);</code></pre>

In seguito, l'app della Guida introduttiva verrà aggiornata per aggiungere funzionalità di caricamento delle immagini mediante la firma di accesso condiviso generata per l'inserimento.
 
<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Come creare un account di archiviazione]: /manage/services/storage/how-to-create-a-storage-account
[Impostazioni app]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

<!---HONumber=62-->