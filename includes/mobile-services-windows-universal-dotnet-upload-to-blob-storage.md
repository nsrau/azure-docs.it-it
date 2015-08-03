##Installare il client di archiviazione per le app di Windows Store

Per poter usare una firma di accesso condiviso per caricare immagini nel servizio di archiviazione BLOB, è necessario aggiungere innanzitutto il pacchetto NuGet che consente di installare la libreria client di archiviazione per le app di Windows Store.

1. In **Esplora soluzioni** in Visual Studio fare clic con il pulsante destro del mouse sul nome del progetto e quindi scegliere **Gestisci pacchetti NuGet**.

2. Nel riquadro a sinistra selezionare la categoria **Online**, cercare `WindowsAzure.Storage`, fare clic su **Installa** nel pacchetto di **Archiviazione di Azure** e quindi accettare i contratti di licenza.

  	![](./media/mobile-services-windows-universal-dotnet-upload-to-blob-storage/mobile-add-storage-nuget-package-dotnet.png)

  	La libreria client per i servizi di archiviazione di Azure verrà aggiunta al progetto.

A questo punto, aggiornare l'app di guida introduttiva in modo da acquisire e caricare le immagini.

##Aggiornamento dell'app client della guida introduttiva in modo da acquisire e caricare le immagini

1. In Visual Studio aprire il file Package.appxmanifest per il progetto app di Windows e nella scheda **Funzionalità**, abilitare **Webcam** e **Microfono**.

   	![](./media/mobile-services-windows-universal-dotnet-upload-to-blob-storage/mobile-app-manifest-camera.png)
 
   	In questo modo si garantirà che l'app sia in grado di utilizzare una fotocamera collegata al computer. Alla prima esecuzione dell'app, verrà chiesto agli utenti di consentire l'accesso alla fotocamera.

2. Ripetere il passaggio precedente per il progetto applicazione Windows Phone.
 
3. Nel progetto di app Windows, aprire il file MainPage. XAML e sostituire l’ elemento**StackPanel** subito dopo il primo**elemento QuickStartTask** con il codice seguente:

		<StackPanel Orientation="Horizontal" Margin="72,0,0,0">
            <TextBox Name="TextInput" Margin="5" MaxHeight="40" MinWidth="300"></TextBox>
            <AppBarButton Label="Photo" Icon="Camera" Name="ButtonCapture" Click="ButtonCapture_Click" />
            <AppBarButton Label="Upload" Icon="Upload" Name="ButtonSave" Click="ButtonSave_Click"/>
        </StackPanel>
        <Grid Name="captureGrid" Margin="62,0,0,0" Visibility="Collapsed" >
            <Grid.RowDefinitions>
                <RowDefinition Height="*" />
                <RowDefinition Height="Auto" />
            </Grid.RowDefinitions>
            <CaptureElement x:Name="previewElement" Grid.Row="0" Tapped="previewElement_Tapped" />
            <Image Name="imagePreview" Grid.Row="0"  />
            <StackPanel Name="captureButtons" Orientation="Horizontal" Grid.Row="1">
                <TextBlock Name="TextCapture" />
                <AppBarButton Label="Retake" Icon="Redo" Name="ButtonRetake" Click="ButtonCapture_Click" />
                <AppBarButton Label="Cancel" Icon="Cancel" Name="ButtonCancel" Click="ButtonCancel_Click" />
            </StackPanel>
        </Grid>

2. Sostituire l'elemento **StackPanel** in **DataTemplate** con il codice seguente:

        <StackPanel Orientation="Vertical">
            <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" 
                        Checked="CheckBoxComplete_Checked" Content="{Binding Text}" 
                        Margin="10,5" VerticalAlignment="Center"/>
            <Image Name="ImageUpload" Source="{Binding ImageUri, Mode=OneWay}"
                    MaxHeight="250"/>
        </StackPanel> 

   	Verrà aggiunta un'immagine in **ItemTemplate** e la relativa origine di associazione verrà impostata come URI dell'immagine caricata nel servizio di archiviazione BLOB.

