<properties linkid="manage-services-storage-using-storage-with-windows-store-apps" urlDisplayName="Azure Storage in Windows Store apps" pageTitle="Use Azure storage in Windows Store Apps | Azure" metaKeywords="" description="Learn how to use Azure blobs, queues, and tables to store data for a Windows Store app." metaCanonical="" services="storage" documentationCenter="" title="How to use Azure Storage in Windows Store Apps" solutions="" authors="tamram" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram"></tags>

# Come utilizzare l'archiviazione di Azure nelle app di Windows Store

In questa guida viene illustrato come iniziare a sviluppare un'applicazione Windows Store che utilizzi l'archiviazione di Azure.

## Download degli strumenti richiesti

-   [Visual Studio 2012][] facilita le operazioni di compilazione, debug, localizzazione, creazione pacchetti e distribuzione delle app di Windows Store.
-   La [libreria client di archiviazione di Windows Azure per Windows Runtime][] contiene i tipi utilizzati per comunicare con l'archiviazione di Azure, inclusi in un pacchetto con un componente di Windows Runtime.
-   Gli [strumenti dei Servizi dati WCF per le app di Windows Store][] ampliano l'esperienza della finestra di dialogo Aggiungi riferimento al servizio con il supporto OData lato client per le app di Windows Store in Visual Studio 2012.

## Sviluppo di applicazioni

### Preparazione

Creare un nuovo progetto di app di Windows Store in Visual Studio 2012:

![store-apps-storage-vs-project][]

Quindi, aggiungere un riferimento alla libreria client di archiviazione di Azure facendo clic con il pulsante destro del mouse su **References**, quindi scegliendo **Add Reference** e passando alla libreria client di archiviazione per Windows Runtime scaricata:

![store-apps-storage-choose-library][]

### Utilizzare la libreria con BLOB e i servizi di accodamento

A questo punto l'applicazione sarà pronta per comunicare con BLOB e i servizi di accodamento. Aggiungere le seguenti istruzioni **using** in modo che sia possibile fare direttamente riferimento ai tipi di archiviazione di Azure:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;

Quindi, aggiungere un pulsante alla propria pagina. Aggiungere il codice seguente al relativo evento **Click** e modificare il metodo di gestione degli eventi con la [parola chiave async][]:

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var blobClient = account.CreateCloudBlobClient();
    var container = blobClient.GetContainerReference("container1");
    await container.CreateIfNotExistsAsync();

In questo codice si suppone che siano disponibili due variabili di stringa, *accountName* e *accountKey*, che rappresentano il nome dlel'account di archiviazione e la chiave account associata a tale account.

Compilare ed eseguire l'applicazione. Fare clic sul pulsante per controllare prima se esiste un contenitore denominato *container1* nel proprio account e crearlo in caso contrario.

### Utilizzare la libreria con il servizio tabelle

I tipi utilizzati per comunicare con il servizio tabelle dipendono dalla libreria di Servizi dati WCF per le app di Windows Store. Quindi, aggiungere un riferimento alle librerie WCF richieste utilizzando la Console di Gestione pacchetti:

![store-apps-storage-package-manager][]

Utilizzare il comando seguente per indirizzare Gestione Pacchetti alla posizione sul proprio computer:

    Install-Package Microsoft.Data.OData.WindowsStore -Source "C:\Program Files (x86)\Microsoft WCF Data Services.0\bin\NuGet"

Questo comando consente di aggiungere automaticamente tutti i riferimenti richiesto al proprio progetto. Qualora non si desideri utilizzare la Console di Gestione pacchetti è inoltre possibile aggiungere la cartella NuGet dei Servizi dati WCF sul computer locale all'elenco di origini pacchetti, quindi aggiungere il riferimento attraverso l'interfaccia utente, come descritto nell'articolo sulla [gestione dei pacchetti NuGet mediante la finestra di dialogo][].

Dopo aver fatto riferimento al pacchetto NuGet dei Servizi dati WCF modificare il codice nell'evento **Click** del proprio pulsante:

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var tableClient = account.CreateCloudTableClient();
    var table = tableClient.GetTableReference("table1");
    await table.CreateIfNotExistsAsync();

Questo codice verifica se esiste una tabella denominata *table1* nell'account, creandone una in caso contrario.

È inoltre possibile aggiungere un riferimento a Microsoft.WindowsAzure.Storage.Table.dll, disponibile nello stesso pacchetto scaricato. Questa libreria contiene funzionalità aggiuntive, come la serializzazione basata su reflection e le query generiche. Si noti che questa libreria non supporta JavaScript.

  [Visual Studio 2012]: http://msdn.microsoft.com/en-us/library/windows/apps/br211384
  [libreria client di archiviazione di Windows Azure per Windows Runtime]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/05/windows-azure-storage-client-library-for-windows-runtime.aspx
  [strumenti dei Servizi dati WCF per le app di Windows Store]: http://www.microsoft.com/en-us/download/details.aspx?id=30714
  [store-apps-storage-vs-project]: ./media/storage-use-store-apps/store-apps-storage-vs-project.png
  [store-apps-storage-choose-library]: ./media/storage-use-store-apps/store-apps-storage-choose-library.png
  [parola chiave async]: http://msdn.microsoft.com/en-US/library/vstudio/hh156513.aspx
  [store-apps-storage-package-manager]: ./media/storage-use-store-apps/store-apps-storage-package-manager.png
  [gestione dei pacchetti NuGet mediante la finestra di dialogo]: http://docs.nuget.org/docs/start-here/Managing-NuGet-Packages-Using-The-Dialog
