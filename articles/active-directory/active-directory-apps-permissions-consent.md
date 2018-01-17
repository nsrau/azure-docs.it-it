---
title: App, autorizzazioni e consenso in Azure Active Directory | Documentazione Microsoft
description: "Azure AD Connect integra le directory locali con Azure Active Directory. Consente di fornire un'identità comune per le applicazioni di Office 365, Azure e SaaS integrate con Azure AD."
keywords: introduzione ad Azure AD Connect, app, informazioni su Azure AD Connect, installare active directory
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 25897cc4-7687-49b6-b0d5-71f51302b6b1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/03/2018
ms.author: billmath
ms.reviewer: jesakowi
ms.custom: oldportal;it-pro;
ms.openlocfilehash: d3e14c18d5e4cd77f4c68d8a5d9d0b915e896695
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="apps-permissions-and-consent-in-azure-active-directory"></a>App, autorizzazioni e consenso in Azure Active Directory
In Azure Active Directory è possibile aggiungere applicazioni alla directory.  Le applicazioni possono variare in base al tipo di applicazione.  Per visualizzare le applicazioni nel portale, selezionare una directory e scegliere le applicazioni.

> [!IMPORTANT]
> Microsoft consiglia di gestire Azure AD usando l'[interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com) nel portale di Azure invece di usare il portale di Azure citato nel presente articolo.

## <a name="types-of-apps"></a>Tipi di app

