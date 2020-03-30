---
title: Creare un'istanza di un'app client pubblica (MSAL.NET) Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'istanza di un'applicazione client pubblica con opzioni di configurazione usando la libreria di autenticazione Microsoft per .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1dd06e139f931bbf8554f05f05c5d9b9ccf200e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083589"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>Creare un'istanza di un'applicazione client pubblica con opzioni di configurazione usando MSAL.NET

In questo articolo viene descritto come creare un'istanza di [un'applicazione client pubblica](msal-client-applications.md) utilizzando Microsoft Authentication Library for .NET (MSAL.NET).  Viene creata un'istanza dell'applicazione con le opzioni di configurazione definite in un file di impostazioni.

Prima di inizializzare un'applicazione, devi prima [registrarla](quickstart-register-app.md) in modo che l'app possa essere integrata con la piattaforma di identità Microsoft. Dopo la registrazione, potrebbero essere necessarie le informazioni seguenti (che possono essere trovate nel portale di Azure):After registration, you may need the following information (which can be found in the Azure portal):

- L'ID client (una stringa che rappresenta un GUID)
- L'URL del provider di identità (denominato l'istanza) e il gruppo di destinatari di accesso per l'applicazione. Questi due parametri sono collettivamente noti come l'autorità.
- ID tenant se si scrive un'applicazione line-of-business esclusivamente per l'organizzazione (denominata anche applicazione single-tenant).
- Per le app Web e talvolta per le app client pubbliche (in particolare quando l'app deve usare un broker), devi anche impostare redirectUri in cui il provider di identità contatterà nuovamente l'applicazione con i token di sicurezza.


Un'applicazione console .NET Core potrebbe avere il file di configurazione appsettings.json seguente:A .NET Core console application could have the following *appsettings.json* configuration file:

```json
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

Il codice seguente legge questo file usando il framework di configurazione .NET:The following code reads this file using the .NET configuration framework:

```csharp
public class SampleConfiguration
{
    /// <summary>
    /// Authentication options
    /// </summary>
    public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

    /// <summary>
    /// Base URL for Microsoft Graph (it varies depending on whether the application is ran
    /// in Microsoft Azure public clouds or national / sovereign clouds
    /// </summary>
    public string MicrosoftGraphBaseEndpoint { get; set; }

    /// <summary>
    /// Reads the configuration from a json file
    /// </summary>
    /// <param name="path">Path to the configuration json file</param>
    /// <returns>SampleConfiguration as read from the json file</returns>
    public static SampleConfiguration ReadFromJsonFile(string path)
    {
        // .NET configuration
        IConfigurationRoot Configuration;
        var builder = new ConfigurationBuilder()
          .SetBasePath(Directory.GetCurrentDirectory())
        .AddJsonFile(path);
        Configuration = builder.Build();

        // Read the auth and graph endpoint config
        SampleConfiguration config = new SampleConfiguration()
        {
            PublicClientApplicationOptions = new PublicClientApplicationOptions()
        };
        Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
        config.MicrosoftGraphBaseEndpoint = Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
        return config;
    }
}
```

Il codice seguente crea l'applicazione, usando la configurazione dal file di impostazioni:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

