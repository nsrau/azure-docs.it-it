---
title: Come configurare il computer per lo sviluppo di applicazioni di Servizi multimediali con .NET
description: Informazioni sui prerequisiti generali per l'uso di Servizi multimediali con Media Services SDK per .NET e su come creare un'app di Visual Studio.
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: ec2804c7-c656-4fbf-b3e4-3f0f78599a7f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/23/2017
ms.author: juliako
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 15828bc74937a036871b26493498232ec7cf6f06
ms.contentlocale: it-it
ms.lasthandoff: 08/28/2017

---
# <a name="media-services-development-with-net"></a>Sviluppo di applicazioni di Servizi multimediali con .NET
[!INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

Questo argomento illustra come iniziare a sviluppare applicazioni di Servizi multimediali con .NET.

La libreria di **Azure Media Services .NET SDK** consente di programmare per Servizi multimediali usando .NET. Per facilitare ancora di più lo sviluppo con .NET, è disponibile la libreria di **Media Services .NET SDK Extensions**, contenente un set di funzioni di supporto e metodi di estensione che semplificano il codice .NET. Entrambe le librerie sono disponibili tramite **NuGet** e **GitHub**.

## <a name="prerequisites"></a>Prerequisiti
* Un account di Servizi multimediali ottenuto con una sottoscrizione di Azure nuova o esistente. Vedere l'argomento [Come creare un account di Servizi multimediali](media-services-portal-create-account.md).
* Sistemi operativi: Windows 10, Windows 7, Windows 2008 R2 o Windows 8.
* .NET Framework 4.5
* Visual Studio.

## <a name="create-and-configure-a-visual-studio-project"></a>Creare e configurare un progetto di Visual Studio
In questa sezione viene illustrato come creare un progetto in Visual Studio e configurarlo per lo sviluppo in Servizi multimediali.  In questo caso, il progetto è un'applicazione console Windows C#, ma la stessa procedura di configurazione si applica ad altri tipi di progetti che è possibile creare per le applicazioni Servizi multimediali, ad esempio un'applicazione Windows Forms o un'applicazione Web ASP.NET.

Questa sezione illustra come usare **NuGet** per aggiungere Media Services .NET SDK Extensions e altre librerie dipendenti.

In alternativa, è possibile ottenere i bit più recenti di Media Services .NET SDK da GitHub ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) o [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), compilare la soluzione e quindi aggiungere i riferimenti al progetto client. Tutte le dipendenze necessarie vengono scaricate ed estratte automaticamente.

1. Creare una nuova applicazione console C# in Visual Studio. Immettere un valore nei campi **Nome**, **Percorso** e **Nome soluzione**, quindi fare clic su OK.
2. Compilare la soluzione.
3. Usare il pacchetto **NuGet** per installare e aggiungere **Azure Media Services .NET SDK Extensions** (**windowsazure.mediaservices.extensions**). Insieme al pacchetto viene installato anche **Media Services .NET SDK** e vengono aggiunte tutte le altre dipendenze necessarie.
   
    Verificare di aver installato la versione più aggiornata di NuGet. Per altre informazioni e per istruzioni di installazione, vedere [NuGet](http://nuget.codeplex.com/).
4. In Esplora soluzioni fare clic con il pulsante destro del mouse sul nome del progetto e scegliere Gestisci pacchetti NuGet.
   
    Viene visualizzata la finestra di dialogo Gestione pacchetti NuGet.
5. Nella raccolta Online eseguire la ricerca di Azure MediaServices Extensions, scegliere Azure Media Services .NET SDK Extensions e fare clic sul pulsante Installa.
   
    Il progetto viene modificato e vengono aggiunti riferimenti a Media Services .NET SDK, alle relative estensioni e ad altri assembly dipendenti.
6. Per ottenere un ambiente di sviluppo più lineare, prendere in considerazione l'abilitazione di NuGet Package Restore. Per altre informazioni vedere l'articolo relativo al [ripristino del pacchetto NuGet](http://docs.nuget.org/consume/package-restore).
7. Aggiungere un riferimento all'assembly **System.Configuration** . che contiene la classe**System.Configuration.ConfigurationManager** usata per accedere ai file di configurazione, ad esempio App.config.
   
    Per aggiungere riferimenti usando la finestra di dialogo Gestione riferimenti, fare clic sul nome del progetto in Esplora soluzioni. Selezionare quindi Aggiungi e Riferimenti.
   
    Viene visualizzata la finestra di dialogo Gestione riferimenti.
8. Negli assembly .NET Framework trovare e selezionare l'assembly System.Configuration e premere OK.
9. Aprire il file App.config e aggiungere una sezione *appSettings* al file.     
   
    Impostare i valori necessari per connettersi all'API di Servizi multimediali. Per altre informazioni, vedere [Accesso all'API di Servizi multimediali di Azure con l'autenticazione di Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

    Se si usa l'[autenticazione utente](media-services-use-aad-auth-to-access-ams-api.md#types-of-authentication), il file di configurazione avrà probabilmente i valori per il dominio tenant di Azure AD e l'endpoint dell'API di REST AMS.
    
    >[!Important]
    >La maggior parte dei codici di esempio di Servizi multimediali di Azure usa un tipo di utente (interattivo) di autenticazione per la connessione all'API di AMS. Questo metodo di autenticazione funzionerà in modo efficace per le app native di gestione o monitoraggio: app per dispositivi mobili, app di Windows e app console. Questo metodo di autenticazione non è adatto per il tipo applicazioni come API delle applicazioni, servizi Web e server.  Per altre informazioni, vedere [Accesso all'API di Servizi multimediali di Azure con l'autenticazione di Azure AD](media-services-use-aad-auth-to-access-ams-api.md).

        <configuration>
        ...
            <appSettings>
              <add key="AADTenantDomain" value="YourAADTenantDomain" />
              <add key="MediaServiceRESTAPIEndpoint" value="YourRESTAPIEndpoint" />
            </appSettings>

        </configuration>

10. Sovrascrivere le istruzioni **using** esistenti all'inizio del file Program.cs con il codice seguente.
           
        using System;
        using System.Configuration;
        using System.IO;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Collections.Generic;
        using System.Linq;

A questo punto, si è pronti per iniziare a sviluppare un'applicazione di Servizi multimediali.    

## <a name="example"></a>Esempio

Di seguito è riportato un semplice esempio che consente di eseguire la connessione all'API AMS ed elencare tutti i processori di contenuti multimediali disponibili.
    
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["MediaServiceRESTAPIEndpoint"];
    
        private static CloudMediaContext _context = null;
        static void Main(string[] args)
        {
            var tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain, AzureEnvironments.AzureCloudEnvironment);
            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
    
            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
    
            // List all available Media Processors
            foreach (var mp in _context.MediaProcessors)
            {
                Console.WriteLine(mp.Name);
            }
    
        }

##<a name="next-steps"></a>Passaggi successivi

Ora [è possibile connettersi all'API AMS](media-services-use-aad-auth-to-access-ams-api.md) e iniziare lo [sviluppo](media-services-dotnet-get-started.md).


## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


