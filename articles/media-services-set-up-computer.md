<properties  linkid="develop-media-services-how-to-guides-set-up-computer" urlDisplayName="Set Up Computer for Media Services" pageTitle="How to Set Up Computer for Media Services - Azure" metaKeywords="" description="Learn about the prerequisites for Media Services using the Media Services SDK for .NET. Also learn how to create a Visual Studio app." metaCanonical="" services="media-services" documentationCenter="" title="Setting up your computer for Media Services development" authors="migree" solutions="" manager="" editor="" />

<h1><a name="setup-dev"> </a><span  class="short header">Configurazione del computer per lo sviluppo di applicazioni Servizi multimediali</span></h1>


In questa sezione vengono illustrati i prerequisiti generali per lo sviluppo di applicazioni Servizi multimediali tramite Media Service SDK per .NET. Verrà inoltre illustrato come creare un'applicazione Visual Studio per lo sviluppo tramite Media Services SDK.

### Prerequisiti

* Un account di Servizi multimediali ottenuto con una sottoscrizione di
  Azure nuova o esistente. Vedere l'argomento [Come creare un account di
  Servizi multimediali][1].
* Sistemi operativi: Windows 7, Windows 2008 R2 o Windows 8.
* .NET Framework 4.5 o .NET Framework 4.
* Visual Studio 2012 o Visual Studio 2010 SP1 (Professional, Premium,
  Ultimate o Express).
* Utilizzare il pacchetto [Nuget windowsazure.mediaservices][2] per
  installare Azure SDK per .NET. Nella sezione seguente viene illustrato
  come utilizzare **Nuget** per installare Azure SDK.

<h2><a name="setup-account"></a><span  class="short header">Configurare un account Azure per Servizi multimediali</span></h2>


Per configurare l'account di Servizi multimediali, utilizzare il portale di gestione di Azure (operazione consigliata). Vedere l'argomento [Come creare un account di Servizi multimediali][1]. Dopo avere creato l'account nel portale di gestione, sarà possibile configurare il computer per lo sviluppo in Servizi multimediali.

### Creare un'applicazione in Visual Studio

In questa sezione viene illustrato come creare un progetto in Visual Studio e configurarlo per lo sviluppo in Servizi multimediali. In questo caso, il progetto è un'applicazione console Windows C#, ma la stessa procedura di configurazione si applica ad altri tipi di progetti che è possibile creare per le applicazioni Servizi multimediali, ad esempio un'applicazione Windows Forms o un'applicazione Web ASP.NET.

1.  Creare una nuova **applicazione console** C# in Visual Studio 2012 o
    Visual Studio 2010 SP1. Immettere un valore nei campi **Nome**,
    **Percorso** e **Nome soluzione**, quindi fare clic su **OK**.
2.  Aggiungere un riferimento all'assembly **System.Configuration**. Per
    aggiungere un riferimento a **System.Configuration**, in **Esplora
    soluzioni** fare clic con il pulsante destro del mouse sul nodo
    **Riferimenti** e quindi scegliere **Aggiungi riferimento...**.
    Nella finestra di dialogo **Gestione riferimenti** selezionare
    **System.Configuration** e quindi fare clic su **OK**.
3.  Aggiungere i riferimenti alle librerie di **Azure SDK per .NET.**
    (Microsoft.WindowsAzure.StorageClient.dll), **Azure Media Services
    SDK per .NET** (Microsoft.WindowsAzure.MediaServices.Client.dll) e
    **WCF Data Services 5.0 per OData V3** (Microsoft.Data.OData.dll)
    utilizzando il pacchetto [NuGet windowsazure.mediaservices][2].


    Per aggiungere riferimenti utilizzando il pacchetto NuGet, eseguire le operazioni seguenti. Scegliere **STRUMENTI** -> **Gestione pacchetti libreria** -> **Console di Gestione pacchetti** dal **menu principale** di Visual Studio. Nella finestra della console immettere **Install-Package windowsazure.mediaservices** e premere **INVIO**.

1.  Sovrascrivere le istruzioni using esistenti all'inizio del file
    Program.cs con il codice seguente.

        using System;     	
        using System.Linq;
    	using System.Configuration;
    	using System.IO;
    	using System.Text;
    	using System.Threading;
    	using System.Threading.Tasks;
    	using System.Collections.Generic;
    	using Microsoft.WindowsAzure;
    	using Microsoft.WindowsAzure.MediaServices.Client;

A questo punto, si è pronti per iniziare a sviluppare un'applicazione Servizi multimediali.

<h2><a name="setup-account"></a><span  class="short header">Connettersi a Servizi multimediali</span></h2>


Quasi tutte le attività utilizzate per la programmazione di applicazioni Servizi multimediali richiedono un riferimento all'oggetto del contesto del server. Il contesto del server consente di accedere a livello di codice a tutti gli oggetti di programmazione di Servizi multimediali.

Per ottenere un riferimento al contesto del server, restituire una nuova istanza del tipo di contesto come nell'esempio di codice seguente. Passare il nome e nome e la chiave dell'account di Servizi multimediali
(forniti durante il processo di configurazione dell'account).

       static CloudMediaContext GetContext()
        { // Gets the service context. 
          return new CloudMediaContext(_accountName, _accountKey);} 

Spesso è utile definire una variabile a livello di modulo di tipo
**CloudMediaContext** per mantenere un riferimento al contesto del
server restituito da un metodo come **GetContext**. Negli altri esempi di codice riportati in questo argomento viene utilizzata una variabile denominata **_context** per fare riferimento al contesto del server.

<h2>Passaggi successivi</h2>


Dopo avere configurato il computer e creato una soluzione Visual Studio per la programmazione di applicazioni Servizi multimediali, passare all'argomento [Come creare e caricare un asset crittografato nell'archiviazione][]. 
[Come creare un account di Servizi multimediali]: http://go.microsoft.com/fwlink/?linkid=256662 
[Come creare e caricare un asset crittografato nell'archiviazione]:http://go.microsoft.com/fwlink/?LinkID=301733&clcid=0x409


[1]: http://www.windowsazure.com/it-it/manage/services/media-services/how-to-create-a-media-services-account/
[2]: http://nuget.org/packages/windowsazure.mediaservices