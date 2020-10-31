---
title: Come usare un'identità gestita assegnata dal sistema per accedere ai dati di Azure Cosmos DB
description: Informazioni su come configurare un Azure Active Directory (Azure AD) identità gestita assegnata dal sistema (identità del servizio gestito) per accedere alle chiavi da Azure Cosmos DB.
author: j-patrick
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 918033f736a28534cd36a4637b41d0a6b3b4cdc7
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93088574"
---
# <a name="use-system-assigned-managed-identities-to-access-azure-cosmos-db-data"></a>Usare identità gestite assegnate dal sistema per accedere ai dati di Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In questo articolo si configurerà una *solida soluzione agnostica* per la rotazione della chiave per accedere alle chiavi Azure Cosmos DB usando le [identità gestite](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md). L'esempio in questo articolo usa funzioni di Azure, ma è possibile usare qualsiasi servizio che supporta le identità gestite. 

Si apprenderà come creare un'app per le funzioni che può accedere ai dati Azure Cosmos DB senza dover copiare le chiavi di Azure Cosmos DB. L'app per le funzioni viene riattivata ogni minuto e registra la temperatura corrente del serbatoio di un acquario. Per informazioni su come configurare un'app per le funzioni attivata dal timer, vedere l'articolo [creare una funzione in Azure attivata da un timer](../azure-functions/functions-create-scheduled-function.md) .

Per semplificare lo scenario, è già stata configurata un'impostazione di [durata (TTL](./time-to-live.md) ) per pulire i documenti di temperatura precedenti. 

## <a name="assign-a-system-assigned-managed-identity-to-a-function-app"></a>Assegnare un'identità gestita assegnata dal sistema a un'app per le funzioni

In questo passaggio si assegnerà un'identità gestita assegnata dal sistema all'app per le funzioni.

