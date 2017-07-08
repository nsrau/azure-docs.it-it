---
title: Usare Archiviazione di Azure nelle app di Windows Store | Microsoft Docs
description: Scoprire come creare un'applicazione di Windows Store che usa l'archiviazione BLOB, code, tabelle o file di Azure.
services: storage
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 63c4b29d-b2f2-4d7c-b164-a0d38f4d14f6
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 12/08/2016
ms.author: marsma
ms.translationtype: Human Translation
ms.sourcegitcommit: ff0c9f9c800b0e4764efb88e43087bd96fa820e9
ms.openlocfilehash: 7b02809da6082886b4e2982a698cef09212b6862
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017


---
# <a name="how-to-use-azure-storage-in-windows-store-apps"></a>Come usare l'archiviazione di Azure nelle app di Windows Store
## <a name="overview"></a>Overview
In questa guida viene illustrato come iniziare a sviluppare un'applicazione Windows Store che utilizzi l'archiviazione di Azure.

## <a name="download-required-tools"></a>Download degli strumenti richiesti
* [Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx) semplifica le operazioni di compilazione, debug, localizzazione, creazione di pacchetti e distribuzione delle app di Windows Store. È richiesto Visual Studio 2012 o versione successiva.
* La [libreria client di archiviazione di Azure](https://www.nuget.org/packages/WindowsAzure.Storage) fornisce una libreria di classi per Windows Runtime per l'uso di Archiviazione di Azure.
* [strumenti di WCF Data Services per app di Windows Store](http://www.microsoft.com/download/details.aspx?id=30714) ampliano l'esperienza della finestra di dialogo Aggiungi riferimento al servizio con il supporto OData lato client per le app di Windows Store in Visual Studio.

## <a name="develop-apps"></a>Sviluppo di applicazioni
### <a name="getting-ready"></a>Preparazione
Creare un nuovo progetto di app di Windows Store in Visual Studio 2012 o versione successiva:

![store-apps-storage-vs-project][store-apps-storage-vs-project]

Quindi aggiungere un riferimento alla libreria client di archiviazione di Azure facendo clic con il pulsante destro del mouse su **Riferimenti**, quindi scegliendo **Aggiungi riferimento** e passando alla libreria client di archiviazione per Windows Runtime scaricata:

![store-apps-storage-choose-library][store-apps-storage-choose-library]

### <a name="using-the-library-with-the-blob-and-queue-services"></a>Usare la libreria con BLOB e i servizi di accodamento
A questo punto l'app è pronta per chiamare i servizi BLOB e di accodamento di Azure. Aggiungere le seguenti istruzioni **using** in modo che sia possibile fare direttamente riferimento ai tipi di archiviazione di Azure:

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
```

Quindi, aggiungere un pulsante alla propria pagina. Aggiungere il codice seguente al relativo evento **Click** e modificare il metodo di gestione degli eventi utilizzando la [parola chiave async](http://msdn.microsoft.com/library/vstudio/hh156513.aspx):

```csharp
var credentials = new StorageCredentials(accountName, accountKey);
var account = new CloudStorageAccount(credentials, true);
var blobClient = account.CreateCloudBlobClient();
var container = blobClient.GetContainerReference("container1");
await container.CreateIfNotExistsAsync();
```

Questo codice presuppone che si abbiano due variabili di stringa, *accountName* e *accountKey*. Esse rappresentano il nome dell’account di archiviazione e la chiave dell'account associati a tale account.

Compilare ed eseguire l'applicazione. Fare clic sul pulsante per controllare prima se esiste un contenitore denominato *container1* nel proprio account e crearlo se non esiste.

### <a name="using-the-library-with-the-table-service"></a>Utilizzare la libreria con il servizio tabelle
I tipi utilizzati per comunicare con il servizio tabelle di Azure dipendono dalla libreria di Servizi dati WCF per le app di Windows Store. Di seguito, aggiungere un riferimento alle librerie WCF richieste usando la Console di Gestione pacchetti:

![store-apps-storage-package-manager][store-apps-storage-package-manager]

Utilizzare il comando seguente per indirizzare Gestione Pacchetti alla posizione sul proprio computer:

    Install-Package Microsoft.Data.OData.WindowsStore -Source "C:\Program Files (x86)\Microsoft WCF Data Services\5.0\bin\NuGet"

Questo comando consente di aggiungere automaticamente tutti i riferimenti richiesto al proprio progetto. Qualora non si desideri usare la Console di Gestione pacchetti è inoltre possibile aggiungere la cartella NuGet dei Servizi dati WCF sul computer locale all'elenco di origini pacchetti, quindi aggiungere il riferimento attraverso l'interfaccia utente, come descritto nell’articolo sulla [Gestione dei pacchetti NuGet mediante la finestra di dialogo](http://docs.nuget.org/docs/start-here/Managing-NuGet-Packages-Using-The-Dialog).

Dopo aver fatto riferimento al pacchetto NuGet dei Servizi dati WCF modificare il codice nell'evento **Click** del proprio pulsante:

```csharp
var credentials = new StorageCredentials(accountName, accountKey);
var account = new CloudStorageAccount(credentials, true);
var tableClient = account.CreateCloudTableClient();
var table = tableClient.GetTableReference("table1");
await table.CreateIfNotExistsAsync();
```

Questo codice verifica se esiste una tabella denominata *table1* nell'account, creandone una se non esiste.

È inoltre possibile aggiungere un riferimento a Microsoft.WindowsAzure.Storage.Table.dll, disponibile nello stesso pacchetto scaricato. Questa libreria contiene funzionalità aggiuntive, come la serializzazione basata su reflection e le query generiche. Si noti che questa libreria non supporta JavaScript.

[store-apps-storage-vs-project]: ./media/storage-use-store-apps/store-apps-storage-vs-project.png
[store-apps-storage-choose-library]: ./media/storage-use-store-apps/store-apps-storage-choose-library.png
[store-apps-storage-package-manager]: ./media/storage-use-store-apps/store-apps-storage-package-manager.png

