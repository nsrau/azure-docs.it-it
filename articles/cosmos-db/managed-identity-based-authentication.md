---
title: Come usare un'identità gestita assegnata dal sistema per accedere ai dati del database Cosmos di AzureHow to use a system-assigned managed identity to access Azure Cosmos DB data
description: Informazioni su come configurare un'identità gestita assegnata dal sistema di Azure Active Directory (Azure AD) per accedere alle chiavi da Azure Cosmos DB.
author: j-patrick
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.openlocfilehash: 8136ad7a1fe29bc3394e959c10aafc52988c0a23
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641195"
---
# <a name="use-system-assigned-managed-identities-to-access-azure-cosmos-db-data"></a>Usare le identità gestite assegnate dal sistema per accedere ai dati del database Cosmos di AzureUse system-assigned managed identities to access Azure Cosmos DB data

In questo articolo verrà configurata una soluzione indipendente dalla rotazione delle *chiavi* per accedere alle chiavi del database Cosmos di Azure usando [identità gestite.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) L'esempio in questo articolo usa Funzioni di Azure, ma è possibile usare qualsiasi servizio che supporta le identità gestite. 

Si apprenderà come creare un'app per le funzioni in grado di accedere ai dati di Azure Cosmos DB senza dover copiare chiavi DB Cosmos di Azure.You ll learn how to create a function app that can access Azure Cosmos DB data without needing to copy any Azure Cosmos DB keys. L'applicazione funzione si sveglierà ogni minuto e registrare la temperatura attuale di un acquario acquario serbatoio di pesce. Per informazioni su come configurare un'app per funzioni attivate dal timer, vedere l'articolo [Creare una funzione in Azure attivata da un timer.](../azure-functions/functions-create-scheduled-function.md)

Per semplificare lo scenario, un'impostazione [Time To Live](./time-to-live.md) è già configurata per pulire i documenti di temperatura meno recenti. 

## <a name="assign-a-system-assigned-managed-identity-to-a-function-app"></a>Assegnare un'identità gestita assegnata dal sistema a un'app per le funzioniAssign a system-assigned managed identity to a function app

In questo passaggio verrà assegnata un'identità gestita assegnata dal sistema all'app per le funzioni.

