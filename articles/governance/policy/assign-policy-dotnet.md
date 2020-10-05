---
title: 'Avvio rapido: Nuova assegnazione di criteri con .NET Core'
description: In questo argomento di avvio rapido si usa .NET Core per creare un'assegnazione di Criteri di Azure per identificare le risorse non conformi.
ms.date: 09/30/2020
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: c4c8f8e9df544b6fc00b5b7701435f5a606f9764
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91604577"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-net-core"></a>Avvio rapido: Creare un'assegnazione di criteri per identificare le risorse non conformi con .NET Core

Il primo passaggio per comprendere la conformità in Azure consiste nell'identificare lo stato delle risorse. In questo avvio rapido si creerà un'assegnazione di criteri per identificare le macchine virtuali che non usano dischi gestiti. Al termine, si identificheranno le macchine virtuali _non conformi_.

La libreria .NET Core viene usata per gestire le risorse di Azure. Questo argomento di avvio rapido illustra come usare la libreria di .NET Core per Criteri di Azure per creare un'assegnazione di criteri.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- Un'entità servizio di Azure, tra cui _clientId_ e _clientSecret_. Se non si hanno entità servizio da usare con Criteri di Azure o se ne vuole creare una nuova, vedere [Librerie di gestione di Azure per l'autenticazione .NET](/dotnet/azure/sdk/authentication#mgmt-auth).
  Ignorare il passaggio per installare i pacchetti di .NET Core perché questa operazione verrà descritta nei passaggi successivi.

## <a name="create-the-azure-policy-project"></a>Creare il progetto di Criteri di Azure

Per consentire a .NET Core di gestire Criteri di Azure, creare una nuova applicazione console e installare i pacchetti richiesti.

1. Controllare che sia installata l'ultima versione di .NET Core (almeno la **3.1.8**). Se non è ancora installata, scaricarla dal sito [dotnet.microsoft.com](https://dotnet.microsoft.com/download/dotnet-core).

1. Inizializzare una nuova applicazione console .NET Core denominata "policyAssignment":

   ```dotnetcli
   dotnet new console --name "policyAssignment"
   ```

1. Cambiare le directory nella nuova cartella di progetto e installare i pacchetti richiesti per Criteri di Azure:

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.ResourceManager --version 3.10.0-preview

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
   using Microsoft.Azure.Management.ResourceManager;
   using Microsoft.Azure.Management.ResourceManager.Models;
   
   namespace policyAssignment
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strName = args[4];
               string strDisplayName = args[5];
               string strPolicyDefID = args[6];
               string strDescription = args[7];
               string strScope = args[8];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                   "https://management.core.windows.net",
                   new ClientCredential(strClientId, strClientSecret));

               using (var client = new PolicyClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var policyAssignment = new PolicyAssignment
                   {
                       DisplayName = strDisplayName,
                       PolicyDefinitionId = strPolicyDefID,
                       Description = strDescription
                   };
                   var response = await client.PolicyAssignments.CreateAsync(strScope, strName, policyAssignment);
               }
           }
       }
   }
   ```

1. Compilare e pubblicare l'applicazione console `policyAssignment`:

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="create-a-policy-assignment"></a>Creare un'assegnazione di criteri

In questo avvio rapido i crea un'assegnazione di criteri e si assegna la definizione **Controlla macchine virtuali che non usano dischi gestiti** (`06a78e20-9358-41c9-923c-fb736d382a4d`). Questa definizione di criteri identifica le risorse che non rispettano le condizioni in essa impostate.

1. Passare alla directory `{run-folder}` definita con il comando `dotnet publish` precedente.

1. Immettere il comando seguente nel terminale:

   ```bash
   policyAssignment.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{subscriptionId}" `
      "audit-vm-manageddisks" `
      "Audit VMs without managed disks Assignment" `
      "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d" `
      "Shows all virtual machines not using managed disks" `
      "{scope}"
   ```

I comandi precedenti usano le informazioni seguenti:

- `{tenantId}`: sostituire con il proprio ID tenant
- `{clientId}`: sostituire con l'ID client dell'entità servizio
- `{clientSecret}`: sostituire con il segreto client dell'entità servizio
- `{subscriptionId}`: sostituire con l'ID sottoscrizione
- **name**: nome univoco per l'oggetto assegnazione di criteri. L'esempio precedente usa _audit-vm-manageddisks_.
- **displayName**: nome visualizzato per l'assegnazione di criteri. In questo caso viene usato _controllare le macchine virtuali senza assegnazione di dischi gestiti_.
- **policyDefID**: percorso di definizione di criteri in base al quale si crea l'assegnazione. In questo caso si tratta dell'ID della definizione di criteri _Controllare le macchine virtuali che non usano i dischi gestiti_.
- **description** - Spiegazione più dettagliata delle operazioni eseguite dal criterio o dei motivi per cui è assegnato a questo ambito.
- **scope** - Ambito che determina le risorse o il raggruppamento di risorse a cui viene applicata l'assegnazione di criteri. Può spaziare da un gruppo di gestione a una singola risorsa. Assicurarsi di sostituire `{scope}` con uno dei criteri seguenti:
  - Gruppo di gestione: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Sottoscrizione: `/subscriptions/{subscriptionId}`
  - Gruppo di risorse: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Risorsa: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

A questo punto si è pronti per identificare le risorse non conformi e comprendere così lo stato di conformità dell'ambiente.

## <a name="identify-non-compliant-resources"></a>Identificare risorse non conformi

Dopo aver creato l'assegnazione di criteri, è possibile identificare le risorse non conformi.

1. Inizializzare una nuova applicazione console .NET Core denominata "policyCompliance":

   ```dotnetcli
   dotnet new console --name "policyCompliance"
   ```

1. Cambiare le directory nella nuova cartella di progetto e installare i pacchetti richiesti per Criteri di Azure:

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.PolicyInsights --version 3.1.0

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
   using Microsoft.Azure.Management.PolicyInsights;
   using Microsoft.Azure.Management.PolicyInsights.Models;
   
   namespace policyAssignment
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strName = args[4];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                   "https://management.core.windows.net",
                   new ClientCredential(strClientId, strClientSecret));
   
               using (var client = new PolicyInsightsClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var policyQueryOptions = new QueryOptions
                   {
                       Filter = $"IsCompliant eq false and PolicyAssignmentId eq '{strName}'",
                       Apply = "groupby(ResourceId)"
                   };
   
                   var response = await client.PolicyStates.ListQueryResultsForSubscriptionAsync(
                       "latest", strSubscriptionId, policyQueryOptions);
                   Console.WriteLine(response.Odatacount);
               }
           }
       }
   }
   ```

