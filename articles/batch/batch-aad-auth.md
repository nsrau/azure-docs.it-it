---
title: Usare Azure Active Directory per l&quot;autenticazione da Azure Batch | Microsoft Docs
description: Batch supporta Azure AD per l&quot;autenticazione dal servizio e dal provider di risorse Batch.
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 03/16/2017
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 28665625cde9c7b164986f7b2d9c845136543aba
ms.lasthandoff: 03/18/2017

---

# <a name="authenticate-from-batch-solutions-with-active-directory"></a>Eseguire l'autenticazione da soluzioni Batch con Active Directory

Azure Batch supporta l'autenticazione con [Azure Active Directory][aad_about] (Azure AD) per il servizio Batch e il servizio di gestione di Batch. Azure AD è il servizio Microsoft di gestione di identità e directory basato sul cloud e multi-tenant. Azure stesso usa Azure AD per l'autenticazione dei relativi clienti, amministratori del servizio e utenti dell'organizzazione.

Questo articolo illustra l'uso di Azure AD per eseguire l'autenticazione dalle applicazioni che usano la libreria .NET per la gestione di Batch o la libreria Batch .NET. Nel contesto delle API Batch .NET, viene descritto come usare Azure AD per eseguire l'autenticazione di un amministratore o un coamministratore della sottoscrizione, usando l'autenticazione integrata. L'utente autenticato potrà quindi inviare richieste ad Azure Batch.

È anche possibile usare Azure AD per autenticare l'accesso a un'applicazione eseguita in modo automatico. Questo articolo è incentrato sull'autenticazione integrata di Azure AD e contiene riferimenti ad altre risorse per informazioni sull'autenticazione delle applicazioni automatiche.

## <a name="use-azure-ad-with-batch-management-solutions"></a>Usare Azure AD con soluzioni di gestione di Batch

La libreria .NET per la gestione di Batch espone tipi per l'uso di account Batch, chiavi dell'account, applicazioni e pacchetti dell'applicazione. La libreria .NET per la gestione di Batch è un client del provider di risorse di Azure e viene usata in combinazione con [Azure Resource Manager][resman_overview] per gestire tali risorse a livello di codice. 

Azure AD è necessario per autenticare le richieste effettuate tramite qualsiasi client del provider di risorse di Azure, inclusa la libreria .NET per la gestione di Batch, e tramite [Azure Resource Manager][resman_overview].

In questa sezione verrà usato il progetto di esempio [AccountManagement][acct_mgmt_sample], disponibile in GitHub, per illustrare l'uso di Azure AD con la libreria .NET per la gestione di Batch. L'esempio AccountManagement è un'applicazione console che accede a una sottoscrizione a livello di codice, crea un gruppo di risorse e un nuovo account Batch ed esegue alcune operazioni sull'account. 

Per altre informazioni sull'uso della libreria .NET per la gestione di Batch e dell'esempio AccountManagement, vedere [Gestire le quote e gli account Batch con la libreria client di gestione Batch per .NET](batch-management-dotnet.md).

### <a name="register-your-application-with-azure-ad"></a>Registrare l'applicazione in Azure AD

[Azure Active Directory Authentication Library][aad_adal] (ADAL) offre un'interfaccia programmatica per Azure AD da usare nelle applicazioni. Per chiamare ADAL da un'applicazione, è necessario registrare l'applicazione in un tenant di Azure AD. Quando si registra l'applicazione, si specificano in Azure AD le informazioni relative all'applicazione, incluso un nome per l'applicazione nel tenant di Azure AD. Azure AD fornisce quindi un ID applicazione che viene usato per associare l'applicazione ad Azure AD in fase di esecuzione. Per altre informazioni sull'ID applicazione, vedere [Oggetti applicazione e oggetti entità servizio in Azure Active Directory](../active-directory/develop/active-directory-application-objects.md).