1. Nel [portale di Azure](https://portal.azure.com/)aprire il riquadro Funzione di **Azure** e passare all'app per le funzioni. 

1. Aprire la scheda**Identità** **delle funzionalità della piattaforma:Open** > the Platform features Identity tab: 

   ![Screenshot che mostra le funzionalità della piattaforma e le opzioni di identità per l'app per le funzioni.](./media/managed-identity-based-authentication/identity-tab-selection.png)

1. Nella scheda **Identità,** **attivare** lo **stato** dell'identità del sistema e selezionare **Salva**. Il riquadro **Identità** dovrebbe essere simile al seguente:The Identity pane should look as follows:  

   ![Screenshot che mostra l'identità del sistema Stato impostato su Attivato.](./media/managed-identity-based-authentication/identity-tab-system-managed-on.png)

## <a name="grant-access-to-your-azure-cosmos-account"></a>Concedere l'accesso all'account Azure CosmosGrant access to your Azure Cosmos account

In questo passaggio si assegnerà un ruolo all'identità gestita assegnata dal sistema dell'app per le funzioni. Il database Cosmos di Azure include più ruoli predefiniti che è possibile assegnare all'identità gestita. Per questa soluzione, si utilizzeranno i due ruoli seguenti:For this solution, you'll use the following two roles:

|Ruolo predefinito  |Descrizione  |
|---------|---------|
|[Collaboratore account DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|È in grado di gestire account Azure Cosmos DB. Consente il recupero delle chiavi di lettura/scrittura. |
|[Lettore di account Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Può leggere i dati degli account Azure Cosmos DB. Consente il recupero delle chiavi di lettura. |

> [!IMPORTANT]
> Il supporto per il controllo degli accessi in base al ruolo in Azure Cosmos DB si applica solo alle operazioni piano di controllo. Le operazioni del piano dati sono protette tramite chiavi master o token di risorse. Per altre informazioni, vedere l'articolo [Proteggere l'accesso ai dati.](secure-access-to-data.md)

> [!TIP] 
> Quando si assegnano i ruoli, assegnare solo l'accesso necessario. Se il servizio richiede solo la lettura dei dati, assegnare il ruolo **Cosmos DB Account Reader** all'identità gestita. Per ulteriori informazioni sull'importanza dell'accesso con privilegi minimi, vedere [l'articolo Minore esposizione degli account con privilegi.](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts)

In questo scenario, l'app per le funzioni leggerà la temperatura dell'acquario, quindi riscriverà i dati in un contenitore in Azure Cosmos DB. Poiché l'app per le funzioni deve scrivere i dati, è necessario assegnare il ruolo **Collaboratore account DocumentDB.** 

1. Accedere al portale di Azure e passare all'account del database Cosmos di Azure.Sign in to the Azure portal and go to your Azure Cosmos DB account. Aprire il riquadro **Controllo di accesso (IAM)** e quindi la scheda **Assegnazioni ruolo:**

   ![Screenshot che mostra il riquadro Controllo di accesso e la scheda Assegnazioni ruolo.](./media/managed-identity-based-authentication/cosmos-db-iam-tab.png)

1. Selezionare **: Aggiungi** > **assegnazione ruolo**.

1. Il pannello **Aggiungi assegnazione ruolo** si apre a destra:

   ![Screenshot del riquadro Aggiungi assegnazione ruolo.](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png)

   * **Ruolo**: Selezionare **DocumentDB Account Contributor**
   * **Assegna accesso a**: nella sottosezione **Seleziona identità gestita assegnata dal sistema** selezionare App **per**le funzioni .
   * **Selezionare:** il riquadro verrà popolato con tutte le app per le funzioni nella sottoscrizione che dispongono di **un'identità del sistema gestito**. In questo caso, selezionare l'app per la funzione **FishTankTemperatureService:In** this case, select the FishTankTemperatureService function app: 

      ![Screenshot che mostra il riquadro Aggiungi assegnazione ruolo popolato da esempi.](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png)

1. Dopo aver selezionato l'app per le funzioni, selezionare **Salva**.

## <a name="programmatically-access-the-azure-cosmos-db-keys"></a>Accedere a livello di codice alle chiavi del database di Azure CosmosProgrammatically access the Azure Cosmos DB keys

A questo punto è presente un'app per le funzioni che dispone di un'identità gestita assegnata dal sistema con il ruolo **Collaboratore account DocumentDB** nelle autorizzazioni di database Cosmos di Azure.Now we have a function app that has a system-assigned managed identity with the DocumentDB Account Contributor role in the Azure Cosmos DB permissions. Il codice dell'app per le funzioni seguente otterrà le chiavi di Azure Cosmos DB, creerà un oggetto CosmosClient, otterrà la temperatura dell'acquario e quindi lo salverà in Azure Cosmos DB.

Questo esempio usa [l'API Elenca chiavi](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListKeys) per accedere alle chiavi dell'account del database Cosmos di Azure.This sample uses the List Keys API to access your Azure Cosmos DB account keys.

> [!IMPORTANT] 
> Se si desidera assegnare il ruolo [Lettore account Cosmos DB,](#grant-access-to-your-azure-cosmos-account) è necessario utilizzare l'API Elenca chiavi di [sola lettura](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListReadOnlyKeys). In questo modo verranno popolate solo le chiavi di sola lettura.

L'API List `DatabaseAccountListKeysResult` Keys restituisce l'oggetto. Questo tipo non è definito nelle librerie di C. Nel codice seguente viene illustrata l'implementazione di questa classe:The following code shows the implementation of this class:  

```csharp 
namespace Monitor 
{
  public class DatabaseAccountListKeysResult
  {
      public string primaryMasterKey {get;set;}
      public string primaryReadonlyMasterKey {get; set;}
      public string secondaryMasterKey {get; set;}
      public string secondaryReadonlyMasterKey {get;set;}
  }
}
```

Nell'esempio viene inoltre utilizzato un semplice documento denominato "TemperatureRecord", definito come segue:

```csharp
using System;

namespace Monitor
{
    public class TemperatureRecord
    {
        public string id { get; set; } = Guid.NewGuid().ToString();
        public DateTime RecordTime { get; set; }
        public int Temperature { get; set; }

    }
}
```

Si userà la libreria [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) per ottenere il token di identità gestita assegnato dal sistema. Per altre informazioni su come ottenere il token `Microsoft.Azure.Service.AppAuthentication` e ottenere ulteriori informazioni sulla libreria, vedere l'articolo Autenticazione da [servizio a servizio.](../key-vault/general/service-to-service-authentication.md)


```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;
using Microsoft.Azure.Cosmos;
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;

namespace Monitor
{
    public static class FishTankTemperatureService
    {
        private static string subscriptionId =
        "<azure subscription id>";
        private static string resourceGroupName =
        "<name of your azure resource group>";
        private static string accountName =
        "<Azure Cosmos DB account name>";
        private static string cosmosDbEndpoint =
        "<Azure Cosmos DB endpoint>";
        private static string databaseName =
        "<Azure Cosmos DB name>";
        private static string containerName =
        "<container to store the temperature in>";

        [FunctionName("FishTankTemperatureService")]
        public static async Task Run([TimerTrigger("0 * * * * *")]TimerInfo myTimer, ILogger log)
        {
            log.LogInformation($"Starting temperature monitoring: {DateTime.Now}");

            // AzureServiceTokenProvider will help us to get the Service Managed token.
            var azureServiceTokenProvider = new AzureServiceTokenProvider();

            // Authenticate to the Azure Resource Manager to get the Service Managed token.
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");

            // Setup the List Keys API to get the Azure Cosmos DB keys.
            string endpoint = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2019-12-12";

            // Setup an HTTP Client and add the access token.
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Post to the endpoint to get the keys result.
            var result = await httpClient.PostAsync(endpoint, new StringContent(""));

            // Get the result back as a DatabaseAccountListKeysResult.
            DatabaseAccountListKeysResult keys = await result.Content.ReadAsAsync<DatabaseAccountListKeysResult>();

            log.LogInformation("Starting to create the client");

            CosmosClient client = new CosmosClient(cosmosDbEndpoint, keys.primaryMasterKey);

            log.LogInformation("Client created");

            var database = client.GetDatabase(databaseName);
            var container = database.GetContainer(containerName);

            log.LogInformation("Get the temperature.");

            var tempRecord = new TemperatureRecord() { RecordTime = DateTime.UtcNow, Temperature = GetTemperature() };

            log.LogInformation("Store temperature");

            await container.CreateItemAsync<TemperatureRecord>(tempRecord);

            log.LogInformation($"Ending temperature monitor: {DateTime.Now}");
        }

        private static int GetTemperature()
        {
            // Fake the temperature sensor for this demo.
            Random r = new Random(DateTime.UtcNow.Second);
            return r.Next(0, 120);
        }
    }
}
```

È ora possibile [distribuire l'app](../azure-functions/functions-create-first-function-vs-code.md)per le funzioni.

## <a name="next-steps"></a>Passaggi successivi

* [Autenticazione basata su certificati con Azure Cosmos DB e Azure Active DirectoryCertificate-based authentication with Azure Cosmos DB and Azure Active Directory](certificate-based-authentication.md)
* [Proteggere le chiavi del database di Azure Cosmos usando l'insieme di credenziali delle chiavi di AzureSecure Azure Cosmos DB keys using Azure Key Vault](access-secrets-from-keyvault.md)
* [Linea di base di sicurezza per Azure Cosmos DBSecurity baseline for Azure Cosmos DB](security-baseline.md)
