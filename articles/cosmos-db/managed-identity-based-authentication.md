---
title: Come usare un'identità gestita assegnata dal sistema per accedere ai dati del database Cosmos di AzureHow to use a system-assigned managed identity to access Azure Cosmos DB data
description: Informazioni su come configurare un'identità gestita assegnata dal sistema Azure AD per accedere alle chiavi da Azure Cosmos DB. msi, identità del servizio gestito, aad, azure Active Directory, identità
author: j-patrick
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.openlocfilehash: 37e5cb817db2c54a07ab04c4dcc31b1976fdf03d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450055"
---
# <a name="how-to-use-a-system-assigned-managed-identity-to-access-azure-cosmos-db-data"></a>Come usare un'identità gestita assegnata dal sistema per accedere ai dati del database Cosmos di AzureHow to use a system-assigned managed identity to access Azure Cosmos DB data

In questo articolo verrà configurata una soluzione affidabile e **indipendente dalla rotazione** delle chiavi per accedere alle chiavi di Azure Cosmos DB sfruttando le [identità gestite.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) L'esempio in questo articolo usa una funzione di Azure.The example in this article uses an Azure Function. Tuttavia, è possibile ottenere questa soluzione utilizzando qualsiasi servizio che supporta le identità gestite. 

Si apprenderà come creare una funzione di Azure che può accedere a Azure Cosmos DB senza dover copiare le chiavi DB di Azure Cosmos.You'll learn how to create an Azure Function that can access Azure Cosmos DB without needing to copy any Azure Cosmos DB keys. La funzione si sveglierà ogni minuto e registrerà la temperatura attuale di un acquario acquario acquario serbatoio di pesce. Per informazioni su come configurare un timer attivato Funzione di Azure vedere il [creare una funzione in Azure che viene attivato da un timer](../azure-functions/functions-create-scheduled-function.md) articolo.

Per semplificare lo scenario, la pulizia dei documenti di temperatura meno recenti viene gestita da un'impostazione [Time To Live](./time-to-live.md) già configurata. 

## <a name="assign-a-system-assigned-managed-identity-to-an-azure-function"></a>Assegnare un'identità gestita assegnata dal sistema a una funzione di AzureAssign a system-assigned managed identity to an Azure Function

In this step, you'll assign a system-assigned managed identity to your Azure Function.

