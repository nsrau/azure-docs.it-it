<properties
	pageTitle="Usare Servizi mobili per caricare immagini nell'archivio BLOB (Android) | Servizi mobili"
	description="Informazioni su come usare Servizi mobili per caricare immagini nell'Archiviazione di Azure e accedere alle immagini dall'app Android."
	services="mobile-services"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="10/05/2015"
	ms.author="ricksal"/>

# Caricare immagini nell'archiviazione di Azure da un dispositivo Android

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../../includes/mobile-services-selector-upload-data-blob-storage.md)]

Questo argomento illustra come abilitare l'app Android di Servizi mobili di Azure in modo che carichi immagini nell'archiviazione di Azure.

Servizi mobili usa un database SQL per archiviare i dati. È tuttavia più efficiente archiviare dati BLOB (Binary Large Object) nell'archiviazione di Azure. In questa esercitazione si abilita l'app di guida introduttiva di Servizi mobili in modo che scatti foto con la fotocamera Android e carichi le immagini nell'archiviazione di Azure.


## Elementi necessari per iniziare

Prima di iniziare questa esercitazione, è necessario completare la procedura introduttiva relativa a Servizi mobili disponibile in [Introduzione a Servizi mobili].

Per completare questa esercitazione, è necessario disporre anche di:

+ Un [account di archiviazione Azure](../storage-create-storage-account.md)
+ Un dispositivo Android con fotocamera

## Funzionamento dell'app

Il caricamento dell'immagine fotografica è un processo a più fasi:

- È prima di tutto necessario scattare una foto, quindi inserire una riga TodoItem nel database SQL che contiene i nuovi campi di metadati usati dall'archiviazione di Azure.
- Un nuovo script SQL **insert** del servizio mobile chiede all'archiviazione di Azure una firma di accesso condiviso.
- Lo script restituisce al client la firma di accesso condiviso e un URI per il BLOB.
- Il client carica la foto, usando la firma di accesso condiviso e l'URI del BLOB.

Informazioni sulla firma di accesso condiviso

L'archiviazione delle credenziali necessarie per caricare i dati nel servizio Archiviazione di Azure all'interno dell'app client non è sicura. Queste credenziali devono essere invece archiviate nel servizio mobile e devono essere usate per generare la firma di accesso condiviso che concede le autorizzazioni per il caricamento di una nuova immagine. La firma di accesso condiviso, una credenziale con scadenza dopo 5 minuti, viene restituita in modo sicuro da Servizi mobili all'app client. L'app usa quindi questa credenziale temporanea per caricare l'immagine. Per altre informazioni, vedere [Firme di accesso condiviso, parte 1: informazioni sul modello di firma di accesso condiviso](storage-dotnet-shared-access-signature-part-1.md).

