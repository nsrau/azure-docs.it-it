---
title: Autenticazione e autorizzazione
description: Informazioni sui vari modi in cui un'app o un servizio può eseguire l'autenticazione agli ancoraggi spaziali di Azure e sui livelli di controllo di cui si dispone per eseguire l'accesso ai coreani spaziali di Azure.Learn about the various ways an app or service can authenticate to Azure Spatial Anchors, and the levels of control that you have to gate access to Azure Spatial Anchors.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: df27a77c202951a6c789703f12712e75bd8b5906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656980"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Autenticazione e autorizzazione agli ancoraggi spaziali di AzureAuthentication and authorization to Azure Spatial Anchors

In questa sezione verranno illustrati i vari modi in cui è possibile eseguire l'autenticazione agli ancoraggi spaziali di Azure dall'app o dal servizio Web e i modi in cui è possibile usare il controllo degli accessi in base al ruolo in Azure Directory (Azure AD) per controllare l'accesso agli account di ancore spaziali.

## <a name="overview"></a>Panoramica

![Panoramica dell'autenticazione agli ancoraggi spaziali di AzureAn overview of authentication to Azure Spatial Anchors](./media/spatial-anchors-authentication-overview.png)

Per accedere a un determinato account di Azure Spatial Anchors, i client devono prima ottenere un token di accesso dal servizio token di sicurezza di azure a realtà mista. I token ottenuti dal servizio token di sicurezza sono attivi per 24 ore e contengono informazioni per i servizi di ancoraggio spaziale per prendere decisioni di autorizzazione sull'account e garantire che solo le entità autorizzate possano accedere a tale account.

I token di accesso possono essere ottenuti in cambio di chiavi account o da token rilasciati da Azure AD.

Le chiavi dell'account consentono di iniziare rapidamente a usare il servizio Azure Spatial Anchors. Tuttavia, prima di distribuire l'applicazione nell'ambiente di produzione, si consiglia di aggiornare l'app per usare l'autenticazione basata su Azure AD.

I token di autenticazione di Azure AD possono essere ottenuti in due modi:Azure AD authentication tokens can be obtained two ways:

- Se si sta creando un'applicazione aziendale e l'azienda usa Azure AD come sistema di identità, è possibile usare l'autenticazione di Azure AD basata sull'utente nell'app e concedere l'accesso agli account di ancoraggio spaziali usando i gruppi di sicurezza di Azure AD esistenti oppure direttamente agli utenti dell'organizzazione.
- In caso contrario, è consigliabile ottenere i token di Azure AD da un servizio Web che supporta l'app. L'uso di un servizio Web di supporto è il metodo di autenticazione consigliato per le applicazioni di produzione, in quanto evita l'incorporamento delle credenziali per l'accesso agli ancoraggi spaziali di Azure nell'applicazione client.

## <a name="account-keys"></a>Chiavi dell'account

L'uso delle chiavi dell'account per l'accesso all'account Azure Spatial Anchors è il modo più semplice per iniziare. Le chiavi dell'account sono disponibili nel portale di Azure.You will find your account keys on the Azure portal. Accedi al tuo account e seleziona la scheda "Chiavi".

![Panoramica dell'autenticazione agli ancoraggi spaziali di AzureAn overview of authentication to Azure Spatial Anchors](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)


Vengono rese disponibili due chiavi, entrambe simultaneamente valide per l'accesso all'account Di ancore spaziali. Si consiglia di aggiornare regolarmente la chiave utilizzata per accedere all'account; la presenza di due chiavi valide separate consente tali aggiornamenti senza tempi di inattività; è sufficiente aggiornare in alternativa la chiave primaria e la chiave secondaria.

L'SDK dispone del supporto incorporato per l'autenticazione con le chiavi dell'account. è sufficiente impostare la proprietà AccountKey sull'oggetto cloudSession.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swift"></a>[Repentino](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndk"></a>[NDK di C](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="c-winrt"></a>[WinRT in C](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

Al termine, l'SDK gestirà lo scambio della chiave dell'account per un token di accesso e la memorizzazione nella cache necessaria dei token per l'app.

> [!WARNING]
> L'uso delle chiavi dell'account è consigliato per l'onboarding rapido, ma solo durante lo sviluppo/prototipazione. È consigliabile non spedire l'applicazione alla produzione usando una chiave dell'account incorporata e usare invece gli approcci di autenticazione di Azure AD basati sull'utente o basati su servizi elencati di seguito.

## <a name="azure-ad-user-authentication"></a>Autenticazione utente di Azure ADAzure AD user authentication

Per le applicazioni destinate agli utenti di Azure Active Directory, l'approccio consigliato consiste nell'usare un token di Azure AD per l'utente, che è possibile ottenere usando la [libreria MSAL.](../../active-directory/develop/msal-overview.md) È necessario seguire i passaggi elencati nella guida introduttiva di [un'app,](../../active-directory/develop/quickstart-register-app.md)che include:

1. Configurazione nel portale di AzureConfiguration in Azure portal
    1.  Registrare l'applicazione in Azure AD come **applicazione nativa.** Come parte della registrazione, è necessario determinare se l'applicazione deve essere multi-tenant o meno e fornire gli URL di reindirizzamento consentiti per l'applicazione.
        1.  Passare alla scheda **Autorizzazioni API**
        2.  Selezionare **Aggiungi un'autorizzazione**
            1.  Selezionare Provider di **risorse di realtà mista** in **API** della scheda
            2.  Selezionare **Autorizzazioni delegate**
            3.  Seleziona la casella **per mixedreality.signin** in **mixedreality**
            4.  Selezionare **Aggiungi autorizzazioni**
        3.  Selezionare **Concedi il consenso dell'amministratore**
    2.  Concedere all'applicazione o agli utenti l'accesso alla risorsa:Grant your application or users access to your resource:
        1.  Passare alla risorsa Ancoraggi spaziali nel portale di AzureNavigate to your Spatial Anchors resource in Azure portal
        2.  Passare alla scheda **Controllo di accesso (IAM)**
        3.  Premi **Aggiungi assegnazione ruolo**
            1.  [Seleziona un ruolo](#role-based-access-control)
            2.  Nel campo **Seleziona** immettere il nome degli utenti, dei gruppi e/o delle applicazioni a cui si desidera assegnare l'accesso.
            3.  Premere **Salva**.
2. Nel codice:In your code:
    1.  Assicurarsi di usare l'ID applicazione e **reindirizzare** **l'URI** dell'applicazione Azure AD come **ID client** e parametri **RedirectUri** in ADAL
    2.  Impostare le informazioni sul tenant:
        1.  Se l'applicazione supporta **solo l'organizzazione**, sostituire questo valore con l'ID **tenant** o il **nome del tenant(** ad esempio, contoso.microsoft.com)
        2.  Se l'applicazione supporta **Account in qualsiasi directory organizzativa,** sostituire questo valore con **Organizzazioni**
        3.  Se l'applicazione supporta **Tutti gli utenti dell'account Microsoft,** sostituire questo valore **con**
    3.  Nella richiesta di token, impostare la **risorsa** su "https://sts.mixedreality.azure.com". Questa "risorsa" indicherà ad Azure AD che l'applicazione richiede un token per il servizio Azure Spatial Anchors.This "resource" will indicate to Azure AD that your application is requesting a token for the Azure Spatial Anchors service.

Con questo, l'applicazione deve essere in grado di ottenere da MSAL un token di Azure AD; è possibile impostare tale token di Azure AD come **authenticationToken** nell'oggetto di configurazione della sessione cloud.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swift"></a>[Repentino](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndk"></a>[NDK di C](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="c-winrt"></a>[WinRT in C](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Autenticazione del servizio Azure ADAzure AD service authentication

L'opzione consigliata per distribuire app che sfruttano gli ancoraggi spaziali di Azure nell'ambiente di produzione consiste nell'eseguire le informazioni su un servizio back-end che esonera le richieste di autenticazione. Lo schema generale dovrebbe essere come descritto in questo diagramma:

![Panoramica dell'autenticazione agli ancoraggi spaziali di AzureAn overview of authentication to Azure Spatial Anchors](./media/spatial-anchors-aad-authentication.png)

In questo caso, si presuppone che l'app utilizzi un proprio meccanismo (ad esempio: account Microsoft, PlayFab, Facebook, ID Google, nome utente/password personalizzati e così via) per l'autenticazione al servizio back-end. Dopo che gli utenti sono autenticati nel servizio back-end, tale servizio può recuperare un token di Azure AD, scambiarlo con un token di accesso per gli ancoraggi spaziali di Azure e restituirlo all'applicazione client.

Il token di accesso di Azure AD viene recuperato tramite la [libreria MSAL.](../../active-directory/develop/msal-overview.md) È necessario seguire i passaggi elencati nella guida introduttiva di [un'app,](../../active-directory/develop/quickstart-register-app.md)che include:

1.  Configurazione nel portale di Azure:Configuration in Azure portal:
    1.  Registrare l'applicazione in Azure AD:Register your application in Azure AD:
        1.  Nel portale di Azure passare ad **Azure Active Directory**e selezionare **Registrazioni app**
        2.  Selezionare la **registrazione di una nuova domanda di lavoro**
        3.  Immettere il nome dell'applicazione, selezionare **App Web/API** come tipo di applicazione e immettere l'URL di autenticazione per il servizio. Quindi premere **Create**.
        4.  Su tale applicazione, premi **Impostazioni**, quindi seleziona la scheda **Chiavi.** **Save** Assicurarsi di salvare il valore della chiave visualizzato in quel momento, poiché sarà necessario includerlo nel codice del servizio Web.
    2.  Concedere all'applicazione e/o agli utenti l'accesso alla risorsa:
        1.  Passare alla risorsa Ancoraggi spaziali nel portale di AzureNavigate to your Spatial Anchors resource in Azure portal
        2.  Passare alla scheda **Controllo di accesso (IAM)**
        3.  Premi **Aggiungi assegnazione ruolo**
        1.  [Seleziona un ruolo](#role-based-access-control)
        2.  Nel campo **di selezione** immettere il nome dell'applicazione creata e a cui si desidera assegnare l'accesso. Se si vuole che gli utenti dell'app abbiano ruoli diversi rispetto all'account Di ancore spaziali, è necessario registrare più applicazioni in Azure AD e assegnare a ogni ruolo un ruolo separato. Implementare quindi la logica di autorizzazione per usare il ruolo corretto per gli utenti.
    3.  Premere **Salva**.
2.  In your code (note: you can use the service sample included on GitHub):
    1.  Assicurarsi di usare l'ID applicazione, il segreto dell'applicazione e reindirizzare l'URI dell'applicazione Azure AD come parametri ID client, segreto e RedirectUri in ADAL
    2.  Impostare l'ID tenant sul proprio ID tenant AAAzure ADD nel parametro authority in ADALSet the tenant ID to your own AAAzure ADD tenant ID in the authority parameter in ADAL
    3.  Nella richiesta di token, impostare la **risorsa** su "https://sts.mixedreality.azure.com"

Con questo, il servizio back-end può recuperare un token di Azure AD. Può quindi scambiarlo con un token MR che tornerà al client. L'uso di un token di Azure AD per recuperare un token MR viene eseguito tramite una chiamata REST. Di seguito è riportata una chiamata di esempio:Here is a sample call:

```
GET https://mrc-auth-prod.trafficmanager.net/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: mrc-auth-prod.trafficmanager.net
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

Dove l'intestazione Authorization è formattata come segue:`Bearer <accoundId>:<accountKey>`

E la risposta contiene il token MR in testo normale.

Tale token MR viene quindi restituito al client. L'app client può quindi impostarla come token di accesso nella configurazione della sessione cloud.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swift"></a>[Repentino](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndk"></a>[NDK di C](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="c-winrt"></a>[WinRT in C](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Per controllare il livello di accesso concesso ad applicazioni, servizi o utenti di Azure AD del servizio, sono stati creati i ruoli seguenti da assegnare in base alle esigenze per gli account di Azure Spatial Anchors:To help control the level of access granted to applications, services or Azure AD users of your service, the following roles have been created for you to assign as needed against your Azure Spatial Anchors accounts:

- **Proprietario account di ancore spaziali**: le applicazioni o gli utenti che dispongono di questo ruolo sono in grado di creare ancoraggi spaziali, eseguire query ed eliminarli. Quando si esegue l'autenticazione all'account utilizzando le chiavi dell'account, il ruolo **Proprietario account di ancore spaziali** viene assegnato all'entità autenticata.
- **Collaboratore account ancorati spaziali:** le applicazioni o gli utenti che dispongono di questo ruolo sono in grado di creare ancoraggi spaziali, eseguire query per tali account, ma non possono eliminarli.
- **Lettore account ancorati spaziali:** le applicazioni o gli utenti che dispongono di questo ruolo possono eseguire query solo sugli ancoraggi spaziali, ma non possono crearne di nuovi, eliminare quelli esistenti o aggiornare i metadati sugli ancoraggi spaziali. Viene in genere utilizzato per le applicazioni in cui alcuni utenti curano l'ambiente, mentre altri possono richiamare solo gli ancoraggi posizionati in precedenza in tale ambiente.

## <a name="next-steps"></a>Passaggi successivi

Creare la prima app con gli ancoraggi spaziali di Azure.Create your first app with Azure Spatial Anchors.

> [!div class="nextstepaction"]
> [Unità (HoloLens)](../quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unità (iOS)](../quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unità (Android)](../quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin (Android)](../quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin (iOS)](../quickstarts/get-started-xamarin-ios.md)
