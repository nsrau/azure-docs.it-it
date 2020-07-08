---
title: Librerie di gestione del bus di servizio di Azure | Microsoft Docs
description: Questo articolo illustra come usare le librerie di gestione del bus di servizio di Azure per eseguire il provisioning dinamico di spazi dei nomi e entità del bus di servizio
ms.devlang: dotnet
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 042edcd1851f86dd2a660673bc87884b68410bfb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85341719"
---
# <a name="service-bus-management-libraries"></a>Librerie di gestione del bus di servizio

Le librerie di gestione del bus di servizio di Azure possono eseguire il provisioning di entità e spazi dei nomi del bus di servizio in modo dinamico, per consentire distribuzioni complesse e scenari di messaggistica e permettere di determinare a livello di codice le entità di cui eseguire il provisioning. Queste librerie sono attualmente disponibili per .NET.

## <a name="supported-functionality"></a>Funzionalità supportate

* Creazione, aggiornamento, eliminazione di spazi dei nomi
* Creazione, aggiornamento, eliminazione di code
* Creazione, aggiornamento, eliminazione di argomenti
* Creazione, aggiornamento, eliminazione di sottoscrizioni

## <a name="prerequisites"></a>Prerequisiti

Per iniziare a usare le librerie di gestione del bus di servizio, è necessario eseguire l'autenticazione con il servizio Azure Active Directory (Azure AD). Azure AD richiede l'autenticazione come entità servizio, che fornisce l'accesso alle risorse di Azure in uso. Per informazioni su come creare un'entità servizio, vedere uno di questi articoli:  

* [Usare il portale di Azure per creare Active Directory applicazione e un'entità servizio che possano accedere alle risorse](/azure/azure-resource-manager/resource-group-create-service-principal-portal)
* [Usare Azure PowerShell per creare un'entità servizio per accedere alle risorse](/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Usare l'interfaccia della riga di comando di Azure per creare un'entità servizio per accedere alle risorse](/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)

Nel corso di queste esercitazioni vengono forniti un `AppId` (ID client), un `TenantId` e un `ClientSecret` (chiave di autenticazione) che sono usati per l'autenticazione da parte delle librerie di gestione. Per il gruppo di risorse in cui si desidera eseguire, è necessario disporre almeno delle autorizzazioni di proprietario o [**collaboratore**](/azure/role-based-access-control/built-in-roles#contributor) [**dei dati del bus di servizio di Azure**](/azure/role-based-access-control/built-in-roles#azure-service-bus-data-owner) .

## <a name="programming-pattern"></a>Modello di programmazione

Il modello di modifica delle risorse del bus di servizio segue un protocollo comune:

1. Ottenere un token da Azure AD usando la libreria **Microsoft.IdentityModel.Clients.ActiveDirectory**:
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.azure.com/", new ClientCredential(clientId, clientSecret));
   ```
2. Creare l'oggetto `ServiceBusManagementClient`:

   ```csharp
   var creds = new TokenCredentials(token);
   var sbClient = new ServiceBusManagementClient(creds)
   {
       SubscriptionId = SettingsCache["SubscriptionId"]
   };
   ```
3. Impostare i parametri `CreateOrUpdate` sui valori specificati:

   ```csharp
   var queueParams = new QueueCreateOrUpdateParameters()
   {
       Location = SettingsCache["DataCenterLocation"],
       EnablePartitioning = true
   };
   ```
4. Eseguire la chiamata:

   ```csharp
   await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
   ```

## <a name="complete-code-to-create-a-queue"></a>Completare il codice per creare una coda
Ecco il codice completo per creare una coda del bus di servizio: 

```csharp
using System;
using System.Threading.Tasks;

using Microsoft.Azure.Management.ServiceBus;
using Microsoft.Azure.Management.ServiceBus.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;

namespace SBusADApp
{
    class Program
    {
        static void Main(string[] args)
        {
            CreateQueue().GetAwaiter().GetResult();
        }

        private static async Task CreateQueue()
        {
            try
            {
                var subscriptionID = "<SUBSCRIPTION ID>";
                var resourceGroupName = "<RESOURCE GROUP NAME>";
                var namespaceName = "<SERVICE BUS NAMESPACE NAME>";
                var queueName = "<NAME OF QUEUE YOU WANT TO CREATE>";

                var token = await GetToken();

                var creds = new TokenCredentials(token);
                var sbClient = new ServiceBusManagementClient(creds)
                {
                    SubscriptionId = subscriptionID,
                };

                var queueParams = new SBQueue();

                Console.WriteLine("Creating queue...");
                await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, queueName, queueParams);
                Console.WriteLine("Created queue successfully.");
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not create a queue...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

        private static async Task<string> GetToken()
        {
            try
            {
                var tenantId = "<AZURE AD TENANT ID>";
                var clientId = "<APPLICATION/CLIENT ID>";
                var clientSecret = "<CLIENT SECRET>";

                var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

                var result = await context.AcquireTokenAsync(
                    "https://management.azure.com/",
                    new ClientCredential(clientId, clientSecret)
                );

                // If the token isn't a valid string, throw an error.
                if (string.IsNullOrEmpty(result.AccessToken))
                {
                    throw new Exception("Token result is empty!");
                }

                return result.AccessToken;
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not get a token...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

    }
}
```

> [!IMPORTANT]
> Per un esempio completo, vedere l'esempio di [gestione .NET su GitHub](https://github.com/Azure-Samples/service-bus-dotnet-management/). 

## <a name="next-steps"></a>Passaggi successivi
[Informazioni di riferimento sull'API Microsoft.Azure.Management.ServiceBus](/dotnet/api/Microsoft.Azure.Management.ServiceBus)
