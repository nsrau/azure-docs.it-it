---
title: 'Avvio rapido: Prima query con .NET Core'
description: Questo argomento di avvio rapido illustra la procedura per abilitare i pacchetti NuGet di Resource Graph per .NET Core ed eseguire la prima query.
ms.date: 06/29/2020
ms.topic: quickstart
ms.openlocfilehash: b452329148f607b6a71c366c51745906247a43a1
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85802596"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-net-core"></a>Avvio rapido: Eseguire la prima query di Resource Graph con .NET Core

Il primo passaggio per usare Azure Resource Graph consiste nel verificare che i pacchetti necessari per .NET Core siano installati. Questo argomento di avvio rapido illustra la procedura per aggiungere i pacchetti all'installazione di .NET Core.

Al termine della procedura, i pacchetti risulteranno aggiunti all'installazione di .NET Core e sarà possibile eseguire la prima query di Resource Graph.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- Un'entità servizio di Azure, tra cui _clientId_ e _clientSecret_. Se non si hanno entità servizio da usare con Resource Graph o se ne vuole crearne una nuova, vedere [Librerie di gestione di Azure per l'autenticazione .NET](/dotnet/azure/sdk/authentication#mgmt-auth).
  Ignorare il passaggio per installare i pacchetti di .NET Core perché questa operazione verrà descritta nei passaggi successivi.

## <a name="create-the-resource-graph-project"></a>Creare il progetto Resource Graph

Per consentire a .NET Core di eseguire query su Azure Resource Graph, creare una nuova applicazione console e installare i pacchetti richiesti.

1. Controllare che sia installata l'ultima versione di .NET Core (almeno la **3.1.5**). Se non è ancora installata, scaricarla dal sito [dotnet.microsoft.com](https://dotnet.microsoft.com/download/dotnet-core).

1. Inizializzare una nuova applicazione console .NET Core denominata "argQuery":

   ```dotnetcli
   dotnet new console --name "argQuery"
   ```

1. Cambiare le directory nella nuova cartella di progetto e installare i pacchetti richiesti per Azure Resource Graph:

   ```dotnetcli
   # Add the Resource Graph package for .NET Core
   dotnet add package Microsoft.Azure.Management.ResourceGraph --version 2.0.0

   # Add the Azure app auth package for .NET Core
   dotnet add package Microsoft.Azure.Services.AppAuthentication --version 1.5.0
   ```

1. Sostituire il file `program.cs` predefinito con il codice seguente e salvare il file aggiornato:

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   using Microsoft.IdentityModel.Clients.ActiveDirectory;
   using Microsoft.Rest;
   using Microsoft.Azure.Management.ResourceGraph;
   using Microsoft.Azure.Management.ResourceGraph.Models;

   namespace argQuery
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strQuery = args[4];

               AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/" + strTenant);
               AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://management.core.windows.net", new ClientCredential(strClientId, strClientSecret));
               ServiceClientCredentials serviceClientCreds = new TokenCredentials(authResult.AccessToken);

               ResourceGraphClient argClient = new ResourceGraphClient(serviceClientCreds);
               QueryRequest request = new QueryRequest();
               request.Subscriptions = new List<string>(){ strSubscriptionId };
               request.Query = strQuery;

               QueryResponse response = argClient.Resources(request);
               Console.WriteLine("Records: " + response.Count);
               Console.WriteLine("Data:\n" + response.Data);
           }
       }
   }
   ```

1. Compilare e pubblicare l'applicazione console `argQuery`:

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="run-your-first-resource-graph-query"></a>Eseguire la prima query di Resource Graph

Dopo aver compilato e pubblicato l'applicazione console .NET Core, è possibile provare a eseguire una query semplice di Resource Graph. La query restituisce le prime cinque risorse di Azure con il **nome** e il **tipo di risorsa** di ogni risorsa.

In ogni chiamata a `argQuery` vengono usate variabili che è necessario sostituire con i propri valori:

- `{tenantId}`: sostituire con il proprio ID tenant
- `{clientId}`: sostituire con l'ID client dell'entità servizio
- `{clientSecret}`: sostituire con il segreto client dell'entità servizio
- `{subscriptionId}`: sostituire con l'ID sottoscrizione

1. Passare alla directory `{run-folder}` definita con il comando `dotnet publish` precedente.

1. Eseguire la prima query di Azure Resource Graph usando l'applicazione console .NET Core compilata.

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5"
   ```

   > [!NOTE]
   > Poiché questo esempio di query non fornisce un modificatore di ordine, ad esempio `order by`, se si esegue questa query più volte, è probabile che venga restituito un set di risorse diverso per ogni richiesta.

1. Impostare il parametro finale su `argQuery.exe` e cambiare la query per applicare `order by` alla proprietà **Name**:

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5 | order by name asc"
   ```

   > [!NOTE]
   > Come nella prima query, se si esegue questa query più volte, è probabile che venga restituito un set di risorse diverso per ogni richiesta. L'ordine dei comandi della query è importante. In questo esempio `order by` segue `limit`. Questo ordine dei comandi prima limita i risultati della query e successivamente li ordina.

1. Impostare il parametro finale su `argQuery.exe` e cambiare la query per applicare prima `order by` alla proprietà **Name** e quindi `limit` ai primi cinque risultati:

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | order by name asc | limit 5"
   ```

Quando la query finale viene eseguita più volte, presupponendo che non vengano apportate modifiche all'ambiente, i risultati restituiti saranno coerenti e ordinati in base alla proprietà **Name**, ma ancora limitati ai primi cinque risultati.

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere l'applicazione console .NET Core e i pacchetti installati, eliminare la cartella di progetto `argQuery`.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata creata un'applicazione console .NET Core con i pacchetti di Resource Graph richiesti ed è stata eseguita la prima query. Per altre informazioni sul linguaggio di Resource Graph, passare alla pagina dei dettagli del linguaggio di query.

> [!div class="nextstepaction"]
> [Altre informazioni sul linguaggio di query](./concepts/query-language.md)