---
title: Usare Azure Active Directory per autenticare le soluzioni del servizio Azure Batch | Microsoft Docs
description: Batch supporta Azure AD per l'autenticazione dal servizio Batch.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 09/28/2017
ms.author: danlep
ms.openlocfilehash: 8ad9c3a779ca52140a78ae905e4825f3144976aa
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Autenticare le soluzioni del servizio Batch con Active Directory

Azure Batch supporta l'autenticazione con [Azure Active Directory][aad_about], ovvero Azure AD. Azure AD è il servizio Microsoft di gestione di identità e directory basato sul cloud e multi-tenant. Azure stesso usa Azure AD per l'autenticazione i propri clienti, gli amministratori del servizio e gli utenti dell'organizzazione.

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

Per altre informazioni sugli endpoint di Azure AD, vedere [Scenari di autenticazione per Azure AD][aad_auth_scenarios].

### <a name="batch-resource-endpoint"></a>Endpoint di risorse Batch

Usare l'**endpoint delle risorse di Azure Batch** per acquisire un token per autenticare le richieste al servizio Batch:

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>Registrare l'applicazione con un tenant

Il primo passaggio nell'uso di Azure AD per eseguire l'autenticazione consiste nella registrazione dell'applicazione nel tenant di Azure AD. La registrazione dell'applicazione consente di chiamare l'[Active Directory Authentication Library][aad_adal], ovvero ADAL, di Azure dal codice. ADAL offre un'API per eseguire l'autenticazione con Azure AD dall'applicazione. La registrazione dell'applicazione è necessaria se si prevede di usare l'autenticazione integrata o un'entità servizio.

Quando si registra l'applicazione, si danno informazioni sull'applicazione ad Azure AD. Azure AD fornisce quindi un ID applicazione che viene usato per associare l'applicazione ad Azure AD in fase di esecuzione. Per altre informazioni sull'ID applicazione, vedere [Oggetti applicazione e oggetti entità servizio in Azure Active Directory](../active-directory/develop/active-directory-application-objects.md).

