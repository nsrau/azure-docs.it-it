---
title: Come configurare il computer per lo sviluppo di applicazioni di Servizi multimediali con .NET
description: Informazioni sui prerequisiti generali per l'uso di Servizi multimediali con Media Services SDK per .NET e su come creare un'app di Visual Studio.
services: media-services
documentationcenter: ''
author: juliako
manager: erikre
editor: ''

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/10/2016
ms.author: juliako

---
# <a name="media-services-development-with-.net"></a>Sviluppo di applicazioni di Servizi multimediali con .NET
[!INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

Questo argomento illustra come iniziare a sviluppare applicazioni di Servizi multimediali con .NET.

La libreria di **Azure Media Services .NET SDK** consente di programmare per Servizi multimediali usando .NET. Per facilitare ancora di più lo sviluppo con .NET, è disponibile la libreria di **Media Services .NET SDK Extensions**, contenente un set di funzioni di supporto e metodi di estensione che semplificano il codice .NET. Entrambe le librerie sono disponibili tramite **NuGet** e **GitHub**.

## <a name="prerequisites"></a>Prerequisiti
* Un account di Servizi multimediali ottenuto con una sottoscrizione di Azure nuova o esistente. Vedere l'argomento [Come creare un account di Servizi multimediali](media-services-portal-create-account.md).
* Sistemi operativi: Windows 10, Windows 7, Windows 2008 R2 o Windows 8.
* .NET Framework 4.5
* Visual Studio 2015, Visual Studio 2013 o Visual Studio 2012  o Visual Studio 2010 SP1 (Professional, Premium, Ultimate o Express).

## <a name="create-and-configure-a-visual-studio-project"></a>Creare e configurare un progetto di Visual Studio
In questa sezione viene illustrato come creare un progetto in Visual Studio e configurarlo per lo sviluppo in Servizi multimediali.  In questo caso, il progetto è un'applicazione console Windows C#, ma la stessa procedura di configurazione si applica ad altri tipi di progetti che è possibile creare per le applicazioni Servizi multimediali, ad esempio un'applicazione Windows Forms o un'applicazione Web ASP.NET.

Questa sezione illustra come usare **NuGet** per aggiungere Media Services .NET SDK e altre librerie dipendenti.

In alternativa, è possibile ottenere i bit più recenti di Media Services .NET SDK da GitHub ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) e [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), compilare la soluzione e quindi aggiungere i riferimenti al progetto client. Si noti che tutte le dipendenze necessarie vengono scaricate ed estratte automaticamente.

1. Creare una nuova applicazione console C# in Visual Studio 2013, Visual Studio 2012 o Visual Studio 2010 SP1. Immettere un valore nei campi **Nome**, **Percorso** e **Nome soluzione**, quindi fare clic su OK.
2. Compilare la soluzione.
3. Usare **NuGet** per installare e aggiungere **Azure Media Services .NET SDK Extensions**. Insieme al pacchetto viene installato anche **Media Services .NET SDK** e vengono aggiunte tutte le altre dipendenze necessarie.
4. Verificare di aver installato la versione più aggiornata di NuGet. Per altre informazioni e per istruzioni di installazione, vedere [NuGet](http://nuget.codeplex.com/).
5. In Esplora soluzioni fare clic con il pulsante destro del mouse sul nome del progetto e scegliere Gestisci pacchetti NuGet.

Viene visualizzata la finestra di dialogo Gestione pacchetti NuGet.

1. Nella raccolta Online eseguire la ricerca di Azure MediaServices Extensions, scegliere Azure Media Services .NET SDK Extensions e fare clic sul pulsante Installa.

Il progetto viene modificato e vengono aggiunti riferimenti a Media Services .NET SDK, alle relative estensioni e ad altri assembly dipendenti.

1. Per ottenere un ambiente di sviluppo più lineare, prendere in considerazione l'abilitazione di NuGet Package Restore. Per altre informazioni vedere l'articolo relativo al [ripristino del pacchetto NuGet](http://docs.nuget.org/consume/package-restore).
2. Aggiungere un riferimento all'assembly **System.Configuration** . che contiene la classe**System.Configuration.ConfigurationManager** usata per accedere ai file di configurazione, ad esempio App.config.

Per aggiungere riferimenti usando la finestra di dialogo Gestione riferimenti, effettuare le seguenti operazioni:

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul nome del progetto. Selezionare quindi Aggiungi e Riferimenti.

Viene visualizzata la finestra di dialogo Gestione riferimenti.

1. Negli assembly .NET Framework trovare e selezionare l'assembly System.Configuration.
2. Premere OK.
3. Aprire il file App.config (aggiungere il file al progetto, se non è stato aggiunto per impostazione predefinita) e aggiungere una sezione *appSettings* al file.     
   Impostare i valori per il nome e la chiave dell'account di Servizi multimediali di Azure, come visualizzato nel seguente esempio.
   
    Per trovare i valori di nome e chiave, passare al portale di Azure e selezionare l'account. Su lato destro verrà visualizzata la finestra Impostazioni. Nella finestra Impostazioni selezionare Chiavi. Facendo clic sull'icona accanto a ciascuna casella di testo, il valore viene copiato negli Appunti di sistema.

        <configuration>
        ...
            <appSettings>
              <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
              <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
            </appSettings>

        </configuration>

1. Sovrascrivere le istruzioni using esistenti all'inizio del file Program.cs con il seguente codice.
   
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

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!--HONumber=Oct16_HO2-->


