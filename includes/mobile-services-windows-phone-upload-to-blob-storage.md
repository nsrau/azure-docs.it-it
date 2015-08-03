
##<a name="add-select-images"></a>Aggiornare l'app client della guida introduttiva in modo da acquisire e caricare le immagini

In questa sezione il progetto dell'esercitazione [Introduzione a Servizi mobili] verrà aggiornato in modo da consentire di scattare foto e caricarle in Archiviazione BLOB di Azure. Per acquisire l'immagine, in questa esercitazione viene usato [CameraCaptureTask] ricavato dallo spazio dei nomi `Microsoft.Phone.Tasks`. Questa classe avvia l'interfaccia utente della fotocamera sul dispositivo Windows Phone per acquisire la foto e salva automaticamente l'immagine nel rullino sul dispositivo Windows Phone. Se non si desidera salvare le immagini nel rullino, usare la classe [PhotoCamera] nello spazio dei nomi `Microsoft.Devices`.

1. In Esplora soluzioni di Visual Studio, nel progetto espandere **Proprietà**. Aprire quindi il file WMAppManifest.xml e nella scheda **Funzionalità** abilitare la fotocamera facendo clic su **ID_CAP_ISV_CAMERA**. Chiudere il file per salvare la modifica.

   	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-WMAppmanifest-wp8.png)

   	In questo modo si garantirà che l'app sia in grado di utilizzare una fotocamera collegata al computer. Alla prima esecuzione dell'app, verrà chiesto agli utenti di consentire l'accesso alla fotocamera.

2. Aprire il file MainPage.xaml e sostituire l'elemento **Grid** denominato **ContentPanel** con il codice seguente:

        <!--ContentPanel - place additional content here-->
        <Grid x:Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
            <Grid.RowDefinitions>
                <RowDefinition Height="Auto" />
                <RowDefinition Height="Auto" />
                <RowDefinition Height="Auto" />
                <RowDefinition Height="Auto" />
                <RowDefinition Height="Auto" />
                <RowDefinition Height="*" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="2*" />
                <ColumnDefinition Width="2*" />
            </Grid.ColumnDefinitions>
            <TextBlock Grid.Row="0" Grid.ColumnSpan="2" Text="Enter some text below, click Capture Image to add a captured image. Then click Save to insert a new TodoItem item into your database" TextWrapping="Wrap" Margin="12"/>
            <TextBox Grid.Row="1" Grid.ColumnSpan="2" Name="TextInput" Text="" />
            <Button Name="ButtonCaptureImage" Grid.Row="2" Click="ButtonCaptureImage_Click">Capture Image</Button>
            <Button Grid.Row ="2" Grid.Column="1" Name="ButtonSave" Click="ButtonSave_Click">Save</Button>
            <TextBlock Grid.Row="3" Grid.ColumnSpan="2" Text="Click refresh below to load the unfinished TodoItems from your database. Use the checkbox to complete and update your TodoItems" TextWrapping="Wrap" Margin="12" />
            <Button Grid.Row="4" Grid.ColumnSpan="2" Name="ButtonRefresh" Click="ButtonRefresh_Click">Refresh</Button>
            <phone:LongListSelector Grid.Row="5" Grid.ColumnSpan="2" Name="ListItems">
                <phone:LongListSelector.ItemTemplate>
                    <DataTemplate>
                        <StackPanel Orientation="Vertical">
                            <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" Checked="CheckBoxComplete_Checked" Content="{Binding Text}" Margin="10,5" VerticalAlignment="Center"/>
                            <Image Name="ImageUpload" Source="{Binding ImageUri, Mode=OneWay}" MaxHeight="150"/>
                        </StackPanel>
                    </DataTemplate>
                </phone:LongListSelector.ItemTemplate>
            </phone:LongListSelector>
        </Grid>


   	Viene aggiunto un nuovo pulsante per avviare [CameraCaptureTask], viene aggiunta un'immagine a **ItemTemplate** e l'origine dell'associazione viene impostata come URI dell'immagine caricata nel servizio di archiviazione BLOB.

3. Aprire il file del progetto MainPage.xaml.cs e aggiungere le istruzioni **using** seguenti:
	
		using Microsoft.Phone.Tasks;
		using System.IO;
		using Microsoft.WindowsAzure.Storage.Auth;
		using Microsoft.WindowsAzure.Storage.Blob;
    
4. Nel file del progetto MainPage.xaml.cs aggiornare la classe TodoItem aggiungendo le proprietà seguenti:

        [JsonProperty(PropertyName = "containerName")]
        public string ContainerName { get; set; }
		
        [JsonProperty(PropertyName = "resourceName")]
        public string ResourceName { get; set; }
		
        [JsonProperty(PropertyName = "sasQueryString")]
        public string SasQueryString { get; set; }
		
        [JsonProperty(PropertyName = "imageUri")]
        public string ImageUri { get; set; } 

