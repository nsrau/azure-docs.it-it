<properties
	pageTitle="Usare l'archiviazione di Azure nelle app di Windows Store | Microsoft Azure"
	description="Scoprire come creare un'applicazione di Windows Store che usa l'archiviazione BLOB, code, tabelle o file di Azure."
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="carmonm" />
<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/07/2016"
	ms.author="tamram"/>
	
# Come usare l'archiviazione di Azure nelle app di Windows Store

## Panoramica

In questa guida viene illustrato come iniziare a sviluppare un'applicazione Windows Store che utilizzi l'archiviazione di Azure.

## Download degli strumenti richiesti

- [Visual Studio 2012](http://msdn.microsoft.com/library/windows/apps/br211384) facilita le operazioni di compilazione, debug, localizzazione, creazione pacchetti e distribuzione delle app di Windows Store.
- La [libreria client di Archiviazione di Azure per Windows Runtime](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/05/windows-azure-storage-client-library-for-windows-runtime.aspx) fornisce una libreria di classi per usare Archiviazione di Azure.
- Gli [strumenti dei Servizi dati WCF per le app di Windows Store](http://www.microsoft.com/download/details.aspx?id=30714) ampliano l'esperienza della finestra di dialogo Aggiungi riferimento al servizio con il supporto OData lato client per le app di Windows Store in Visual Studio 2012.

## Sviluppo di applicazioni

### Preparazione

Creare un nuovo progetto di app di Windows Store in Visual Studio 2012 o versione successiva:

![store-apps-storage-vs-project][store-apps-storage-vs-project]

Di seguito, aggiungere un riferimento alla libreria client di archiviazione di Azure facendo clic con il pulsante destro del mouse su **References**, quindi scegliendo **Add Reference** e passando alla libreria client di archiviazione per Windows Runtime scaricata:

![store-apps-storage-choose-library][store-apps-storage-choose-library]

### Usare la libreria con BLOB e i servizi di accodamento

A questo punto l'app è pronta per chiamare i servizi BLOB e di accodamento di Azure. Aggiungere le seguenti istruzioni **using** in modo che sia possibile fare direttamente riferimento ai tipi di archiviazione di Azure:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;

Quindi, aggiungere un pulsante alla propria pagina. Aggiungere il codice seguente al relativo evento **Click** e modificare il metodo di gestione degli eventi utilizzando la [parola chiave async](http://msdn.microsoft.com/library/vstudio/hh156513.aspx):

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var blobClient = account.CreateCloudBlobClient();
    var container = blobClient.GetContainerReference("container1");
    await container.CreateIfNotExistsAsync();

Questo codice presuppone che si abbiano due variabili di stringa, *accountName* e *accountKey*. Esse rappresentano il nome dell’account di archiviazione e la chiave dell'account associati a tale account.

Compilare ed eseguire l'applicazione. Fare clic sul pulsante per controllare prima se esiste un contenitore denominato *container1* nel proprio account e crearlo se non esiste.

### Utilizzare la libreria con il servizio tabelle

I tipi utilizzati per comunicare con il servizio tabelle di Azure dipendono dalla libreria di Servizi dati WCF per le app di Windows Store. Di seguito, aggiungere un riferimento alle librerie WCF richieste usando la Console di Gestione pacchetti:

![store-apps-storage-package-manager][store-apps-storage-package-manager]

Usare il comando seguente per indirizzare Gestione Pacchetti alla posizione sul proprio computer:

    Install-Package Microsoft.Data.OData.WindowsStore -Source "C:\Program Files (x86)\Microsoft WCF Data Services\5.0\bin\NuGet"

Questo comando consente di aggiungere automaticamente tutti i riferimenti richiesto al proprio progetto. Qualora non si desideri usare la Console di Gestione pacchetti è inoltre possibile aggiungere la cartella NuGet dei Servizi dati WCF sul computer locale all'elenco di origini pacchetti, quindi aggiungere il riferimento attraverso l'interfaccia utente, come descritto nell’articolo sulla [Gestione dei pacchetti NuGet mediante la finestra di dialogo](http://docs.nuget.org/docs/start-here/Managing-NuGet-Packages-Using-The-Dialog).

Dopo aver fatto riferimento al pacchetto NuGet dei Servizi dati WCF modificare il codice nell'evento **Click** del proprio pulsante:

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var tableClient = account.CreateCloudTableClient();
    var table = tableClient.GetTableReference("table1");
    await table.CreateIfNotExistsAsync();

Questo codice verifica se esiste una tabella denominata *table1* nell'account, creandone una se non esiste.

È inoltre possibile aggiungere un riferimento a Microsoft.WindowsAzure.Storage.Table.dll, disponibile nello stesso pacchetto scaricato. Questa libreria contiene funzionalità aggiuntive, come la serializzazione basata su reflection e le query generiche. Si noti che questa libreria non supporta JavaScript.



[store-apps-storage-vs-project]: ./media/storage-use-store-apps/store-apps-storage-vs-project.png
[store-apps-storage-choose-library]: ./media/storage-use-store-apps/store-apps-storage-choose-library.png
[store-apps-storage-package-manager]: ./media/storage-use-store-apps/store-apps-storage-package-manager.png

<!---HONumber=AcomDC_0114_2016-->