1. Compilare e pubblicare l'applicazione console `policyAssignment`:

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

1. Passare alla directory `{run-folder}` definita con il comando `dotnet publish` precedente.

1. Immettere il comando seguente nel terminale:

   ```bash
   policyCompliance.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{subscriptionId}" `
      "audit-vm-manageddisks"
   ```

I comandi precedenti usano le informazioni seguenti:

- `{tenantId}`: sostituire con il proprio ID tenant
- `{clientId}`: sostituire con l'ID client dell'entità servizio
- `{clientSecret}`: sostituire con il segreto client dell'entità servizio
- `{subscriptionId}`: sostituire con l'ID sottoscrizione
- **name**: nome univoco per l'oggetto assegnazione di criteri. L'esempio precedente usa _audit-vm-manageddisks_.

I risultati in `response` corrispondono agli elementi visualizzati nella scheda **Conformità risorsa** di un'assegnazione di criteri nella visualizzazione del portale di Azure.

## <a name="clean-up-resources"></a>Pulire le risorse

- Eliminare l'assegnazione di criteri _controllare le macchine virtuali senza assegnazione di dischi gestiti_ tramite il portale. La definizione di criteri è integrata, quindi non esiste alcuna definizione da rimuovere.

- Per rimuovere le applicazioni console .NET Core e i pacchetti installati, eliminare le cartelle di progetto `policyAssignment` e `policyCompliance`.

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stata assegnata una definizione dei criteri per identificare le risorse non conformi nell'ambiente Azure.

Per altre informazioni sull'assegnazione di definizioni di criteri per convalidare la conformità delle nuove risorse, continuare con l'esercitazione seguente:

> [!div class="nextstepaction"]
> [Creazione e gestione dei criteri](./tutorials/create-and-manage.md)
