##<a name="add-select-images"></a>Aggiornare l'app client della guida introduttiva in modo da acquisire e caricare le immagini

1. In Visual Studio aprire il file Package.appxmanifest e, nella scheda **Funzionalità**, abilitare **Webcam** e **Microfono**.

   	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-app-manifest-camera.png)
 
   	In questo modo si garantirà che l'app sia in grado di utilizzare una fotocamera collegata al computer. Alla prima esecuzione dell'app, verrà chiesto agli utenti di consentire l'accesso alla fotocamera.

1. Aprire il file MainPage.xaml file e sostituire l'elemento **StackPanel** subito dopo il primo elemento **Task** con il codice seguente:

        <StackPanel Orientation="Horizontal" Margin="72,0,0,0">
            <TextBox Name="TextInput" Margin="5" MaxHeight="40" MinWidth="300"></TextBox>
            <Button Name="btnTakePhoto" Style="{StaticResource PhotoAppBarButtonStyle}"
                    Click="OnTakePhotoClick" />
            <Button Name="ButtonSave" Style="{StaticResource UploadAppBarButtonStyle}" 
                    Click="ButtonSave_Click"/>
        </StackPanel>

2. Sostituire l'elemento **StackPanel** in **DataTemplate** con il codice seguente:

        <StackPanel Orientation="Vertical">
            <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" 
                        Checked="CheckBoxComplete_Checked" Content="{Binding Text}" 
                        Margin="10,5" VerticalAlignment="Center"/>
            <Image Name="ImageUpload" Source="{Binding ImageUri, Mode=OneWay}"
                    MaxHeight="250"/>
        </StackPanel> 

   	Verrà aggiunta un'immagine in **ItemTemplate** e la relativa origine di associazione verrà impostata come URI dell'immagine caricata nel servizio di archiviazione BLOB.

3. Aprire il file del progetto MainPage.xaml.cs e aggiungere le istruzioni **using** seguenti:
	
		using Windows.Media.Capture;
		using Windows.Storage;
		using Windows.UI.Popups;
		using Microsoft.WindowsAzure.Storage.Auth;
		using Microsoft.WindowsAzure.Storage.Blob;
    
4. Nella classe TodoItem aggiungere le proprietà seguenti:

        [JsonProperty(PropertyName = "containerName")]
        public string ContainerName { get; set; }
		
        [JsonProperty(PropertyName = "resourceName")]
        public string ResourceName { get; set; }
		
        [JsonProperty(PropertyName = "sasQueryString")]
        public string SasQueryString { get; set; }
		
        [JsonProperty(PropertyName = "imageUri")]
        public string ImageUri { get; set; } 

   	>[AZURE.NOTE]Per aggiungere nuove proprietà all'oggetto TodoItem in un servizio mobile back-end di JavaScript, è necessario lo Schema dinamico abilitato nel servizio mobile. Quando lo schema dinamico è abilitato, nella tabella TodoItem vengono automaticamente aggiunte nuove colonne mappate a queste nuove proprietà. Per un servizio mobile back-end di .NET, vedere l'argomento[Come apportare modifiche al modello dati a un servizio mobile back-end di .NET](../articles/mobile-services/mobile-services-dotnet-backend-how-to-use-code-first-migrations.md).

5. Nella classe MainPage aggiungere il codice seguente:

        // Use a StorageFile to hold the captured image for upload.
        StorageFile media = null;
		
		private async void OnTakePhotoClick(object sender, RoutedEventArgs e)
		{
			// Capture a new photo or video from the device.
			CameraCaptureUI cameraCapture = new CameraCaptureUI();
			media = await cameraCapture
				.CaptureFileAsync(CameraCaptureUIMode.PhotoOrVideo);
        }

  	Questo codice consente di visualizzare l'interfaccia utente della fotocamera per acquisire un'immagine, che viene quindi salvata in un file di archiviazione.

6. Sostituire il metodo `InsertTodoItem` esistente con il codice seguente:
 
        private async void InsertTodoItem(TodoItem todoItem)
        {
            string errorString = string.Empty;
			
            if (media != null)
            {
                // Set blob properties of TodoItem.
                todoItem.ContainerName = "todoitemimages";
                todoItem.ResourceName = media.Name;
            }
			
            // Send the item to be inserted. When blob properties are set this
            // generates an SAS in the response.
            await todoTable.InsertAsync(todoItem);
			
            // If we have a returned SAS, then upload the blob.
            if (!string.IsNullOrEmpty(todoItem.SasQueryString))
            {
                // Get the URI generated that contains the SAS 
                // and extract the storage credentials.
                StorageCredentials cred = new StorageCredentials(todoItem.SasQueryString);
                var imageUri = new Uri(todoItem.ImageUri);
				
                // Instantiate a Blob store container based on the info in the returned item.
                CloudBlobContainer container = new CloudBlobContainer(
                    new Uri(string.Format("https://{0}/{1}",
                        imageUri.Host, todoItem.ContainerName)), cred);

                // Get the new image as a stream.
                using (var fileStream = await media.OpenStreamForReadAsync())
                {                   					
                    // Upload the new image as a BLOB from the stream.
                    CloudBlockBlob blobFromSASCredential =
                        container.GetBlockBlobReference(todoItem.ResourceName);
                    await blobFromSASCredential.UploadFromStreamAsync(fileStream.AsInputStream());
                }
				
				// When you request an SAS at the container-level instead of the blob-level,
				// you are able to upload multiple streams using the same container credentials.
            }
			
            // Add the new item to the collection.
            items.Add(todoItem);
        }

	Questo codice invia al servizio mobile la richiesta di inserire un nuovo oggetto TodoItem, incluso il nome file di immagine. La risposta contiene la firma di accesso condiviso, che viene quindi utilizzata per inserire l'immagine nell'archivio BLOB, e l'URI dell'immagine per l'associazione dati.

Il passaggio finale prevede il test dell'app e la convalida del completamento dei caricamenti.
		
##<a name="test"></a>Testare il caricamento delle immagini nell'app

1. In Visual Studio premere F5 per eseguire l'app.

2. Immettere del testo nell'apposita casella sotto **Insert a TodoItem**, quindi fare clic su **Photo**.

   	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-appbar.png)

  	Verrà visualizzata l'interfaccia utente di acquisizione della fotocamera.

3. Fare clic sull'immagine per acquisire una foto, quindi fare clic su **OK**.
  
   	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-camera.png)

4. Fare clic su **Upload** per inserire il nuovo elemento e caricare l'immagine.

	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-appbar2.png)

5. Il nuovo elemento appare nella visualizzazione elenco insieme all'immagine caricata.

	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-ie.png)

   	>[AZURE.NOTE]L'immagine viene scaricata automaticamente dal servizio di archiviazione BLOB quando la proprietà <code>imageUri</code> del nuovo elemento è associata al controllo <strong>Image</strong>.

<!---HONumber=August15_HO6-->