<properties
    pageTitle="Connettersi ad Archiviazione di Azure nell'app Xamarin.Forms"
    description="Aggiungere immagini all'app per dispositivi mobili Xamarin.Forms ";todo list"; connettendosi all'archivio BLOB di Azure"
    documentationCenter="xamarin"
    authors="lindydonna"
    manager="erikre"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="donnam"/>  

#Connettersi ad Archiviazione di Azure nell'app Xamarin.Forms

## Overview

Azure Mobile Apps SDK per client e server supporta la sincronizzazione offline di dati strutturati con operazioni CRUD sull'endpoint /tables. In genere questi dati vengono archiviati in un database o in un archivio simile e di solito questi archivi dati non possono archiviare in modo efficiente dati binari di grandi dimensioni. Alcune applicazioni hanno anche dati correlati archiviati altrove, ad esempio archivi BLOB, condivisioni file, ed è utile poter creare associazioni tra i record nell'endpoint /tables e altri dati.

Questo argomento illustra come aggiungere il supporto per le immagini al progetto di avvio rapido "todo list" per le app per dispositivi mobili. Prima di tutto è necessario completare l'esercitazione [Creare un'app Xamarin.Forms].

In questa esercitazione si creerà un account di archiviazione e si aggiungerà una stringa di connessione al back-end di App per dispositivi mobili. Si aggiungerà quindi un nuovo controller, che eredita dal nuovo tipo di App per dispositivi mobili `StorageController<T>`, al progetto server.

>[AZURE.TIP] In questa esercitazione è disponibile un [esempio complementare](https://azure.microsoft.com/documentation/samples/app-service-mobile-dotnet-todo-list-files/) che può essere distribuito nel proprio account Azure.

## Prerequisiti

* Completare l'esercitazione [Creare un'app Xamarin.Forms], che elenca altri prerequisiti. Questo articolo usa l'app completata di quell'esercitazione.

