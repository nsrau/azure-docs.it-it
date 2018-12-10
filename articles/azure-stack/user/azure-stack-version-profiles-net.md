---
title: Tramite i profili delle versioni API con .NET SDK in Azure Stack | Microsoft Docs
description: Imparare a utilizzare i profili delle versioni API con .NET in Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: cfebbdb9b88a1de6a05f06e6ed72ebc9cddddcf6
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53074452"
---
# <a name="use-api-version-profiles-with-net-in-azure-stack"></a>Usare i profili delle versioni API con .NET in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

.NET SDK per Azure Stack Resource Manager fornisce strumenti che consentono di compilare e gestire l'infrastruttura. I provider di risorse nel SDK includono calcolo, rete, archiviazione, servizi app, e [KeyVault](../../key-vault/key-vault-whatis.md). .NET SDK include 14 pacchetti NuGet che devono essere scaricati per la soluzione di progetto ogni volta che incorporano le informazioni sul profilo. Tuttavia, è possibile scaricare in modo specifico quali provider di risorse si userà per il 2018-03-01-hybrid o 2017-03-09-profile per ottimizzare la memoria per l'applicazione. Ogni pacchetto è costituito da un provider di risorse, la rispettiva versione dell'API e il profilo di API a cui appartiene. I profili di API in .NET SDK consentono lo sviluppo cloud ibride grazie alla possibilità di passare tra le risorse di Azure globali e le risorse in Azure Stack.

## <a name="net-and-api-version-profiles"></a>Profili delle versioni di .NET e API

Un profilo di API è una combinazione di versioni di API e i provider di risorse. È possibile usare un profilo di API per ottenere la versione più recente e più stabile di ogni tipo di risorsa in un pacchetto di provider di risorse.

-   Per usare le versioni più recenti di tutti i servizi, usare il **più recente** profilo dei pacchetti. Questo profilo è parte di **partire** pacchetto NuGet.

-   Per usare i servizi compatibili con Azure Stack, usare il **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg** oppure **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg** pacchetti.

    -   Sono disponibili due pacchetti per ogni provider di risorse per ogni profilo.

    -   Verificare che il **ResourceProvider** parte del pacchetto NuGet precedente viene modificato per il provider corretto.

-   Per usare la versione API più recente di un servizio, usare il **più recente** profilo del pacchetto NuGet specifico. Ad esempio, se si desidera utilizzare il **più recente-API** versione del servizio di calcolo da solo, utilizzare il **più recente** profilo del **calcolo** pacchetto. Il **più recente** profilo fa parte delle **partire** pacchetto NuGet.

-   Per usare versioni API specifiche per un tipo di risorsa in un provider di risorse specifico, usare le versioni API specifiche definite all'interno del pacchetto.

Si noti che è possibile combinare tutte le opzioni nella stessa applicazione.

## <a name="install-the-azure-net-sdk"></a>Installare Azure .NET SDK

1.  Installare Git. Per istruzioni, vedere [Guida introduttiva: installazione di Git][].

2.  Per installare i pacchetti NuGet corretti, vedere [ricerca e installazione di un pacchetto][].

3.  I pacchetti che devono essere installati dipende dalla versione del profilo da utilizzare. Il nome del pacchetto per le versioni di profili sono:

    1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg**

    2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg**

4.  Per installare i pacchetti NuGet corretti per Visual Studio Code, vedere il collegamento seguente per scaricare il [istruzioni di gestione pacchetti NuGet][].

5.  Se non è disponibile, creare una sottoscrizione e salvare l'ID sottoscrizione da usare in un secondo momento. Per istruzioni su come creare una sottoscrizione, vedere [creare le sottoscrizioni di offerte in Azure Stack][].

6.  Creare un'entità servizio e salvare l'ID Client e il segreto Client. Per istruzioni su come creare un'entità servizio per Azure Stack, vedere [Fornire l'accesso delle applicazioni in Azure Stack][]. Si noti che l'ID Client è noto anche come ID dell'applicazione durante la creazione di un'entità servizio.