3. Nel progetto di applicazione Windows Phone, aprire il file MainPage. XAML e sostituire l’**elemento ButtonSave** con il codice seguente:

        <StackPanel Grid.Row ="1" Grid.Column="1"  Orientation="Horizontal">
            <AppBarButton Label="Photo" Icon="Camera" Name="ButtonCapture" 
                          Click="ButtonCapture_Click" Height="78" Width="62" />
            <AppBarButton Label="Upload" Icon="Upload" Name="ButtonSave" 
                          Click="ButtonSave_Click"/>
        </StackPanel>
        <Grid Grid.Row="2" Name="captureGrid" Grid.RowSpan="3" Grid.ColumnSpan="2" 
              Canvas.ZIndex="99" Background="{ThemeResource ApplicationPageBackgroundThemeBrush}" 
              Visibility="Collapsed">
            <Grid.RowDefinitions>
                <RowDefinition Height="*" />
                <RowDefinition Height="Auto" />
            </Grid.RowDefinitions>
            <CaptureElement Grid.Row="0" x:Name="previewElement" Tapped="previewElement_Tapped" />                    
            <Image Grid.Row="0" Name="imagePreview" Visibility="Collapsed" />
            <StackPanel Grid.Row="1" Name="captureButtons" 
                        Orientation="Horizontal" Visibility="Collapsed">
                <TextBlock Name="TextCapture" VerticalAlignment="Bottom" />
                <AppBarButton Label="Retake" Icon="Redo" Name="ButtonRetake" 
                              Click="ButtonRetake_Click" />
                <AppBarButton Label="Cancel" Icon="Cancel" Name="ButtonCancel" 
                              Click="ButtonCancel_Click" />
            </StackPanel>
        </Grid>

2. Sostituire l'elemento **StackPanel** in **DataTemplate** con il codice seguente:

        <StackPanel Orientation="Vertical">
            <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" 
                      Checked="CheckBoxComplete_Checked" Content="{Binding Text}" 
                      Margin="10,5" VerticalAlignment="Center"/>
            <Image Name="ImageUpload" Source="{Binding ImageUri, Mode=OneWay}" 
                   MaxHeight="150"/>
        </StackPanel>

4. Nella cartella DataModel condivisa, aprire il file di progetto TodoItem.cs e aggiungere le seguenti proprietà alla classe TodoItem:

        [JsonProperty(PropertyName = "containerName")]
        public string ContainerName { get; set; }
		
        [JsonProperty(PropertyName = "resourceName")]
        public string ResourceName { get; set; }
		
        [JsonProperty(PropertyName = "sasQueryString")]
        public string SasQueryString { get; set; }
		
        [JsonProperty(PropertyName = "imageUri")]
        public string ImageUri { get; set; } 

3. Aprire il file del progetto MainPage.cs condiviso e aggiungere l'istruzione **usando** le seguenti istruzioni:
	
		using Windows.Media.Capture;
		using Windows.Media.MediaProperties;
		using Windows.Storage;
		using Windows.UI.Xaml.Input;
		using Microsoft.WindowsAzure.Storage.Auth;
		using Microsoft.WindowsAzure.Storage.Blob;
		using Windows.UI.Xaml.Media.Imaging;

5. Nella classe MainPage aggiungere il codice seguente:

        // Use a StorageFile to hold the captured image for upload.
        StorageFile media = null;
        MediaCapture cameraCapture;
        bool IsCaptureInProgress;

        private async Task CaptureImage()
        {
            // Capture a new photo or video from the device.
            cameraCapture = new MediaCapture();
            cameraCapture.Failed += cameraCapture_Failed;

            // Initialize the camera for capture.
            await cameraCapture.InitializeAsync();

            #if WINDOWS_PHONE_APP
            cameraCapture.SetPreviewRotation(VideoRotation.Clockwise90Degrees);
            cameraCapture.SetRecordRotation(VideoRotation.Clockwise90Degrees);
            #endif

            captureGrid.Visibility = Windows.UI.Xaml.Visibility.Visible;
            previewElement.Visibility = Windows.UI.Xaml.Visibility.Visible;
            previewElement.Source = cameraCapture;
            await cameraCapture.StartPreviewAsync();
        }

        private async void previewElement_Tapped(object sender, TappedRoutedEventArgs e)
        {
            // Block multiple taps.
            if (!IsCaptureInProgress)
            {                
                IsCaptureInProgress = true;

                // Create the temporary storage file.
                media = await ApplicationData.Current.LocalFolder
                    .CreateFileAsync("capture_file.jpg", CreationCollisionOption.ReplaceExisting);

                // Take the picture and store it locally as a JPEG.
                await cameraCapture.CapturePhotoToStorageFileAsync(
                    ImageEncodingProperties.CreateJpeg(), media);

                captureButtons.Visibility = Visibility.Visible;

				// Use the stored image as the preview source.
                BitmapImage tempBitmap = new BitmapImage(new Uri(media.Path));
                imagePreview.Source = tempBitmap;
                imagePreview.Visibility = Visibility.Visible;
                previewElement.Visibility = Visibility.Collapsed;
                IsCaptureInProgress = false;
            }
        }

        private async void cameraCapture_Failed(MediaCapture sender, MediaCaptureFailedEventArgs errorEventArgs)
        {
            // It's safest to return this back onto the UI thread to show the message dialog.
            MessageDialog dialog = new MessageDialog(errorEventArgs.Message);
            await this.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, 
                async () => { await dialog.ShowAsync(); });            
        }

        private async void ButtonCapture_Click(object sender, RoutedEventArgs e)
        {
            // Prevent multiple initializations.
            ButtonCapture.IsEnabled = false;

            await CaptureImage();
        }

        private async void ButtonRetake_Click(object sender, Windows.UI.Xaml.RoutedEventArgs e)
        {
            // Reset the capture and then start again.
            await ResetCaptureAsync();
            await CaptureImage();
        }

        private async void ButtonCancel_Click(object sender, Windows.UI.Xaml.RoutedEventArgs e)
        {
            await ResetCaptureAsync();
        }

        private async Task ResetCaptureAsync()
        {
            captureGrid.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
            imagePreview.Visibility = Visibility.Collapsed;
            captureButtons.Visibility = Visibility.Collapsed;
            previewElement.Source = null;
            ButtonCapture.IsEnabled = true;

            // Make sure we stop the preview and release resources.
            await cameraCapture.StopPreviewAsync();
            cameraCapture.Dispose();
            media = null;
        }

  	Questo codice consente di visualizzare l'interfaccia utente per acquisire un'immagine, che viene quindi salvata in un file di archiviazione.

