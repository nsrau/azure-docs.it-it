---
title: Gestire i dati di riferimento negli ambienti GA con C#-Azure Time Series Insights | Microsoft Docs
description: Informazioni su come gestire i dati di riferimento per l'ambiente GA creando un'applicazione personalizzata scritta in C#.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/15/2020
ms.custom: seodec18
ms.openlocfilehash: f0ce0f7d90540274d24a7e0248e6f197b74033a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416973"
---
# <a name="manage-ga-reference-data-for-an-azure-time-series-insights-environment-using-c"></a>Gestire i dati di riferimento di GA per un ambiente di Azure Time Series Insights con C #

Questo articolo illustra come combinare C#, [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)e Azure Active Directory per eseguire richieste API a livello di codice al riferimento Azure Time Series Insights GA [Gestione dati API](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api).

> [!TIP]
> Vedere gli esempi di codice C# [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample)di GA all'indirizzo.

## <a name="summary"></a>Riepilogo

Il codice di esempio seguente illustra le funzionalità seguenti:

* Acquisizione di un token di accesso con [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) **PublicClientApplication**.
* Operazioni di creazione, lettura, aggiornamento ed eliminazione sequenziali sul [riferimento GA gestione dati API](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api).
* Codici di risposta comuni, inclusi i [codici di errore comuni](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api#validation-and-error-handling).
    
    L'API di riferimento Gestione dati elabora ogni elemento singolarmente e un errore con un elemento non impedisce il completamento corretto degli altri. Se, ad esempio, la richiesta contiene 100 elementi e un elemento contiene un errore, verranno scritti 99 elementi e ne verrà rifiutato uno.

## <a name="prerequisites-and-setup"></a>Prerequisiti e configurazione

Prima di compilare ed eseguire lo script di esempio, completare questa procedura:

1. Effettuare [il provisioning di un ambiente di Azure Time Series Insights GA](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started
) .

1. [Creare un set di dati di riferimento](time-series-insights-add-reference-data-set.md) nell'ambiente in uso. Usare lo schema di dati di riferimento seguente:

   | Nome chiave | Tipo |
   | --- | --- |
   | uuid | Stringa | 

1. Configurare l'ambiente di Azure Time Series Insights per Azure Active Directory, come descritto in [autenticazione e autorizzazione](time-series-insights-authentication-and-authorization.md). Utilizzare `http://localhost:8080/` come **URI di reindirizzamento**.

1. Installare le dipendenze di progetto obbligatorie.

1. Modificare il codice di esempio seguente sostituendo ogni **#PLACEHOLDER #** con l'identificatore di ambiente appropriato.

1. Eseguire `dotnet run` nella directory radice del progetto. Quando richiesto, usare il profilo utente per accedere ad Azure. 

## <a name="project-dependencies"></a>Dipendenze progetto

Si consiglia di usare la versione più recente di Visual Studio e **NETCore. app**:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) -versione 16.4.2 +
* [NETCore. app](https://www.nuget.org/packages/Microsoft.NETCore.App/2.2.8) -Version 2.2.8

Il codice di esempio ha due dipendenze obbligatorie:

* MSAL.NET [Microsoft. Identity. client](https://www.nuget.org/packages/Microsoft.Identity.Client/) -pacchetto 4.7.1.
* [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json) -pacchetto 12.0.3.

Aggiungere i pacchetti usando [NuGet 2.12 +](https://www.nuget.org/):

* `dotnet add package Newtonsoft.Json --version 12.0.3`
* `dotnet add package Microsoft.Identity.Client --version 4.7.1`

Oppure:

1. Dichiarare un `csharp-tsi-msal-ga-sample.csproj` file:

    ```XML
    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <LangVersion>latest</LangVersion>
        <RootNamespace>csharp-tsi-msal-ga-sample</RootNamespace>
        <RunWorkingDirectory>$(MSBuildThisFileDirectory)</RunWorkingDirectory>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Microsoft.Identity.Client" Version="4.7.1.0" Culture="neutral" PublicKeyToken="0a613f4dd989e8ae" />
        <PackageReference Include="Newtonsoft.Json" Version="12.0.3" />
      </ItemGroup>
    </Project>
    ```
1. Quindi eseguire `dotnet restore`.

## <a name="c-sample-code"></a>Codice di esempio C#

```csharp
// Copyright (c) Microsoft Corporation.  All rights reserved.

namespace CsharpTsiMsalGaSample
{
    using Microsoft.Identity.Client;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;

    public static class Program
    {
        /**
         * Review the product documentation for detailed configuration steps or skip ahead and configure your environment settings.
         * 
         * https://docs.microsoft.com/azure/time-series-insights/time-series-insights-authentication-and-authorization
         */

        // Azure Time Series Insights environment configuration
        internal static string EnvironmentFqdn = "#PLACEHOLDER#.env.timeseries.azure.com";
        internal static string EnvironmentReferenceDataSetName = "#PLACEHOLDER#";

        // Azure Active Directory application configuration
        internal static string AadClientApplicationId = "#PLACEHOLDER#";
        internal static string[] AadScopes = new string[] { "https://api.timeseries.azure.com//user_impersonation" };
        internal static string AadRedirectUri = "http://localhost:8080/";
        internal static string AadTenantName = "#PLACEHOLDER#";
        internal static string AadAuthenticationAuthority = "https://login.microsoftonline.com/" + AadTenantName + ".onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/";

        private static async Task<string> AcquireAccessTokenAsync()
        {
            if (AadClientApplicationId == "#PLACEHOLDER#" || AadScopes.Length == 0 || AadRedirectUri == "#PLACEHOLDER#" || AadTenantName.StartsWith("#PLACEHOLDER#"))
            {
                throw new Exception($"Use the link {"https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started"} to update the values of 'AadClientApplicationId', 'AadScopes', 'AadRedirectUri', and 'AadAuthenticationAuthority'.");
            }

            /**
             * MSAL.NET configuration. Review the product documentation for more information about MSAL.NET authentication options.
             * 
             * https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/
             */

            IPublicClientApplication app = PublicClientApplicationBuilder
                .Create(AadClientApplicationId)
                .WithRedirectUri(AadRedirectUri)
                .WithAuthority(AadAuthenticationAuthority)
                .Build();

            AuthenticationResult result = await app
                .AcquireTokenInteractive(AadScopes)
                .ExecuteAsync();

            Console.WriteLine("MSAL Authentication Token Acquired: {0}", result.AccessToken);
            Console.WriteLine("");
            return result.AccessToken;
        }

        // System.Net.HttpClient helper to wrap HTTP POST made to the GA Reference Data API
        private static async Task<HttpResponseMessage> AsyncHttpPostRequestHelper(HttpClient httpClient, string input)
        {
             if (EnvironmentFqdn.StartsWith("#PLACEHOLDER#") || EnvironmentReferenceDataSetName == "#PLACEHOLDER#")
             {
                throw new Exception($"Use the link {"https://docs.microsoft.com/azure/time-series-insights/time-series-insights-authentication-and-authorization"} to update the values of 'EnvironmentFqdn' and 'EnvironmentReferenceDataSetName'.");
             }

             Console.WriteLine("HTTP JSON Request Body: {0}", input);
             Console.WriteLine("");
             HttpContent requestBody = new StringContent(input, Encoding.UTF8, "application/json");

             Uri uri = new UriBuilder("https", EnvironmentFqdn)
             {
                Path = $"referencedatasets/{EnvironmentReferenceDataSetName}/$batch",
                Query = "api-version=2016-12-12"
             }.Uri;
                
             Console.WriteLine("Making HTTP POST to URI: {0}", uri);
             Console.WriteLine("");

             HttpResponseMessage response = await httpClient.PostAsync(uri, requestBody);
             if (response.IsSuccessStatusCode)
             {
                var jsonString = await response.Content.ReadAsStringAsync();
                var jsonStringTransferObject = JsonConvert.DeserializeObject<object>(jsonString);
                Console.WriteLine("HTTP JSON Response Body: {0}", jsonStringTransferObject);
                Console.WriteLine("");
                return response;
             }

             return null;
        }

        private static async Task TsiMsalGaSample()
        {
            Console.WriteLine("Beginning demo...");
            Console.WriteLine("");
            Console.WriteLine("The following samples assume a single Key Name (uuid) with String type...");
            Console.WriteLine("");

            string accessToken = await AcquireAccessTokenAsync();
            var httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Add("Authorization", "Bearer " + accessToken);

            {   
                // CREATE reference data
                Console.WriteLine("CREATE reference data example...");
                Console.WriteLine("");
                string createInput = @"
                    {
                        ""put"": [
                            {
                                ""uuid"": ""Fan1""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""White"",
                                ""floor"": 2
                            },
                            {
                                ""uuid"": ""Fan3"",
                                ""color"": ""Red"",
                                ""maxSpeed"": 5
                            }
                        ]
                    }";


                var createResponse = await AsyncHttpPostRequestHelper(httpClient, createInput);

                // READ reference data
                Console.WriteLine("READ reference data example...");
                Console.WriteLine("");
                string readInput = @"
                    {
                        ""get"": [
                            {
                                ""uuid"": ""Fan1""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""White"",
                                ""desc"": ""example""
                            },
                            {
                                ""uuid"": ""Fan3"",
                                ""color"": ""Red"",
                                ""maxSpeed"": 5
                            }
                        ]
                    }";

                var readResponse = await AsyncHttpPostRequestHelper(httpClient, readInput);

                // UPDATE reference data
                Console.WriteLine("UPDATE reference data example...");
                Console.WriteLine("");
                string updateInput = @"
                    {
                        ""patch"": [
                            {
                                ""uuid"": ""Fan1"",
                                ""color"": ""Red""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""Orange""
                            },
                            {
                                ""uuid"": ""Fan3"",
                                ""desc"": ""Example""
                            }
                        ]
                    }";

                var inputResponse = await AsyncHttpPostRequestHelper(httpClient, updateInput);

                // DELETE reference data
                Console.WriteLine("DELETE reference data example...");
                Console.WriteLine("");
                string deleteInput = @"
                    {
                        ""delete"": [
                            {
                                ""uuid"": ""Fan1""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""Orange""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""desc"": ""Blue""
                            }
                        ]
                    }";

                var deleteResponse = await AsyncHttpPostRequestHelper(httpClient, deleteInput);
            }
        }

        internal static void Main(string[] args)
        {
            Task.Run(async () => await TsiMsalGaSample()).Wait();
        }
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

- Leggere la documentazione di riferimento di GA [Gestione dati API](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api) .
