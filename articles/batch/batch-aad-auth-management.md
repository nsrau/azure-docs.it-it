---
title: Usare Azure Active Directory per autenticare le soluzioni di gestione Batch | Microsoft Docs
description: Le applicazioni compilate con Azure Resource Manager e il provider di risorse Batch vengono autenticate con Azure AD.
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
ms.date: 04/27/2017
ms.author: tamram
ms.openlocfilehash: 26d4adf4f74f9aacc4cf8cf24be293ebdb4d63c8
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="authenticate-batch-management-solutions-with-active-directory"></a>Autenticare le soluzioni di gestione Batch con Active Directory

Le applicazioni che chiamano il servizio di gestione di Azure Batch vengono autenticate con [Azure Active Directory] [ aad_about] (Azure AD). Azure AD è il servizio Microsoft di gestione di identità e directory basato sul cloud e multi-tenant. Azure stesso usa Azure AD per l'autenticazione dei relativi clienti, amministratori del servizio e utenti dell'organizzazione.

La libreria .NET per la gestione di Batch espone tipi per l'uso di account Batch, chiavi dell'account, applicazioni e pacchetti dell'applicazione. La libreria .NET per la gestione di Batch è un client del provider di risorse di Azure e viene usata in combinazione con [Azure Resource Manager][resman_overview] per gestire tali risorse a livello di codice. Azure AD è necessario per autenticare le richieste effettuate tramite qualsiasi client del provider di risorse di Azure, inclusa la libreria .NET per la gestione di Batch, e tramite [Azure Resource Manager][resman_overview].

Questo articolo illustra l'uso di Azure AD per eseguire l'autenticazione dalle applicazioni che usano la libreria .NET per la gestione di Batch. Viene descritto come usare Azure AD per autenticare un amministratore o un co-amministratore della sottoscrizione tramite l'autenticazione integrata. In questa sezione si usa il progetto di esempio [AccountManagement][acct_mgmt_sample], disponibile in GitHub, per illustrare l'uso di Azure AD con la libreria .NET per la gestione di Batch.

Per altre informazioni sull'uso della libreria .NET per la gestione di Batch e dell'esempio AccountManagement, vedere [Gestire le quote e gli account Batch con la libreria client di gestione Batch per .NET](batch-management-dotnet.md).

## <a name="register-your-application-with-azure-ad"></a>Registrare l'applicazione in Azure AD

[Azure Active Directory Authentication Library][aad_adal] (ADAL) offre un'interfaccia programmatica per Azure AD da usare nelle applicazioni. Per chiamare ADAL da un'applicazione, è necessario registrare l'applicazione in un tenant di Azure AD. Quando si registra l'applicazione, si specificano in Azure AD le informazioni relative all'applicazione, incluso un nome per l'applicazione nel tenant di Azure AD. Azure AD fornisce quindi un ID applicazione che viene usato per associare l'applicazione ad Azure AD in fase di esecuzione. Per altre informazioni sull'ID applicazione, vedere [Oggetti applicazione e oggetti entità servizio in Azure Active Directory](../active-directory/develop/active-directory-application-objects.md).

