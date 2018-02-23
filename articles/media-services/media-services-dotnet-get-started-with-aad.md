---
title: Usare l'autenticazione di Azure AD per accedere all'API Servizi multimediali di Microsoft Azure con .NET | Microsoft Docs
description: In questo argomento viene illustrato come usare l'autenticazione di Azure Active Directory (Azure AD) per accedere all'API Servizi multimediali di Microsoft Azure (AMS) con .NET.
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.openlocfilehash: a9355200a05a3aa1b494b76977d38ddc42bfe179
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-ad-authentication-to-access-azure-media-services-api-with-net"></a>Usare l'autenticazione di Azure AD per accedere all'API di Servizi multimediali di Azure con .NET

A partire da windowsazure.mediaservices 4.0.0.4, Servizi multimediali di Azure supporta l'autenticazione basata su Azure Active Directory (Azure AD). In questo argomento viene illustrato come usare l'autenticazione di Azure AD per accedere all'API Servizi multimediali di Microsoft Azure con .NET.

## <a name="prerequisites"></a>prerequisiti

- Un account Azure. Per informazioni dettagliate, vedere la pagina relativa alla [versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Account di Servizi multimediali. Per altre informazioni, vedere [Creare un account Servizi multimediali di Azure con il portale di Azure](media-services-portal-create-account.md).
- Il pacchetto [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) più recente.
- Familiarità con l'argomento di [panoramica dell'accesso all'API Servizi multimediali di Azure con autenticazione Azure Active Directory](media-services-use-aad-auth-to-access-ams-api.md). 

Quando si usa l'autenticazione di Azure AD con Servizi multimediali di Azure, è possibile eseguire l'autenticazione in uno di due modi:

- L'**autenticazione utente** consente di eseguire l'autenticazione di una persona che usa l'app per interagire con le risorse di Servizi multimediali di Azure. L'applicazione interattiva deve prima richiedere all'utente le credenziali. Un esempio è un'app della console di gestione usata dagli utenti autorizzati per monitorare i processi di codifica o lo streaming live. 
- L'**autenticazione basata su un'entità servizio** consente di eseguire l'autenticazione di un servizio. Le applicazioni che usano in genere questo metodo di autenticazione sono app che eseguono servizi daemon, servizi di livello intermedio o processi pianificati, ad esempio app Web, app per le funzioni, app per la logica, API o microservizi.

>[!IMPORTANT]
>Attualmente, Servizi multimediali di Azure supporta un modello di autenticazione di Servizio di controllo di accesso Azure. L'autorizzazione di Controllo di accesso, tuttavia, verrà dichiarata deprecata il 1° giugno 2018. È consigliabile eseguire la migrazione all'autenticazione di Azure Active Directory il più presto possibile.

## <a name="get-an-azure-ad-access-token"></a>Ottenere un token di accesso di Azure AD

Per connettersi all'API Servizi multimediali di Azure con l'autenticazione Azure AD, l'app client deve richiedere un token di accesso di Azure AD. Quando si usa l'SDK del client .NET di Servizi multimediali, molte informazioni dettagliate su come acquisire un token di accesso di Azure AD sono riepilogate e semplificate nelle classi [AzureAdTokenProvider](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenProvider.cs) e [AzureAdTokenCredentials](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenCredentials.cs). 

Ad esempio, non è necessario fornire dettagli sull'autorità di Azure AD, sull'URI di risorsa di Servizi multimediali o sull'applicazione Azure AD nativa. Si tratta di valori noti già configurati per la classe del provider del token di accesso di Azure AD. 

Se non si usa l'SDK .NET di Servizi multimediali di Azure, è consigliabile usare [Azure AD Authentication Library](../active-directory/develop/active-directory-authentication-libraries.md). Per ottenere i valori dei parametri da usare con Azure Active Directory Authentication Library, vedere [Utilizzo del portale di Azure per accedere alle impostazioni di autenticazione di Azure AD](media-services-portal-get-started-with-aad.md).

È anche possibile sostituire l'implementazione predefinita di **AzureAdTokenProvider** con la propria implementazione.

## <a name="install-and-configure-azure-media-services-net-sdk"></a>Installare e configurare l'SDK .NET di Servizi multimediali di Azure

>[!NOTE] 
>Per usare l'autenticazione di Azure AD con l'SDK .NET di Servizi Multimediali, è necessario disporre del pacchetto [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) più recente. Aggiungere anche un riferimento all'assembly **Microsoft.IdentityModel.Clients.ActiveDirectory**. Se si usa un'app esistente, includere l'assembly **Microsoft.WindowsAzure.MediaServices.Client.Common.Authentication.dll**. 

1. Creare una nuova applicazione console C# in Visual Studio.
2. Usare il pacchetto NuGet [windowsazure.mediaservices](https://www.nuget.org/packages/windowsazure.mediaservices) per installare l'**SDK .NET di Servizi multimediali di Azure**. 

    Per aggiungere riferimenti usando NuGet, seguire questa procedura: in **Esplora soluzioni** fare clic con il pulsante destro del mouse sul nome del progetto e quindi scegliere **Gestisci pacchetti NuGet**. Cercare quindi **windowsazure.mediaservices** e fare clic su **Installa**.
    
    -oppure-

    Eseguire il comando seguente nella finestra **Console di Gestione pacchetti** in Visual Studio.

        Install-Package windowsazure.mediaservices -Version 4.0.0.4

3. Aggiungere **mediante** al codice sorgente.

        using Microsoft.WindowsAzure.MediaServices.Client; 

## <a name="use-user-authentication"></a>Usare l'autenticazione utente

Per connettersi all'API Servizi multimediali di Azure con l'opzione di autenticazione utente, l'app client deve richiedere un token di Azure AD tramite i parametri seguenti:  

- Endpoint tenant di Azure AD. Le informazioni sul tenant possono essere recuperate dal portale di Azure. Passare il mouse sull'utente connesso nell'angolo superiore destro.
- URI di risorsa per Servizi multimediali.
- ID client dell'applicazione Servizi multimediali (nativa). 
- URI di reindirizzamento dell'applicazione Servizi multimediali (nativa). 

I valori per questi parametri sono reperibili in **AzureEnvironments.AzureCloudEnvironment**. La costante **AzureEnvironments.AzureCloudEnvironment** è un helper nell'SDK .NET che consente di ottenere le impostazioni delle variabili di ambiente ideali per un data center Azure pubblico. 

Contiene le impostazioni di ambiente predefinite per l'accesso a Servizi multimediali esclusivamente nei data center pubblici. Per le regioni cloud sovrane o governative è possibile usare rispettivamente **AzureChinaCloudEnvironment**, **AzureUsGovernmentEnvironment** o **AzureGermanCloudEnvironment**.

L'esempio di codice seguente mostra come creare un token:
    
    var tokenCredentials = new AzureAdTokenCredentials("microsoft.onmicrosoft.com", AzureEnvironments.AzureCloudEnvironment);
    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
  
Prima di iniziare la programmazione basata su Servizi multimediali, è necessario creare un'istanza di **CloudMediaContext** che rappresenta il contesto del server. **CloudMediaContext** contiene riferimenti a raccolte importanti composte da processi, asset, file, criteri di accesso e localizzatori. 

È anche necessario passare l'**URI di risorsa per i servizi REST multimediali** al costruttore **CloudMediaContext**. Per ottenere l'URI di risorsa per i servizi REST multimediali, accedere al portale di Azure, selezionare l'account Servizi multimediali di Azure, fare clic su **Accesso API**, quindi selezionare **Connettersi all'API Servizi multimediali di Azure con l'autorizzazione utente (interattiva)**. 

L'esempio di codice seguente mostra come creare un'istanza **CloudMediaContext**:

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

L'esempio seguente mostra come creare il token di Azure AD e il contesto:

    namespace AADAuthSample
    {
        class Program
        {
            static void Main(string[] args)
            {
                // Specify your Azure AD tenant domain, for example "microsoft.onmicrosoft.com".
                var tokenCredentials = new AzureAdTokenCredentials("{YOUR AAD TENANT DOMAIN HERE}", AzureEnvironments.AzureCloudEnvironment);
    
                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
    
                // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".
                CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
                var assets = context.Assets;
                foreach (var a in assets)
                {
                    Console.WriteLine(a.Name);
                }
            }
    
        }
    }

>[!NOTE]
>Se si verifica un'eccezione che indica "Errore del server remoto: (401) Non autorizzato", vedere la sezione [Controllo di accesso](media-services-use-aad-auth-to-access-ams-api.md#access-control) di Accesso all'interfaccia API di Servizi multimediali di Azure con l'autenticazione di Azure Active Directory.

## <a name="use-service-principal-authentication"></a>Usare l'autenticazione basata su entità servizio
    
Per connettersi all'API Servizi multimediali di Azure con l'opzione dell'entità servizio, l'app di livello intermedio (API o applicazione Web) deve richiedere un token di Azure AD con i parametri seguenti:  

- Endpoint del tenant di Azure AD. Le informazioni sul tenant possono essere recuperate dal portale di Azure. Passare il mouse sull'utente connesso nell'angolo superiore destro.
- URI di risorsa per Servizi multimediali.
- Valori dell'applicazione Azure AD: **ID client** e **Segreto client**.

I valori per i parametri **ID client** e **Segreto client** sono reperibili nel portale di Azure. Per altre informazioni, vedere [Introduzione all'autenticazione di Azure AD tramite il portale di Azure](media-services-portal-get-started-with-aad.md).

L'esempio di codice seguente mostra come creare un token tramite il costruttore **AzureAdTokenCredentials** che accetta **AzureAdClientSymmetricKey** come parametro: 
    
    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"), 
                                AzureEnvironments.AzureCloudEnvironment);

    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

È anche possibile specificare il costruttore **AzureAdTokenCredentials** che accetta **AzureAdClientCertificate** come parametro. 

Per istruzioni su come creare e configurare un certificato in un modulo utilizzabile da Azure AD, vedere [Authenticating to Azure AD in daemon apps with certificates - manual configuration steps](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md) (Autenticazione in Azure AD nelle app daemon con certificati - Passaggi di configurazione manuale).

    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientCertificate("{YOUR CLIENT ID HERE}", "{YOUR CLIENT CERTIFICATE THUMBPRINT}"), 
                                AzureEnvironments.AzureCloudEnvironment);

Prima di iniziare la programmazione basata su Servizi multimediali, è necessario creare un'istanza di **CloudMediaContext** che rappresenta il contesto del server. È anche necessario passare l'**URI di risorsa per i servizi REST multimediali** al costruttore **CloudMediaContext**. È possibile ottenere dal portale di Azure anche il valore dell'**URI di risorsa per i servizi REST multimediali**.

L'esempio di codice seguente mostra come creare un'istanza **CloudMediaContext**:

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
L'esempio seguente mostra come creare il token di Azure AD e il contesto:

    namespace AADAuthSample
    {
    
        class Program
        {
            static void Main(string[] args)
            {
                var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                            new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"), 
                                            AzureEnvironments.AzureCloudEnvironment);
            
                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
    
                // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
                CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
                var assets = context.Assets;
                foreach (var a in assets)
                {
                    Console.WriteLine(a.Name);
                }
    
                Console.ReadLine();
            }
    
        }
    }

## <a name="next-steps"></a>Passaggi successivi

Introduzione al [caricamento di file nell'account](media-services-dotnet-upload-files.md).
