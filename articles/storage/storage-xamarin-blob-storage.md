---
title: Come usare l'archiviazione BLOB da Xamarin | Microsoft Docs
description: La libreria client di Archiviazione di Azure per Xamarin consente agli sviluppatori di creare app per Windows Store, iOS e Android con le proprie interfacce utente native. Questa esercitazione illustra come usare Xamarin per creare un'applicazione che usa l'archiviazione BLOB di Azure.
services: storage
documentationcenter: xamarin
author: michaelhauss
manager: vamshik
editor: tysonn
ms.assetid: 44cb845d-cf78-4942-95b8-952da4f9a2c2
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/11/2017
ms.author: michaelhauss
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 0952c0e7189dd360098744a7e19b04cd330cb617
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="how-to-use-blob-storage-from-xamarin"></a>Come usare l'archiviazione BLOB da Xamarin
[!INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## <a name="overview"></a>Panoramica
Xamarin consente agli sviluppatori di utilizzare una base di codici C# condivisa per creare applicazioni per Windows Store, iOS e Android con le proprie interfacce utente native. Questa esercitazione illustra come usare l'archiviazione BLOB di Azure con un'applicazione per Xamarin. Per maggiori informazioni su Archiviazione di Azure, prima di approfondire il codice, vedere [Introduzione ad Archiviazione di Microsoft Azure](storage-introduction.md).

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-mobile-authentication-guidance](../../includes/storage-mobile-authentication-guidance.md)]

## <a name="create-a-new-xamarin-application"></a>Creare una nuova applicazione Xamarin
Per questa esercitazione verrà generata un'applicazione destinata ad Android, iOS e Windows. Questa applicazione creerà semplicemente un contenitore nel quale caricherà un BLOB. Verrà usato Visual Studio in Windows, ma le stesse istruzioni possono essere applicate per la creazione di un'applicazione con Xamarin Studio su macOS.

Attenersi alla procedura seguente per creare l'applicazione:

1. Se necessario, scaricare e installare [Xamarin per Visual Studio](https://www.xamarin.com/download).
2. Aprire Visual Studio e creare un'applicazione vuota (nativa portatile): **File > Nuovo > Progetto > Multipiattaforma > Applicazione vuota (nativa portatile)**.
3. Nel riquadro Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e selezionare **Gestisci pacchetti NuGet per la soluzione**. Cercare **WindowsAzure.Storage** e installare la versione stabile più recente su tutti i progetti nella soluzione.
4. Compilare ed eseguire il progetto.

A questo punto si disporrà di un'applicazione che consente di fare clic su un pulsante e incrementare un contatore.

## <a name="create-container-and-upload-blob"></a>Creare un contenitore e caricare un BLOB
Quindi, sotto il progetto `(Portable)`, si aggiungerà codice a `MyClass.cs`. Questo codice crea un contenitore e carica in esso un oggetto BLOB. `MyClass.cs` deve apparire come segue:

```csharp
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

        public static async Task performBlobOperation()
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
```

Assicurarsi di sostituire "your_account_name_here" e "your_account_key_here" con il nome e la chiave dell'account effettivi. 

Tutti i progetti iOS, Android e Windows Phone contengono riferimenti al progetto portatile, vale a dire che è possibile scrivere tutto il codice condiviso in una posizione e utilizzarlo in tutti i progetti. È ora possibile aggiungere la seguente riga di codice in ogni progetto per iniziare a sfruttare i vantaggi: `MyClass.performBlobOperation()`

### <a name="xamarinappdroid--mainactivitycs"></a>XamarinApp.Droid > MainActivity.cs

```csharp
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

            await MyClass.performBlobOperation();
            }
        }
    }
}
```

### <a name="xamarinappios--viewcontrollercs"></a>XamarinApp.iOS > ViewController.cs

```csharp
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

                await MyClass.performBlobOperation();
            }

            public override void DidReceiveMemoryWarning ()
            {
                base.DidReceiveMemoryWarning ();
                // Release any cached data, images, etc that aren't in use.
            }
        }
    }
}
```

### <a name="xamarinappwinphone--mainpagexaml--mainpagexamlcs"></a>XamarinApp.WinPhone > MainPage.xaml > MainPage.xaml.cs

```csharp
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

                await MyClass.performBlobOperation();
            }
        }
    }
}
```

## <a name="run-the-application"></a>Eseguire l'applicazione
È ora possibile eseguire questa applicazione in un emulatore Android o Windows Phone. È inoltre possibile eseguire questa applicazione in un emulatore iOS, ma questo richiederà un Mac. Per istruzioni specifiche su come eseguire questa operazione, leggere la documentazione relativa alla [connessione di Visual Studio a un Mac](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/)

Dopo aver eseguito l'applicazione, verrà creato il contenitore `mycontainer` nell'account di archiviazione. Questo deve contenere il BLOB, `myblob`, che include il testo, `Hello, world!`. È possibile verificarlo tramite [Esplora archivi di Microsoft Azure](http://storageexplorer.com/).

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato illustrato come creare un'applicazione multipiattaforma in Xamarin che usa Archiviazione di Azure, con particolare attenzione a uno scenario di archiviazione BLOB. È tuttavia possibile svolgere molte altre operazioni, non solo con l'archiviazione BLOB, ma anche con archiviazione tabelle, archiviazione file e archiviazione code. Per altre informazioni, vedere gli articoli seguenti:

* [Introduzione all'archiviazione BLOB di Azure con .NET](storage-dotnet-how-to-use-blobs.md)
* [Introduzione all'archiviazione tabelle di Azure con .NET](storage-dotnet-how-to-use-tables.md)
* [Introduzione all'archiviazione code di Azure con .NET](storage-dotnet-how-to-use-queues.md)
* [Introduzione ad Archiviazione file di Azure in Windows](storage-dotnet-how-to-use-files.md)

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]