6. Sostituire il metodo `InsertTodoItem` esistente con il codice seguente:
 
        private async Task InsertTodoItem(TodoItem todoItem)
        {
            string errorString = string.Empty;

            if (media != null)
            {
                // Set blob properties of TodoItem.
                todoItem.ContainerName = "todoitemimages";

                // Use a unigue GUID to avoid collisions.
                todoItem.ResourceName = Guid.NewGuid().ToString();
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
                using (var inputStream = await media.OpenReadAsync())
                {
                    // Upload the new image as a BLOB from the stream.
                    CloudBlockBlob blobFromSASCredential =
                        container.GetBlockBlobReference(todoItem.ResourceName);
                    await blobFromSASCredential.UploadFromStreamAsync(inputStream);
                }

                // When you request an SAS at the container-level instead of the blob-level,
                // you are able to upload multiple streams using the same container credentials.

                await ResetCaptureAsync();
            }

            // Add the new item to the collection.
            items.Add(todoItem);
        }

	Questo codice invia una richiesta al servizio mobile per inserire un nuovo oggetto TodoItem. La risposta contiene la firma, viene quindi utilizzata per caricare l'immagine dall'archiviazione locale nell'archiviazione Blob di Azure. L'URL dell'immagine caricata, viene utilizzato nell'associazione dati.

Il passaggio finale consiste nel testare entrambe le versioni dell'applicazione e convalidare il completamento dei caricamenti da entrambi i dispositivi.
		
##<a name="test"></a>Testare il caricamento delle immagini nell'app

1. In Visual Studio, assicurarsi che il progetto di Windows sia impostato come progetto predefinito, quindi premere F5 chiave per eseguire l'applicazione.

2. Immettere del testo nell'apposita casella sotto **Insert a TodoItem**, quindi fare clic su **Photo**.

3. Fare clic o toccare l'anteprima per acquisire un'immagine, quindi fare clic su**caricare**per inserire il nuovo elemento e caricare l'immagine.

	![](./media/mobile-services-windows-universal-dotnet-upload-to-blob-storage/mobile-quickstart-blob-appbar2.png)

4. Il nuovo elemento appare nella visualizzazione elenco insieme all'immagine caricata.

	![](./media/mobile-services-windows-universal-dotnet-upload-to-blob-storage/mobile-quickstart-blob-ie.png)

   	>[AZURE.NOTE]L'immagine viene scaricata automaticamente dal servizio di archiviazione BLOB quando la proprietà *imageUri* del nuovo elemento è associata al controllo **Image**.

5. Arrestare l'applicazione e riavviare la versione del progetto dell'applicazione Windows Phone.

	Viene visualizzata l'immagine caricata in precedenza.

6. Come in precedenza, immettere il testo nella casella di testo, quindi fare clic su**foto**.

   	![](./media/mobile-services-windows-universal-dotnet-upload-to-blob-storage/mobile-upload-blob-app-view-wp8.png)

3. Toccare l'anteprima per acquisire un'immagine, quindi fare clic su**caricare**per inserire il nuovo elemento e caricare l'immagine.

	![](./media/mobile-services-windows-universal-dotnet-upload-to-blob-storage/mobile-upload-blob-app-view-final-wp8.png)

È stata completata l'esercitazione di immagini di caricamento.

<!---HONumber=July15_HO4-->