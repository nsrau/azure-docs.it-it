<properties 
	pageTitle="Come configurare il computer per Servizi multimediali - Azure" 
	description="Informazioni sui prerequisiti generali per l'uso di Servizi multimediali con Media Services SDK per .NET e su come creare un'app di Visual Studio." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/30/2014" 
	ms.author="juliako"/>





<h1><a name="setup-dev"> </a><span class="short header">Configurazione del computer per lo sviluppo di applicazioni Servizi multimediali</span></h1> 

In questa sezione vengono illustrati i prerequisiti generali per lo sviluppo di applicazioni Servizi multimediali tramite Media Services SDK per .NET. Verrà inoltre illustrato come creare un'applicazione Visual Studio per lo sviluppo tramite Media Services SDK. 

### Prerequisiti

-   Un account di Servizi multimediali ottenuto con una sottoscrizione di Azure nuova o esistente. Vedere l'argomento [Come creare un account di Servizi multimediali](media-services-create-account.md).
-   Sistemi operativi: Windows 7, Windows 2008 R2 o Windows 8.
-   .NET Framework 4.
-   Visual Studio 2013, Visual Studio 2012 o Visual Studio 2010 SP1 (Professional, Premium, Ultimate o Express). 
-   Usare il pacchetto Nuget [windowsazure.mediaservices](http://nuget.org/packages/windowsazure.mediaservices) per installare Azure SDK per .NET. Nella sezione seguente viene illustrato come usare **Nuget** per installare Azure SDK.
  
<h2><a name="setup-account"></a><span class="short header">Configurare un account Azure per Servizi multimediali</span></h2>

Per configurare l'account di Servizi multimediali, usare il portale di gestione di Azure (operazione consigliata). Vedere l'argomento [Come creare un account di Servizi multimediali][]. Dopo avere creato l'account nel portale di gestione, sarà possibile configurare il computer per lo sviluppo in Servizi multimediali. 

### Creare un'applicazione in Visual Studio

In questa sezione viene illustrato come creare un progetto in Visual Studio e configurarlo per lo sviluppo in Servizi multimediali.  In questo caso, il progetto è un'applicazione console Windows C#, ma la stessa procedura di configurazione si applica ad altri tipi di progetti che è possibile creare per le applicazioni Servizi multimediali, ad esempio un'applicazione Windows Forms o un'applicazione Web ASP.NET.

   1. Creare una nuova **applicazione console** C# in  Visual Studio 2013, Visual Studio 2012 o Visual Studio 2010 SP1. Immettere un valore nei campi **Nome**, **Percorso** e **Nome soluzione**, quindi fare clic su **OK**.
   2. Assicurarsi di impostare .NET Framework 4 come framework di destinazione. A questo scopo, fare clic con il pulsante destro del mouse sul progetto di Visual Studio e scegliere Proprietà. Nella scheda Applicazione impostare .NET Framework 4 come framework di destinazione.   
   3. Aggiungere un riferimento all'assembly **System.Configuration**. Per aggiungere un riferimento a **System.Configuration**, in **Esplora soluzioni** fare clic con il pulsante destro del mouse sul nodo **Riferimenti** e quindi scegliere **Aggiungi riferimento...**. Nella finestra di dialogo **Gestione riferimenti** selezionare **System.Configuration** e quindi fare clic su **OK**.
   4. Usare il pacchetto Nuget [windowsazure.mediaservices](http://nuget.org/packages/windowsazure.mediaservices) per aggiungere riferimenti ad **Azure SDK per .NET.** Verranno installati anche tutti gli assembly dipendenti.  

	Per aggiungere riferimenti usando il pacchetto NuGet, eseguire le operazioni seguenti. Nel **menu principale** di Visual Studio scegliere **STRUMENTI** -> **Gestione pacchetti libreria** -> **Console di Gestione pacchetti**. Nella finestra della console digitare **Install-Package windowsazure.mediaservices** e premere **INVIO**.
   4. Sovrascrivere le istruzioni using esistenti all'inizio del file Program.cs con il codice seguente.

   		using System;
		using System.Linq;
		using System.Configuration;
		using System.IO;
		using System.Text;
		using System.Threading;
		using System.Threading.Tasks;
		using System.Collections.Generic;
		using Microsoft.WindowsAzure.MediaServices.Client;

A questo punto, si è pronti per iniziare a sviluppare un'applicazione Servizi multimediali.    
   
<h2><a name="setup-account"></a><span class="short header">Connettersi a Servizi multimediali</span></h2> 

Quasi tutte le attività usate per la programmazione di applicazioni Servizi multimediali richiedono un riferimento all'oggetto del contesto del server. Il contesto del server consente di accedere a livello di codice a tutti gli oggetti di programmazione di Servizi multimediali.

Per ottenere un riferimento al contesto del server, creare una nuova istanza del tipo di contesto come nell'esempio di codice seguente. Passare il nome e nome e la chiave dell'account di Servizi multimediali (forniti durante il processo di configurazione dell'account). 

    // Create and cache the Media Services credentials in a static class variable.
	_cachedCredentials = new MediaServicesCredentials(
	                _mediaServicesAccountName,
	                _mediaServicesAccountKey);

	// Use the cached credentials to create CloudMediaContext.
	_context = new CloudMediaContext(_cachedCredentials);


Spesso è utile definire una variabile a livello di modulo di tipo **CloudMediaContext** per mantenere un riferimento al contesto del server. Per altre informazioni, vedere [Connessione a Servizi multimediali con Media Services SDK per .NET](http://msdn.microsoft.com/library/azure/jj129571.aspx).

Negli altri esempi di codice riportati in questo argomento viene usata una variabile denominata **_context** per fare riferimento al contesto del server. 

<h2>Passaggi successivi</h2>
Dopo avere configurato il computer e creato una soluzione Visual Studio per la programmazione di applicazioni Servizi multimediali, passare all'argomento [Come creare e caricare un asset crittografato nell'archiviazione][].
[Come creare un account di Servizi multimediali]: ../media-services-create-account/
[Come creare e caricare un asset crittografato nell'archiviazione]: ../media-services-create-encrypted-asset-upload-storage/



<!--HONumber=42-->
