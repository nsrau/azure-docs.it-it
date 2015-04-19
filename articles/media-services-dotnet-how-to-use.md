<properties 
	pageTitle="Come configurare il computer per lo sviluppo di applicazioni di Servizi multimediali con .NET" 
	description="Informazioni sui prerequisiti generali per l'uso di Servizi multimediali con l'SDK di Servizi multimediali per .NET e su come creare un'app di Visual Studio." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="juliako"/>

# Sviluppo di applicazioni di Servizi multimediali con .NET 

Questo argomento illustra come iniziare a sviluppare applicazioni di Servizi multimediali con .NET. 

La libreria dell'**SDK di Servizi multimediali per .NET** consente di programmare per Servizi multimediali usando .NET. Per facilitare ancora di più lo sviluppo con .NET, è disponibile la libreria delle **estensioni dell'SDK di Servizi multimediali di Azure**, contenente un set di funzioni di supporto e metodi di estensione che semplificano il codice .NET. Entrambe le librerie sono disponibili tramite **NuGet** e **GitHub**.
 

## Prerequisiti

-   Un account di Servizi multimediali ottenuto con una sottoscrizione di Azure nuova o esistente. Vedere l'argomento [Come creare un account di Servizi multimediali](media-services-create-account.md).
-   Sistemi operativi: Windows 7, Windows 2008 R2 o Windows 8.
-   .NET Framework 4.5.
-   Visual Studio 2013, Visual Studio 2012 o Visual Studio 2010 SP1 (Professional, Premium, Ultimate o Express). 
  

## Creare e configurare un progetto di Visual Studio 

In questa sezione viene illustrato come creare un progetto in Visual Studio e configurarlo per lo sviluppo in Servizi multimediali.  In questo caso, il progetto è un'applicazione console Windows C#, ma la stessa procedura di configurazione si applica ad altri tipi di progetti che è possibile creare per le applicazioni Servizi multimediali, ad esempio un'applicazione Windows Forms o un'applicazione Web ASP.NET.

Questa sezione illustra come usare **NuGet** per aggiungere l'SDK di Servizi multimediali per .NET e altre librerie dipendenti. 

In alternativa, è possibile ottenere i bit più recenti dell'SDK di Servizi multimediali per .NET da GitHub ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) e [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), compilare la soluzione e quindi aggiungere i riferimenti al progetto client. Si noti che tutte le dipendenze necessarie vengono scaricate ed estratte automaticamente.   

1. Creare una nuova applicazione console C# in Visual Studio 2013, Visual Studio 2012 o Visual Studio 2010 SP1. Immettere un valore nei campi **Nome**, **Percorso** e **Nome soluzione**, quindi fare clic su OK. 

2. Compilare la soluzione.

2. Usare **NuGet** per installare e aggiungere le **estensioni dell'SDK di Servizi multimediali di Azure per .NET**. Insieme al pacchetto viene installato anche l'**SDK di Servizi multimediali per .NET** e vengono aggiunte tutte le altre dipendenze necessarie.
	1. Verificare di aver installato la versione più aggiornata di NuGet. Per altre informazioni e per le istruzioni di installazione, vedere [NuGet](http://nuget.codeplex.com/).
	
	2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul nome del progetto e scegliere Gestisci pacchetti NuGet.
	
		The Manage NuGet Packages dialog box appears.

	3. Nella raccolta Online eseguire la ricerca di Azure MediaServices Extensions, scegliere Azure Media Services .NET SDK Extensions e fare clic sul pulsante Installa.
 
		Il progetto verrà modificato e verranno aggiunti riferimenti all'SDK di Servizi multimediali per .NET, alle relative estensioni e ad altri assembly dipendenti.

	4. Per ottenere un ambiente di sviluppo più lineare, prendere in considerazione l'abilitazione di NuGet Package Restore. Per altre informazioni vedere [NuGet Package Restore"](http://docs.nuget.org/consume/package-restore).

3. Aggiungere un riferimento all'assembly **System.Configuration**, che contiene la classe System.Configuration.**ConfigurationManager** usata per accedere ai file di configurazione, ad esempio App.config. 

	Per aggiungere riferimenti usando la finestra di dialogo Gestione riferimenti, effettuare le seguenti operazioni: 

	1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul nome del progetto. Selezionare quindi Aggiungi e Riferimenti.

		Viene visualizzata la finestra di dialogo Gestione riferimenti.

	2. Negli assembly .NET Framework trovare e selezionare l'assembly System.Configuration.
	3. Premere OK.


4. Aprire il file App.config (aggiungere il file al progetto, se non è stato aggiunto per impostazione predefinita) e aggiungere una sezione  *appSettings* al file. Impostare i valori per il nome e la chiave dell'account di Servizi multimediali di Azure, come visualizzato nel seguente esempio. 
	
	Per ottenere le informazioni sul **nome** e sulla **chiave dell'account**, aprire il **portale di gestione di Azure**, selezionare l'account di Servizi multimediali e fare clic sul pulsante **GESTISCI CHIAVI**.


	<pre><code>
	&lt;configuration&gt;
        &lt;appSettings&gt;
    	&lt;add key="MediaServicesAccountName" value="Media-Services-Account-Name" /&gt;
        	&lt;add key="MediaServicesAccountKey" value="Media-Services-Account-Key" /&gt;
  	    &lt;/appSettings&gt;
	&lt;/configuration&gt;
	</code></pre>


5. Sovrascrivere le istruzioni using esistenti all'inizio del file Program.cs con il seguente codice.

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using System.Configuration;
		using System.Threading;
		using System.IO;
		using Microsoft.WindowsAzure.MediaServices.Client;

A questo punto, si è pronti per iniziare a sviluppare un'applicazione di Servizi multimediali.    

<!--HONumber=45--> 
