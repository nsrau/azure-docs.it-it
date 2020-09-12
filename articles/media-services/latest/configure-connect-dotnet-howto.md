---
title: Connettersi alle API di Servizi multimediali di Azure v3 - .NET
description: Questo argomento illustra come connettersi alle API di Servizi multimediali v3 mediante .NET.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: b9b72e8340722f8ddc97b3769ce22e607974a508
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89297400"
---
# <a name="connect-to-media-services-v3-api---net"></a>Connettersi alle API di Servizi multimediali v3 - .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Questo articolo spiega come connettersi all'SDK .NET di Servizi multimediali di Azure v3 usando il metodo di accesso dell'entità servizio.

## <a name="prerequisites"></a>Prerequisiti

- [Creare un account di Servizi multimediali di Azure](./create-account-howto.md). Assicurarsi di ricordare il nome del gruppo di risorse e quello dell'account di Servizi multimediali
- Installare uno strumento da usare per lo sviluppo in .NET. I passaggi descritti in questo articolo mostrano come usare [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Per usare Visual Studio Code, vedere [Working with C#](https://code.visualstudio.com/docs/languages/csharp) (Uso di C#). In alternativa, è possibile usare un altro editor di codice.

> [!IMPORTANT]
> Vedere [Convenzioni di denominazione](media-services-apis-overview.md#naming-conventions).

## <a name="create-a-console-application"></a>Creare un'applicazione console

1. Avviare Visual Studio. 
1. Scegliere **Nuovo** > **progetto** dal menu **File**. 
1. Creare un'applicazione console di **.NET Core**.

L'app di esempio in questo argomento usa la destinazione `netcoreapp2.0`. Il codice usa "principale asincrono", disponibile a partire da C# 7.1. Per altri dettagli, vedere questo [blog](/archive/blogs/benwilli/async-main-is-available-but-hidden).

## <a name="add-required-nuget-packages"></a>Aggiungere i pacchetti NuGet necessari

1. In Visual Studio selezionare  **Strumenti**  > **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.
2. Nella finestra **Console di Gestione pacchetti** usare il comando `Install-Package` per aggiungere i pacchetti NuGet seguenti. Ad esempio: `Install-Package Microsoft.Azure.Management.Media`.

|Pacchetto|Descrizione|
|---|---|
|`Microsoft.Azure.Management.Media`|SDK di Servizi multimediali di Azure. <br/>Per assicurarsi di usare il pacchetto di Servizi multimediali di Azure più recente, vedere [Microsoft.Azure.Management.Media](https://www.nuget.org/packages/Microsoft.Azure.Management.Media).|
|`Microsoft.Rest.ClientRuntime.Azure.Authentication`|Libreria di autenticazione ADAL di SDK Azure per .NET|
|`Microsoft.Extensions.Configuration.EnvironmentVariables`|Leggere i valori di configurazione nelle variabili di ambiente e nei file JSON locali|
|`Microsoft.Extensions.Configuration.Json`|Leggere i valori di configurazione nelle variabili di ambiente e nei file JSON locali
|`WindowsAzure.Storage`|SDK di archiviazione|

## <a name="create-and-configure-the-app-settings-file"></a>Creare e configurare il file di impostazione dell'app

### <a name="create-appsettingsjson"></a>Creare appsettings.json

1. Selezionare **Generale** > **File di testo**.
1. Assegnare il nome "appsettings.json".
1. Impostare la proprietà "Copia nella directory di output" del file .json su "Copia se più recente" (in modo che l'applicazione sia in grado di accedervi al momento della pubblicazione).

### <a name="set-values-in-appsettingsjson"></a>Impostare i valori in appsettings.json

Eseguire il comando `az ams account sp create` come descritto in [Accedere alle API](./access-api-howto.md). Il comando restituisce un json che è necessario copiare in "appsettings.json".
 
## <a name="add-configuration-file"></a>Aggiungere un file di configurazione

Per praticità, aggiungere un file di configurazione responsabile della lettura dei valori da "appsettings.json".

1. Aggiungere una nuova classe .cs al progetto. Denominarlo `ConfigWrapper`. 
1. Incollare il codice seguente in questo file (nell'esempio, lo spazio dei nomi è `ConsoleApp1`).

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

- [Esercitazione: Caricare, codificare ed eseguire lo streaming dei video - .NET](stream-files-tutorial-with-api.md) 
- [Esercitazione: Eseguire lo streaming live con Servizi multimediali v3 - .NET](stream-live-tutorial-with-api.md)
- [Esercitazione: Analizzare i video con Servizi multimediali v3 - .NET](analyze-videos-tutorial-with-api.md)
- [Creare un input del processo da un file locale - .NET](job-input-from-local-file-how-to.md)
- [Creare un input del processo da un URL HTTPS - .NET](job-input-from-http-how-to.md)
- [Codificare con una trasformazione personalizzata - .NET](customize-encoder-presets-how-to.md)
- [Usare la crittografia dinamica AES-128 e il servizio di distribuzione delle chiavi - .NET](protect-with-aes128.md)
- [Usare il servizio di crittografia dinamica e di distribuzione di licenze DRM - .NET](protect-with-drm.md)
- [Ottenere una chiave di firma dai criteri esistenti - .NET](get-content-key-policy-dotnet-howto.md)
- [Creare filtri con Servizi multimediali - .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Esempi di video on-demand avanzato di Funzioni di Azure v2 con Servizi multimediali v3](https://aka.ms/ams3functions)

## <a name="see-also"></a>Vedere anche

* [Riferimento .NET](/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
* Per altri esempi di codice, vedere l'archivio di esempi [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet).
