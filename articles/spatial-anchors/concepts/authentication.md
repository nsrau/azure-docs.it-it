---
title: Autenticazione e autorizzazione
description: Informazioni sui vari modi in cui un'app o un servizio è in grado di eseguire l'autenticazione agli ancoraggi spaziali di Azure e i livelli di controllo di cui è necessario controllare l'accesso agli ancoraggi spaziali di Azure.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 823ce8d523a231875705d7c4d3f46cfd8fd24994
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74270592"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Autenticazione e autorizzazione per gli ancoraggi spaziali di Azure

In questa sezione vengono illustrati i vari modi in cui è possibile eseguire l'autenticazione a ancoraggi spaziali di Azure dall'app o dal servizio Web e i modi in cui è possibile usare il controllo degli accessi in base al ruolo nella directory di Azure (Azure AD) per controllare l'accesso agli account di ancoraggio spaziali.

## <a name="overview"></a>Overview

![Panoramica dell'autenticazione per gli ancoraggi spaziali di Azure](./media/spatial-anchors-authentication-overview.png)

Per accedere a un account di ancoraggio spaziale di Azure specificato, i client devono prima ottenere un token di accesso dal servizio token di sicurezza di Azure Mixed Reality (STS). I token ottenuti da STS Live per 24 ore e contengono informazioni per i servizi di ancoraggio spaziali per prendere decisioni di autorizzazione per l'account e assicurarsi che solo le entità autorizzate possano accedere a tale account.

I token di accesso possono essere ottenuti in Exchange da chiavi di account o da token rilasciati da Azure AD.

Le chiavi dell'account consentono di iniziare rapidamente a usare il servizio ancoraggi spaziali di Azure. Tuttavia, prima di distribuire l'applicazione in produzione, è consigliabile aggiornare l'app per l'uso dell'autenticazione basata su Azure AD.

Azure AD token di autenticazione possono essere ottenuti in due modi:

- Se si compila un'applicazione aziendale e l'azienda USA Azure AD come sistema di identità, è possibile usare l'autenticazione Azure AD basata sull'utente nell'app e concedere l'accesso agli account di ancoraggio spaziali usando i gruppi di sicurezza di Azure AD esistenti oppure direttamente agli utenti dell'organizzazione.
- In caso contrario, è consigliabile ottenere Azure AD token da un servizio Web che supporta l'app. L'uso di un servizio Web di supporto è il metodo consigliato per l'autenticazione per le applicazioni di produzione, in quanto evita di incorporare le credenziali per l'accesso agli ancoraggi spaziali di Azure nell'applicazione client.

## <a name="account-keys"></a>Chiavi dell'account

L'uso delle chiavi dell'account per l'accesso all'account degli ancoraggi spaziali di Azure è il modo più semplice per iniziare. Le chiavi dell'account sono disponibili nella portale di Azure. Passare all'account e selezionare la scheda "chiavi".

![Panoramica dell'autenticazione per gli ancoraggi spaziali di Azure](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)


Sono disponibili due chiavi, entrambe simultaneamente valide per l'accesso all'account degli ancoraggi spaziali. Si consiglia di aggiornare regolarmente la chiave usata per accedere all'account. la presenza di due chiavi valide separate Abilita tali aggiornamenti senza tempi di inattività; è necessario solo aggiornare in alternativa la chiave primaria e la chiave secondaria.

L'SDK include il supporto predefinito per l'autenticazione con le chiavi dell'account. è sufficiente impostare la proprietà AccountKey nell'oggetto cloudSession.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

Al termine dell'operazione, l'SDK gestirà lo scambio della chiave dell'account per un token di accesso e la memorizzazione nella cache dei token necessari per l'app.

> [!WARNING]
> L'uso delle chiavi dell'account è consigliato per il caricamento rapido, ma solo durante lo sviluppo e la creazione di prototipi. Si consiglia di non spedire l'applicazione alla produzione usando una chiave dell'account incorporata e di usare invece gli approcci di autenticazione Azure AD basati sull'utente o sul servizio elencati di seguito.

## <a name="azure-ad-user-authentication"></a>Autenticazione utente Azure AD

Per le applicazioni destinate a Azure Active Directory utenti, l'approccio consigliato consiste nell'usare un token Azure AD per l'utente, che è possibile ottenere usando la libreria ADAL, come descritto nella documentazione seguente: [https://docs.microsoft.com/azure/active-directory/develop/v1-overview](../../active-directory/develop/v1-overview.md); attenersi alla procedura riportata in "avvio rapido", che include:

1. Configurazione in portale di Azure
    1.  Registrare l'applicazione in Azure AD come **applicazione nativa**. Nell'ambito della registrazione, è necessario determinare se l'applicazione deve essere multi-tenant o meno e fornire gli URL di reindirizzamento consentiti per l'applicazione.
    2.  Concedere all'applicazione o agli utenti l'accesso alla risorsa:
        1.  Passare alla risorsa ancoraggi spaziali in portale di Azure
        2.  Passa alla scheda **controllo di accesso (IAM)**
        3.  Hit **Aggiungi assegnazione di ruolo**
            1.  [Selezionare un ruolo](#role-based-access-control)
            2.  Nel campo **Seleziona** , immettere il nome degli utenti, i gruppi e/o le applicazioni a cui si desidera assegnare l'accesso...
            3.  Premere **Salva**.
2. Nel codice:
    1.  Assicurarsi di usare l' **ID applicazione** e l' **URI di reindirizzamento** dell'applicazione Azure ad come **ID client** e parametri **RedirectUri** in Adal
    2.  Impostare le informazioni sul tenant:
        1.  Se l'applicazione supporta **solo l'organizzazione**, sostituire questo valore con l' **ID tenant** o il **nome del tenant** (ad esempio, contoso.Microsoft.com)
        2.  Se l'applicazione supporta gli **account in qualsiasi directory organizzativa**, sostituire questo valore con le **organizzazioni**
        3.  Se l'applicazione supporta **tutti gli utenti account Microsoft**, sostituire questo valore con **Common**
    3.  Nella richiesta di token impostare la **risorsa** su "https://sts.mixedreality.azure.com". Questa "risorsa" indicherà Azure AD che l'applicazione richiede un token per il servizio Azure Spatial Anchors.

In questo modo, l'applicazione deve essere in grado di ottenere da ADAL un token di Azure AD; è possibile impostare il token Azure AD come **authenticationToken** nell'oggetto config della sessione cloud.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Autenticazione del servizio Azure AD

L'opzione consigliata per la distribuzione di app che sfruttano gli ancoraggi spaziali di Azure alla produzione consiste nell'utilizzare un servizio back-end che effettuerà il broker delle richieste di autenticazione. Lo schema generale dovrebbe essere come descritto in questo diagramma:

![Panoramica dell'autenticazione per gli ancoraggi spaziali di Azure](./media/spatial-anchors-aad-authentication.png)

Si presuppone che l'app usi un proprio meccanismo, ad esempio account Microsoft, PlayFab, Facebook, Google ID, nome utente/password personalizzato e così via, per eseguire l'autenticazione al servizio back-end. Una volta che gli utenti vengono autenticati nel servizio back-end, il servizio può recuperare un token di Azure AD, scambiarlo per un token di accesso per gli ancoraggi spaziali di Azure e restituirlo all'applicazione client.

Il token di accesso Azure AD viene recuperato tramite la libreria ADAL, come descritto nella documentazione seguente: [https://docs.microsoft.com/azure/active-directory/develop/v1-overview](../../active-directory/develop/v1-overview.md); attenersi alla procedura riportata in "avvio rapido", che include:

1.  Configurazione in portale di Azure:
    1.  Registrare l'applicazione in Azure AD:
        1.  In portale di Azure passare a **Azure Active Directory**e selezionare registrazioni per l' **app**
        2.  Selezionare **registrazione nuova applicazione**
        3.  Immettere il nome dell'applicazione, selezionare **app Web/API** come tipo di applicazione e immettere l'URL di autenticazione per il servizio. Quindi fare clic su **Crea**.
        4.  In tale applicazione, fare clic su **Impostazioni**, quindi selezionare la scheda **chiavi** . Immettere il nome della chiave, selezionare una durata e quindi fare clic su **Salva**. Assicurarsi di salvare il valore della chiave visualizzato in quel momento, in quanto sarà necessario includerlo nel codice del servizio Web.
    2.  Concedere all'applicazione e/o agli utenti l'accesso alla risorsa:
        1.  Passare alla risorsa ancoraggi spaziali in portale di Azure
        2.  Passa alla scheda **controllo di accesso (IAM)**
        3.  Hit **Aggiungi assegnazione di ruolo**
        1.  [Selezionare un ruolo](#role-based-access-control)
        2.  Nel campo **Seleziona** immettere il nome dell'applicazione o delle applicazioni create e a cui si desidera assegnare l'accesso. Se si vuole che gli utenti dell'app abbiano ruoli diversi rispetto all'account degli ancoraggi spaziali, è necessario registrare più applicazioni nel Azure AD e assegnare a ognuno un ruolo separato. Implementare quindi la logica di autorizzazione per usare il ruolo appropriato per gli utenti.
    3.  Premere **Salva**.
2.  Nel codice (Nota: è possibile usare l'esempio di servizio incluso in GitHub):
    1.  Assicurarsi di usare l'ID applicazione, il segreto applicazione e l'URI di reindirizzamento dell'applicazione Azure AD come parametri ID client, segreto e RedirectUri in ADAL
    2.  Impostare l'ID tenant sul proprio AAAzure Aggiungi ID tenant nel parametro Authority in ADAL
    3.  Nella richiesta di token impostare la **risorsa** su "https://sts.mixedreality.azure.com".

In questo modo, il servizio back-end può recuperare un token di Azure AD. Può quindi scambiarlo per un token MR che tornerà al client. L'uso di un token di Azure AD per recuperare un token MR viene eseguito tramite una chiamata REST. Di seguito è riportato un esempio di chiamata:

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

Dove l'intestazione di autorizzazione è formattata come segue: `Bearer <accoundId>:<accountKey>`

E la risposta contiene il token MR in testo normale.

Il token MR viene quindi restituito al client. L'app client può quindi impostarla come token di accesso nella configurazione della sessione cloud.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Per controllare il livello di accesso concesso a applicazioni, servizi o Azure AD utenti del servizio, sono stati creati i seguenti ruoli per l'assegnazione in base alle esigenze per gli account di ancoraggio spaziali di Azure:

- **Proprietario dell'account di ancoraggi spaziali**: le applicazioni o gli utenti con questo ruolo sono in grado di creare ancoraggi spaziali, eseguire query per tali elementi ed eliminarli. Quando si esegue l'autenticazione all'account usando le chiavi dell'account, il ruolo di **proprietario dell'account di ancoraggi spaziali** viene assegnato all'entità autenticata.
- **Collaboratore account ancoraggi spaziali**: le applicazioni o gli utenti che dispongono di questo ruolo sono in grado di creare ancoraggi spaziali, eseguire query per tali elementi, ma non eliminarli.
- **Lettore di account di ancoraggio spaziale**: le applicazioni o gli utenti con questo ruolo sono in grado di eseguire query solo per gli ancoraggi spaziali, ma non possono crearne di nuovi, eliminare quelli esistenti o aggiornare i metadati negli ancoraggi spaziali. Questa operazione viene in genere usata per le applicazioni in cui alcuni utenti curano l'ambiente, mentre altri possono richiamare solo gli ancoraggi precedentemente posizionati in quell'ambiente.

## <a name="next-steps"></a>Passaggi successivi

Creare la prima app con gli ancoraggi spaziali di Azure.

> [!div class="nextstepaction"]
> [Unity](../unity-overview.yml)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)