## Esempio di codice
[Qui](https://github.com/Azure/mobile-services-samples/tree/master/UploadImages)è disponibile la parte di codice sorgente client completo di questa app. Per eseguirlo è necessario completare le parti di back-end di servizi mobili di questa esercitazione.

## Aggiornare lo script insert registrato nel portale di Azure classico

[AZURE.INCLUDE [mobile-services-configure-blob-storage](../../includes/mobile-services-configure-blob-storage.md)]


## Aggiornare l'app di guida introduttiva per acquisire e caricare immagini.

### Fare riferimento alla libreria client per Android di Archiviazione di Azure

1. Per aggiungere un riferimento alla libreria, nel file **app** > **build.gradle** aggiungere questa riga alla sezione `dependencies`:

		compile 'com.microsoft.azure.android:azure-storage-android:0.6.0@aar'


2. Cambiare il valore `minSdkVersion` in 15 (richiesto dall'API della fotocamera).

3. Premere l'icona **Sync Project with Gradle Files**.

### Aggiornare il file manifesto per la fotocamera e l'archiviazione

1. Specificare che l'app richiede la disponibilità di una fotocamera aggiungendo questa riga al file **AndroidManifest.xml**:

	    <uses-feature android:name="android.hardware.camera"
	        android:required="true" />

2. Specificare che l'app necessita di autorizzazioni di scrittura nella risorsa di archiviazione esterna aggiungendo questa riga al file **AndroidManifest.xml**:

	    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

	Si noti che la risorsa di archiviazione esterna di Android non è necessariamente una scheda SD. Per altre informazioni, vedere la pagina relativa al [salvataggio di file](http://developer.android.com/training/basics/data-storage/files.html).

### Aggiornare i file delle risorse per la nuova interfaccia utente

1. Aggiungere titoli per i nuovi pulsanti aggiungendo il codice seguente al file **strings.xml** nella directory *values*:

	    <string name="preview_button_text">Take Photo</string>
	    <string name="upload_button_text">Upload</string>

2. Nel file **activity\_to\_do.xml** nella cartella **res => layout** aggiungere il codice seguente prima del codice esistente per il pulsante **Add**.

         <Button
             android:id="@+id/buttonPreview"
             android:layout_width="120dip"
             android:layout_height="wrap_content"
             android:onClick="takePicture"
             android:text="@string/preview_button_text" />

3. Sostituire l'elemento pulsante **Add** con il codice seguente:

         <Button
             android:id="@+id/buttonUpload"
             android:layout_width="100dip"
             android:layout_height="wrap_content"
             android:onClick="uploadPhoto"
             android:text="@string/upload_button_text" />


### Aggiungere codice per l'acquisizione di foto

1. In **ToDoActivity.java** aggiungere il codice seguente per creare un oggetto **File** con un nome univoco.

		// Create a File object for storing the photo
	    private File createImageFile() throws IOException {
	        // Create an image file name
	        String timeStamp = new SimpleDateFormat("yyyyMMdd_HHmmss").format(new Date());
	        String imageFileName = "JPEG_" + timeStamp + "_";
	        File storageDir = Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_PICTURES);
	        File image = File.createTempFile(
	                imageFileName,  /* prefix */
	                ".jpg",         /* suffix */
	                storageDir      /* directory */
	        );
	        return image;
	    }

5. Aggiungere questo codice per avviare l'app della fotocamera Android. È possibile scattare foto e, quando una ha l'aspetto desiderato, premere **Save** per salvarla nel file appena creato.

		// Run an Intent to start up the Android camera
	    static final int REQUEST_TAKE_PHOTO = 1;
	    public Uri mPhotoFileUri = null;
	    public File mPhotoFile = null;

	    public void takePicture(View view) {
	        Intent takePictureIntent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
	        // Ensure that there's a camera activity to handle the intent
	        if (takePictureIntent.resolveActivity(getPackageManager()) != null) {
	            // Create the File where the photo should go
	            try {
	                mPhotoFile = createImageFile();
	            } catch (IOException ex) {
	                // Error occurred while creating the File
	                //
	            }
	            // Continue only if the File was successfully created
	            if (mPhotoFile != null) {
	                mPhotoFileUri = Uri.fromFile(mPhotoFile);
	                takePictureIntent.putExtra(MediaStore.EXTRA_OUTPUT, mPhotoFileUri);
	                startActivityForResult(takePictureIntent, REQUEST_TAKE_PHOTO);
	            }
	        }
	    }

### Aggiungere codice per caricare un file di foto nell'archivio BLOB


1. Aggiungere prima di tutto alcuni nuovi campi di metadati all'oggetto `ToDoItem` aggiungendo il codice seguente a **ToDoItem.java**.

		/**
	     *  imageUri - points to location in storage where photo will go
	     */
	    @com.google.gson.annotations.SerializedName("imageUri")
	    private String mImageUri;

	    /**
	     * Returns the item ImageUri
	     */
	    public String getImageUri() {
	        return mImageUri;
	    }

	    /**
	     * Sets the item ImageUri
	     *
	     * @param ImageUri
	     *            Uri to set
	     */
	    public final void setImageUri(String ImageUri) {
	        mImageUri = ImageUri;
	    }

	    /**
	     * ContainerName - like a directory, holds blobs
	     */
	    @com.google.gson.annotations.SerializedName("containerName")
	    private String mContainerName;

	    /**
	     * Returns the item ContainerName
	     */
	    public String getContainerName() {
	        return mContainerName;
	    }

	    /**
	     * Sets the item ContainerName
	     *
	     * @param ContainerName
	     *            Uri to set
	     */
	    public final void setContainerName(String ContainerName) {
	        mContainerName = ContainerName;
	    }

	    /**
	     *  ResourceName
	     */
	    @com.google.gson.annotations.SerializedName("resourceName")
	    private String mResourceName;

	    /**
	     * Returns the item ResourceName
	     */
	    public String getResourceName() {
	        return mResourceName;
	    }

	    /**
	     * Sets the item ResourceName
	     *
	     * @param ResourceName
	     *            Uri to set
	     */
	    public final void setResourceName(String ResourceName) {
	        mResourceName = ResourceName;
	    }

	    /**
	     *  SasQueryString - permission to write to storage
	     */
	    @com.google.gson.annotations.SerializedName("sasQueryString")
	    private String mSasQueryString;

	    /**
	     * Returns the item SasQueryString
	     */
	    public String getSasQueryString() {
	        return mSasQueryString;
	    }

	    /**
	     * Sets the item SasQueryString
	     *
	     * @param SasQueryString
	     *            Uri to set
	     */
	    public final void setSasQueryString(String SasQueryString) {
	        mSasQueryString = SasQueryString;
	    }

2. Sostituire il costruttore di parametro 0 con il codice seguente:

	    /**
	     * ToDoItem constructor
	     */
	    public ToDoItem() {
	        mContainerName = "";
	        mResourceName = "";
	        mImageUri = "";
	        mSasQueryString = "";
	    }

3. Sostituire il costruttore di parametro multi con il codice seguente:

	    /**
	     * Initializes a new ToDoItem
	     *
	     * @param text
	     *            The item text
	     * @param id
	     *            The item id
	     */
	    public ToDoItem(String text,
	                    String id,
	                    String containerName,
	                    String resourceName,
	                    String imageUri,
	                    String sasQueryString) {
	        this.setText(text);
	        this.setId(id);
	        this.setContainerName(containerName);
	        this.setResourceName(resourceName);
	        this.setImageUri(imageUri);
	        this.setSasQueryString(sasQueryString);
	    }


4. Nel file **ToDoActivity.java** sostituire il metodo **addItem** in **ToDoActivity.java** con il codice seguente che consente di caricare l'immagine.

	    /**
	     * Add a new item
	     *
	     * @param view
	     *            The view that originated the call
	     */
	    public void uploadPhoto(View view) {
	        if (mClient == null) {
	            return;
	        }

	        // Create a new item
	        final ToDoItem item = new ToDoItem();

	        item.setText(mTextNewToDo.getText().toString());
	        item.setComplete(false);
	        item.setContainerName("todoitemimages");

	        // Use a unigue GUID to avoid collisions.
	        UUID uuid = UUID.randomUUID();
	        String uuidInString = uuid.toString();
	        item.setResourceName(uuidInString);

	        // Send the item to be inserted. When blob properties are set this
	        // generates an SAS in the response.
	        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
	            @Override
	            protected Void doInBackground(Void... params) {
	                try {
		                    final ToDoItem entity = addItemInTable(item);

		                    // If we have a returned SAS, then upload the blob.
		                    if (entity.getSasQueryString() != null) {

	                       // Get the URI generated that contains the SAS
	                        // and extract the storage credentials.
	                        StorageCredentials cred =
								new StorageCredentialsSharedAccessSignature(entity.getSasQueryString());
	                        URI imageUri = new URI(entity.getImageUri());

	                        // Upload the new image as a BLOB from a stream.
	                        CloudBlockBlob blobFromSASCredential =
	                                new CloudBlockBlob(imageUri, cred);

	                        blobFromSASCredential.uploadFromFile(mPhotoFileUri.getPath());
  	                    }

	                    runOnUiThread(new Runnable() {
	                        @Override
	                        public void run() {
	                            if(!entity.isComplete()){
	                                mAdapter.add(entity);
	                            }
	                        }
	                    });
	                } catch (final Exception e) {
	                    createAndShowDialogFromTask(e, "Error");
	                }
	                return null;
	            }
	        };

	        runAsyncTask(task);

	        mTextNewToDo.setText("");
	    }


Questo codice invia una richiesta al servizio mobile per inserire un nuovo oggetto TodoItem. La risposta contiene la firma di accesso condiviso, che viene quindi usata per caricare l'immagine dalla risorsa di archiviazione locale a un archivio BLOB in Archiviazione di Azure.


## Testare il caricamento delle immagini

1. In Android Studio premere **Run**. Nella finestra di dialogo scegliere il dispositivo da usare.

2. Quando viene visualizzata l'interfaccia utente dell'app, immettere testo nella casella di testo con etichetta **Add a ToDo item**.

3. Premere **Take Photo**. All'avvio dell'app della fotocamera, scattare una foto. Premere il segno di spunta per accettare la foto.

4. Premere **Upload**. Si noti che ToDoItem è stato aggiunto all'elenco, come di consueto.

5. Nel portale di Azure classico passare all'account di archiviazione, quindi selezionare la scheda **Contenitori** e premere il nome del contenitore nell'elenco.

6. Verrà visualizzato un elenco di file BLOB caricati. Selezionare un file e premere **Download**.

7. L'immagine caricata viene ora visualizzata in una finestra del browser.


## <a name="next-steps"> </a>Passaggi successivi

Ora che le immagini sono state caricate in modo sicuro integrando il servizio mobile con il servizio BLOB, leggere altri argomenti sul servizio back-end e sull'integrazione:

+ [Inviare posta elettronica da Servizi mobili con SendGrid]

  Informazioni su come aggiungere funzionalità di posta elettronica a un servizio mobile tramite il servizio di posta elettronica SendGrid. Questo argomento illustra come aggiungere script sul lato server per inviare messaggi di posta elettronica tramite SendGrid.

+ [Pianificare processi back-end in Servizi mobili]

  Informazioni su come usare la funzionalità di pianificazione di processi di Servizi mobili per definire codice dello script del server da eseguire in base a una pianificazione definita dall'utente.

+ [Riferimento per gli script del server di Servizi mobili]

  Argomenti di riferimento per l'utilizzo di script del server per eseguire operazioni sul lato server e per l'integrazione con altri componenti e risorse esterne di Azure.

+ [Riferimento per i concetti e le procedure di .NET per Servizi mobili]

  Altre informazioni su come usare Servizi mobili con .NET.


<!-- Anchors. -->
[Install the Storage Client library]: #install-storage-client
[Update the client app to capture images]: #add-select-images
[Update the insert script to generate an SAS]: #update-scripts
[Upload images to test the app]: #test
[Next Steps]: #next-steps

<!-- Images. -->

[2]: ./media/mobile-services-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png


<!-- URLs. -->
[Inviare posta elettronica da Servizi mobili con SendGrid]: store-sendgrid-mobile-services-send-email-scripts.md
[Pianificare processi back-end in Servizi mobili]: mobile-services-schedule-recurring-tasks.md
[Send push notifications to Windows Store apps using Service Bus from a .NET back-end]: http://go.microsoft.com/fwlink/?LinkId=277073&clcid=0x409
[Riferimento per gli script del server di Servizi mobili]: mobile-services-how-to-use-server-scripts.md
[Introduzione a Servizi mobili]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md

[Azure classic portal]: https://manage.windowsazure.com/
[How To Create a Storage Account]: ../storage-create-storage-account.md
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866
[Riferimento per i concetti e le procedure di .NET per Servizi mobili]: mobile-services-windows-dotnet-how-to-use-client-library.md
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

<!---HONumber=AcomDC_1203_2015-->