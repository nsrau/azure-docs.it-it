---
title: Autenticazione e autorizzazione
description: Informazioni sui diversi modi di eseguire l'autenticazione per Ancoraggi nello spazio di Azure usando un'app o un servizio e sui livelli di controllo disponibili per limitare l'accesso ad Ancoraggi nello spazio di Azure.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: e4d25637498bec223e294eecf2be6dc88fa2aa0d
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88997167"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Autenticazione e autorizzazione per Ancoraggi nello spazio di Azure

Questa sezione descrive sia i diversi modi in cui è possibile eseguire l'autenticazione per Ancoraggi nello spazio di Azure usando l'app o il servizio Web, sia i modi in cui è possibile usare il controllo degli accessi in base al ruolo di Azure Directory (Azure AD) per limitare l'accesso agli account di Ancoraggi nello spazio.

## <a name="overview"></a>Panoramica

![Panoramica sull'autenticazione per Ancoraggi nello spazio di Azure](./media/spatial-anchors-authentication-overview.png)

Per accedere a uno specifico account di Ancoraggi nello spazio di Azure, i client devono prima ottenere un token di accesso dal servizio token di sicurezza (STS) di Realtà mista di Azure. I token rilasciati dal servizio STS Live sono validi per 24 ore e contengono informazioni su cui si basano i servizi di Ancoraggi nello spazio per prendere decisioni sulle autorizzazioni relative all'account e garantire che solo le entità autorizzate possano accedere a tale account.

I token di accesso possono essere ottenuti in Exchange da chiavi dell'account o da token rilasciati da Azure AD.

Le chiavi dell'account consentono di iniziare a usare il servizio Ancoraggi spaziali di Azure rapidamente. Prima di distribuire l'applicazione in produzione, è tuttavia consigliabile aggiornare l'app in modo che usi l'autenticazione basata su Azure AD.

È possibile ottenere i token di autenticazione di Azure AD nei due modi seguenti:

- Se si sta compilando un'applicazione aziendale e la propria azienda usa Azure AD come sistema di identità, è possibile usare l'autenticazione di Azure AD basata sull'utente nell'app e concedere l'accesso agli account di Ancoraggi nello spazio tramite gruppi di sicurezza Azure AD già esistenti o direttamente agli utenti della propria organizzazione.
- In alternativa, è consigliabile ottenere i token di Azure AD da un servizio Web che supporta l'app. Per l'autenticazione relativa ad applicazioni di produzione, è consigliabile usare un servizio Web per evitare di dover incorporare le credenziali per l'accesso ad Ancoraggi nello spazio di Azure nell'applicazione client.

## <a name="account-keys"></a>Chiavi dell'account

Il modo più semplice per iniziare è usare le chiavi dell'account per accedere all'account di Ancoraggi nello spazio di Azure. Le chiavi dell'account sono disponibili nel portale di Azure. Accedere all'account e selezionare la scheda "Chiavi".

![Panoramica sull'autenticazione per Ancoraggi nello spazio di Azure](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)

Le chiavi disponibili sono due e per l'accesso all'account di Ancoraggi nello spazio sono valide entrambe, l'una contemporaneamente all'altra. È consigliabile aggiornare regolarmente la chiave usata per accedere all'account. La disponibilità di due chiavi valide separate consente di eseguire gli aggiornamenti delle chiavi senza incorrere in tempi di inattività, perché la chiave primaria e quella secondaria possono essere aggiornate l'una alternativamente all'altra.

L'SDK include il supporto predefinito per eseguire l'autenticazione con le chiavi dell'account. È sufficiente impostare la proprietà AccountKey nell'oggetto cloudSession.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndk"></a>[C++/NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

Al termine dell'operazione, l'SDK gestirà lo scambio della chiave dell'account con un token di accesso ed eseguirà la memorizzazione nella cache dei token necessari per l'app.

> [!WARNING]
> È consigliabile usare le chiavi dell'account per velocizzare l'onboarding, ma solo durante lo sviluppo e la creazione di prototipi. Per la spedizione dell'applicazione in produzione, anziché una chiave dell'account incorporata è fortemente opportuno usare gli approcci di autenticazione di Azure AD basati sull'utente o su un servizio. Tali approcci sono elencati di seguito.

## <a name="azure-ad-user-authentication"></a>Autenticazione utente di Azure AD

Per le applicazioni destinate agli utenti di Azure Active Directory, l'approccio consigliato consiste nell'usare un token Azure AD per l'utente. Il token può essere ottenuto tramite la [libreria MSAL](../../active-directory/develop/msal-overview.md). Seguire la procedura illustrata nell'argomento [Registrare un'applicazione](../../active-directory/develop/quickstart-register-app.md) della guida introduttiva, che prevede i passaggi seguenti:

1. Eseguire la configurazione nel portale di Azure
    1.  Registrare l'applicazione in Azure AD come **applicazione nativa**. Come parte della registrazione sarà necessario indicare se l'applicazione dovrà essere multi-tenant o meno e specificare gli URL di reindirizzamento consentiti per l'applicazione.
        1.  Passare alla scheda **Autorizzazioni API**
        2.  Selezionare **Aggiungi un'autorizzazione**
            1.  Nella scheda **API usate dall'organizzazione** selezionare **Realtà mista**
            2.  Selezionare **Autorizzazioni delegate**
            3.  In **mixedreality** selezionare la casella **mixedreality.signin**
            4.  Selezionare **Aggiungi autorizzazioni**
        3.  Selezionare **Concedi consenso amministratore**
    2.  Concedere all'applicazione o agli utenti l'accesso alla risorsa:
        1.  Passare alla risorsa di Ancoraggi nello spazio nel portale di Azure
        2.  Passare alla scheda **Controllo di accesso (IAM)**
        3.  Fare clic su **Aggiungi un'assegnazione di ruolo**
            1.  [Selezionare un ruolo](#role-based-access-control)
            2.  Nel campo **Seleziona** immettere i nomi degli utenti, dei gruppi e/o delle applicazioni a cui si desidera assegnare l'accesso.
            3.  Premere **Salva**.
2. Nel codice:
    1.  Usare l'**ID applicazione** e l'**URI di reindirizzamento** dell'applicazione di Azure AD come parametri **ID client** e **RedirectUri** in MSAL
    2.  Impostare le informazioni relative al tenant:
        1.  Se l'applicazione supporta **Solo l'organizzazione personale**, sostituire questo valore con l'**ID tenant** o il **nome del tenant** (ad esempio, contoso.microsoft.com)
        2.  Se l'applicazione supporta **Account in qualsiasi directory organizzativa**, sostituire questo valore con **Organizzazioni**
        3.  Se l'applicazione supporta **Tutti gli utenti di account Microsoft**, sostituire questo valore con **Comuni**
    3.  Nella richiesta di token impostare l'**ambito** su "https://sts.mixedreality.azure.com//.default". Questo ambito indicherà ad Azure AD che l'applicazione richiede un token per il servizio token di sicurezza (STS) di Realtà mista.

In questo modo, l'applicazione dovrebbe essere in grado di ottenere dalla libreria MSAL un token di Azure AD. È possibile impostare tale token di Azure AD come **authenticationToken** nell'oggetto config della sessione cloud.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndk"></a>[C++/NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Autenticazione per il servizio Azure AD

Per la distribuzione di app che usano Ancoraggi nello spazio di Azure in produzione è consigliabile usare un servizio back-end in grado di eseguire il broker delle richieste di autenticazione. Lo schema generale dovrebbe corrispondere a quello descritto nel diagramma seguente:

![Panoramica sull'autenticazione per Ancoraggi nello spazio di Azure](./media/spatial-anchors-aad-authentication.png)

In questo schema si presuppone che l'app esegua l'autenticazione per il servizio back-end usando un meccanismo proprio, ad esempio account Microsoft, PlayFab, Facebook, ID Google, nome utente e password personalizzati e così via. Dopo che gli utenti si sono autenticati per il servizio back-end, tale servizio potrà recuperare un token di Azure AD, scambiarlo con un token di accesso per Ancoraggio nello spazio di Azure e restituirlo all'applicazione client.

Il token di accesso di Azure AD viene recuperato tramite la [libreria MSAL](../../active-directory/develop/msal-overview.md). Seguire la procedura illustrata nell'argomento [Registrare un'applicazione](../../active-directory/develop/quickstart-register-app.md) della guida introduttiva, che prevede i passaggi seguenti:

1.  Eseguire la configurazione nel portale di Azure:
    1.  Registrare l'applicazione in Azure AD:
        1.  Nel portale di Azure selezionare **Azure Active Directory** e quindi **Registrazioni app**
        2.  Selezionare **Registrazione nuova applicazione**
        3.  Immettere il nome dell'applicazione, impostare il tipo di applicazione su **App Web/API** e immettere l'URL di autenticazione per il servizio. Quindi fare clic su **Crea**.
        4.  In tale applicazione, fare clic su **Impostazioni**, quindi selezionare la scheda **certificati e segreti** . creare un nuovo segreto client, selezionare una durata e fare clic su **Aggiungi**. Assicurarsi di salvare il valore del segreto, perché sarà necessario includerlo nel codice del servizio Web.
    2.  Concedere all'applicazione e/o agli utenti l'accesso alla risorsa:
        1.  Accedere alla risorsa di Ancoraggi nello spazio nel portale di Azure
        2.  Passare alla scheda **Controllo di accesso (IAM)**
        3.  Fare clic su **Aggiungi un'assegnazione di ruolo**
        1.  [Selezionare un ruolo](#role-based-access-control)
        2.  Nel campo **Seleziona** immettere i nomi delle applicazioni create e alle quali si desidera assegnare l'accesso. Se si desidera che gli utenti dell'app dispongano di ruoli diversi rispetto all'account di Ancoraggi nello spazio, è necessario registrare più applicazioni in Azure AD e assegnare un ruolo distinto a ognuno. Quindi implementare la logica di autorizzazione in modo che venga usato il ruolo degli utenti corretto.
        3.  Nota: nella selezione **Aggiungi assegnazione ruolo** si vuole che l'opzione **assegna accesso** a sia impostata su "Azure ad utente, gruppo o entità servizio".
    3.  Premere **Salva**.
2.  Nel codice (si noti che è possibile usare l'esempio di servizio incluso in GitHub):
    1.  Usare l'ID, il segreto e l'URI di reindirizzamento dell'applicazione di Azure AD come parametri ID client, segreto e RedirectUri in MSAL
    2.  Impostare l'ID tenant sull'ID tenant di Azure AD specificato nel parametro relativo all'autorità in MSAL.
    3.  Nella richiesta del token impostare l'**ambito** su "https://sts.mixedreality.azure.com//.default"

In questo modo il servizio back-end potrà recuperare un token di Azure AD. Il token potrà quindi essere scambiato con un token MR che verrà restituito al client. Il recupero di un token MR tramite un token di Azure AD viene eseguito tramite una chiamata REST. Di seguito è riportata una chiamata di esempio:

```
GET https://sts.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: sts.mixedreality.azure.com
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

In questa chiamata l'intestazione dell'autorizzazione è formattata nel modo seguente: `Bearer <Azure_AD_token>`.

La risposta include un token MR in testo normale.

Il token MR viene restituito al client. L'app client può quindi impostarlo come token di accesso nell'oggetto config della sessione cloud.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndk"></a>[C++/NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Per consentire il controllo del livello di accesso concesso alle applicazioni, ai servizi o agli utenti di Azure AD del servizio, sono stati creati i ruoli descritti di seguito, che consentono di eseguire l'assegnazione in base alle esigenze correlate all'account di Ancoraggi nello spazio.

- **Proprietario dell'account per gli ancoraggi spaziali**: le applicazioni o gli utenti con questo ruolo possono creare ancoraggi nello spazio, eseguire query per tali ancoraggi ed eliminarli. Quando si esegue l'autenticazione per il proprio account usando le chiavi dell'account, il ruolo di **proprietario dell'account per gli ancoraggi spaziali** viene assegnato all'entità autenticata.
- **Collaboratore per l'account per gli ancoraggi spaziali**: le applicazioni o gli utenti con questo ruolo possono creare ancoraggi nello spazio ed eseguire query per tali ancoraggi, ma non possono eliminarli.
- **Ruolo Lettore dell'account per gli ancoraggi spaziali**: le applicazioni o gli utenti con questo ruolo possono solo eseguire query per ancoraggi nello spazio già esistenti. Non possono creare ancoraggi nuovi, né eliminare quelli esistenti, né aggiornare i relativi metadati. Questo ruolo viene in genere usato per applicazioni in cui alcuni utenti gestiscono l'ambiente e altri possono solo richiamare gli ancoraggi precedentemente posizionati in tali ambienti.

## <a name="next-steps"></a>Passaggi successivi

Creare la prima app con Ancoraggi nello spazio di Azure.

> [!div class="nextstepaction"]
> [Unity (HoloLens)](../quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity (iOS)](../quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unity (Android)](../quickstarts/get-started-unity-android.md)

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
