---
title: Connettersi all'API di Servizi multimediali di Azure - .NETConnect to Azure Media Services v3 API - .NET
description: In questo articolo viene illustrato come connettersi all'API di Servizi multimediali v3 con .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2019
ms.author: juliako
ms.openlocfilehash: b8f4de1a5b9d8216ae2442631f5f9135c3c72d0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269809"
---
# <a name="connect-to-media-services-v3-api---net"></a>Connettersi all'API di Servizi multimediali v3 - .NETConnect to Media Services v3 API - .NET

Questo articolo illustra come connettersi a Azure Media Services v3 .NET SDK usando il metodo di accesso dell'entità servizio.

## <a name="prerequisites"></a>Prerequisiti

- [Creare un account di Servizi multimediali di Azure](create-account-cli-how-to.md). Assicurarsi di ricordare il nome del gruppo di risorse e il nome dell'account di Servizi multimediali
- Installare uno strumento che si desidera utilizzare per lo sviluppo .NET. I passaggi descritti in questo articolo illustrano come utilizzare [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). È possibile utilizzare il codice di Visual Studio, vedere [Utilizzo di C.](https://code.visualstudio.com/docs/languages/csharp) In alternativa, è possibile utilizzare un editor di codice diverso.

> [!IMPORTANT]
> Esaminare [le convenzioni di denominazione](media-services-apis-overview.md#naming-conventions).

## <a name="create-a-console-application"></a>Creare un'applicazione console

1. Avviare Visual Studio. 
1. Scegliere **Nuovo** > **progetto**dal menu **File** . 
1. Creare un'applicazione console **.NET Core.Create** a .NET Core console application.

L'app di esempio `netcoreapp2.0`in questo argomento, targets. Il codice usa 'async main', che è disponibile a partire da C .NET 7.1. Vedere questo [blog](https://blogs.msdn.microsoft.com/benwilli/2017/12/08/async-main-is-available-but-hidden/) per ulteriori dettagli.

## <a name="add-required-nuget-packages"></a>Aggiungere i pacchetti NuGet necessari

1. In Visual Studio selezionare **Strumenti** > **NuGet Gestione** > pacchetti**console di gestione NuGet**.
2. Nella finestra Console di `Install-Package` gestione **pacchetti** usare il comando per aggiungere i pacchetti NuGet seguenti. Ad esempio: `Install-Package Microsoft.Azure.Management.Media`.

|Pacchetto|Descrizione|
|---|---|
|`Microsoft.Azure.Management.Media`|SDK di Servizi multimediali di Azure.Azure Media Services SDK. <br/>Per assicurarsi di usare il pacchetto di Servizi multimediali di Azure più recente, controllare [Microsoft.Azure.Management.Media](https://www.nuget.org/packages/Microsoft.Azure.Management.Media).|
|`Microsoft.Rest.ClientRuntime.Azure.Authentication`|ADAL authentication library for Azure SDK for NET|
|`Microsoft.Extensions.Configuration.EnvironmentVariables`|Leggere i valori di configurazione dalle variabili di ambiente e dai file JSON localiRead configuration values from environment variables and local JSON files|
|`Microsoft.Extensions.Configuration.Json`|Leggere i valori di configurazione dalle variabili di ambiente e dai file JSON localiRead configuration values from environment variables and local JSON files
|`WindowsAzure.Storage`|SDK di archiviazione|

## <a name="create-and-configure-the-app-settings-file"></a>Creare e configurare il file di impostazioni dell'app

### <a name="create-appsettingsjson"></a>Creare appsettings.jsonCreate appsettings.json

1. Vai **vai File** > di**testo**generale .
1. Denominarlo "appsettings.json".
1. Impostare la proprietà "Copia nella directory di output" del file .json su "Copia se più recente" (in modo che l'applicazione sia in grado di accedervi quando viene pubblicata).

### <a name="set-values-in-appsettingsjson"></a>Impostare i valori in appsettings.jsonSet values in appsettings.json

Eseguire `az ams account sp create` il comando come descritto nelle API di [accesso](access-api-cli-how-to.md). Il comando restituisce json che è necessario copiare in "appsettings.json".
 
## <a name="add-configuration-file"></a>Aggiungere un file di configurazione

Per comodità, aggiungere un file di configurazione responsabile della lettura dei valori da "appsettings.json".

1. Aggiungere una nuova classe con estensione cs al progetto. Denominarlo `ConfigWrapper`. 
1. Incollare il codice riportato di seguito in questo `ConsoleApp1`file (in questo esempio si presuppone che lo spazio dei nomi sia ).

```csharp
using System;

using Microsoft.Extensions.Configuration;

namespace ConsoleApp1
{
    public class ConfigWrapper
    {
        private readonly IConfiguration _config;

        public ConfigWrapper(IConfiguration config)
        {
            _config = config;
        }

        public string SubscriptionId
        {
            get { return _config["SubscriptionId"]; }
        }

        public string ResourceGroup
        {
            get { return _config["ResourceGroup"]; }
        }

        public string AccountName
        {
            get { return _config["AccountName"]; }
        }

        public string AadTenantId
        {
            get { return _config["AadTenantId"]; }
        }

        public string AadClientId
        {
            get { return _config["AadClientId"]; }
        }

        public string AadSecret
        {
            get { return _config["AadSecret"]; }
        }

        public Uri ArmAadAudience
        {
            get { return new Uri(_config["ArmAadAudience"]); }
        }

        public Uri AadEndpoint
        {
            get { return new Uri(_config["AadEndpoint"]); }
        }

        public Uri ArmEndpoint
        {
            get { return new Uri(_config["ArmEndpoint"]); }
        }

        public string Region
        {
            get { return _config["Region"]; }
        }
    }
}
```

## <a name="connect-to-the-net-client"></a>Connettersi al client .NET

Per iniziare a usare le API di Servizi multimediali con .NET, è necessario creare un oggetto **AzureMediaServicesClient**. Per eseguire questa operazione, specificare le credenziali necessarie per consentire al client di connettersi ad Azure tramite Azure AD. Nel codice seguente, la funzione GetCredentialsAsync crea l'oggetto ServiceClientCredentials in base alle credenziali fornite nel file di configurazione locale.

1. Aprire `Program.cs`.
1. Incollare il codice seguente:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;

using Microsoft.Azure.Management.Media;
using Microsoft.Azure.Management.Media.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace ConsoleApp1
{
    class Program
    {
        public static async Task Main(string[] args)
        {
            
            ConfigWrapper config = new ConfigWrapper(new ConfigurationBuilder()
                .SetBasePath(Directory.GetCurrentDirectory())
                .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
                .AddEnvironmentVariables()
                .Build());

            try
            {
                IAzureMediaServicesClient client = await CreateMediaServicesClientAsync(config);
                Console.WriteLine("connected");
            }
            catch (Exception exception)
            {
                if (exception.Source.Contains("ActiveDirectory"))
                {
                    Console.Error.WriteLine("TIP: Make sure that you have filled out the appsettings.json file before running this sample.");
                }

                Console.Error.WriteLine($"{exception.Message}");

                ApiErrorException apiException = exception.GetBaseException() as ApiErrorException;
                if (apiException != null)
                {
                    Console.Error.WriteLine(
                        $"ERROR: API call failed with error code '{apiException.Body.Error.Code}' and message '{apiException.Body.Error.Message}'.");
                }
            }

            Console.WriteLine("Press Enter to continue.");
            Console.ReadLine();
        }
 
        private static async Task<ServiceClientCredentials> GetCredentialsAsync(ConfigWrapper config)
        {
            // Use ApplicationTokenProvider.LoginSilentWithCertificateAsync or UserTokenProvider.LoginSilentAsync to get a token using service principal with certificate
            //// ClientAssertionCertificate
            //// ApplicationTokenProvider.LoginSilentWithCertificateAsync

            // Use ApplicationTokenProvider.LoginSilentAsync to get a token using a service principal with symmetric key
            ClientCredential clientCredential = new ClientCredential(config.AadClientId, config.AadSecret);
            return await ApplicationTokenProvider.LoginSilentAsync(config.AadTenantId, clientCredential, ActiveDirectoryServiceSettings.Azure);
        }

        private static async Task<IAzureMediaServicesClient> CreateMediaServicesClientAsync(ConfigWrapper config)
        {
            var credentials = await GetCredentialsAsync(config);

            return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
            {
                SubscriptionId = config.SubscriptionId,
            };
        }

    }
}
```

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Caricare, codificare e riprodurre in streaming video - .NETTutorial: Upload, encode, and stream videos - .NET](stream-files-tutorial-with-api.md) 
- [Esercitazione: Streaming in diretta con Servizi multimediali v3 - .NETTutorial: Stream live with Media Services v3 - .NET](stream-live-tutorial-with-api.md)
- [Esercitazione: Analizzare i video con Servizi multimediali v3 - .NETTutorial: Analyze videos with Media Services v3 - .NET](analyze-videos-tutorial-with-api.md)
- [Creare un input del processo da un file locale - .NET](job-input-from-local-file-how-to.md)
- [Creare un input del processo da un URL HTTPS - .NET](job-input-from-http-how-to.md)
- [Codificare con una trasformazione personalizzata - .NET](customize-encoder-presets-how-to.md)
- [Usare la crittografia dinamica AES-128 e il servizio di distribuzione delle chiavi - .NET](protect-with-aes128.md)
- [Usare il servizio di crittografia dinamica e di distribuzione di licenze DRM - .NET](protect-with-drm.md)
- [Ottenere una chiave di firma dai criteri esistenti - .NET](get-content-key-policy-dotnet-howto.md)
- [Creare filtri con Servizi multimediali - .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Esempi di video on-demand avanzato di Funzioni di Azure v2 con Servizi multimediali v3](https://aka.ms/ams3functions)

## <a name="see-also"></a>Vedere anche

* [Riferimento .NET](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
* Per altri esempi di codice, vedere il repository degli esempi di [.NET SDK.](https://github.com/Azure-Samples/media-services-v3-dotnet)