>[AZURE.NOTE] Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, passare alla pagina [Prova il servizio app](https://tryappservice.azure.com/?appServiceName=mobile). In questa pagina è possibile creare immediatamente un'app per dispositivi mobili iniziale temporanea in App Service. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Creare un account di archiviazione

1. Creare un account di archiviazione seguendo le istruzioni nella sezione [Creare un account di archiviazione di Azure] dell'esercitazione.

2. Nel portale di Azure passare all'account di archiviazione appena creato e fare clic sull'icona **Chiavi**. Copiare la **Stringa di connessione primaria**.

3. Passare al back-end dell'app per dispositivi mobili In **Tutte le impostazioni** -> **Impostazioni applicazione** -> **Stringhe di connessione** creare una nuova chiave denominata `MS_AzureStorageAccountConnectionString` e usare il valore copiato dall'account di archiviazione. Usare **Personalizzata** come tipo di chiave.

## Aggiungere un controller di archiviazione al server

È necessario aggiungere un nuovo controller al progetto server che risponderà alle richieste di un token di firma di accesso condiviso per Archiviazione di Azure e restituirà un elenco di file corrispondenti a un record:

- [Aggiungere un controller di archiviazione al progetto server](#add-controller-code)
- [Route registrate dal controller di archiviazione](#routes-registered)
- [Comunicazione tra client e server](#client-communication)

###<a name="add-controller-code"></a>Aggiungere un controller di archiviazione al progetto server

1. In Visual Studio aprire il progetto server .NET. Aggiungere il pacchetto NuGet [Microsoft.Azure.Mobile.Server.Files]. Assicurarsi di selezionare **Includi versione preliminare**.

2. In Visual Studio aprire il progetto server .NET. Fare clic con il pulsante destro del mouse sulla cartella **Controller** e quindi scegliere **Aggiungi** -> **Controller** -> **Controller Web API 2 - Vuoto**. Assegnare un nome al controller`TodoItemStorageController`.

3. Aggiungere le istruzioni using seguenti:

        using Microsoft.Azure.Mobile.Server.Files;
        using Microsoft.Azure.Mobile.Server.Files.Controllers;

4. Modificare la classe di base in `StorageController`:
    
        public class TodoItemStorageController : StorageController<TodoItem>

5. Aggiungere alla classe i metodi seguenti:

        [HttpPost]
        [Route("tables/TodoItem/{id}/StorageToken")]
        public async Task<HttpResponseMessage> PostStorageTokenRequest(string id, StorageTokenRequest value)
        {
            StorageToken token = await GetStorageTokenAsync(id, value);

            return Request.CreateResponse(token);
        }

        // Get the files associated with this record
        [HttpGet]
        [Route("tables/TodoItem/{id}/MobileServiceFiles")]
        public async Task<HttpResponseMessage> GetFiles(string id)
        {
            IEnumerable<MobileServiceFile> files = await GetRecordFilesAsync(id);

            return Request.CreateResponse(files);
        }

        [HttpDelete]
        [Route("tables/TodoItem/{id}/MobileServiceFiles/{name}")]
        public Task Delete(string id, string name)
        {
            return base.DeleteFileAsync(id, name);
        }

6. Aggiornare la configurazione dell'API Web per configurare il routing degli attributi. In **Startup.MobileApp.cs** aggiungere la riga seguente al metodo `ConfigureMobileApp()` dopo la definizione della variabile `config`:

        config.MapHttpAttributeRoutes();

7. Pubblicare il progetto server nel back-end dell'app per dispositivi mobili.

###<a name="routes-registered"></a>Route registrate dal controller di archiviazione

Il nuovo `TodoItemStorageController` espone due sottorisorse nel record gestito:

- StorageToken

    + HTTP POST: crea un token di archiviazione
    
        `/tables/TodoItem/{id}/MobileServiceFiles`  
    
- MobileServiceFiles

    + HTTP GET: recupera un elenco di file associati al record
    
        `/tables/TodoItem/{id}/MobileServiceFiles`

    + HTTP DELETE: elimina il file specificato nell'identificatore di risorsa file
    
        `/tables/TodoItem/{id}/MobileServiceFiles/{fileid}`

###<a name="client-communication"></a>Comunicazione tra client e server

Si noti che per `TodoItemStorageController` *non* è disponibile una route per caricare o scaricare un BLOB. Per eseguire queste operazioni, un client per dispositivi mobili interagisce *direttamente* con l'archivio BLOB, dopo aver ottenuto un token di firma di accesso condiviso per accedere in modo sicuro a un BLOB o un contenitore specifico. Questa è una progettazione architetturale importante, perché diversamente l'accesso all'archiviazione sarebbe limitato dalla scalabilità e dalla disponibilità del back-end mobile. Connettendosi invece direttamente all'archiviazione di Azure, il client mobile può sfruttarne le funzionalità, come il partizionamento automatico e la distribuzione geografica.

Una firma di accesso condiviso fornisce accesso delegato controllato alle risorse dell'account di archiviazione. Questo significa che è possibile concedere a un client autorizzazioni limitate per BLOB, code o tabelle per un periodo di tempo specificato e con un set di autorizzazioni specificato senza dover condividere le chiavi di accesso dell'account. Per altre informazioni, vedere [Informazioni sulle firme di accesso condiviso].

Il diagramma seguente mostra le interazioni di client e server. Prima di caricare un file, il client richiede al servizio un token di firma di accesso condiviso. Il servizio usa la stringa di connessione a risorsa di archiviazione per generare una nuova firma di accesso condiviso, che restituisce quindi al client. La firma di accesso condiviso ha una durata limitata e limita le autorizzazioni solo a un file o un contenitore specifico. Il client mobile usa quindi questa firma di accesso condiviso e Azure Storage SDK per client per caricare il file nell'archivio BLOB.

![Richiesta di un token di firma di accesso condiviso](./media/app-service-mobile-xamarin-forms-blob-storage/storage-token-diagram.png)

## Aggiornare l'app client per aggiungere il supporto per le immagini

Aprire il progetto di avvio rapido Xamarin.Forms in Visual Studio o Xamarin Studio. Verranno installati i pacchetti NuGet e aggiornati il progetto della libreria portabile e i progetti client per iOS, Android e Windows:

- [Aggiungere i pacchetti NuGet](#add-nuget)
- [Aggiungere l'interfaccia IPlatform](#add-iplatform)
- [Aggiungere la classe FileHelper](#add-filehelper)
- [Aggiungere un gestore di sincronizzazione file](#file-sync-handler)
- [Aggiornare TodoItemManager](#update-todoitemmanager)
- [Aggiungere una visualizzazione dettagli](#add-details-view)
- [Aggiornare la visualizzazione principale](#update-main-view)
- [Aggiornare il progetto Android](#update-android), il [progetto iOS](#update-ios) e il [progetto Windows](#update-windows)

>[AZURE.NOTE] Questa esercitazione contiene solo istruzioni per le piattaforme Android, iOS e Windows Store, non per Windows Phone.

###<a name="add-nuget"></a>Aggiungere i pacchetti NuGet

Fare clic con il pulsante destro del mouse sulla soluzione e quindi scegliere **Gestisci pacchetti NuGet per la soluzione**. Aggiungere i pacchetti NuGet seguenti a **tutti** i progetti presenti nella soluzione. Assicurarsi di selezionare **Includi versione preliminare**.

  - [Microsoft.Azure.Mobile.Client.Files]

  - [Microsoft.Azure.Mobile.Client.SQLiteStore]

  - [PCLStorage]

Per praticità, questo esempio usa la libreria [PCLStorage], che però non è richiesta da Azure Mobile Apps SDK per client.

[PCLStorage]: https://www.nuget.org/packages/PCLStorage/

###<a name="add-iplatform"></a>Aggiungere l'interfaccia IPlatform

Creare una nuova interfaccia `IPlatform` nel progetto di libreria portabile principale. Viene seguito il modello [Xamarin.Forms DependencyService] per caricare la classe specifica della piattaforma in fase di esecuzione. Successivamente si aggiungeranno le implementazioni specifiche della piattaforma in ogni progetto client.

1. Aggiungere le istruzioni using seguenti:

        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Sync;

2. Sostituire l'implementazione con il codice seguente:

        public interface IPlatform
        {
            Task <string> GetTodoFilesPathAsync();

            Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata);

            Task<string> TakePhotoAsync(object context);

            Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename);
        }

###<a name="add-filehelper"></a>Aggiungere la classe FileHelper

1. Creare una nuova classe `FileHelper` nel progetto di libreria portabile principale. Aggiungere le istruzioni using seguenti:

        using System.IO;
        using PCLStorage;
        using System.Threading.Tasks;
        using Xamarin.Forms;

2. Aggiungere la definizione di classe:

        public class FileHelper
        {
            public static async Task<string> CopyTodoItemFileAsync(string itemId, string filePath)
            {
                IFolder localStorage = FileSystem.Current.LocalStorage;

                string fileName = Path.GetFileName(filePath);
                string targetPath = await GetLocalFilePathAsync(itemId, fileName);

                var sourceFile = await localStorage.GetFileAsync(filePath);
                var sourceStream = await sourceFile.OpenAsync(FileAccess.Read);

                var targetFile = await localStorage.CreateFileAsync(targetPath, CreationCollisionOption.ReplaceExisting);

                using (var targetStream = await targetFile.OpenAsync(FileAccess.ReadAndWrite)) {
                    await sourceStream.CopyToAsync(targetStream);
                }

                return targetPath;
            }

            public static async Task<string> GetLocalFilePathAsync(string itemId, string fileName)
            {
                IPlatform platform = DependencyService.Get<IPlatform>();

                string recordFilesPath = Path.Combine(await platform.GetTodoFilesPathAsync(), itemId);

                    var checkExists = await FileSystem.Current.LocalStorage.CheckExistsAsync(recordFilesPath);
                    if (checkExists == ExistenceCheckResult.NotFound) {
                        await FileSystem.Current.LocalStorage.CreateFolderAsync(recordFilesPath, CreationCollisionOption.ReplaceExisting);
                    }

                return Path.Combine(recordFilesPath, fileName);
            }

            public static async Task DeleteLocalFileAsync(Microsoft.WindowsAzure.MobileServices.Files.MobileServiceFile fileName)
            {
                string localPath = await GetLocalFilePathAsync(fileName.ParentId, fileName.Name);
                var checkExists = await FileSystem.Current.LocalStorage.CheckExistsAsync(localPath);

                if (checkExists == ExistenceCheckResult.FileExists) {
                    var file = await FileSystem.Current.LocalStorage.GetFileAsync(localPath);
                    await file.DeleteAsync();
                }
            }
        }

###<a name="file-sync-handler"></a>Aggiungere un gestore di sincronizzazione file

Creare una nuova classe `TodoItemFileSyncHandler` nel progetto di libreria portabile principale. Questa classe contiene i callback da Azure SDK per notificare al codice quando un file viene aggiunto o rimosso.

Azure Mobile SDK per client non archivia in effetti i dati dei file. Questo SDK richiama l'implementazione di `IFileSyncHandler` che a sua volta determina se e come i file vengono archiviati nel dispositivo locale.

1. Aggiungere le istruzioni using seguenti:

        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Xamarin.Forms;

2. Sostituire la definizione di classe con il codice seguente:

        public class TodoItemFileSyncHandler : IFileSyncHandler
        {
            private readonly TodoItemManager todoItemManager;

            public TodoItemFileSyncHandler(TodoItemManager itemManager)
            {
                this.todoItemManager = itemManager;
            }

            public Task<IMobileServiceFileDataSource> GetDataSource(MobileServiceFileMetadata metadata)
            {
                IPlatform platform = DependencyService.Get<IPlatform>();
                return platform.GetFileDataSource(metadata);
            }

            public async Task ProcessFileSynchronizationAction(MobileServiceFile file, FileSynchronizationAction action)
            {
                if (action == FileSynchronizationAction.Delete) {
                    await FileHelper.DeleteLocalFileAsync(file);
                }
                else { // Create or update. We're aggressively downloading all files.
                    await this.todoItemManager.DownloadFileAsync(file);
                }
            }
        }

###<a name="update-todoitemmanager"></a>Aggiornare TodoItemManager

1. In **TodoItemManager.cs** rimuovere il commento dalla riga `#define OFFLINE_SYNC_ENABLED`.

2. In **TodoItemManager.cs** aggiungere le istruzioni using seguenti:

        using System.IO;
        using Xamarin.Forms;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Eventing;

3. Nel costruttore di `TodoItemManager` aggiungere il codice seguente dopo la chiamata a `DefineTable()`:

        // Initialize file sync
        this.client.InitializeFileSyncContext(new TodoItemFileSyncHandler(this), store);

4. Nel costruttore sostituire la chiamata a `InitializeAsync` con il codice seguente. Questo garantisce che siano presenti callback quando i record vengono modificati nell'archivio locale. La funzionalità di sincronizzazione file usa questi callback per attivare il gestore di sincronizzazione file.

        this.client.SyncContext.InitializeAsync(store, StoreTrackingOptions.NotifyLocalAndServerOperations);

5. In `SyncAsync()` aggiungere il codice seguente dopo la chiamata a `PushAsync()`:

        await this.todoTable.PushFileChangesAsync();

6. Aggiungere i metodi seguenti a `TodoItemManager`:

        internal async Task DownloadFileAsync(MobileServiceFile file)
        {
            var todoItem = await todoTable.LookupAsync(file.ParentId);
            IPlatform platform = DependencyService.Get<IPlatform>();

            string filePath = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name); 
            await platform.DownloadFileAsync(this.todoTable, file, filePath);
        }

        internal async Task<MobileServiceFile> AddImage(TodoItem todoItem, string imagePath)
        {
            string targetPath = await FileHelper.CopyTodoItemFileAsync(todoItem.Id, imagePath);
            return await this.todoTable.AddFileAsync(todoItem, Path.GetFileName(targetPath));
        }

        internal async Task DeleteImage(TodoItem todoItem, MobileServiceFile file)
        {
            await this.todoTable.DeleteFileAsync(file);
        }

        internal async Task<IEnumerable<MobileServiceFile>> GetImageFilesAsync(TodoItem todoItem)
        {
            return await this.todoTable.GetFilesAsync(todoItem);
        }

###<a name="add-details-view"></a>Aggiungere una visualizzazione dettagli

In questa sezione si aggiungerà una nuova visualizzazione dettagli per un elemento todo. La visualizzazione viene creata quando l'utente seleziona un elemento todo e consente di aggiungere nuove immagini a un elemento.

1. Aggiungere una nuova classe **TodoItemImage** al progetto di libreria portabile con l'implementazione seguente:

        public class TodoItemImage : INotifyPropertyChanged
        {
            private string name;
            private string uri;

            public MobileServiceFile File { get; private set; }

            public string Name
            {
                get { return name; }
                set
                {
                    name = value;
                    OnPropertyChanged(nameof(Name));
                }
            }

            public string Uri
            {
                get { return uri; }      
                set
                {
                    uri = value;
                    OnPropertyChanged(nameof(Uri));
                }
            }

            public TodoItemImage(MobileServiceFile file, TodoItem todoItem)
            {
                Name = file.Name;
                File = file;

                FileHelper.GetLocalFilePathAsync(todoItem.Id, file.Name).ContinueWith(x => this.Uri = x.Result);
            }

            public event PropertyChangedEventHandler PropertyChanged;

            private void OnPropertyChanged(string propertyName)
            {
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
            }
        }

2. Modificare **App.cs**. Sostituire l'inizializzazione di `MainPage` con il codice seguente:
    
        MainPage = new NavigationPage(new TodoList());

3. In **App.cs** aggiungere la proprietà seguente:

        public static object UIContext { get; set; }

4. Fare clic con il pulsante destro del mouse sul progetto di libreria portabile e scegliere **Aggiungi** -> **Nuovo elemento** -> **Multipiattaforma** -> **Form Xaml Page**. Assegnare alla visualizzazione il nome `TodoItemDetailsView`.

5. Aprire **TodoItemDetailsView.xaml** e sostituire il corpo di ContentPage con il codice seguente:

          <Grid>
            <Grid.RowDefinitions>
              <RowDefinition Height="Auto"/>
              <RowDefinition Height="Auto"/>
              <RowDefinition Height="*"/>
            </Grid.RowDefinitions>
            <Button Clicked="OnAdd" Text="Add image"></Button>
            <ListView x:Name="imagesList"
                      ItemsSource="{Binding Images}"
                      IsPullToRefreshEnabled="false"
                      Grid.Row="2">
              <ListView.ItemTemplate>
                <DataTemplate>
                  <ImageCell ImageSource="{Binding Uri}"
                             Text="{Binding Name}">
                  </ImageCell>
                </DataTemplate>
              </ListView.ItemTemplate>
            </ListView>
          </Grid>

6. Modificare **TodoItemDetailsView.xaml.cs** e aggiungere le istruzioni using seguenti:

        using System.Collections.ObjectModel;
        using Microsoft.WindowsAzure.MobileServices.Files;

7. Sostituire l'implementazione di `TodoItemDetailsView` con il codice seguente:

        public partial class TodoItemDetailsView : ContentPage
        {
            private TodoItemManager manager;

            public TodoItem TodoItem { get; set; }        
            public ObservableCollection<TodoItemImage> Images { get; set; }

            public TodoItemDetailsView(TodoItem todoItem, TodoItemManager manager)
            {
                InitializeComponent();
                this.Title = todoItem.Name;

                this.TodoItem = todoItem;
                this.manager = manager;

                this.Images = new ObservableCollection<TodoItemImage>();
                this.BindingContext = this;
            }

            public async Task LoadImagesAsync()
            {
                IEnumerable<MobileServiceFile> files = await this.manager.GetImageFilesAsync(TodoItem);
                this.Images.Clear();

                foreach (var f in files) {
                    var todoImage = new TodoItemImage(f, this.TodoItem);
                    this.Images.Add(todoImage);
                }
            }

            public async void OnAdd(object sender, EventArgs e)
            {
                IPlatform mediaProvider = DependencyService.Get<IPlatform>();
                string sourceImagePath = await mediaProvider.TakePhotoAsync(App.UIContext);

                if (sourceImagePath != null) {
                    MobileServiceFile file = await this.manager.AddImage(this.TodoItem, sourceImagePath);

                    var image = new TodoItemImage(file, this.TodoItem);
                    this.Images.Add(image);
                }
            }
        }

###<a name="update-main-view"></a>Aggiornare la visualizzazione principale 

Aggiornare la visualizzazione principale in modo da aprire la visualizzazione dettagli quando si seleziona un elemento todo.

In **TodoList.xaml.cs** sostituire l'implementazione di `OnSelected` con il codice seguente:

    public async void OnSelected(object sender, SelectedItemChangedEventArgs e)
    {
        var todo = e.SelectedItem as TodoItem;

        if (todo != null) {
            var detailsView = new TodoItemDetailsView(todo, manager);
            await detailsView.LoadImagesAsync();
            await Navigation.PushAsync(detailsView);
        }

        todoList.SelectedItem = null;
    }

###<a name="update-android"></a>Aggiornare il progetto Android

Aggiungere il codice specifico della piattaforma al progetto Android, incluso il codice per il download di un file e l'uso della fotocamera per acquisire una nuova immagine.

Questo codice usa [DependencyService](https://developer.xamarin.com/guides/xamarin-forms/dependency-service/) di Xamarin.Forms per caricare la classe specifica della piattaforma in fase di esecuzione.

1. Aggiungere il componente **Xamarin.Mobile** al progetto Android.

2. Aggiungere una nuova classe `DroidPlatform` con l'implementazione seguente. Sostituire "YourNamespace" con lo spazio dei nomi principale del progetto.

        using System;
        using System.IO;
        using System.Threading.Tasks;
        using Android.Content;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Xamarin.Media;

        [assembly: Xamarin.Forms.Dependency(typeof(YourNamespace.Droid.DroidPlatform))]
        namespace YourNamespace.Droid
        {
            public class DroidPlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        var uiContext = context as Context;
                        if (uiContext != null) {
                            var mediaPicker = new MediaPicker(uiContext);
                            var photo = await mediaPicker.TakePhotoAsync(new StoreCameraMediaOptions());

                            return photo.Path;
                        }
                    }
                    catch (TaskCanceledException) {
                    }

                    return null;
                }

                public Task<string> GetTodoFilesPathAsync()
                {
                    string appData = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
                    string filesPath = Path.Combine(appData, "TodoItemFiles");

                    if (!Directory.Exists(filesPath)) {
                        Directory.CreateDirectory(filesPath);
                    }

                    return Task.FromResult(filesPath);
                }
            }
        }

3. Modificare **MainActivity.cs**. In `OnCreate` aggiungere il codice seguente prima della chiamata a `LoadApplication()`:

        App.UIContext = this;

###<a name="update-ios"></a>Aggiornare il progetto iOS

Aggiungere codice specifico della piattaforma al progetto iOS.

1. Aggiungere il componente **Xamarin.Mobile** al progetto iOS.

2. Aggiungere una nuova classe `TouchPlatform` con l'implementazione seguente. Sostituire "YourNamespace" con lo spazio dei nomi principale del progetto.

        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Xamarin.Media;

        [assembly: Xamarin.Forms.Dependency(typeof(YourNamespace.iOS.TouchPlatform))]
        namespace YourNamespace.iOS
        {
            class TouchPlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        var mediaPicker = new MediaPicker();
                        var mediaFile = await mediaPicker.PickPhotoAsync();
                        return mediaFile.Path;
                    }
                    catch (TaskCanceledException) {
                        return null;
                    }
                }

                public Task<string> GetTodoFilesPathAsync()
                {
                    string filesPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments), "TodoItemFiles");

                    if (!Directory.Exists(filesPath)) {
                        Directory.CreateDirectory(filesPath);
                    }

                    return Task.FromResult(filesPath);
                }
            }
        }

3. Modificare **AppDelegate.cs** e rimuovere il commento dalla chiamata a `SQLitePCL.CurrentPlatform.Init()`.

###<a name="update-windows"></a>Aggiornare il progetto Windows

1. Installare l'estensione di Visual Studio [SQLite per Windows 8.1](http://go.microsoft.com/fwlink/?LinkID=716919). Per altre informazioni, vedere l'esercitazione [Abilitare la sincronizzazione offline per l'app di Windows](app-service-mobile-windows-store-dotnet-get-started-offline-data.md).

2. Modificare **Package.appxmanifest** e selezionare la funzionalità **Webcam**.

3. Aggiungere una nuova classe `WindowsStorePlatform` con l'implementazione seguente. Sostituire "YourNamespace" con lo spazio dei nomi principale del progetto.

        using System;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Windows.Foundation;
        using Windows.Media.Capture;
        using Windows.Storage;
        using YourNamespace;

        [assembly: Xamarin.Forms.Dependency(typeof(WinApp.WindowsStorePlatform))]
        namespace WinApp
        {
            public class WindowsStorePlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> GetTodoFilesPathAsync()
                {
                    var storageFolder = ApplicationData.Current.LocalFolder;
                    var filePath = "TodoItemFiles";

                    var result = await storageFolder.TryGetItemAsync(filePath);

                    if (result == null) {
                        result = await storageFolder.CreateFolderAsync(filePath);
                    }

                    return result.Name; // later operations will use relative paths
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        CameraCaptureUI dialog = new CameraCaptureUI();
                        Size aspectRatio = new Size(16, 9);
                        dialog.PhotoSettings.CroppedAspectRatio = aspectRatio;

                        StorageFile file = await dialog.CaptureFileAsync(CameraCaptureUIMode.Photo);
                        return file.Path;
                    }
                    catch (TaskCanceledException) {
                        return null;
                    }
                }
            }
        }

##Riepilogo

Questo articolo descrive come usare il nuovo supporto file in Azure Mobile SDK per client e server per consentire l'interazione con Archiviazione di Azure.

- Creare un account di archiviazione e aggiungere la stringa di connessione al back-end dell'app per dispositivi mobili. Solo il back-end include la chiave per Archiviazione di Azure. Il client mobile richiede un token di firma di accesso condiviso ogni volta che deve accedere ad Archiviazione di Azure. Per altre informazioni sui token di firma di accesso condiviso in Archiviazione di Azure, vedere [Informazioni sulle firme di accesso condiviso].

- Creare un controller che rappresenti le sottoclassi `StorageController` per gestire le richieste di token di firma di accesso condiviso e per ottenere i file associati a un record. Per impostazione predefinita, i file vengono associati a un record usando l'ID record come parte del nome del contenitore. Il comportamento può essere personalizzato specificando un'implementazione di `IContainerNameResolver`. Anche i criteri del token di firma di accesso condiviso possono essere personalizzati.

- Azure Mobile Client SDK per client non archivia in effetti i dati dei file. L'SDK per client richiama invece `IFileSyncHandler`, che quindi stabilisce se e come archiviare i file nel dispositivo locale. Il gestore di sincronizzazione viene registrato come indicato di seguito:

        client.InitializeFileSync(new MyFileSyncHandler(), store);

      + `IFileSyncHandler.GetDataSource` viene chiamato quando Azure Mobile SDK per client necessita dei dati dei file, ad esempio nell'ambito del processo di caricamento. In questo modo è possibile gestire se e come vengono archiviati i file nel dispositivo locale e restituire le informazioni, se necessario.

      + `IFileSyncHandler.ProcessFileSynchronizationAction` viene richiamato nell'ambito del flusso di sincronizzazione dei file. Vengono forniti un riferimento a file e un valore di enumerazione FileSynchronizationAction per poter decidere in che modo l'applicazione dovrà gestire l'evento. Ad esempio, scaricare automaticamente un file quando questo viene creato o aggiornato, eliminare un file dal dispositivo locale quando il file viene eliminato nel server.

- È possibile usare un oggetto `MobileServiceFile` in modalità online oppure offline, usando rispettivamente `IMobileServiceTable` o `IMobileServiceSyncTable`. Nello scenario offline, il caricamento viene eseguito quando l'app chiama `PushFileChangesAsync`, attivando l'elaborazione della coda operazioni offline. Per ogni operazione su file, Azure Mobile SDK per client richiama il metodo `GetDataSource` nell'istanza di `IFileSyncHandler` per recuperare il contenuto del file per il caricamento.

- Per recuperare i file di un elemento, chiamare il metodo '`GetFilesAsync` nell'istanza di `IMobileServiceTable<T>` o IMobileServiceSyncTable<T>'. Questo metodo restituisce un elenco di file associato all'elemento di dati fornito. Nota: questa è un'operazione *locale* che restituirà i file in base allo stato dell'oggetto al momento dell'ultima sincronizzazione. Per ottenere un elenco aggiornato di file dal server, è necessario avviare prima un'operazione di sincronizzazione.

        IEnumerable<MobileServiceFile> files = await myTable.GetFilesAsync(myItem);

- La funzionalità di sincronizzazione file usa le notifiche di modifica dei record nell'archivio locale per recuperare i record che il client ha ricevuto come parte di un'operazione push o pull. A questo scopo, attivare le notifiche locali e del server per il contesto di sincronizzazione con il parametro `StoreTrackingOptions`.

        this.client.SyncContext.InitializeAsync(store, StoreTrackingOptions.NotifyLocalAndServerOperations);

      + Sono disponibili altre opzioni di verifica dell'archivio, ad esempio notifiche solo locali o solo del server. È possibile aggiungere un callback personalizzato con la proprietà `EventManager` di `IMobileServiceClient`:

            jobService.MobileService.EventManager.Subscribe<StoreOperationCompletedEvent>(StoreOperationEventHandler);

- È possibile aggiungere o rimuovere file da un record modificando direttamente l'archivio BLOB, perché l'associazione si ottiene tramite una convenzione di denominazione. Tuttavia, in questo caso è necessario **aggiornare sempre il timestamp del record quando vengono modificati i BLOB associati**. Azure Mobile SDK per client aggiorna sempre un record quando aggiunge o rimuove un file.

    Questo requisito è necessario perché alcuni client mobili hanno già il record nell'archivio locale. Quando questi client effettuano un pull incrementale, il record non viene restituito e il client non esegue una query sui nuovi file associati. Per evitare questo problema, è consigliabile aggiornare il timestamp del record durante l'esecuzione di una modifica dell'archivio BLOB che non usa Azure Mobile SDK per client.

- Il progetto client usa il modello [Xamarin.Forms DependencyService] per caricare la classe specifica della piattaforma in fase di esecuzione. In questo esempio è stata definita un'interfaccia `IPlatform` con implementazioni in ogni progetto specifico della piattaforma.

<!-- URLs. -->

[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
[Creare un'app Xamarin.Forms]: app-service-mobile-xamarin-forms-get-started.md
[Xamarin.Forms DependencyService]: https://developer.xamarin.com/guides/xamarin-forms/dependency-service/
[Microsoft.Azure.Mobile.Client.Files]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.Files/
[Microsoft.Azure.Mobile.Client.SQLiteStore]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[Microsoft.Azure.Mobile.Server.Files]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Files/
[Informazioni sulle firme di accesso condiviso]: ../storage/storage-dotnet-shared-access-signature-part-1.md
[Creare un account di archiviazione di Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account

<!---HONumber=AcomDC_0824_2016-->