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
ms.openlocfilehash: 20e96ad7a99fdb8c90f3b7990965d7225aef8be0
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555014"
---
# <a name="use-api-version-profiles-with-net-in-azure-stack"></a>Usare i profili delle versioni API con .NET in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

.NET SDK per Azure Stack Resource Manager fornisce strumenti che consentono di compilare e gestire l'infrastruttura. I provider di risorse nel SDK includono calcolo, rete, archiviazione, servizi app, e [KeyVault](../../key-vault/key-vault-whatis.md). .NET SDK include i pacchetti NuGet 14. Questi pacchetti devono essere scaricati alla soluzione di progetto ogni volta che incorpora le informazioni sul profilo. Tuttavia, è possibile scaricare in modo specifico quali provider di risorse si userà per il 2018-03-01-hybrid o 2017-03-09-profile per ottimizzare la memoria per l'applicazione. Ogni pacchetto è costituito da un provider di risorse, la rispettiva versione dell'API e il profilo di API a cui appartiene. I profili di API in .NET SDK consentono lo sviluppo cloud ibride grazie alla possibilità di passare tra le risorse di Azure globali e le risorse in Azure Stack.

## <a name="net-and-api-version-profiles"></a>Profili delle versioni di .NET e API

Un profilo di API è una combinazione di versioni di API e i provider di risorse. È possibile usare un profilo di API per ottenere la versione più recente e più stabile di ogni tipo di risorsa in un pacchetto di provider di risorse.

-   Per usare le versioni più recenti di tutti i servizi, usare il **più recente** profilo dei pacchetti. Questo profilo è parte di **partire** pacchetto NuGet.

-   Per usare i servizi compatibili con Azure Stack, usare il **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg** oppure **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg** pacchetti.

    -   Sono disponibili due pacchetti per ogni provider di risorse per ogni profilo.

    -   Verificare che il **ResourceProvider** parte del pacchetto NuGet precedente viene modificato per il provider corretto.

-   Per usare la versione API più recente di un servizio, usare il **più recente** profilo del pacchetto NuGet specifico. Ad esempio, se si desidera utilizzare il **più recente-API** versione del servizio di calcolo da solo, utilizzare il **più recente** profilo del **calcolo** pacchetto. Il **più recente** profilo fa parte delle **partire** pacchetto NuGet.

-   Per usare versioni API specifiche per un tipo di risorsa in un provider di risorse specifico, usare le versioni API specifiche definite all'interno del pacchetto.

È possibile combinare tutte le opzioni nella stessa applicazione.

## <a name="install-the-azure-net-sdk"></a>Installare Azure .NET SDK

1.  Installare Git. Per istruzioni, vedere [Guida introduttiva: installazione di Git][].

2.  Per installare i pacchetti NuGet corretti, vedere [ricerca e installazione di un pacchetto][].

3.  I pacchetti che devono essere installati dipende dalla versione del profilo da utilizzare. I nomi dei pacchetti per le versioni di profili sono:

    1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg**

    2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg**

4.  Per installare i pacchetti NuGet corretti per Visual Studio Code, vedere il collegamento seguente per scaricare il [istruzioni di gestione pacchetti NuGet][].

5.  Se non è disponibile, creare una sottoscrizione e salvare l'ID sottoscrizione da usare in un secondo momento. Per istruzioni su come creare una sottoscrizione, vedere [creare le sottoscrizioni di offerte in Azure Stack][].

6.  Creare un'entità servizio e salvare l'ID Client e il segreto Client. Per istruzioni su come creare un'entità servizio per Azure Stack, vedere [Fornire l'accesso delle applicazioni in Azure Stack][]. L'ID Client è noto anche come ID dell'applicazione durante la creazione di un servizio dell'entità.

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
| Località                  | POSIZIONE_RISORSA     | Percorso per Azure Stack.

Per trovare l'ID Tenant per Azure Stack, seguire le istruzioni disponibili [qui](../azure-stack-csp-ref-operations.md). Per impostare le variabili di ambiente, procedere come segue:

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

