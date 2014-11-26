## <a name="storage-client-server"></a>Installare il client di archiviazione nel progetto di servizio mobile

Per poter utilizzare una firma di accesso condiviso per caricare immagini nel servizio di archiviazione BLOB, è necessario innanzitutto aggiungere il pacchetto NuGet che consente di installare la libreria client di archiviazione nel progetto di servizio mobile.

1.  In **Solution Explorer** in Visual Studio in Visual Studio fare clic con il pulsante destro del mouse sul progetto di servizio mobile e quindi scegliere **Gestisci pacchetti NuGet**.

2.  Nel riquadro sinistro selezionare la categoria **Online**, selezionare **Solo stabili**, cercare **WindowsAzure.Storage**, fare clic su **Installa** nel pacchetto **Archiviazione di Azure** e quindi accettare il contratto di licenza.

    ![](./media/mobile-services-configure-blob-storage/mobile-add-storage-nuget-package-dotnet.png)

    La libreria client per i servizi di archiviazione di Azure verrà aggiunta al progetto di servizio mobile.

## <a name="update-data-model"></a>Aggiornare la definizione di TodoItem nel modello di dati

La classe TodoItem definisce l'oggetto dati ed è necessario aggiungere a questa classe le stesse proprietà aggiunte per il client.

1.  In Visual Studio 2013 aprire il progetto di servizio mobile, espandere la cartella DataObjects, quindi aprire il file di progetto TodoItem.cs.

2.  Aggiungere le nuove proprietà seguenti alla classe **TodoItem**:

        public string containerName { get; set; }
        public string resourceName { get; set; }
        public string sasQueryString { get; set; }
        public string imageUri { get; set; } 

    Queste proprietà consentono di generare la firma di accesso condiviso e di archiviare le informazioni sulle immagini. Si noti che la convenzione per l'utilizzo di maiuscole e minuscole di queste proprietà corrisponde alla versione back-end di JavaScript.

    > [WACOM.NOTE] Quando si utilizza l'inizializzatore del database predefinito, Entity Framework elimina e crea nuovamente il database quando rileva una modifica nel modello di dati nella definizione Code First. Per apportare modifiche al modello di dati e conservare i dati esistenti nel database, è necessario utilizzare Migrazioni Code First. L'inizializzatore predefinito non può essere utilizzato su un database SQL in Azure. Per altre informazioni vedere [Come utilizzare le Migrazioni Code First per aggiornare il modello di dati][Come utilizzare le Migrazioni Code First per aggiornare il modello di dati].

## <a name="update-scripts"></a>Aggiornamento del controller TodoItem per generare una firma di accesso condiviso

L'elemento **TodoItemController** viene aggiornato in modo che il metodo **PostTodoItem** generi una firma di accesso condiviso quando viene inserito un nuovo elemento Todo. Inoltre:

1.  Se non è ancora stato creato un account di archiviazione, vedere [Come creare un account di archiviazione][Come creare un account di archiviazione].

2.  Nel portale di gestione fare clic su **Storage**, sull'account di archiviazione e quindi su **Manage Keys**.

    ![][1]

3.  Prendere nota dei valori di **Storage Account Name** e **Access Key**.

    ![][2]

4.  Nel servizio mobile fare clic sulla scheda **Configure**, scorrere fino a **App settings** e immettere una coppia **Name** e **Value** per ognuno degli elementi seguenti ottenuti dall'account di archiviazione, quindi fare clic su **Save**.

    -   `STORAGE_ACCOUNT_NAME`
    -   `STORAGE_ACCOUNT_ACCESS_KEY`

    ![][3]

    La chiave di accesso all'account di archiviazione è archiviata in formato crittografato nelle impostazioni dell'app. È possibile accedere a questa chiave da qualsiasi script del server in fase di esecuzione. Per altre informazioni, vedere [Impostazioni app][Impostazioni app].

5.  In Esplora soluzioni di Visual Studio aprire il file Web.config relativo al progetto di servizio mobile e aggiungere le nuove app settings seguenti, sostituendo i segnaposto con il nome dell'account di archiviazione e la chiave di accesso appena specificati nel portale:

        <add key="STORAGE_ACCOUNT_NAME" value="**your_account_name**" />
        <add key="STORAGE_ACCOUNT_ACCESS_KEY" value="**your_access_token_secret**" />

    Il servizio mobile utilizza le impostazioni memorizzate quando è in esecuzione sul computer locale, consentendo in tal modo di testare il codice prima di pubblicarlo. Quando è in esecuzione in Azure, il servizio mobile utilizza invece i valori delle impostazioni dell'app configurati nel portale, ignorando le impostazioni di progetto.