1. **App a tenant singolo** </br>
    - **App a tenant singolo**: spesso indicate come app line-of-business (LOB). Si tratta del caso in cui un utente all'interno dell'organizzazione sviluppa le proprie app e vuole che gli utenti nell'organizzazione siano in grado di accedere a tali app.
    ![](media/active-directory-apps-permissions-consent/apps2.png)
    - **App del proxy dell'app**: quando si espone un'applicazione locale con il proxy dell'app Azure AD, un'app a tenant singolo viene registrata nel tenant (oltre al servizio proxy dell'app). Questa app è ciò che rappresenta l'applicazione locale per tutte le interazioni cloud (ad esempio, autenticazione). L'app proxy richiede Azure AD Basic o versione successiva.


2. **App multi-tenant**
    - **App multi-tenant per cui altri utenti hanno ottenuto il consenso**: analoghe ad "app a tenant singolo sviluppate dall'organizzazione". La differenza principale (oltre la logica nell'app stessa) è che gli utenti di altri tenant possono ottenere il consenso e accedere all'app.</br>
    ![](media/active-directory-apps-permissions-consent/apps4.png)
    - **App multi-tenant sviluppate da terzi per cui Contoso ha ottenuto il consenso**, o, in breve, "app con consenso". Si tratta del rovescio della medaglia delle "app multi-tenant sviluppate dall'organizzazione". Quando un'altra organizzazione sviluppa un'app multi-tenant, gli utenti dell'organizzazione possono ottenere il consenso di usare e accedere all'app.
    - **App proprietarie di Microsoft**: app che rappresentano i servizi Microsoft. Il consenso è subordinato all'iscrizione al servizio. Per alcune app proprietarie, sono a volte previste esperienze utente e logiche particolari spesso usate quando vengono definiti i criteri per l'accesso all'app.</br>
    ![](media/active-directory-apps-permissions-consent/apps3.png)
    - **App preintegrate**: app disponibili nella raccolta di app di Azure AD, che è possibile aggiungere alla directory per fornire accesso Single Sign-On (e, in alcuni casi, provisioning) alle app SaaS più diffuse.
    - **Accesso Single Sign-On di Azure AD**: SSO "reale", per le app che possono essere integrate con Azure AD, tramite un protocollo di accesso supportato, ad esempio SAML 2.0 oppure OpenID Connect. La procedura guidata ne illustra l'impostazione.
    - **Password Single Sign-On**: Azure AD archivia in modo sicuro le credenziali dell'utente relative all'app e tali credenziali vengono "inserite" nel modulo di accesso dall'estensione browser di accesso all'app di Azure AD. Scenario noto anche come "insieme di credenziali delle password".

## <a name="permissions"></a>Autorizzazioni

Quando un'app viene registrata, l'utente che esegue la registrazione (ovvero, lo sviluppatore) definisce le autorizzazioni e le risorse a cui l'app deve avere accesso. Le risorse vengono, a loro volta, definite come altre app. Ad esempio, un utente che crea un'app per la lettura dei messaggi di posta elettronica, indicherà che l'app richiede l'autorizzazione per l'accesso alle cassette postali come utente connesso nella risorsa "Office 365 Exchange Online":
    
![](media/active-directory-apps-permissions-consent/apps6.png)

Affinché un'app (client) possa richiedere una determinata autorizzazione da un'altra app (risorsa), lo sviluppatore dell'app risorsa definisce le autorizzazioni esistenti. In questo esempio, Microsoft, proprietario dell'app risorsa "Office 365 Exchange Online", ha definito un'autorizzazione per l'accesso alle cassette postali come utente connesso.

Quando si definiscono le autorizzazioni, lo sviluppatore di app deve definire se l'autorizzazione può essere concessa o se è necessario il consenso dell'amministratore. In questo modo, gli sviluppatori possono permettere agli utenti di ottenere il consenso alle app in modo indipendente richiedendo solo autorizzazioni con riservatezza ridotta, ma richiedono agli amministratori di concedere il consenso alle autorizzazioni più riservate. Ad esempio, l'app risorsa "Azure Active Directory" è stata definita in modo che gli utenti possano ottenere il consenso per le app richiedendo autorizzazioni limitate di sola lettura.  Tuttavia, il consenso dell'amministratore è obbligatorio per le autorizzazioni di lettura complete e tutte le autorizzazioni di scrittura.

Poiché i client nativi non sono autenticati, un'app definita come app client nativa può richiedere solo autorizzazioni delegate. Ciò significa che deve essere sempre presente un utente effettivo quando si ottiene un token. Le app e le API Web (client riservati) devono sempre eseguire l'autenticazione con Azure AD quando ottengono un token di accesso. Vale a dire che hanno anche la possibilità di richiedere autorizzazioni di sola app. Ad esempio, se un servizio back-end deve eseguire l'autenticazione a un altro servizio back-end. Le autorizzazioni che richiedono autorizzazioni di sola app richiedono sempre il consenso dell'amministratore.

Riepilogo:



- Un'app (client) indica le autorizzazioni necessarie per altre app (risorse).
- Un'app (risorsa) indica le autorizzazioni esposte ad altre app (client).
- Un'autorizzazione può essere di tipo sola app o delegata.
- Un'autorizzazione delegata può essere contrassegnata come "consente il consenso dell'utente," o "richiede il consenso dell'amministratore".
- Un'app può comportarsi come un client (dichiarando che richiede le autorizzazioni a una risorsa), come una risorsa (dichiarando le autorizzazioni che espone) o come entrambi.

## <a name="controls"></a>Controlli

Di seguito è riportato un elenco dei vari controlli amministratore disponibili per tutti i comportamenti descritti.

Nel portale di Azure, in **Gestisci**, selezionare **Impostazioni utente**.

![](media/active-directory-apps-permissions-consent/apps11.png)



- È possibile controllare se gli utenti possono ottenere il consenso alle applicazioni:

Nel portale di Azure selezionare **Gli utenti possono consentire alle app di accedere ai propri dati**.
![](media/active-directory-apps-permissions-consent/apps12.png)



- È possibile stabilire se gli utenti possono registrare le proprie app line-of-business a tenant singolo:

Nel portale di Azure selezionare **Gli utenti possono registrare applicazioni**.
![](media/active-directory-apps-permissions-consent/apps13.png)

>[!NOTE]
>Anche se si consente agli utenti di registrare le app LOB a tenant singolo, vi sono limiti alle registrazioni possibili.  
>Ad esempio, per gli sviluppatori che non sono amministratori della directory.
>
>- Gli utenti non possono rendere un'app a tenant singolo un'app multi-tenant.
>- Quando si registrano le app LOB a tenant singolo, gli utenti non possono richiedere autorizzazioni di sola app ad altre app.
>- Quando si registrano le app LOB a tenant singolo, gli utenti non possono richiedere autorizzazioni delegate ad altre app se tali autorizzazioni richiedono il consenso dell'amministratore.
>- Gli utenti non possono apportare modifiche alle app di cui non sono proprietari.


## <a name="example"></a>Esempio

A titolo di esempio, si consideri l'app "FabrikamMail per Office 365", alla quale si è notato che gli utenti nel tenant dispongono dell'accesso. "FabrikamMail" è un'app di lettura della posta elettronica per Android pubblicata da "Fabrikam, Inc.". Si tratta del caso di app multi-tenant sviluppate da terzi per cui Contoso ha ottenuto il consenso.

Se gli utenti dispongono del consenso, ricevono una richiesta di consenso al primo accesso: ![](media/active-directory-apps-permissions-consent/apps14.png)

"Access your mailboxes" (Accedi alle cassette postali) è la stringa di consenso dell'utente per l'autorizzazione per l'accesso alle cassette postali come utente connesso esposta da "Office 365 Exchange Online" (ovvero, Exchange).

È possibile visualizzare le autorizzazioni esaminando l'oggetto entità servizio per Exchange (risorsa), aggiunto al momento dell'iscrizione a Office 365. È possibile pensare all'oggetto entità servizio come a un'"istanza" dell'app nel tenant usata per registrare varie configurazioni e opzioni.  Per visualizzare questo scenario, usare `Get-AzureADServicePrincipal` in PowerShell.

    PS C:\> Get-AzureADServicePrincipal -ObjectId 383f7b97-6754-4d3d-9474-3908ebcba1c6 | fl *
    
    DeletionTimeStamp         : 
    ObjectId                  : 383f7b97-6754-4d3d-9474-3908ebcba1c6
    ObjectType                : ServicePrincipal
    AccountEnabled            : True
    AppDisplayName            : Office 365 Exchange Online
    AppId                     : 00000002-0000-0ff1-ce00-000000000000
    AppOwnerTenantId          : 
    AppRoleAssignmentRequired : False
    AppRoles                  : {...}
    DisplayName               : Microsoft.Exchange
    ErrorUrl                  : 
    Homepage                  : 
    KeyCredentials            : {}
    LogoutUrl                 : 
    Oauth2Permissions         : {...
                                , class OAuth2Permission {
                                  AdminConsentDescription : Allows the app to have the same access to mailboxes as the signed-in user via Exchange Web Services.
                                  AdminConsentDisplayName : Access mailboxes as the signed-in user via Exchange Web Services
                                  Id                      : 3b5f3d61-589b-4a3c-a359-5dd4b5ee5bd5
                                  IsEnabled               : True
                                  Type                    : User
                                  UserConsentDescription  : Allows the app full access to your mailboxes on your behalf.
                                  UserConsentDisplayName  : Access your mailboxes
                                  Value                   : full_access_as_user
                                },
                                ...}
    PasswordCredentials       : {}
    PublisherName             : 
    ReplyUrl                  : 
    SamlMetadataUrl           : 
    ServicePrincipalNames     : {00000002-0000-0ff1-ce00-000000000000/outlook.office365.com, 00000002-0000-0ff1-ce00-000000000000/mail.office365.com, 00000002-0000-0ff1-ce00-000000000000/outlook.com, 
                                00000002-0000-0ff1-ce00-000000000000/*.outlook.com...}
    Tags                      : {}

Il consenso viene avviato quando l'utente fa clic su "Accept" (Accetto). Innanzitutto, viene creato un oggetto entità servizio per "FabrikamMail per Office 365" nel tenant. L'oggetto sarà come indicato di seguito:

    PS C:\> Get-AzureADServicePrincipal -SearchString "FabrikamMail for Office 365" | fl *
    
    DeletionTimeStamp         : 
    ObjectId                  : a8b16333-851d-42e8-acd2-eac155849b37
    ObjectType                : ServicePrincipal
    AccountEnabled            : True
    AppDisplayName            : FabrikamMail for Office 365
    AppId                     : aba7c072-2267-4031-8960-e7a2db6e0590
    AppOwnerTenantId          : 4a4076e0-a70f-41c6-b819-6f9c4a86df89
    AppRoleAssignmentRequired : False
    AppRoles                  : {}
    DisplayName               : FabrikamMail for Office 365
    ErrorUrl                  : 
    Homepage                  : 
    KeyCredentials            : {}
    LogoutUrl                 : 
    Oauth2Permissions         : {}
    PasswordCredentials       : {}
    PublisherName             : Fabrikam, Inc.
    ReplyUrl                  : 
    SamlMetadataUrl           : 
    ServicePrincipalNames     : {aba7c072-2267-4031-8960-e7a2db6e0590}
    Tags                      : {WindowsAzureActiveDirectoryIntegratedApp}

Il consenso a un'app crea un collegamento Oauth2PermissionGrant tra gli elementi seguenti:
  
- Oggetto utente
- App client ServicePrincipalName (SPN)
- App risorsa ServicePrincipalName (SPN)
- Autorizzazioni nell'app risorsa  

Nel caso di FabrikamMail, sarà simile a quanto segue:

    PS C:\> Get-AzureADUserOAuth2PermissionGrant -ObjectId ddiggle@aadpremiumlab.onmicrosoft.com | fl *
    
    ClientId    : a8b16333-851d-42e8-acd2-eac155849b37
    ConsentType : Principal
    ExpiryTime  : 05/15/2017 07:02:39 AM
    ObjectId    : M2OxqB2F6EKs0urBVYSbN5d7PzhUZz1NlH25COvLocbJjoxkUFfRQauryBKwBWet
    PrincipalId : 648c8ec9-5750-41d1-abab-c812b00567ad
    ResourceId  : 383f7b97-6754-4d3d-9474-3908ebcba1c6
    Scope       : full_access_as_user
    StartTime   : 01/01/0001 12:00:00 AM

(**ClientId** è l'ID dell'oggetto entità servizio di FabrikamMail (quello appena creato), **PrincipalId** è l'ID dell'oggetto utente (dell'utente che ha concesso il consenso), **ResourceId** è l'ID dell'oggetto entità servizio di Exchange Scope è l'autorizzazione di Exchange che ha ottenuto il consenso).

Se agli utenti non è stato concesso il consenso, visualizzeranno una schermata indicante la necessità di disporre dell'autorizzazione.