Per registrare l'applicazione di esempio AccountManagement, seguire la procedura descritta nella sezione [Aggiunta di un'applicazione](../active-directory/develop/active-directory-integrating-applications.md#adding-an-application) dell'articolo [Integrazione di applicazioni con Azure Active Directory][aad_integrate]. Specificare **Nativa** come tipo di applicazione. L'URI OAuth 2.0 standard per l'**URI di reindirizzamento** è `urn:ietf:wg:oauth:2.0:oob`. In **URI di reindirizzamento** è possibile specificare qualsiasi URI valido, ad esempio `http://myaccountmanagementsample`, perché non è necessario che sia un endpoint reale:

![](./media/batch-aad-auth-management/app-registration-management-plane.png)

Al termine del processo di registrazione, verranno elencati l'ID applicazione e l'ID oggetto (entità servizio) dell'applicazione.  

![](./media/batch-aad-auth-management/app-registration-client-id.png)

## <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>Concedere all'API di Azure Resource Manager l'accesso all'applicazione

Successivamente, sarà necessario delegare l'accesso all'applicazione all'API di Azure Resource Manager. L'identificatore di Azure AD per l'API di Resource Manager è **API di gestione del servizio Microsoft Azure**.

Seguire questa procedura nel portale di Azure:

1. Nel riquadro di spostamento sinistro del portale di Azure scegliere **Altri servizi** e fare clic su **Registrazioni per l'app** e quindi su **Aggiungi**.
2. Cercare il nome dell'applicazione nell'elenco di registrazioni di app:

    ![Cercare il nome dell'applicazione](./media/batch-aad-auth-management/search-app-registration.png)

3. Visualizzare il pannello **Impostazioni**. Nella sezione **Accesso all'API** selezionare **Autorizzazioni necessarie**.
4. Fare clic su **Aggiungi** per aggiungere una nuova autorizzazione necessaria. 
5. Nel passaggio 1 immettere **API di gestione del servizio Microsoft Azure**, selezionare tale API nell'elenco dei risultati e fare clic sul pulsante **Seleziona**.
6. Nel passaggio 2 selezionare la casella di controllo accanto ad **Access Azure classic deployment model as organization users** (Accedi a modello di distribuzione classica di Azure come utente dell'organizzazione) e fare clic sul pulsante **Seleziona**.
7. Fare clic sul pulsante **Fine**.

Il pannello **Autorizzazioni necessarie** mostra ora che le autorizzazioni per l'applicazione sono concesse sia ad ADAL che alle API di Resource Manager. Le autorizzazioni vengono concesse ad ADAL per impostazione predefinita quando si registra per la prima volta l'app in Azure AD.

![Delegare le autorizzazioni all'API di Azure Resource Manager](./media/batch-aad-auth-management/required-permissions-management-plane.png)

## <a name="azure-ad-endpoints"></a>Endpoint di Azure Active Directory

Per autenticare le soluzioni di gestione Batch con Azure AD, è necessario usare due endpoint noti.

- L'**endpoint comune di Azure AD** consente di usare un'interfaccia di raccolta di credenziali generiche quando non è disponibile un tenant specifico, come nel caso l'autenticazione integrata:

    `https://login.microsoftonline.com/common`

- L'**endpoint di Azure Resource Manager** consente di acquisire un token per autenticare le richieste al servizio di gestione Batch:

    `https://management.core.windows.net/`

L'applicazione di esempio AccountManagement definisce le costanti per tali endpoint. Lasciare invariate queste costanti:

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure Resource Manager endpoint 
private const string ResourceUri = "https://management.core.windows.net/";
```

## <a name="reference-your-application-id"></a>Fare riferimento all'ID applicazione 

L'ID applicazione (detto anche ID client) viene usato dall'applicazione client per accedere ad Azure AD in fase di esecuzione. Dopo aver registrato l'applicazione nel portale di Azure, aggiornare il codice per usare l'ID applicazione fornito da Azure AD per l'applicazione registrata. Nell'applicazione di esempio AccountManagement, copiare l'ID applicazione dal portale di Azure alla costante appropriata:

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Azure AD Graph API. For information
// about registering an application in Azure Active Directory, please see "Adding an Application" here:
// https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
private const string ClientId = "<application-id>";
```
Copiare anche l'URI di reindirizzamento specificato durante il processo di registrazione. L'URI specificato nel codice di reindirizzamento deve corrispondere a quello specificato quando è stata registrata l'applicazione.

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Acquisire un token di autenticazione di Azure AD

Dopo aver registrare l'esempio AccountManagement nel tenant di Azure AD e aggiornato il codice sorgente di esempio con i valori appropriati, l'esempio è pronto per eseguire l'autenticazione tramite Azure AD. Quando si esegue l'esempio, ADAL tenta di acquisire un token di autenticazione. A questo punto, richiede all'utente credenziali Microsoft: 

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

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'esecuzione dell'[applicazione di esempio AccountManagement][acct_mgmt_sample], vedere [Gestire le quote e gli account Batch con la libreria client di gestione Batch per .NET](batch-management-dotnet.md).

Per altre informazioni su Azure AD, vedere [Documentazione di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/). Esempi dettagliati dell'uso di ADAL sono disponibili nella libreria degli [esempi di codice per Azure](https://azure.microsoft.com/resources/samples/?service=active-directory).

Per eseguire l'autenticazione di applicazioni del servizio Batch con Azure AD, vedere [Authenticate Batch service solutions with Active Directory](batch-aad-auth.md) (Autenticare le soluzioni del servizio Batch con Active Directoy). 


[aad_about]: ../active-directory/active-directory-whatis.md "Informazioni su Azure Active Directory"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Scenari di autenticazione per Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrazione di applicazioni con Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[azure_portal]: http://portal.azure.com
[resman_overview]: ../azure-resource-manager/resource-group-overview.md
