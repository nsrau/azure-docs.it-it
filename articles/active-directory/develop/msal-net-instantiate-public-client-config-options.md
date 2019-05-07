---
title: Creare un'istanza di un'app client pubblici con le opzioni (Microsoft Authentication Library per .NET) | Azure
description: Informazioni su come creare un'istanza di un'applicazione client pubblica con opzioni di configurazione usando Microsoft Authentication Library per .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a483cd6e22ce3c9fafb9b2b4d839e22c248f020
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65158701"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>Creare un'istanza di un'applicazione client pubblica con opzioni di configurazione tramite MSAL.NET

Questo articolo descrive come creare un'istanza di un [applicazione client pubblica](msal-client-applications.md) usando Microsoft Authentication Library per .NET (MSAL.NET).  L'applicazione viene creata un'istanza con le opzioni di configurazione definite in un file di impostazioni.

Prima di inizializzare un'applicazione, è necessario innanzitutto [registrare](quickstart-register-app.md) , in modo che l'app può essere integrato con la piattaforma delle identità Microsoft. Dopo la registrazione, potrebbe essere necessario le informazioni seguenti (che sono reperibile nel portale di Azure):

- L'ID client (una stringa che rappresenta un GUID)
- URL di provider di identità (l'istanza denominata) e i destinatari di accesso per l'applicazione. Questi due parametri sono noti collettivamente come autorità.
- L'ID tenant se si sta scrivendo un'applicazione line of business esclusivamente per l'organizzazione (anche denominati applicazione a tenant singolo).
- Per le app web e talvolta per le app client pubblico (in particolare quando l'app deve usare un broker), verranno inoltre impostate l'URI di reindirizzamento in cui il provider di identità contatterà back dell'applicazione con i token di sicurezza.


Un'applicazione console .NET Core può avere quanto segue *appSettings. JSON* file di configurazione:

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

Il codice seguente legge questo file utilizza la configurazione di .NET framework:

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

