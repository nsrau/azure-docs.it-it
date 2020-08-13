---
title: Gestire i dati di riferimento in un ambiente di disponibilità generale con C# - Azure Time Series Insights | Microsoft Docs
description: Informazioni su come gestire i dati di riferimento per l'ambiente di disponibilità generale creando un'applicazione personalizzata scritta in C#.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/12/2020
ms.custom: seodec18
ms.openlocfilehash: c2a33c701278a900e502da9e6d9520ea213ce4c3
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88168100"
---
# <a name="manage-reference-data-for-an-azure-time-series-insights-gen-1-environment-using-c-sharp"></a>Gestire i dati di riferimento per un ambiente Azure Time Series Insights generazione 1 con C Sharp

Questo articolo illustra come combinare C#, [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)e Azure Active Directory per eseguire richieste API a livello di codice al riferimento Azure Time Series Insights generazione 1 [Gestione dati API](https://docs.microsoft.com/rest/api/time-series-insights/gen1-reference-data-api).

> [!TIP]
> Vedere gli esempi di codice C# in disponibilità generale in [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen1-sample/csharp-tsi-gen1-sample).

## <a name="summary"></a>Summary

Il codice di esempio seguente illustra le funzionalità seguenti:

* Acquisizione di un token di accesso con [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) **PublicClientApplication**.
* Operazioni di creazione, lettura, aggiornamento ed eliminazione sequenziali sul riferimento di generazione 1 [Gestione dati API](https://docs.microsoft.com/rest/api/time-series-insights/gen1-reference-data-api).
* Codici di risposta comuni che includono [codici di errore comuni](https://docs.microsoft.com/rest/api/time-series-insights/gen1-reference-data-api#validation-and-error-handling).

    L'API di gestione dati di riferimento elabora ogni elemento singolarmente e un errore con un elemento non impedisce il completamento corretto degli altri. Se, ad esempio, la richiesta contiene 100 elementi e un elemento contiene un errore, verranno scritti 99 elementi e ne verrà rifiutato uno.

## <a name="prerequisites-and-setup"></a>Prerequisiti e configurazione

Prima di compilare ed eseguire lo script di esempio, completare questa procedura:

1. Effettuare [il provisioning di un ambiente Azure Time Series Insights di generazione 1](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started
)

1. [Creare un set di dati di riferimento](time-series-insights-add-reference-data-set.md) nell'ambiente. Usare lo schema dei dati di riferimento seguente:

   | Nome della chiave | Type |
   | --- | --- |
   | uuid | string |

1. Configurare l'ambiente di Azure Time Series Insights per Azure Active Directory come descritto in [Autenticazione e autorizzazione](time-series-insights-authentication-and-authorization.md). Usare `http://localhost:8080/` come **URI di reindirizzamento**.

1. Installare le dipendenze del progetto richieste.

1. Modificare il codice di esempio seguente sostituendo ogni occorrenza di **#PLACEHOLDER#** con l'identificatore di ambiente appropriato.

1. Eseguire `dotnet run` all'interno della directory radice del progetto. Quando richiesto, usare il profilo utente per accedere ad Azure.

## <a name="project-dependencies"></a>Dipendenze progetto

Si consiglia di usare la versione più recente di Visual Studio e **NETCore.app**:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) - Versione 16.4.2 o successive
* [NETCore.app](https://www.nuget.org/packages/Microsoft.NETCore.App/2.2.8) - Versione 2.2.8

Il codice di esempio ha due dipendenze obbligatorie:

* MSAL.NET [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) - Pacchetto 4.7.1.
* [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json) - Pacchetto 12.0.3.

Aggiungere i pacchetti usando [NuGet 2.12 o versioni successive](https://www.nuget.org/):

* `dotnet add package Newtonsoft.Json --version 12.0.3`
* `dotnet add package Microsoft.Identity.Client --version 4.7.1`

Oppure:

1. Dichiarare un file `csharp-tsi-msal-ga-sample.csproj`:

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

* Leggere la documentazione di riferimento per l' [API Gestione dati di riferimento](https://docs.microsoft.com/rest/api/time-series-insights/gen1-reference-data-api) di generazione 1.