7.  Assicurarsi che all'entità servizio è installato il ruolo di collaboratore/proprietario della sottoscrizione. Per istruzioni su come assegnare un ruolo all'entità servizio, vedere [Fornire l'accesso delle applicazioni in Azure Stack][].

## <a name="prerequisites"></a>Prerequisiti

Per usare Azure .NET SDK con Azure Stack, è necessario fornire i valori seguenti e quindi impostare i valori con le variabili di ambiente. Per impostare le variabili di ambiente, vedere le istruzioni che seguono la tabella per il sistema operativo.

| Valore                     | Variabili di ambiente   | DESCRIZIONE                                                                                                             |
|---------------------------|-------------------------|-------------------------------------------------------------------------------------------------------------------------|
| ID tenant                 | AZURE_TENANT_ID       | Il valore di Azure Stack [ *ID tenant*][].                                                                          |
| ID client                 | AZURE_CLIENT_ID       | Il servizio di ID entità applicazione salvato quando l'entità servizio è stato creato nella sezione precedente di questo articolo. |
| ID sottoscrizione           | AZURE_SUBSCRIPTION_ID | Il [ *ID sottoscrizione* ][] è la modalità di accesso offerte in Azure Stack.                                                      |
| Client Secret             | AZURE_CLIENT_SECRET   | Segreto dell'applicazione dell'entità servizio salvato quando è stata creata l'entità servizio.                                      |
| Endpoint di Resource Manager | ARM_ENDPOINT           | Visualizzare [ *l'endpoint di gestione risorse di Azure Stack*][].                                                                    |

Per trovare l'ID Tenant per Azure Stack, seguire le istruzioni disponibili [qui](../azure-stack-csp-ref-operations.md). Per impostare le variabili di ambiente, eseguire le operazioni seguenti:

### <a name="microsoft-windows"></a>Microsoft Windows

Per impostare le variabili di ambiente in un prompt dei comandi di Windows, usare il formato seguente:

```shell
Set Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="macos-linux-and-unix-based-systems"></a>sistemi basati su Unix, Linux e macOS

In sistemi basati su Unix, è possibile usare il comando seguente:

```shell
Export Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="the-azure-stack-resource-manager-endpoint"></a>L'endpoint di gestione risorse di Azure Stack

Microsoft Azure Resource Manager è un framework di gestione che consente agli amministratori di distribuire, gestire e monitorare le risorse di Azure. Azure Resource Manager può gestire queste attività come un gruppo, anziché singolarmente e in un'unica operazione.

È possibile ottenere le informazioni sui metadati dall'endpoint di Resource Manager. L'endpoint restituisce un file JSON con le informazioni necessarie per eseguire il codice.

Tenere presente le considerazioni seguenti:

- Il **ResourceManagerUrl** in Azure Stack Development Kit (ASDK) è: https://management.local.azurestack.external/

- Il **ResourceManagerUrl** in sistemi integrati: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` per recuperare i metadati richiesti: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

File JSON di esempio:

```json
{ 
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",
   "graphEndpoint": "https://graph.windows.net/",
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": 
      {
      "loginEndpoint": "https://login.windows.net/",
      "audiences": ["https://management.yourtenant.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>Profili dell'API esistenti

1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg**: profilo più recente creato per Azure Stack. Usare questo profilo per i servizi di essere più compatibile con Azure Stack, purché si è in corrispondenza del timestamp 1808 o altri.

2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg**: se si usa un timestamp minore rispetto alla build 1808, usare questo profilo.

3.  **Più recente**: profilo costituito le versioni più recenti di tutti i servizi. Usare le versioni più recenti di tutti i servizi. Questo profilo è parte di **partire** pacchetto NuGet.

Per altre informazioni sui profili di Azure Stack e l'API, vedere una [Riepilogo dei profili di API][].

## <a name="azure-net-sdk-api-profile-usage"></a>Azure .NET SDK API profilare l'utilizzo

Il codice seguente deve essere utilizzato per creare un client di profilo. Questo parametro è solo necessario per Azure Stack o in altri cloud privato. Per impostazione predefinita, Azure globale ha già queste impostazioni.

Il codice seguente è necessaria per autenticare l'entità servizio in Azure Stack. Crea un token dal tenant ID e la base di autenticazione, che è specifica per Azure Stack.

```csharp
public class CustomLoginCredentials : ServiceClientCredentials
{
    private string clientId;
    private string clientSecret;
    private string resourceId;
    private string tenantId;

    private const string authenticationBase = "https://login.windows.net/{0}";

    public CustomLoginCredentials(string servicePrincipalId, string servicePrincipalSecret, string azureEnvironmentResourceId, string azureEnvironmentTenandId)
    {
        clientId = servicePrincipalId;
        clientSecret = servicePrincipalSecret;
        resourceId = azureEnvironmentResourceId;
        tenantId = azureEnvironmentTenandId;
    }
```

Ciò consentirà di usare i pacchetti NuGet dell'API del profilo per distribuire l'applicazione correttamente in Azure Stack.

## <a name="define-azure-stack-environment-setting-functions"></a>Definire le funzioni di impostazione ambiente Azure Stack

Per autenticare l'entità servizio nell'ambiente Azure Stack, usare il codice seguente:

```csharp
private string AuthenticationToken { get; set; }
public override void InitializeServiceClient<T>(ServiceClient<T> client)
{
    var authenticationContext = new AuthenticationContext(String.Format(authenticationBase, tenantId));
    var credential = new ClientCredential(clientId, clientSecret);
    var result = authenticationContext.AcquireTokenAsync(resource: resourceId,
    clientCredential: credential).Result;
    if (result == null)
    {
        throw new InvalidOperationException("Failed to obtain the JWT token");
    }
    AuthenticationToken = result.AccessToken;
}
```

Questa impostazione sostituisce il client del servizio di inizializzazione per l'autenticazione ad Azure Stack.

## <a name="samples-using-api-profiles"></a>Esempi che usano i profili delle API

È possibile usare gli esempi seguenti disponibili nei repository di GitHub come riferimento per la creazione di soluzioni con i profili .NET e API di Azure Stack.

-   [Progetto di test a una macchina virtuale, rete virtuale, i gruppi di risorse e account di archiviazione][]
-   Gestire macchine virtuali con .NET

### <a name="sample-unit-test-project"></a>Progetto di Unit Test di esempio 

1.  Clonare il repository usando il comando seguente:

    ```shell
    git clone https://github.com/Azure-Samples/hybrid-compute-dotnet-manage-vm.git
    ```

2.  Creare un'entità servizio Azure e assegnare un ruolo per accedere alla sottoscrizione. Per istruzioni sulla creazione di un'entità servizio, vedere [usare Azure PowerShell per creare un'entità servizio con un certificato][].

3.  Recuperare i valori richiesti seguenti:

    1.  ID tenant
    2.  ID client
    3.  Client Secret
    4.  ID sottoscrizione
    5.  Endpoint di Resource Manager

4.  Impostare le variabili di ambiente seguenti usando le informazioni che precedentemente recuperati dall'entità servizio creata utilizzando il prompt dei comandi:

    1.  esportare AZURE_TENANT_ID = {proprio id tenant}
    2.  esportare AZURE_CLIENT_ID = {l'id client}
    3.  esportare AZURE_CLIENT_SECRET = {il segreto client}
    4.  esportare AZURE_SUBSCRIPTION_ID = {id sottoscrizione}
    5.  esportare ARM_ENDPOINT = {l'URL di gestione risorse di Azure Stack}

   In Windows, usare **impostata** invece di **esportare**.

5.  Verificare che la variabile di percorso è impostata nel percorso di Azure Stack. Ad esempio, locale = "local".

6.  Impostare le credenziali di accesso personalizzata che consentono l'autenticazione ad Azure Stack. Si noti che questa parte del codice è incluso in questo esempio nella cartella di autorizzazione.

   ```csharp
   public class CustomLoginCredentials : ServiceClientCredentials
   {
       private string clientId;
       private string clientSecret;
       private string resourceId;
       private string tenantId;
       private const string authenticationBase = "https://login.windows.net/{0}";
       public CustomLoginCredentials(string servicePrincipalId, string servicePrincipalSecret, string azureEnvironmentResourceId, string azureEnvironmentTenandId)
       {
           clientId = servicePrincipalId;
           clientSecret = servicePrincipalSecret;
           resourceId = azureEnvironmentResourceId;
           tenantId = azureEnvironmentTenandId;
       }
   private string AuthenticationToken { get; set; }
   ```

7.  Aggiungere il codice seguente, se si usa Azure Stack per sostituire il client del servizio di inizializzazione per l'autenticazione ad Azure Stack. Si noti che una parte del codice è già incluso in questo esempio nella cartella di autorizzazione.

   ```csharp
   public override void InitializeServiceClient<T>(ServiceClient<T> client)
   {
      var authenticationContext = new AuthenticationContext(String.Format(authenticationBase, tenantId));
      var credential = new ClientCredential(clientId, clientSecret);
      var result = authenticationContext.AcquireTokenAsync(resource: resourceId,
                clientCredential: credential).Result;
      if (result == null)
      {
          throw new InvalidOperationException("Failed to obtain the JWT token");
      }
      AuthenticationToken = result.AccessToken;
   }
   ```
 
8.  Usando Gestione pacchetti NuGet, cercare "2018-03-01-hybrid" e installare i pacchetti associati a questo profilo per i provider di risorse di calcolo, rete, archiviazione, KeyVault e servizi App.

2.  All'interno di ogni attività nel file con estensione cs, impostare i parametri necessari per lavorare con Azure Stack. L'esempio come indicato di seguito per l'attività `CreateResourceGroupTest`:

   ```csharp
   var location = Environment.GetEnvironmentVariable("AZURE_LOCATION");
   var baseUriString = Environment.GetEnvironmentVariable("AZURE_BASE_URL");
   var resourceGroupName = Environment.GetEnvironmentVariable("AZURE_RESOURCEGROUP");
   var servicePrincipalId = Environment.GetEnvironmentVariable("AZURE_CLIENT_ID");
   var servicePrincipalSecret = Environment.GetEnvironmentVariable("AZURE_CLIENT_SECRET");
   var azureResourceId = Environment.GetEnvironmentVariable("AZURE_RESOURCE_ID");
   var tenantId = Environment.GetEnvironmentVariable("AZURE_TENANT_ID");
   var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
   var credentials = new CustomLoginCredentials(servicePrincipalId, servicePrincipalSecret, azureResourceId, tenantId);
   ```

1.  Fare clic con il pulsante destro su ogni attività e selezionare **eseguire test**.

    1.  I segni di spunta verde nella finestra del riquadro laterale avvisano l'utente che ogni attività è stata creata correttamente in base i parametri specificati. Verificare che la sottoscrizione di Azure Stack per assicurarsi che le risorse sono state create correttamente.

    2.  Per altre informazioni su come eseguire unit test, vedere [eseguire unit test con Esplora Test.][]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui profili di API, vedere:

- [Gestire i profili di versione API in Azure Stack](azure-stack-version-profiles.md)
- [Versioni di API del provider di risorse supportate da profili](azure-stack-profiles-azure-resource-manager-versions.md)

  [Guida introduttiva: installazione di Git]: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git
  [Ricerca e installazione di un pacchetto]: /nuget/tools/package-manager-ui
  [Istruzioni di gestione pacchetti NuGet]: https://marketplace.visualstudio.com/items?itemName=jmrog.vscode-nuget-package-manager
  [Creare le sottoscrizioni di offerte in Azure Stack]: ../azure-stack-subscribe-plan-provision-vm.md
  [Fornire l'accesso delle applicazioni in Azure Stack]: ../azure-stack-create-service-principals.md
  [* tenant ID *]: ../azure-stack-identity-overview.md
  [* ID * sottoscrizione di]: ../azure-stack-plan-offer-quota-overview.md#subscriptions
  [* di Azure Stack resource manager endpoint *]: ../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint
  [Riepilogo dei profili di API]: ../user/azure-stack-version-profiles.md#summary-of-api-profiles
  [Progetto di test a una macchina virtuale, rete virtuale, i gruppi di risorse e account di archiviazione]: https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject
  [Usare Azure PowerShell per creare un'entità servizio con un certificato]: ../azure-stack-create-service-principals.md
  [Eseguire unit test con Esplora Test.]: /visualstudio/test/run-unit-tests-with-test-explorer?view=vs-2017
