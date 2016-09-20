<properties
	pageTitle="Come usare l'archiviazione BLOB da Xamarin | Microsoft Azure"
	description="La libreria client di Archiviazione di Azure per Xamarin consente agli sviluppatori di creare app per Windows Store, iOS e Android con le proprie interfacce utente native. Questa esercitazione illustra come usare Xamarin per creare un'applicazione che usa l'archiviazione BLOB di Azure."
	services="storage"
	documentationCenter="xamarin"
	authors="micurd"
	manager=""
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/31/2016"
	ms.author="micurd"/>  

# Come usare l'archiviazione BLOB da Xamarin

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Overview

Xamarin consente agli sviluppatori di utilizzare una base di codici C# condivisa per creare applicazioni per Windows Store, iOS e Android con le proprie interfacce utente native. Questa esercitazione illustra come usare l'archiviazione BLOB di Azure con un'applicazione per Xamarin. Per maggiori informazioni su Archiviazione di Azure, prima di approfondire il codice, vedere [Introduzione ad Archiviazione di Microsoft Azure](storage-introduction.md).

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-mobile-authentication-guidance](../../includes/storage-mobile-authentication-guidance.md)]

## Creare una nuova applicazione Xamarin

Per questa guida introduttiva verrà generata un'applicazione destinata ad Android, iOS e Windows. Questa applicazione creerà semplicemente un contenitore nel quale caricherà un BLOB. Per questa guida introduttiva verrà usato Visual Studio in Windows, ma le stesse istruzioni possono essere applicate durante la creazione di un'applicazione con Xamarin Studio su Mac OS.

Attenersi alla procedura seguente per creare l'applicazione:

