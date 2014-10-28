Viene registrato un nuovo script insert che genera una firma di accesso condiviso quando viene inserito un nuovo elemento Todo.

1.  Se non è ancora stato creato un account di archiviazione, vedere [Come creare un account di archiviazione][Come creare un account di archiviazione].

2.  Nel portale di gestione fare clic su **Storage**, sull'account di archiviazione e quindi su **Manage Keys**.

    ![][]

3.  Prendere nota dei valori di **Storage Account Name** e **Access Key**.

    ![][1]

4.  Nel servizio mobile fare clic sulla scheda **Configure**, scorrere fino a **App settings** e immettere una coppia **Name** e **Value** per ognuno degli elementi seguenti ottenuti dall'account di archiviazione, quindi fare clic su **Save**.

    -   `STORAGE_ACCOUNT_NAME`
    -   `STORAGE_ACCOUNT_ACCESS_KEY`

    ![][2]

    La chiave di accesso all'account di archiviazione è archiviata in formato crittografato nelle impostazioni dell'app. È possibile accedere a questa chiave da qualsiasi script del server in fase di esecuzione. Per altre informazioni, vedere [Impostazioni app][Impostazioni app].

5.  Fare clic sulla scheda **Data** e quindi sulla tabella **TodoItem**.

    ![][3]

6.  In **todoitem** fare clic sulla scheda **Script** e selezionare **Insert**. Sostituire la funzione insert con il codice seguente, quindi fare clic su **Save**:

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

    ![][4]

    La funzione richiamata quando si verifica un inserimento nella tabella TodoItem verrà sostituita da un nuovo script. Il nuovo script genera una nuova firma di accesso condiviso per l'inserimento, valida per 5 minuti, quindi assegna il valore di questa firma generata alla proprietà `sasQueryString` dell'elemento restituito. La proprietà `imageUri` viene impostata anche sul percorso risorse del nuovo BLOB, per abilitare la visualizzazione di immagini durante l'associazione nell'interfaccia utente client.

    > [WACOM.NOTE] Questo codice consente di creare una firma di accesso condiviso per un singolo BLOB. Per caricare più BLOB in un contenitore usando la stessa firma di accesso condiviso, chiamare il [metodo generateSharedAccessSignature][metodo generateSharedAccessSignature] con un nome risorse BLOB vuoto, ad esempio:
    >
    >     blobService.generateSharedAccessSignature(containerName, '', sharedAccessPolicy);
    >
    > </p>

In seguito, l'app della Guida introduttiva verrà aggiornata per aggiungere funzionalità di caricamento delle immagini mediante la firma di accesso condiviso generata per l'inserimento.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Come creare un account di archiviazione]: /it-it/manage/services/storage/how-to-create-a-storage-account
  []: ./media/mobile-services-configure-blob-storage/mobile-blob-storage-account.png
  [1]: ./media/mobile-services-configure-blob-storage/mobile-blob-storage-account-keys.png
  [2]: ./media/mobile-services-configure-blob-storage/mobile-blob-storage-app-settings.png
  [Impostazioni app]: http://msdn.microsoft.com/it-it/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
  [3]: ./media/mobile-services-configure-blob-storage/mobile-portal-data-tables.png
  [4]: ./media/mobile-services-configure-blob-storage/mobile-insert-script-blob.png
  [metodo generateSharedAccessSignature]: http://go.microsoft.com/fwlink/?LinkId=390455