Per registrare l'applicazione di esempio AccountManagement, seguire la procedura descritta nella sezione [Aggiunta di un'applicazione](../active-directory/develop/active-directory-integrating-applications.md#adding-an-application) dell'articolo [Integrazione di applicazioni con Azure Active Directory][aad_integrate]. Specificare **Nativa** come tipo di applicazione. In **URI di reindirizzamento** è possibile specificare qualsiasi URI valido, ad esempio `http://myaccountmanagementsample`, perché non è necessario che sia un endpoint reale:

![](./media/batch-aad-auth/app-registration-management-plane.png)

Al termine del processo di registrazione, verranno elencati l'ID applicazione e l'ID oggetto (entità servizio) dell'applicazione.  

![](./media/batch-aad-auth/app-registration-client-id.png)

### <a name="update-your-code-to-reference-your-application-id"></a>Aggiornare il codice per fare riferimento all'ID applicazione 

L'ID applicazione (detto anche ID client) viene usato dall'applicazione client per accedere ad Azure AD in fase di esecuzione. Dopo aver registrato l'applicazione nel portale di Azure, aggiornare il codice per usare l'ID applicazione fornito da Azure AD per l'applicazione registrata. Nell'applicazione di esempio AccountManagement, copiare l'ID applicazione dal portale di Azure alla costante appropriata:

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Azure AD Graph API. For information
// about registering an application in Azure Active Directory, please see "Adding an Application" here:
// https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
private const string ClientId = "<application-id>";
```
Copiare anche l'URI di reindirizzamento specificato durante il processo di registrazione.

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

### <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>Concedere all'API di Azure Resource Manager l'accesso all'applicazione

Successivamente, sarà necessario delegare l'accesso all'applicazione all'API di Azure Resource Manager. L'identificatore di Azure AD per l'API di Resource Manager è **API di gestione del servizio Microsoft Azure**.

Seguire questa procedura nel portale di Azure:

1. Nel riquadro di spostamento sinistro del portale di Azure scegliere **Altri servizi** e fare clic su **Registrazioni per l'app** e quindi su **Aggiungi**.
2. Cercare il nome dell'applicazione nell'elenco di registrazioni di app:

    ![Cercare il nome dell'applicazione](./media/batch-aad-auth/search-app-registration.png)

3. Visualizzare il pannello **Impostazioni**. Nella sezione **Accesso all'API** selezionare **Autorizzazioni necessarie**.
4. Fare clic su **Aggiungi** per aggiungere una nuova autorizzazione necessaria. 
5. Nel passaggio 1 immettere **API di gestione del servizio Microsoft Azure**, selezionare tale API nell'elenco dei risultati e fare clic sul pulsante **Seleziona**.
6. Nel passaggio 2 selezionare la casella di controllo accanto ad **Access Azure classic deployment model as organization users** (Accedi a modello di distribuzione classica di Azure come utente dell'organizzazione) e fare clic sul pulsante **Seleziona**.
7. Fare clic sul pulsante **Fine**.

Il pannello **Autorizzazioni necessarie** mostra ora che le autorizzazioni per l'applicazione sono concesse sia ad ADAL che alle API di Resource Manager. Le autorizzazioni vengono concesse ad ADAL per impostazione predefinita quando si registra per la prima volta l'app in Azure AD.

![Delegare le autorizzazioni all'API di Azure Resource Manager](./media/batch-aad-auth/required-permissions-management-plane.png)


### <a name="acquire-an-azure-ad-authentication-token"></a>Acquisire un token di autenticazione di Azure AD

L'applicazione di esempio AccountManagement definisce costanti che forniscono l'endpoint per Azure AD e per Azure Resource Manager. L'applicazione di esempio usa le costanti per eseguire query su Azure AD per le informazioni sulla sottoscrizione. Lasciare invariate queste costanti:

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure service management resource 
private const string ResourceUri = "https://management.core.windows.net/";
```

Dopo che l'esempio AccountManagement è stato registrato nel tenant di Azure AD e sono stati specificati i valori necessari nel codice sorgente di esempio, l'esempio è pronto per l'autenticazione con Azure AD. Quando si esegue l'esempio, ADAL tenta di acquisire un token di autenticazione. A questo punto, richiede all'utente credenziali Microsoft: 

```csharp
// Obtain an access token using the "common" AAD resource. This allows the application
// to query AAD for information that lies outside the application's tenant (such as for
// querying subscription information in your Azure account).
AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
AuthenticationResult authResult = authContext.AcquireToken(ResourceUri,
                                                        ClientId,
                                                        new Uri(RedirectUri),
                                                        PromptBehavior.Auto);
```

Dopo che sono state specificate le credenziali, l'applicazione di esempio può procedere con l'invio di richieste autenticate al servizio di gestione di Batch. 

## <a name="use-azure-ad-with-batch-service-solutions"></a>Usare Azure AD con soluzioni del servizio Batch

La libreria Batch .NET fornisce tipi per la creazione di flussi di lavoro a elaborazione parallela con il servizio Batch. Il servizio Batch supporta sia l'autenticazione con [chiave condivisa](https://docs.microsoft.com/rest/api/batchservice/authenticate-requests-to-the-azure-batch-service) che l'autenticazione tramite Azure AD. Questa sezione illustra l'autenticazione tramite Azure AD.

>[!NOTE]
>Quando si crea un account Batch, è possibile specificare se i pool devono essere allocati in una sottoscrizione gestita da Batch o in una sottoscrizione utente. Se i pool vengono allocati dall'account in una sottoscrizione utente, per l'autenticazione delle richieste alle risorse in tale account è necessario usare Azure AD.
>
>

L'autenticazione di applicazioni Batch .NET tramite Azure AD è simile all'autenticazione delle applicazioni .NET per la gestione di Batch. Esistono tuttavia alcune differenze, descritte in questa sezione.

### <a name="batch-service-endpoints"></a>Endpoint del servizio Batch

Gli endpoint del servizio Batch sono diversi da quelli usati con .NET per la gestione di Batch.

L'endpoint di Azure AD per il servizio Batch è:

`https://login.microsoftonline.com/common`

L'endpoint delle risorse per il servizio Batch è:

`https://batch.core.windows.net/`

### <a name="grant-the-batch-service-api-access-to-your-application"></a>Concedere all'API del servizio Batch l'accesso all'applicazione

Prima di eseguire l'autenticazione tramite Azure AD dall'applicazione Batch, è necessario registrare l'applicazione in Azure AD e concedere l'accesso all'API del servizio Batch. L'identificatore di Azure AD per l'API del servizio Batch è **Microsoft Azure Batch (MicrosoftAzureBatch)**.

1. Per registrare l'applicazione Batch, seguire la procedura descritta nella sezione [Aggiunta di un'applicazione](../active-directory/develop/active-directory-integrating-applications.md#adding-an-application) dell'articolo [Integrazione di applicazioni con Azure Active Directory][aad_integrate]. In **URI di reindirizzamento** è possibile specificare qualsiasi URI valido. Non è necessario che sia un endpoint reale.

    Al termine della registrazione dell'applicazione, verranno visualizzati l'ID applicazione e l'ID oggetto:

    ![Registrare l'applicazione Batch in Azure AD](./media/batch-aad-auth/app-registration-data-plane.png)

2. Visualizzare quindi il pannello **Impostazioni**. Nella sezione **Accesso all'API** selezionare **Autorizzazioni necessarie**.
3. Nel pannello **Autorizzazioni necessarie** fare clic sul pulsante **Aggiungi**.
4. Nel passaggio 1 cercare **MicrosoftAzureBatch**, selezionare **Microsoft Azure Batch (MicrosoftAzureBatch)** e fare clic sul pulsante **Seleziona**.
5. Nel passaggio 2 selezionare la casella di controllo accanto ad **Access Azure Batch Service** (Accedi al servizio Azure Batch) e fare clic sul pulsante **Seleziona**.
6. Fare clic sul pulsante **Fine**.

Il pannello **Autorizzazioni necessarie** mostra ora che l'applicazione Azure AD concede l'accesso alle API sia di Azure AD che di Azure Batch. 

![Autorizzazioni delle API](./media/batch-aad-auth/required-permissions-data-plane.png)

### <a name="authentication-for-batch-accounts-in-a-user-subscription"></a>Autenticazione per account Batch in una sottoscrizione utente

Quando si crea un nuovo account Batch, è possibile scegliere la sottoscrizione in cui verranno allocati i pool. La scelta influisce sulla modalità di autenticazione delle richieste effettuate alle risorse nell'account.

Per impostazione predefinita, i pool di Batch vengono allocati in una sottoscrizione del servizio Batch. Se si sceglie questa opzione, possibile eseguire l'autenticazione delle richieste alle risorse in tale account con chiave condivisa o con Azure AD.

È anche possibile specificare l'allocazione dei pool di Batch in una determinata sottoscrizione utente. Se si sceglie questa opzione, è necessario eseguire l'autenticazione con Azure AD.

### <a name="best-practices-for-using-azure-ad-with-batch"></a>Procedure consigliate per l'uso di Azure AD con Batch

Un token di autenticazione di Azure AD scade dopo un'ora. Quando si usa un oggetto **BatchClient** di lunga durata, è consigliabile recuperare un token da ADAL a ogni richiesta per assicurarsi di avere sempre un token valido. 

A tale scopo, in .NET scrivere un metodo che recuperi il token da Azure AD e passi tale metodo a un oggetto **BatchTokenCredentials** come delegato. Il metodo delegato verrà chiamato a ogni richiesta al servizio Batch per garantire che venga fornito un token valido. Per impostazione predefinita, ADAL memorizza i token nella cache, quindi un nuovo token viene recuperato da Azure AD solo quando necessario. Per un esempio, vedere [Esempio di codice: uso di Azure AD con Batch .NET](#code-example-using-azure-ad-with-batch-net) nella sezione successiva. Per altre informazioni sui token in Azure AD, vedere [Scenari di autenticazione per Azure AD][aad_auth_scenarios].

### <a name="code-example-using-azure-ad-with-batch-net"></a>Esempio di codice: uso di Azure AD con Batch .NET

Per scrivere codice Batch .NET per l'autenticazione con Azure AD, fare riferimento al pacchetto [Azure Batch .NET](https://www.nuget.org/packages/Azure.Batch/) e al pacchetto [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

Includere le istruzioni `using` seguenti nel codice:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Fare riferimento all'endpoint comune di Azure AD e all'endpoint di Azure AD per il servizio Batch nel codice:  

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/common";
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Fare riferimento all'endpoint dell'account Batch:

```csharp
private const string BatchAccountEndpoint = "https://myaccount.westcentralus.batch.azure.com";
```

Specificare l'ID dell'applicazione (ID client). L'ID applicazione è disponibile nella registrazione dell'app nel portale di Azure. Per recuperarlo, vedere la sezione intitolata [Concedere all'API del servizio Batch l'accesso all'applicazione](#grant-the-batch-service-api-access-to-your-application). 

```csharp
private const string ClientId = "<application-id>";
```

Specificare anche un URI di reindirizzamento, che può corrispondere a qualsiasi URI valido.

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Scrivere un metodo di callback per acquisire il token di autenticazione da Azure AD. Il metodo **AcquireTokenAsync** richiede all'utente le credenziali e le usa per acquisire un nuovo token.

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

Costruire un oggetto **BatchTokenCredentials** che accetta il delegato come parametro. Usare tali credenziali per aprire un oggetto **BatchClient**. È quindi possibile usare tale oggetto **BatchClient** per operazioni successive sul servizio Batch.

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountEndpoint, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

Il metodo di callback **GetAuthenticationTokenAsync** illustrato sopra usa Azure AD per l'autenticazione integrata di un utente che interagisce con l'applicazione. Con la chiamata al metodo **AcquireTokenAsync** vengono richieste le credenziali all'utente e l'applicazione procede dopo che sono state specificate. È anche possibile usare Azure AD per eseguire l'autenticazione di un'applicazione automatica usando un'entità servizio di Azure AD. Per altre informazioni, vedere [Oggetti applicazione e oggetti entità servizio in Azure Active Directory](../active-directory/develop/active-directory-application-objects.md) e [Usare il portale per creare un'applicazione Active Directory e un'entità servizio che accedono alle risorse](../resource-group-create-service-principal-portal.md).  
 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'esecuzione dell'[applicazione di esempio AccountManagement][acct_mgmt_sample], vedere [Gestire le quote e gli account Batch con la libreria client di gestione Batch per .NET](batch-management-dotnet.md).

Per altre informazioni su Azure AD, vedere [Documentazione di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/). Esempi dettagliati dell'uso di ADAL sono disponibili nella libreria degli [esempi di codice per Azure](https://azure.microsoft.com/resources/samples/?service=active-directory).


[aad_about]: ../active-directory/active-directory-whatis.md "Informazioni su Azure Active Directory"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Scenari di autenticazione per Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrazione di applicazioni con Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[azure_portal]: http://portal.azure.com
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