6.  Nella cartella Controllers aprire il file TodoItemController.cs e aggiungere le direttive **using** seguenti:

        using System;
        using Microsoft.WindowsAzure.Storage.Auth;
        using Microsoft.WindowsAzure.Storage.Blob;

7.  Sostituire il metodo **PostTodoItem** esistente con il codice seguente:

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            string storageAccountName;
            string storageAccountKey;

            // Try to get the Azure storage account token from app settings.  
            if (!(Services.Settings.TryGetValue("STORAGE_ACCOUNT_NAME", out storageAccountName) |
            Services.Settings.TryGetValue("STORAGE_ACCOUNT_ACCESS_KEY", out storageAccountKey)))
            {
                Services.Log.Error("Could not retrieve storage account settings.");
            }

            // Set the URI for the Blob Storage service.
            Uri blobEndpoint = new Uri(string.Format("https://{0}.blob.core.windows.net", storageAccountName));

            // Create the BLOB service client.
            CloudBlobClient blobClient = new CloudBlobClient(blobEndpoint, 
                new StorageCredentials(storageAccountName, storageAccountKey));

            if (item.containerName != null)
            {
                // Set the BLOB store container name on the item, which must be lowercase.
                item.containerName = item.containerName.ToLower();

                // Create a container, if it doesn't already exist.
                CloudBlobContainer container = blobClient.GetContainerReference(item.containerName);
                await container.CreateIfNotExistsAsync();

                // Create a shared access permission policy. 
                BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

                // Enable anonymous read access to BLOBs.
                containerPermissions.PublicAccess = BlobContainerPublicAccessType.Blob;
                container.SetPermissions(containerPermissions);

                // Define a policy that gives write access to the container for 5 minutes.                                   
                SharedAccessBlobPolicy sasPolicy = new SharedAccessBlobPolicy()
                {
                    SharedAccessStartTime = DateTime.UtcNow,
                    SharedAccessExpiryTime = DateTime.UtcNow.AddMinutes(5),
                    Permissions = SharedAccessBlobPermissions.Write
                };

                // Get the SAS as a string.
                item.sasQueryString = container.GetSharedAccessSignature(sasPolicy); 

                // Set the URL used to store the image.
                item.imageUri = string.Format("{0}{1}/{2}", blobEndpoint.ToString(), 
                    item.containerName, item.resourceName);
            }

            // Complete the insert operation.
            TodoItem current = await InsertAsync(item);
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    Il nuovo POST ora genera una nuova firma di accesso condiviso per l'elemento inserito, valida per 5 minuti, quindi assegna il valore della firma generata alla proprietà `sasQueryString` dell'elemento restituito. La proprietà `imageUri` viene impostata anche sul percorso risorse del nuovo BLOB, per abilitare la visualizzazione di immagini durante l'associazione nell'interfaccia utente client.

    > [WACOM.NOTE] Questo codice consente di creare una firma di accesso condiviso per un singolo BLOB. Per caricare più BLOB in un contenitore usando la stessa firma di accesso condiviso, chiamare il [metodo generateSharedAccessSignature][metodo generateSharedAccessSignature] con un nome risorse BLOB vuoto, ad esempio:
    >
    >     blobService.generateSharedAccessSignature(containerName, '', sharedAccessPolicy);
    >
    > </p>

In seguito, l'app della Guida introduttiva verrà aggiornata per aggiungere funzionalità di caricamento delle immagini mediante la firma di accesso condiviso generata per l'inserimento.

 

   
  [Come utilizzare le Migrazioni Code First per aggiornare il modello di dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations
  [Come creare un account di archiviazione]: /it-it/manage/services/storage/how-to-create-a-storage-account
  [1]: ./media/mobile-services-configure-blob-storage/mobile-blob-storage-account.png
  [2]: ./media/mobile-services-configure-blob-storage/mobile-blob-storage-account-keys.png
  [3]: ./media/mobile-services-configure-blob-storage/mobile-blob-storage-app-settings.png
  [Impostazioni app]: http://msdn.microsoft.com/it-it/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
  [metodo generateSharedAccessSignature]: http://go.microsoft.com/fwlink/?LinkId=390455
