---
title: Come configurare il computer per lo sviluppo di applicazioni di Servizi multimediali con .NET
description: Informazioni sui prerequisiti generali per l'uso di Servizi multimediali con Media Services SDK per .NET e su come creare un'app di Visual Studio.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: ec2804c7-c656-4fbf-b3e4-3f0f78599a7f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2017
ms.author: juliako
ms.openlocfilehash: b5daa2198d76534e5756684e0482d59c047d634f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="media-services-development-with-net"></a>Sviluppo di applicazioni di Servizi multimediali con .NET
[!INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

Questo articolo illustra come iniziare a sviluppare applicazioni di Servizi multimediali con .NET.

La libreria di **Azure Media Services .NET SDK** consente di programmare per Servizi multimediali usando .NET. Per facilitare ancora di più lo sviluppo con .NET, è disponibile la libreria di **Media Services .NET SDK Extensions**, contenente un set di funzioni di supporto e metodi di estensione che semplificano il codice .NET. Entrambe le librerie sono disponibili tramite **NuGet** e **GitHub**.

## <a name="prerequisites"></a>prerequisiti
* Un account di Servizi multimediali ottenuto con una sottoscrizione di Azure nuova o esistente. Vedere l'articolo [Come creare un account di Servizi multimediali](media-services-portal-create-account.md).
* Sistemi operativi: Windows 10, Windows 7, Windows 2008 R2 o Windows 8.
* .NET Framework 4.5 o versioni successive.
* Visual Studio.

## <a name="create-and-configure-a-visual-studio-project"></a>Creare e configurare un progetto di Visual Studio
In questa sezione viene illustrato come creare un progetto in Visual Studio e configurarlo per lo sviluppo in Servizi multimediali.  In questo caso, il progetto è un'applicazione console Windows C#, ma la stessa procedura di configurazione si applica ad altri tipi di progetti che è possibile creare per le applicazioni Servizi multimediali, ad esempio un'applicazione Windows Forms o un'applicazione Web ASP.NET.

Questa sezione illustra come usare **NuGet** per aggiungere Media Services .NET SDK Extensions e altre librerie dipendenti.

In alternativa, è possibile ottenere i bit più recenti di Media Services .NET SDK da GitHub ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) o [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), compilare la soluzione e quindi aggiungere i riferimenti al progetto client. Tutte le dipendenze necessarie vengono scaricate ed estratte automaticamente.

1. Creare una nuova applicazione console C# in Visual Studio. Immettere un valore nei campi **Nome**, **Percorso** e **Nome soluzione**, quindi fare clic su OK.
2. Compilare la soluzione.
3. Usare il pacchetto **NuGet** per installare e aggiungere **Azure Media Services .NET SDK Extensions** (**windowsazure.mediaservices.extensions**). Insieme al pacchetto viene installato anche **Media Services .NET SDK** e vengono aggiunte tutte le altre dipendenze necessarie.
   
    Verificare di aver installato la versione più aggiornata di NuGet. Per altre informazioni e per istruzioni di installazione, vedere [NuGet](http://nuget.codeplex.com/).

    1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **Gestisci pacchetti NuGet**.

    2. Viene visualizzata la finestra di dialogo Gestione pacchetti NuGet.

    3. Nella raccolta online cercare Estensioni di Servizi multimediali di Azure, scegliere **Estensioni dell'SDK di Servizi multimediali di Azure per .NET**(**windowsazure.mediaservices.extensions**) e quindi fare clic sul pulsante **Installa**.
   
    4. Il progetto viene modificato e vengono aggiunti riferimenti a Media Services .NET SDK, alle relative estensioni e ad altri assembly dipendenti.
4. Per ottenere un ambiente di sviluppo più lineare, prendere in considerazione l'abilitazione di NuGet Package Restore. Per altre informazioni vedere l'articolo relativo al [ripristino del pacchetto NuGet](http://docs.nuget.org/consume/package-restore).
5. Aggiungere un riferimento all'assembly **System.Configuration** . che contiene la classe**System.Configuration.ConfigurationManager** usata per accedere ai file di configurazione, ad esempio App.config.
   
    1. Per aggiungere riferimenti usando la finestra di dialogo Gestione riferimenti, fare clic sul nome del progetto in Esplora soluzioni. Fare clic su **Aggiungi** e quindi su **Riferimento**.
   
    2. Viene visualizzata la finestra di dialogo Gestione riferimenti.
    3. Negli assembly .NET Framework individuare e selezionare l'assembly System.Configuration e scegliere **OK**.
6. Aprire il file App.config e aggiungere una sezione **appSettings** al file. Impostare i valori necessari per connettersi all'API di Servizi multimediali. Per altre informazioni, vedere [Accesso all'API di Servizi multimediali di Azure con l'autenticazione di Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

    Impostare i valori necessari per connettersi usando il metodo di autenticazione **Entità servizio**.

        ```csharp
                <configuration>
                ...
                    <appSettings>
                        <add key="AMSAADTenantDomain" value="tenant"/>
                        <add key="AMSRESTAPIEndpoint" value="endpoint"/>
                        <add key="AMSClientId" value="id"/>
                        <add key="AMSClientSecret" value="secret"/>
                    </appSettings>
                </configuration>
        ```

7. Aggiungere il riferimento **System.Configuration** al progetto.
8. Sovrascrivere le istruzioni **using** esistenti all'inizio del file Program.cs con il codice seguente:

    ```csharp      
            using System;
            using System.Configuration;
            using System.IO;
            using Microsoft.WindowsAzure.MediaServices.Client;
            using System.Threading;
            using System.Collections.Generic;
            using System.Linq;
    ```

    A questo punto, si è pronti per iniziare a sviluppare un'applicazione di Servizi multimediali.    

## <a name="example"></a>Esempio

Di seguito è riportato un semplice esempio che consente di eseguire la connessione all'API AMS ed elencare tutti i processori di contenuti multimediali disponibili.

```csharp
        class Program
        {
            // Read values from the App.config file.

            private static readonly string _AADTenantDomain =
                ConfigurationManager.AppSettings["AMSAADTenantDomain"];
            private static readonly string _RESTAPIEndpoint =
                ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
            private static readonly string _AMSClientId =
                ConfigurationManager.AppSettings["AMSClientId"];
            private static readonly string _AMSClientSecret =
                ConfigurationManager.AppSettings["AMSClientSecret"];
        
            private static CloudMediaContext _context = null;
            static void Main(string[] args)
            {
                AzureAdTokenCredentials tokenCredentials = 
                    new AzureAdTokenCredentials(_AADTenantDomain,
                        new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                        AzureEnvironments.AzureCloudEnvironment);

                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

                _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
        
                // List all available Media Processors
                foreach (var mp in _context.MediaProcessors)
                {
                    Console.WriteLine(mp.Name);
                }
        
            }
 ```

## <a name="next-steps"></a>Passaggi successivi

Ora [è possibile connettersi all'API AMS](media-services-use-aad-auth-to-access-ams-api.md) e iniziare lo [sviluppo](media-services-dotnet-get-started.md).


## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