- Il **ResourceManagerUrl** in sistemi integrati è: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` Per recuperare i metadati richiesti: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

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

1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg**: Profilo più recente creato per Azure Stack. Usare questo profilo per i servizi di essere più compatibile con Azure Stack, purché si è in corrispondenza del timestamp 1808 o altri.

2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg**: Se si usa un timestamp minore rispetto alla build 1808, usare questo profilo.

3.  **Più recente**: Profilo costituito le versioni più recenti di tutti i servizi. Usare le versioni più recenti di tutti i servizi. Questo profilo è parte di **partire** pacchetto NuGet.

Per altre informazioni sui profili di Azure Stack e l'API, vedere una [Riepilogo dei profili di API][].

## <a name="azure-net-sdk-api-profile-usage"></a>Azure .NET SDK API profilare l'utilizzo

Il codice seguente deve essere utilizzato per creare un client di gestione risorse. Codice simile può essere usato per creare un'istanza di altri provider di risorse (ad esempio calcolo, rete e archiviazione) ai client. 

```csharp
var client = new ResourceManagementClient(armEndpoint, credentials)
{
    SubscriptionId = subscriptionId
};
```

Il `credentials` parametro nel codice precedente è necessario per creare un'istanza di un client. Il codice seguente genera un token di autenticazione per l'ID tenant e l'entità servizio.

```csharp
var azureStackSettings = getActiveDirectoryServiceSettings(armEndpoint);
var credentials = ApplicationTokenProvider.LoginSilentAsync(tenantId, servicePrincipalId, servicePrincipalSecret, azureStackSettings).GetAwaiter().GetResult();
```
Il `getActiveDirectoryServiceSettings` chiamata nel codice recupera gli endpoint di Azure Stack dall'endpoint dei metadati. Le variabili di ambiente dalla chiamata effettuata indicato: 

```csharp
public static ActiveDirectoryServiceSettings getActiveDirectoryServiceSettings(string armEndpoint)
{
    var settings = new ActiveDirectoryServiceSettings();
    try
    {
        var request = (HttpWebRequest)HttpWebRequest.Create(string.Format("{0}/metadata/endpoints?api-version=1.0", armEndpoint));
        request.Method = "GET";
        request.UserAgent = ComponentName;
        request.Accept = "application/xml";
        using (HttpWebResponse response = (HttpWebResponse)request.GetResponse())
        {
            using (StreamReader sr = new StreamReader(response.GetResponseStream()))
            {
                var rawResponse = sr.ReadToEnd();
                var deserialized = JObject.Parse(rawResponse);
                var authenticationObj = deserialized.GetValue("authentication").Value<JObject>();
                var loginEndpoint = authenticationObj.GetValue("loginEndpoint").Value<string>();
                var audiencesObj = authenticationObj.GetValue("audiences").Value<JArray>();
                settings.AuthenticationEndpoint = new Uri(loginEndpoint);
                settings.TokenAudience = new Uri(audiencesObj[0].Value<string>());
                settings.ValidateAuthority = loginEndpoint.TrimEnd('/').EndsWith("/adfs", StringComparison.OrdinalIgnoreCase) ? false : true;
            }
        }
    }
    catch (Exception ex)
    {
        Console.WriteLine(String.Format("Could not get AD service settings. Exception: {0}", ex.Message));
    }
    return settings;
}
```
Ciò consentirà di usare i pacchetti NuGet dell'API del profilo per distribuire l'applicazione correttamente in Azure Stack.

## <a name="samples-using-api-profiles"></a>Esempi che usano i profili delle API

Gli esempi seguenti è utilizzabile come riferimento per la creazione di soluzioni con i profili .NET e API di Azure Stack.
- [Gestire gruppi di risorse](https://github.com/Azure-Samples/hybrid-resources-dotnet-manage-resource-group)
- [Gestire gli account di archiviazione](https://github.com/Azure-Samples/hybird-storage-dotnet-manage-storage-accounts)
- [Gestire una macchina virtuale](https://github.com/Azure-Samples/hybrid-compute-dotnet-manage-vm)

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
  [* il Azure endpoint Stack di Resource Manager *]: ../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint
  [Riepilogo dei profili di API]: ../user/azure-stack-version-profiles.md#summary-of-api-profiles
  [Test Project to Virtual Machine, vNet, resource groups, and storage account]: https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject
  [Use Azure PowerShell to create a service principal with a certificate]: ../azure-stack-create-service-principals.md
  [Run unit tests with Test Explorer.]: /visualstudio/test/run-unit-tests-with-test-explorer?view=vs-2017