5. Nel file del progetto MainPage.xaml.cs aggiornare la classe MainPage. Aggiungere il codice seguente per dichiarare [CameraCaptureTask] e un oggetto stream che fa riferimento all'immagine acquisita:

        // Using the CameraCaptureTask to allow the user to capture a todo item image //
        CameraCaptureTask cameraCaptureTask;
		
        // Using a stream reference to upload the image to blob storage.
        Stream imageStream = null;

6. Nel file del progetto MainPage.xaml.cs aggiornare la classe MainPage. Aggiungere il codice seguente per aggiornare il costruttore in modo da creare CameraCaptureTask e aggiungere un gestore dell'evento Completed:

        // Constructor
        public MainPage()
        {
            InitializeComponent();
			
            cameraCaptureTask = new CameraCaptureTask();
            cameraCaptureTask.Completed += cameraCaptureTask_Completed;
        }
		
        void cameraCaptureTask_Completed(object sender, PhotoResult e)
        {
            imageStream = e.ChosenPhoto;
        }

7. Nel file del progetto MainPage.xaml.cs aggiornare la classe MainPage. Aggiungere il codice seguente che visualizza l'interfaccia utente della fotocamera per consentire all'utente di acquisire un'immagine quando si fa clic sul pulsante **Capture Image**:

        private void ButtonCaptureImage_Click(object sender, RoutedEventArgs e)
        {
            cameraCaptureTask.Show();
        }


8. Nel file del progetto MainPage.xaml.cs aggiornare la classe MainPage. Sostituire il metodo `InsertTodoItem` esistente con il codice seguente:
 
        private async void InsertTodoItem(TodoItem todoItem)
        {
            string errorString = string.Empty;            
			
            if (imageStream != null)
            {
                // Set blob properties of TodoItem.
                todoItem.ContainerName = "todoitemimages";
                todoItem.ResourceName = Guid.NewGuid().ToString() + ".jpg";
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
				
                // Upload the new image as a BLOB from the stream.
                CloudBlockBlob blobFromSASCredential =
                    container.GetBlockBlobReference(todoItem.ResourceName);
                await blobFromSASCredential.UploadFromStreamAsync(imageStream);
				
				// When you request an SAS at the container-level instead of the blob-level,
				// you are able to upload multiple streams using the same container credentials.

                imageStream = null;
            }              
			
            // Add the new item to the collection.
            items.Add(todoItem);
            TextInput.Text = "";
        }


	Questo codice invia al servizio mobile la richiesta di inserire un nuovo oggetto TodoItem, incluso il nome file di immagine. La risposta contiene la firma di accesso condiviso, che viene quindi utilizzata per inserire l'immagine nell'archivio BLOB, e l'URI dell'immagine per l'associazione dati.

Il passaggio finale prevede il test dell'app e la convalida del completamento dei caricamenti.
		
##<a name="test"></a>Testare il caricamento delle immagini nell'app

1. In Visual Studio, è possibile premere F5 per testare l'app nell'emulatore o con un vero e proprio dispositivo di destinazione.

2. Immettere un testo nella casella di testo, quindi fare clic su **Capture Image**.

   	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-wp8.png)

  	Verrà visualizzata l'interfaccia utente di acquisizione della fotocamera.

3. Per scattare una foto, fare clic sull'immagine o sul pulsante della snapshot sul telefono.
  
   	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-camera-wp8.png)

4. Fare clic su **accept** per accettare l'immagine e uscire dall'interfaccia utente della fotocamera.

    ![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-camera-accept-wp8.png)

5. Fare clic su **Save** per inserire il nuovo elemento e caricare l'immagine.

	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-save-wp8.png)

6. Il nuovo elemento appare nella visualizzazione elenco insieme all'immagine caricata.

	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-final-wp8.png)

   >[AZURE.NOTE]L'immagine viene scaricata automaticamente dal servizio di archiviazione BLOB quando la proprietà <code>imageUri</code> del nuovo elemento è associata al controllo <strong>Image</strong>.


[Introduzione a Servizi mobili]: ../articles/mobile-services-windows-phone-get-started.md
[CameraCaptureTask]: http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.tasks.cameracapturetask(v=vs.105).aspx
[PhotoCamera]: http://msdn.microsoft.com/library/windowsphone/develop/microsoft.devices.photocamera(v=vs.105).aspx

<!---HONumber=July15_HO4-->