1. Nel [portale di Azure](https://portal.azure.com/)aprire il riquadro **funzione di Azure** e passare all'app per le funzioni. 

1. Aprire la scheda identità **funzionalità piattaforma**  >  **Identity** : 

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-selection.png" alt-text="Screenshot che illustra le funzionalità della piattaforma e le opzioni di identità per l'app per le funzioni.":::

1. Nella scheda **identità** **, attivare lo** **stato** di identità del sistema e selezionare **Salva** . Il riquadro **Identity** avrà un aspetto simile al seguente:  

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-system-managed-on.png" alt-text="Screenshot che illustra le funzionalità della piattaforma e le opzioni di identità per l'app per le funzioni.":::

## <a name="grant-access-to-your-azure-cosmos-account"></a>Concedi l'accesso all'account Azure Cosmos

In questo passaggio si assegna un ruolo all'identità gestita assegnata dal sistema dell'app per le funzioni. Azure Cosmos DB dispone di più ruoli predefiniti che è possibile assegnare all'identità gestita. Per questa soluzione si useranno i due ruoli seguenti:

|Ruolo predefinito  |Descrizione  |
|---------|---------|
|[Collaboratore account DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|È in grado di gestire account Azure Cosmos DB. Consente il recupero delle chiavi di lettura/scrittura. |
|[Ruolo Lettore dell'account Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Può leggere i dati degli account Azure Cosmos DB. Consente il recupero delle chiavi di lettura. |

> [!IMPORTANT]
> Il supporto per il controllo degli accessi in base al ruolo in Azure Cosmos DB si applica solo alle operazioni del piano di controllo. Le operazioni del piano dati sono protette tramite chiavi primarie o token di risorsa. Per altre informazioni, vedere l'articolo [proteggere l'accesso ai dati](secure-access-to-data.md) .

> [!TIP] 
> Quando si assegnano i ruoli, assegnare solo l'accesso necessario. Se il servizio richiede solo la lettura dei dati, assegnare il ruolo di **lettore Account Cosmos DB** all'identità gestita. Per ulteriori informazioni sull'importanza dell'accesso con privilegi minimi, vedere l'articolo sull' [esposizione inferiore di account con privilegi](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts) .

In questo scenario, l'app per le funzioni leggerà la temperatura dell'acquario, quindi eseguirà il writeback dei dati in un contenitore Azure Cosmos DB. Poiché l'app per le funzioni deve scrivere i dati, è necessario assegnare il ruolo di **collaboratore account DocumentDB** . 

### <a name="assign-the-role-using-azure-portal"></a>Assegnare il ruolo utilizzando portale di Azure

1. Accedere al portale di Azure e passare all'account di Azure Cosmos DB. Aprire il riquadro **controllo di accesso (IAM)** e quindi la scheda **assegnazioni di ruolo** :

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab.png" alt-text="Screenshot che illustra le funzionalità della piattaforma e le opzioni di identità per l'app per le funzioni.":::

1. Selezionare **Aggiungi** > **Aggiungi assegnazione di ruolo** .

1. Si apre il pannello **Aggiungi assegnazione ruolo** a destra:

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png" alt-text="Screenshot che illustra le funzionalità della piattaforma e le opzioni di identità per l'app per le funzioni.":::

   * **Ruolo** : selezionare **collaboratore account DocumentDB**
   * **Assegnare l'accesso a** : nella sottosezione **Seleziona identità gestita assegnata dal sistema** Selezionare **app per le funzioni** .
   * **Select** : il riquadro verrà popolato con tutte le app per le funzioni nella sottoscrizione che hanno un' **identità del sistema gestito** . In questo caso, selezionare l'app per le funzioni **FishTankTemperatureService** : 

      :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png" alt-text="Screenshot che illustra le funzionalità della piattaforma e le opzioni di identità per l'app per le funzioni.":::

1. Dopo aver selezionato l'app per le funzioni, selezionare **Salva** .

### <a name="assign-the-role-using-azure-cli"></a>Assegnare il ruolo usando l'interfaccia della riga di comando di Azure

Per assegnare il ruolo usando l'interfaccia della riga di comando di Azure, aprire il Azure Cloud Shell ed eseguire i comandi seguenti:

```azurecli-interactive

scope=$(az cosmosdb show --name '<Your_Azure_Cosmos_account_name>' --resource-group '<CosmosDB_Resource_Group>' --query id)

principalId=$(az webapp identity show -n '<Your_Azure_Function_name>' -g '<Azure_Function_Resource_Group>' --query principalId)

az role assignment create --assignee $principalId --role "DocumentDB Account Contributor" --scope $scope
```

## <a name="programmatically-access-the-azure-cosmos-db-keys"></a>Accedere a livello di codice alle chiavi Azure Cosmos DB

A questo punto è disponibile un'app per le funzioni con un'identità gestita assegnata dal sistema con il ruolo **collaboratore account DocumentDB** nelle autorizzazioni Azure Cosmos DB. Il codice dell'app per le funzioni seguente consente di ottenere le chiavi Azure Cosmos DB, creare un oggetto CosmosClient, ottenere la temperatura dell'acquario e quindi salvarlo Azure Cosmos DB.

Questo esempio usa l' [API list keys](/rest/api/cosmos-db-resource-provider/2020-04-01/databaseaccounts/listkeys) per accedere alle chiavi dell'account Azure Cosmos DB.

> [!IMPORTANT] 
> Se si vuole [assegnare il ruolo di lettore Account Cosmos DB](#grant-access-to-your-azure-cosmos-account) , è necessario usare l' [API elenca chiavi](/rest/api/cosmos-db-resource-provider/2020-04-01/databaseaccounts/listreadonlykeys)di sola lettura. In questo modo si popolano solo le chiavi di sola lettura.

L'API List Keys restituisce l' `DatabaseAccountListKeysResult` oggetto. Questo tipo non è definito nelle librerie C#. Il codice seguente illustra l'implementazione di questa classe:  

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

Nell'esempio viene inoltre usato un documento semplice denominato "TemperatureRecord", che viene definito come segue:

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

Si userà la libreria [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) per ottenere il token di identità gestito assegnato dal sistema. Per informazioni su altri modi per ottenere il token e trovare altre informazioni sulla `Microsoft.Azure.Service.AppAuthentication` libreria, vedere l'articolo relativo all' [autenticazione da servizio a servizio](../key-vault/general/service-to-service-authentication.md) .


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

A questo punto è possibile [distribuire l'app per le funzioni](../azure-functions/functions-create-first-function-vs-code.md).

## <a name="next-steps"></a>Passaggi successivi

* [Autenticazione basata su certificati con Azure Cosmos DB e Azure Active Directory](certificate-based-authentication.md)
* [Proteggere le chiavi di Azure Cosmos DB usando Azure Key Vault](access-secrets-from-keyvault.md)
* [Baseline della sicurezza per Azure Cosmos DB](security-baseline.md)
