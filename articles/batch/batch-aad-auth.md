---
title: Usare Azure Active Directory per autenticare le soluzioni del servizio Azure Batch | Microsoft Docs
description: Batch supporta Azure AD per l'autenticazione dal servizio Batch.
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 08/15/2019
ms.author: lahugh
ms.openlocfilehash: 8f95b802e51b942421bc580d9c3d5704092f5b1d
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624046"
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Autenticare le soluzioni del servizio Batch con Active Directory

Azure Batch supporta l'autenticazione con [Azure Active Directory][aad_about] (Azure ad). Azure AD è il servizio Microsoft di gestione di identità e directory basato sul cloud e multi-tenant. Azure stesso usa Azure AD per l'autenticazione i propri clienti, gli amministratori del servizio e gli utenti dell'organizzazione.

Quando si usa l'autenticazione di Azure AD con Azure Batch, è possibile eseguire l'autenticazione in uno dei due modi:

- Usando **l'autenticazione integrata** per autenticare un utente che interagisce con l'applicazione. Un'applicazione che usa l'autenticazione integrata raccoglie le credenziali dell'utente e le usa per autenticare l'accesso alle risorse di Batch.
- Usando un'**entità servizio** per autenticare un'applicazione automatica. L'entità servizio definisce i criteri e le autorizzazioni per un'applicazione al fine di rappresentare l'applicazione al momento dell'accesso alle risorse in fase di runtime.

Per altre informazioni su Azure AD, vedere [Documentazione di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="endpoints-for-authentication"></a>Endpoint per l'autenticazione

Per autenticare le applicazioni Batch con Azure AD, è necessario includere endpoint noti nel codice.

### <a name="azure-ad-endpoint"></a>Endpoint di Azure AD

L'endpoint dell'autorità di base di Azure AD è:

`https://login.microsoftonline.com/`

Per eseguire l'autenticazione con Azure AD, usare l'endpoint con l'ID tenant, ovvero l'ID directory. L'ID tenant identifica il tenant di Azure AD da usare per l'autenticazione. Per recuperare l'ID tenant, attenersi alla procedura descritta in [Ottenere l'ID tenant per Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE] 
> L'endpoint specifico per il tenant è obbligatorio quando si esegue l'autenticazione tramite un'entità servizio. 
> 
> L'endpoint specifico del tenant è facoltativo quando si esegue l'autenticazione usando l'autenticazione integrata, ma è consigliabile usarlo. Tuttavia, è anche possibile usare l'endpoint di Azure AD comune. L'endpoint comune consente di usare un'interfaccia di raccolta di credenziali generiche quando non è disponibile un tenant specifico. L'endpoint comune è `https://login.microsoftonline.com/common`.
>
>

Per ulteriori informazioni sugli endpoint Azure AD, vedere [scenari di autenticazione per Azure ad][aad_auth_scenarios].

### <a name="batch-resource-endpoint"></a>Endpoint di risorse Batch

Usare l'**endpoint delle risorse di Azure Batch** per acquisire un token per autenticare le richieste al servizio Batch:

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>Registrare l'applicazione con un tenant

Il primo passaggio nell'uso di Azure AD per eseguire l'autenticazione consiste nella registrazione dell'applicazione nel tenant di Azure AD. La registrazione dell'applicazione permette di chiamare [Active Directory Authentication Library][aad_adal] dal codice. ADAL offre un'API per eseguire l'autenticazione con Azure AD dall'applicazione. La registrazione dell'applicazione è necessaria se si prevede di usare l'autenticazione integrata o un'entità servizio.

Quando si registra l'applicazione, si danno informazioni sull'applicazione ad Azure AD. Azure AD fornisce quindi un ID applicazione (denominato anche *ID client*) da usare per associare l'applicazione ad Azure AD in fase di esecuzione. Per altre informazioni sull'ID applicazione, vedere [Oggetti applicazione e oggetti entità servizio in Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Per registrare l'applicazione batch, attenersi alla procedura descritta nella sezione [aggiunta di un'applicazione](../active-directory/develop/quickstart-register-app.md) in [integrazione di applicazioni con Azure Active Directory][aad_integrate]. Se si registra l'applicazione come applicazione nativa, è possibile specificare qualsiasi URI valido per l'**URI di reindirizzamento**. Non è necessario che sia un endpoint reale.

Al termine della registrazione dell'applicazione, verrà visualizzata l'ID applicazione:

![Registrare l'applicazione Batch in Azure AD](./media/batch-aad-auth/app-registration-data-plane.png)

Per altre informazioni sulla registrazione di un'applicazione con Azure AD, vedere [Scenari di autenticazione per Azure AD](../active-directory/develop/authentication-scenarios.md).

## <a name="get-the-tenant-id-for-your-active-directory"></a>Ottenere l'ID tenant per Active Directory

L'ID tenant identifica il tenant di Azure AD che offre servizi di autenticazione all'applicazione. Per ottenere l'ID tenant, seguire questa procedura:

1. Nel portale di Azure selezionare Active Directory.
1. Selezionare **Proprietà**.
1. Copiare il valore GUID specificato per **ID directory**. Questo valore viene chiamato anche ID tenant.

![Copiare l'ID directory](./media/batch-aad-auth/aad-directory-id.png)

## <a name="use-integrated-authentication"></a>Usare l'autenticazione integrata

Per eseguire l'autenticazione con l'autenticazione integrata, è necessario concedere le autorizzazioni per l'applicazione per connettersi all'API del servizio Batch. Questo passaggio consente all'applicazione di autenticare le chiamate all'API del servizio Batch con Azure AD.

Dopo aver registrato l'applicazione, seguire questi passaggi nel portale di Azure per concedere l'accesso al servizio Batch:

1. Nel riquadro di spostamento a sinistra del portale di Azure scegliere **Tutti i servizi**. Selezionare **registrazioni**per l'app.
1. Cercare il nome dell'applicazione nell'elenco di registrazioni di app:

    ![Cercare il nome dell'applicazione](./media/batch-aad-auth/search-app-registration.png)

1. Selezionare l'applicazione e selezionare **autorizzazioni API**.
1. Nella sezione **autorizzazioni API** selezionare **Aggiungi un'autorizzazione**.
1. In **Selezionare un'API** cercare l'API di Batch. Cercare ognuna di queste stringhe fino a trovare l'API:
    1. **Microsoft Azure Batch**
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** è l'ID dell'API Batch.
1. Dopo aver individuato l'API batch, selezionarla e selezionare **Seleziona**.
1. In **Seleziona autorizzazioni**selezionare la casella di controllo accanto a **accesso Azure batch servizio** e quindi selezionare **Aggiungi autorizzazioni**.

La sezione **autorizzazioni API** Mostra ora che l'applicazione Azure ad ha accesso sia ai Microsoft Graph che all'API del servizio batch. Le autorizzazioni vengono concesse per Microsoft Graph automaticamente alla prima registrazione dell'app con Azure AD.

![Concedere le autorizzazioni delle API](./media/batch-aad-auth/required-permissions-data-plane.png)

## <a name="use-a-service-principal"></a>Usare un’entità servizio

Per autenticare un'applicazione in esecuzione automatica, usare un'entità servizio. Dopo aver registrato l'applicazione, seguire questi passaggi nel portale di Azure per configurare un'entità servizio:

1. Richiedere un segreto per l'applicazione.
1. Assegnare il controllo degli accessi in base al ruolo all'applicazione.

### <a name="request-a-secret-for-your-application"></a>Richiedere un segreto per l'applicazione

Quando l'applicazione esegue l'autenticazione con un'entità servizio, invia sia l'ID applicazione che un segreto per Azure AD. Sarà necessario creare e copiare la chiave privata da usare dal codice.

Seguire questa procedura nel portale di Azure:

1. Nel riquadro di spostamento a sinistra del portale di Azure scegliere **Tutti i servizi**. Selezionare **registrazioni**per l'app.
1. Selezionare l'applicazione dall'elenco registrazioni per l'app.
1. Selezionare l'applicazione e quindi selezionare **certificati & segreti**. Nella sezione **segreti client** selezionare **nuovo segreto client**.
1. Per creare un segreto, immettere una descrizione per il segreto. Selezionare quindi una scadenza per il segreto di un anno, due anni o nessuna scadenza.
1. Selezionare **Aggiungi** per creare e visualizzare il segreto. Copiare il valore del segreto in un luogo sicuro, perché non sarà possibile accedervi di nuovo dopo aver lasciato la pagina.

    ![Creare una chiave privata](./media/batch-aad-auth/secret-key.png)

### <a name="assign-rbac-to-your-application"></a>Assegna RBAC all'applicazione

Per eseguire l'autenticazione con un'entità servizio, è necessario assegnare il controllo degli accessi in base al ruolo all'applicazione. A tale scopo, seguire questa procedura:

1. Nel portale di Azure passare all'account Batch usato dall'applicazione.
1. Nella sezione **Impostazioni** dell'account batch selezionare **controllo di accesso (IAM)** .
1. Selezionare la scheda **Assegnazioni di ruolo**.
1. Selezionare **Aggiungi assegnazione di ruolo**.
1. Dall'elenco a discesa **Ruolo** scegliere il ruolo *Collaboratore* o *Lettore* per l'applicazione. Per altre informazioni sui ruoli, vedere [Introduzione al controllo degli accessi in base al ruolo nel portale di Azure](../role-based-access-control/overview.md).  
1. Nel campo **Seleziona** immettere il nome dell'applicazione. Selezionare l'applicazione dall'elenco e quindi fare clic su **Salva**.

L'applicazione dovrebbe ora essere visualizzata nelle impostazioni di controllo di accesso con un ruolo con controllo degli accessi in base al ruolo assegnato.

![Assegnare un ruolo con controllo degli accessi in base al ruolo all'applicazione](./media/batch-aad-auth/app-rbac-role.png)

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Ottenere l'ID tenant per Azure Active Directory

L'ID tenant identifica il tenant di Azure AD che offre servizi di autenticazione all'applicazione. Per ottenere l'ID tenant, seguire questa procedura:

1. Nel portale di Azure selezionare Active Directory.
1. Selezionare **Proprietà**.
1. Copiare il valore GUID specificato per **ID directory**. Questo valore viene chiamato anche ID tenant.

![Copiare l'ID directory](./media/batch-aad-auth/aad-directory-id.png)

## <a name="code-examples"></a>Esempi di codice

Gli esempi di codice in questa sezione illustrano come eseguire l'autenticazione con Azure AD mediante l'autenticazione integrata e con un'entità servizio. La maggior parte di questi esempi di codice usa .NET, ma i concetti sono simili anche per altri linguaggi.

> [!NOTE]
> Un token di autenticazione di Azure AD scade dopo un'ora. Quando si usa un oggetto **BatchClient** di lunga durata, è consigliabile recuperare un token da ADAL a ogni richiesta per assicurarsi di avere sempre un token valido. 
>
>
> A tale scopo, in .NET scrivere un metodo che recuperi il token da Azure AD e passi tale metodo a un oggetto **BatchTokenCredentials** come delegato. Il metodo delegato verrà chiamato a ogni richiesta al servizio Batch per garantire che venga fornito un token valido. Per impostazione predefinita, ADAL memorizza i token nella cache, quindi un nuovo token viene recuperato da Azure AD solo quando necessario. Per ulteriori informazioni sui token in Azure AD, vedere [scenari di autenticazione per Azure ad][aad_auth_scenarios].
>
>

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>Esempio di codice: uso dell'autenticazione integrata di Azure AD con .NET di Batch

Per eseguire l'autenticazione con l'autenticazione integrata di Batch .NET, fare riferimento al pacchetto [Azure Batch .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch/) e al pacchetto [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

Includere le istruzioni `using` seguenti nel codice:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Fare riferimento all'endpoint di Azure AD nel codice, che include l'ID tenant. Per recuperare l'ID tenant, attenersi alla procedura descritta in [Ottenere l'ID tenant per Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Riferimento all'endpoint delle risorse per il servizio Batch:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Riferimento all'account Batch:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Specificare l'ID dell'applicazione (ID client). L'ID dell'applicazione è disponibile nella registrazione dell'app nel portale di Azure:

```csharp
private const string ClientId = "<application-id>";
```

Copiare anche l'URI di reindirizzamento specificato, se l'applicazione è stata registrata come applicazione nativa. L'URI di reindirizzamento specificato nel codice deve corrispondere a quello specificato quando è stata registrata l'applicazione:

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Scrivere un metodo di callback per acquisire il token di autenticazione da Azure AD. Il metodo di callback **GetAuthenticationTokenAsync** illustrato qui chiama ADAL per eseguire l'autenticazione a un utente che interagisce con l'applicazione. Il metodo **AcquireTokenAsync** indicato da ADAL richiede all'utente le credenziali e l'applicazione procede dopo che sono state specificate, a meno che queste credenziali non siano già state memorizzate nella cache:

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    var authContext = new AuthenticationContext(AuthorityUri);

    // Acquire the authentication token from Azure AD.
    var authResult = await authContext.AcquireTokenAsync(BatchResourceUri, 
                                                        ClientId, 
                                                        new Uri(RedirectUri), 
                                                        new PlatformParameters(PromptBehavior.Auto));

    return authResult.AccessToken;
}
```

Costruire un oggetto **BatchTokenCredentials** che accetta il delegato come parametro. Usare tali credenziali per aprire un oggetto **BatchClient**. È possibile usare l'oggetto **BatchClient** per operazioni successive sul servizio Batch:

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>Esempio di codice: uso di un'entità servizio Azure AD con Batch .NET

Per eseguire l'autenticazione con un'entità servizio di Batch .NET, fare riferimento al pacchetto [Azure Batch .NET](https://www.nuget.org/packages/Azure.Batch/) e al pacchetto [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

Includere le istruzioni `using` seguenti nel codice:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Fare riferimento all'endpoint di Azure AD nel codice, che include l'ID tenant. Quando si usa un'entità servizio, è necessario indicare un endpoint specifico del tenant. Per recuperare l'ID tenant, attenersi alla procedura descritta in [Ottenere l'ID tenant per Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Riferimento all'endpoint delle risorse per il servizio Batch:  

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Riferimento all'account Batch:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Specificare l'ID dell'applicazione (ID client). L'ID dell'applicazione è disponibile nella registrazione dell'app nel portale di Azure:

```csharp
private const string ClientId = "<application-id>";
```

Specificare la chiave privata copiata dal portale di Azure:

```csharp
private const string ClientKey = "<secret-key>";
```

Scrivere un metodo di callback per acquisire il token di autenticazione da Azure AD. Il metodo di callback **GetAuthenticationTokenAsync** illustrato di seguito chiama ADAL per l'autenticazione automatica:

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

Costruire un oggetto **BatchTokenCredentials** che accetta il delegato come parametro. Usare tali credenziali per aprire un oggetto **BatchClient**. Usare quindi l'oggetto **BatchClient** per operazioni successive sul servizio Batch:

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-python"></a>Esempio di codice: uso di un'entità servizio Azure AD con Batch Python

Per eseguire l'autenticazione con un'entità servizio di Batch Python, installare e fare riferimento ai moduli [azure-batch](https://pypi.org/project/azure-batch/) e [azure-common](https://pypi.org/project/azure-common/).

```python
from azure.batch import BatchServiceClient
from azure.common.credentials import ServicePrincipalCredentials
```

Quando si usa un'entità servizio, è necessario specificare l'ID tenant. Per recuperare l'ID tenant, attenersi alla procedura descritta in [Ottenere l'ID tenant per Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```python
TENANT_ID = "<tenant-id>"
```

Riferimento all'endpoint delle risorse per il servizio Batch:  

```python
RESOURCE = "https://batch.core.windows.net/"
```

Riferimento all'account Batch:

```python
BATCH_ACCOUNT_URL = "https://myaccount.mylocation.batch.azure.com"
```

Specificare l'ID dell'applicazione (ID client). L'ID dell'applicazione è disponibile nella registrazione dell'app nel portale di Azure:

```python
CLIENT_ID = "<application-id>"
```

Specificare la chiave privata copiata dal portale di Azure:

```python
SECRET = "<secret-key>"
```

Creare un oggetto **ServicePrincipalCredentials**:

```python
credentials = ServicePrincipalCredentials(
    client_id=CLIENT_ID,
    secret=SECRET,
    tenant=TENANT_ID,
    resource=RESOURCE
)
```

Usare le credenziali dell'entità servizio per aprire un oggetto **BatchServiceClient**. Usare quindi l'oggetto **BatchServiceClient** per operazioni successive sul servizio Batch.

```python
    batch_client = BatchServiceClient(
    credentials,
    base_url=BATCH_ACCOUNT_URL
)
```

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su Azure AD, vedere [Documentazione di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/). Esempi dettagliati dell'uso di ADAL sono disponibili nella libreria degli [esempi di codice per Azure](https://azure.microsoft.com/resources/samples/?service=active-directory).

- Per altre informazioni sull'entità servizio, vedere [Oggetti applicazione e oggetti entità servizio in Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). Per creare un'entità servizio tramite il portale di Azure, vedere [Usare il portale per creare un'applicazione Azure Active Directory e un'entità servizio che possano accedere alle risorse](../active-directory/develop/howto-create-service-principal-portal.md). È anche possibile creare un'entità servizio con PowerShell o con l'interfaccia della riga di comando di Azure.

- Per eseguire l'autenticazione di applicazioni di gestione batch con Azure AD, vedere [Authenticate Batch Management solutions with Active Directory](batch-aad-auth-management.md) (Autenticare le soluzioni di gestione batch con Active Directoy).

- Per un esempio di Python sulla creazione di un client di Batch autenticato tramite token di Azure AD, vedere l'esempio [Deploying Azure Batch Custom Image with a Python Script](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md) (Distribuzione di un'immagine personalizzata di Azure Batch con uno script Python).

[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Informazioni su Azure Active Directory"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Scenari di autenticazione per Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrazione di applicazioni con Azure Active Directory"
[azure_portal]: https://portal.azure.com