1. Se necessario, scaricare e installare [Xamarin per Visual Studio](https://www.xamarin.com/download).
2. Aprire Visual Studio e creare un'applicazione vuota (condivisa nativa): **File > Nuovo > Progetto > Multipiattaforma > Blank App (Native Shared)** (Applicazione vuota (condivisa nativa)).
3. Nel riquadro Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e selezionare **Gestisci pacchetti NuGet per la soluzione**. Cercare **WindowsAzure.Storage** e installare la versione stabile più recente su tutti i progetti nella soluzione.
4. Compilare ed eseguire il progetto.

A questo punto si disporrà di un'applicazione che consente di fare clic su un pulsante e incrementare un contatore.

> [AZURE.NOTE] La libreria client di Archiviazione di Azure per Xamarin funziona anche per le applicazioni Xamarin.Forms.

## Creare un contenitore e caricare un BLOB

Successivamente verrà aggiunto del codice alla classe condivisa `MyClass.cs` che crea un contenitore e carica un BLOB al suo interno. `MyClass.cs` deve apparire come segue:

	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Blob;
	using System.Threading.Tasks;

	namespace XamarinApp
	{
		public class MyClass
		{
			public MyClass ()
			{
			}

		    public static async Task createContainerAndUpload()
		    {
		        // Retrieve storage account from connection string.
		        CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here");

		        // Create the blob client.
		        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

		        // Retrieve reference to a previously created container.
		        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

				// Create the container if it doesn't already exist.
            	await container.CreateIfNotExistsAsync();

		        // Retrieve reference to a blob named "myblob".
		        CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

		        // Create the "myblob" blob with the text "Hello, world!"
		        await blockBlob.UploadTextAsync("Hello, world!");
		    }
		}
	}

Assicurarsi di sostituire "your\_account\_name\_here" e "your\_account\_key\_here" con il nome e la chiave dell'account effettivi. È quindi possibile usare questa classe condivisa nell'applicazione iOS, Android e Windows Phone. È semplicemente possibile aggiungere `MyClass.createContainerAndUpload()` a ogni progetto. Ad esempio:

### XamarinApp.Droid > MainActivity.cs

	using Android.App;
	using Android.Widget;
	using Android.OS;

	namespace XamarinApp.Droid
	{
		[Activity (Label = "XamarinApp.Droid", MainLauncher = true, Icon = "@drawable/icon")]
		public class MainActivity : Activity
		{
			int count = 1;

			protected override async void OnCreate (Bundle bundle)
			{
				base.OnCreate (bundle);

				// Set our view from the "main" layout resource
				SetContentView (Resource.Layout.Main);

				// Get our button from the layout resource,
				// and attach an event to it
				Button button = FindViewById<Button> (Resource.Id.myButton);

				button.Click += delegate {
					button.Text = string.Format ("{0} clicks!", count++);
				};

	      	  await MyClass.createContainerAndUpload();
			}
		}
	}

### XamarinApp.iOS > ViewController.cs

	using System;
	using UIKit;

	namespace XamarinApp.iOS
	{
		public partial class ViewController : UIViewController
		{
			int count = 1;

			public ViewController (IntPtr handle) : base (handle)
			{
			}

			public override async void ViewDidLoad ()
			{
				base.ViewDidLoad ();
				// Perform any additional setup after loading the view, typically from a nib.
				Button.AccessibilityIdentifier = "myButton";
				Button.TouchUpInside += delegate {
					var title = string.Format ("{0} clicks!", count++);
					Button.SetTitle (title, UIControlState.Normal);
				};

	            await MyClass.createContainerAndUpload();
	    	}

			public override void DidReceiveMemoryWarning ()
			{
				base.DidReceiveMemoryWarning ();
				// Release any cached data, images, etc that aren't in use.
			}
		}
	}

### XamarinApp.WinPhone > MainPage.xaml > MainPage.xaml.cs

	using Windows.UI.Xaml.Controls;
	using Windows.UI.Xaml.Navigation;

	// The Blank Page item template is documented at http://go.microsoft.com/fwlink/?LinkId=391641

	namespace XamarinApp.WinPhone
	{
	    /// <summary>
	    /// An empty page that can be used on its own or navigated to within a Frame.
	    /// </summary>
	    public sealed partial class MainPage : Page
	    {
	        int count = 1;

	        public MainPage()
	        {
	            this.InitializeComponent();

	            this.NavigationCacheMode = NavigationCacheMode.Required;
	        }

	        /// <summary>
	        /// Invoked when this page is about to be displayed in a Frame.
	        /// </summary>
	        /// <param name="e">Event data that describes how this page was reached.
	        /// This parameter is typically used to configure the page.</param>
	        protected override async void OnNavigatedTo(NavigationEventArgs e)
	        {
	            // TODO: Prepare page for display here.

	            // TODO: If your application contains multiple pages, ensure that you are
	            // handling the hardware Back button by registering for the
	            // Windows.Phone.UI.Input.HardwareButtons.BackPressed event.
	            // If you are using the NavigationHelper provided by some templates,
	            // this event is handled for you.
	            Button.Click += delegate {
	                var title = string.Format("{0} clicks!", count++);
	                Button.Content = title;
	            };

	            await MyClass.createContainerAndUpload();
	        }
	    }
	}


## Eseguire l'applicazione

È ora possibile eseguire questa applicazione in un emulatore Android o Windows Phone. È inoltre possibile eseguire questa applicazione in un emulatore iOS, ma questo richiederà un Mac. Per istruzioni specifiche su come eseguire questa operazione, leggere la documentazione relativa alla [connessione di Visual Studio a un Mac](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/)

Dopo aver eseguito l'applicazione, verrà creato il contenitore `mycontainer` nell'account di archiviazione. Questo deve contenere il BLOB, `myblob`, che include il testo, `Hello, world!`. È possibile verificarlo tramite [Esplora archivi di Microsoft Azure](http://storageexplorer.com/).

## Passaggi successivi

Questa guida introduttiva ha descritto come creare un'applicazione multipiattaforma in Xamarin che usa Archiviazione di Azure. Questa guida si è specificamente incentrata su uno scenario in archiviazione BLOB. Tuttavia, è possibile svolgere molte altre operazioni, non solo con l'archiviazione BLOB, ma anche con archiviazione tabelle, archiviazione file e archiviazione code. Per altre informazioni, vedere gli articoli seguenti:
- [Introduzione all'archiviazione BLOB di Azure con .NET](storage-dotnet-how-to-use-blobs.md)
- [Introduzione all'archiviazione tabelle di Azure con .NET](storage-dotnet-how-to-use-tables.md)
- [Introduzione all'archiviazione code di Azure con .NET](storage-dotnet-how-to-use-queues.md)
- [Introduzione ad Archiviazione file di Azure in Windows](storage-dotnet-how-to-use-files.md)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

<!---HONumber=AcomDC_0907_2016-->