Per registrare l'applicazione Batch, seguire la procedura descritta nella sezione [Aggiunta di un'applicazione](../active-directory/develop/active-directory-integrating-applications.md#adding-an-application) dell'articolo [Integrazione di applicazioni con Azure Active Directory][aad_integrate]. Se si registra l'applicazione come applicazione nativa, è possibile specificare qualsiasi URI valido per l'**URI di reindirizzamento**. Non è necessario che sia un endpoint reale.

Al termine della registrazione dell'applicazione, verrà visualizzata l'ID applicazione:

![Registrare l'applicazione Batch in Azure AD](./media/batch-aad-auth/app-registration-data-plane.png)

Per altre informazioni sulla registrazione di un'applicazione con Azure AD, vedere [Scenari di autenticazione per Azure AD](../active-directory/develop/active-directory-authentication-scenarios.md).

## <a name="get-the-tenant-id-for-your-active-directory"></a>Ottenere l'ID tenant per Active Directory

L'ID tenant identifica il tenant di Azure AD che offre servizi di autenticazione all'applicazione. Per ottenere l'ID tenant, seguire questa procedura:

1. Nel portale di Azure selezionare Active Directory.
2. Fare clic su **Proprietà**.
3. Copiare il valore GUID indicato per l'ID directory. Questo valore viene chiamato anche ID tenant.

![Copiare l'ID directory](./media/batch-aad-auth/aad-directory-id.png)


## <a name="use-integrated-authentication"></a>Usare l'autenticazione integrata

Per eseguire l'autenticazione con l'autenticazione integrata, è necessario concedere le autorizzazioni per l'applicazione per connettersi all'API del servizio Batch. Questo passaggio consente all'applicazione di autenticare le chiamate all'API del servizio Batch con Azure AD.

Dopo aver [registrato l'applicazione](#register-your-application-with-an-azure-ad-tenant), seguire questi passaggi nel portale di Azure per concedere l'accesso al servizio Batch:

1. Nel riquadro di spostamento a sinistra del portale di Azure scegliere **Tutti i servizi**. Fare clic su **Registrazioni per l'app**.
2. Cercare il nome dell'applicazione nell'elenco di registrazioni di app:

    ![Cercare il nome dell'applicazione](./media/batch-aad-auth/search-app-registration.png)

3. Aprire il pannello **Impostazioni** per l'applicazione. Nella sezione **Accesso all'API** selezionare **Autorizzazioni necessarie**.
4. Nel pannello **Autorizzazioni necessarie** fare clic sul pulsante **Aggiungi**.
5. Nel passaggio 1 cercare l'API Batch. Cercare ognuna di queste stringhe fino a trovare l'API:
    1. **MicrosoftAzureBatch**.
    2. **Microsoft Azure Batch**. I tenant di Azure AD più recenti potrebbero usare questo nome.
    3. **ddbf3205-c6bd-46ae-8127-60eb93363864** è l'ID dell'API Batch. 
6. Dopo aver trovato l'API Batch, selezionarla e fare clic sul pulsante **Seleziona**.
6. Nel passaggio 2 selezionare la casella di controllo accanto ad **Access Azure Batch Service** (Accedi al servizio Azure Batch) e fare clic sul pulsante **Seleziona**.
7. Fare clic sul pulsante **Fine**.

Il pannello **Autorizzazioni necessarie** mostra ora che l'applicazione Azure AD concede l'accesso alle API sia di ADAL che del servizio Batch. Le autorizzazioni vengono automaticamente concesse ad ADAL quando si registra per la prima volta l'app in Azure AD.

![Concedere le autorizzazioni delle API](./media/batch-aad-auth/required-permissions-data-plane.png)

## <a name="use-a-service-principal"></a>Usare un’entità servizio 

Per autenticare un'applicazione in esecuzione automatica, usare un'entità servizio. Dopo aver registrato l'applicazione, seguire questi passaggi nel portale di Azure per configurare un'entità servizio:

1. Richiedere una chiave privata per l'applicazione.
2. Assegnare un ruolo con controllo degli accessi in base al ruolo all'applicazione.

### <a name="request-a-secret-key-for-your-application"></a>Richiedere una chiave privata per l'applicazione

Quando l'applicazione autentica con un'entità servizio, invia l'ID dell'applicazione e una chiave privata ad Azure AD. Sarà necessario creare e copiare la chiave privata da usare dal codice.

Seguire questa procedura nel portale di Azure:

1. Nel riquadro di spostamento a sinistra del portale di Azure scegliere **Tutti i servizi**. Fare clic su **Registrazioni per l'app**.
2. Cercare il nome dell'applicazione nell'elenco di registrazioni dell'app.
3. Visualizzare il pannello **Impostazioni**. Nella sezione **Accesso all'API** selezionare **Chiavi**.
4. Per creare una chiave, immettere una descrizione per la chiave. Quindi selezionare la durata della chiave di uno o due anni. 
5. Fare clic sul pulsante **Salva** per creare e visualizzare la chiave. Copiare il valore della chiave in una posizione sicura, poiché non sarà possibile accedervi nuovamente dopo aver chiuso il pannello. 

    ![Creare una chiave privata](./media/batch-aad-auth/secret-key.png)

### <a name="assign-an-rbac-role-to-your-application"></a>Assegnare un ruolo con controllo degli accessi in base al ruolo all'applicazione

Per eseguire l'autenticazione con un'entità servizio, è necessario assegnare il ruolo con controllo degli accessi in base al ruolo all'applicazione. A tale scopo, seguire questa procedura:

1. Nel portale di Azure passare all'account Batch usato dall'applicazione.
2. Nel pannello **Impostazioni** per l'account Batch selezionare **Controllo di accesso (IAM)**.
3. Fare clic su **Add** . 
4. Dall'elenco a discesa **Ruolo** scegliere il ruolo _Collaboratore_ o _Lettore_ per l'applicazione. Per altre informazioni sui ruoli, vedere [Introduzione al controllo degli accessi in base al ruolo nel portale di Azure](../active-directory/role-based-access-control-what-is.md).  
5. Nel campo **Seleziona** immettere il nome dell'applicazione. Selezionare l'applicazione dall'elenco e fare clic su **Salva**.

L'applicazione dovrebbe ora essere visualizzata nelle impostazioni di controllo di accesso con un ruolo con controllo degli accessi in base al ruolo assegnato. 

![Assegnare un ruolo con controllo degli accessi in base al ruolo all'applicazione](./media/batch-aad-auth/app-rbac-role.png)

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Ottenere l'ID tenant per Azure Active Directory

L'ID tenant identifica il tenant di Azure AD che offre servizi di autenticazione all'applicazione. Per ottenere l'ID tenant, seguire questa procedura:

1. Nel portale di Azure selezionare Active Directory.
2. Fare clic su **Proprietà**.
3. Copiare il valore GUID indicato per l'ID directory. Questo valore viene chiamato anche ID tenant.

![Copiare l'ID directory](./media/batch-aad-auth/aad-directory-id.png)


## <a name="code-examples"></a>Esempi di codice

Gli esempi di codice in questa sezione illustrano come eseguire l'autenticazione con Azure AD mediante l'autenticazione integrata e con un'entità servizio. Questi esempi di codice usano .NET, ma i concetti sono simili per le altre lingue.

> [!NOTE]
> Un token di autenticazione di Azure AD scade dopo un'ora. Quando si usa un oggetto **BatchClient** di lunga durata, è consigliabile recuperare un token da ADAL a ogni richiesta per assicurarsi di avere sempre un token valido. 
>
>
> A tale scopo, in .NET scrivere un metodo che recuperi il token da Azure AD e passi tale metodo a un oggetto **BatchTokenCredentials** come delegato. Il metodo delegato verrà chiamato a ogni richiesta al servizio Batch per garantire che venga fornito un token valido. Per impostazione predefinita, ADAL memorizza i token nella cache, quindi un nuovo token viene recuperato da Azure AD solo quando necessario. Per altre informazioni sui token in Azure AD, vedere [Scenari di autenticazione per Azure AD][aad_auth_scenarios].
>
>

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>Esempio di codice: uso dell'autenticazione integrata di Azure AD con .NET di Batch

Per eseguire l'autenticazione con l'autenticazione integrata di Batch .NET, fare riferimento al pacchetto [Azure Batch .NET](https://www.nuget.org/packages/Azure.Batch/) e al pacchetto [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

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

Copiare anche l'URI di reindirizzamento specificato durante il processo di registrazione. L'URI di reindirizzamento specificato nel codice deve corrispondere a quello specificato quando è stata registrata l'applicazione:

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

Costruire un oggetto **BatchTokenCredentials** che accetta il delegato come parametro. Usare tali credenziali per aprire un oggetto **BatchClient**. È quindi possibile usare l'oggetto **BatchClient** per operazioni successive sul servizio Batch:

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

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure AD, vedere [Documentazione di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/). Esempi dettagliati dell'uso di ADAL sono disponibili nella libreria degli [esempi di codice per Azure](https://azure.microsoft.com/resources/samples/?service=active-directory).

Per altre informazioni sull'entità servizio, vedere [Oggetti applicazione e oggetti entità servizio in Azure Active Directory](../active-directory/develop/active-directory-application-objects.md). Per creare un'entità servizio tramite il portale di Azure, vedere [Usare il portale per creare un'applicazione Azure Active Directory e un'entità servizio che possano accedere alle risorse](../resource-group-create-service-principal-portal.md). È anche possibile creare un'entità servizio con PowerShell o con l'interfaccia della riga di comando di Azure.

Per eseguire l'autenticazione di applicazioni di gestione batch con Azure AD, vedere [Authenticate Batch Management solutions with Active Directory](batch-aad-auth-management.md) (Autenticare le soluzioni di gestione batch con Active Directoy).

Per un esempio Python di come creare un client Batch autenticato con un token di Azure AD, vedere l'esempio di [autenticazione di Azure Active Directory](http://azure-sdk-for-python.readthedocs.io/en/latest/batch.html#azure-active-directory-authentication) nella documentazione di Azure SDK per Python.

[aad_about]: ../active-directory/active-directory-whatis.md "Informazioni su Azure Active Directory"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Scenari di autenticazione per Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrazione di applicazioni con Azure Active Directory"
[azure_portal]: http://portal.azure.com