1. Nel [portale di Azure](https://portal.azure.com/)aprire il riquadro Funzione di **Azure** e passare all'app per le funzioni. 

1. Aprire la scheda**Identità** **delle funzionalità della piattaforma:Open** > the Platform features Identity tab: 

   ![Scheda Identità](./media/managed-identity-based-authentication/identity-tab-selection.png)

1. Nella scheda **Identità** **attivare** lo stato **Identità sistema.** Assicurarsi di selezionare **Salva**e confermare che si desidera attivare l'identità del sistema. Alla fine il riquadro **Identità** di sistema dovrebbe avere il seguente aspetto:  

   ![Identità di sistema attivata](./media/managed-identity-based-authentication/identity-tab-system-managed-on.png)

## <a name="grant-the-managed-identity-access-to-your-azure-cosmos-account"></a>Concedere l'accesso dell'identità gestita all'account Azure CosmosGrant the managed identity access to your Azure Cosmos account

In questo passaggio si assegnerà un ruolo all'identità gestita assegnata dal sistema della funzione di Azure.In this step, you'll assign a role to the Azure Function's system-assigned managed identity. Il database Cosmos di Azure include più ruoli predefiniti che è possibile assegnare all'identità gestita. Per questa soluzione, si utilizzeranno i due ruoli seguenti:For this solution, you will use the following two roles:

|Ruolo predefinito  |Descrizione  |
|---------|---------|
|[Collaboratore account DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|È in grado di gestire account Azure Cosmos DB. Consente il recupero delle chiavi di lettura/scrittura. |
|[Lettore di account Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Può leggere i dati degli account Azure Cosmos DB. Consente il recupero delle chiavi di lettura. |

> [!IMPORTANT]
> Il supporto RBAC in Azure Cosmos DB è applicabile solo alle operazioni piano di controllo. Le operazioni del piano dati sono protette tramite chiavi master o token di risorse. Per altre informazioni, vedere l'articolo [Proteggere l'accesso ai dati.](secure-access-to-data.md)

> [!TIP] 
> Quando si assegnano i ruoli, assegnare solo l'accesso necessario. Se il servizio richiede solo la lettura dei dati, assegnare l'identità gestita al ruolo **Cosmos DB Account Reader.** Per ulteriori informazioni sull'importanza dell'accesso con privilegi minimi, vedere [l'articolo Minore dell'esposizione degli account con privilegi.](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts)

Per lo scenario, si leggerà la temperatura, quindi si riscriverà i dati in un contenitore in Azure Cosmos DB. Poiché è necessario scrivere i dati, si utilizzerà il ruolo **Collaboratore account DocumentDB.** 

1. Accedere al portale di Azure e passare all'account Azure Cosmos DB. Aprire il **riquadro Gestione accessi (IAM)** e quindi la scheda **Assegnazioni ruolo:**

   ![Riquadro IAM](./media/managed-identity-based-authentication/cosmos-db-iam-tab.png)

1. Selezionare il pulsante **Aggiungi,** quindi **aggiungere l'assegnazione**di ruolo .

1. Il pannello **Aggiungi assegnazione ruolo** si apre a destra:

   ![Aggiungi ruolo](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png)

   * **Ruolo** - Seleziona **collaboratore account DocumentDB**
   * **Assegna accesso a:** nella sottosezione Seleziona **identità gestita assegnata dal sistema** selezionare **App per**le funzioni .
   * **Selezionare:** il riquadro verrà popolato con tutte le app per le funzioni, nella sottoscrizione, che dispongono di **un'identità del sistema gestito**. Nel nostro caso seleziono l'app per le funzioni **SummaryService:** 

      ![Seleziona assegnazione](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png)

1. Dopo aver selezionato l'identità dell'app per le funzioni, fare clic su **Salva**.

## <a name="programmatically-access-the-azure-cosmos-db-keys-from-the-azure-function"></a>Accedere a livello di codice alle chiavi del database Cosmos di Azure dalla funzione di AzureProgrammatically access the Azure Cosmos DB keys from the Azure Function

A questo punto è un'app per le funzioni con un'identità gestita assegnata dal sistema. A tale identità viene assegnato il ruolo **Collaboratore account DocumentDB** nelle autorizzazioni di Azure Cosmos DB. Il codice dell'app per le funzioni seguente otterrà le chiavi di Azure Cosmos DB, creerà un oggetto CosmosClient, otterrà la temperatura, quindi lo salverà in Cosmos DB.

Questo esempio usa [l'API Elenca chiavi](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListKeys) per accedere alle chiavi dell'account del database Cosmos di Azure.This sample uses the List Keys API to access your Azure Cosmos DB account keys.

> [!IMPORTANT] 
> Se si desidera assegnare il ruolo [ **Lettore account Cosmos DB,** ](#grant-the-managed-identity-access-to-your-azure-cosmos-account) sarà necessario utilizzare l'API [List Keys di](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListReadOnlyKeys)sola lettura . In questo modo verranno popolate solo le chiavi di sola lettura.

L'API List `DatabaseAccountListKeysResult` Keys restituisce l'oggetto. Questo tipo non è definito nelle librerie di C. Nel codice seguente viene illustrata l'implementazione di questa classe:The following code shows the implementation of this class:  

```csharp 
namespace SummarizationService 
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

Si userà la libreria [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) per ottenere il token di identità gestita assegnato dal sistema. Per altre informazioni su altri modi per `Microsoft.Azure.Service.AppAuthentication` ottenere il token e altre informazioni sulla libreria, vedere l'articolo [Servizio a autenticazione](../key-vault/general/service-to-service-authentication.md) del servizio.

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
    public static class TemperatureMonitor
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

        [FunctionName("TemperatureMonitor")]
        public static async Task Run([TimerTrigger("0 * * * * *")]TimerInfo myTimer, ILogger log)
        {
            log.LogInformation($"Starting temperature monitoring: {DateTime.Now}");

            // AzureServiceTokenProvider will help us to get the Service Managed token.
            var azureServiceTokenProvider = new AzureServiceTokenProvider();

            // In order to get the Service Managed token we need to authenticate to the Azure Resource Manager.
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");

            // To get the Azure Cosmos DB keys setup the List Keys API:
            string endpoint = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2019-12-12";

            // setup an HTTP Client and add the access token.
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Post to the endpoint to get the keys result.
            var result = await httpClient.PostAsync(endpoint, new StringContent(""));

            // Get the Result back as a DatabaseAccountListKeysResult.
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
            // fake the temperature sensor for this demo
            Random r = new Random(DateTime.UtcNow.Second);
            return r.Next(0, 120);
        }
    }
}
```

È ora possibile [distribuire la funzione di Azure](../azure-functions/functions-create-first-function-vs-code.md).

## <a name="next-steps"></a>Passaggi successivi

* [Autenticazione basata su certificati con Azure Cosmos DB e Active Directory](certificate-based-authentication.md)
* [Proteggere le chiavi di Azure Cosmos con Azure Key Vault](access-secrets-from-keyvault.md)
* [Linea di base di sicurezza per Azure Cosmos DBSecurity baseline for Azure Cosmos DB](security-baseline.md)
