---
title: Autenticazione e autorizzazione
description: Informazioni sui vari modi in cui un'app o un servizio è in grado di eseguire l'autenticazione a ancoraggi spaziali di Azure e i livelli di controllo di cui è necessario controllare l'accesso agli ancoraggi spaziali.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 10/08/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: a3d88c8d5d42e3dec2142df1ede7a9ee50898e92
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93242348"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Autenticazione e autorizzazione per Ancoraggi nello spazio di Azure

Questo articolo illustra i diversi modi in cui è possibile eseguire l'autenticazione a ancoraggi spaziali di Azure dall'app o dal servizio Web. Verranno inoltre fornite informazioni sui modi in cui è possibile utilizzare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) in Azure Active Directory (Azure AD) per controllare l'accesso agli account di ancoraggio spaziali.

## <a name="overview"></a>Panoramica

![Diagramma che mostra una panoramica dell'autenticazione per gli ancoraggi spaziali di Azure.](./media/spatial-anchors-authentication-overview.png)

Per accedere a uno specifico account di Ancoraggi nello spazio di Azure, i client devono prima ottenere un token di accesso dal servizio token di sicurezza (STS) di Realtà mista di Azure. I token ottenuti da STS hanno una durata di 24 ore. Contengono informazioni che i servizi di ancoraggio spaziali utilizzano per prendere decisioni di autorizzazione per l'account e assicurarsi che solo le entità autorizzate possano accedere all'account.

È possibile ottenere i token di accesso in Exchange per i token o le chiavi dell'account rilasciati da Azure AD.

Le chiavi dell'account consentono di iniziare rapidamente a usare il servizio di ancoraggio spaziale di Azure. Tuttavia, prima di distribuire l'applicazione in produzione, è consigliabile aggiornare l'app per usare l'autenticazione Azure AD.

È possibile ottenere Azure AD token di autenticazione in due modi:

- Se si sta creando un'applicazione aziendale e l'azienda USA Azure AD come sistema di identità, è possibile usare l'autenticazione Azure AD basata sull'utente nell'app. Si concede quindi l'accesso agli account di ancoraggio spaziali usando i gruppi di sicurezza Azure AD esistenti. È anche possibile concedere l'accesso direttamente agli utenti dell'organizzazione.
- In caso contrario, è consigliabile ottenere Azure AD token da un servizio Web che supporta l'app. Questo metodo è consigliato per le applicazioni di produzione perché consente di evitare di incorporare le credenziali per l'accesso agli ancoraggi spaziali di Azure nell'applicazione client.

## <a name="account-keys"></a>Chiavi dell'account

Il modo più semplice per iniziare consiste nell'usare le chiavi dell'account per l'accesso all'account degli ancoraggi spaziali di Azure. È possibile ottenere le chiavi dell'account nella portale di Azure. Passare all'account e selezionare la scheda **chiavi** :

![Screenshot che mostra la scheda chiavi con il pulsante copia per la chiave primaria evidenziata.](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)

Sono disponibili due chiavi. Entrambi sono contemporaneamente validi per l'accesso all'account di ancoraggi spaziali. Si consiglia di aggiornare regolarmente la chiave usata per accedere all'account. La presenza di due chiavi valide separate Abilita questi aggiornamenti senza tempi di inattività. È necessario solo aggiornare la chiave primaria e la chiave secondaria in alternativa.

L'SDK include il supporto predefinito per l'autenticazione tramite chiavi dell'account. È sufficiente impostare la `AccountKey` proprietà sull' `cloudSession` oggetto:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

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

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

Dopo aver impostato questa proprietà, l'SDK gestirà lo scambio della chiave dell'account per un token di accesso e la memorizzazione nella cache necessaria per i token per l'app.

> [!WARNING]
> Si consiglia di usare le chiavi dell'account per il caricamento rapido, ma solo durante lo sviluppo e la creazione di prototipi. Non è consigliabile distribuire l'applicazione in produzione con una chiave di account incorporata. Usare invece gli approcci di autenticazione Azure AD basati sull'utente o sui servizi descritti di seguito.

## <a name="azure-ad-user-authentication"></a>Autenticazione utente di Azure AD

Per le applicazioni destinate Azure Active Directory utenti, si consiglia di usare un token Azure AD per l'utente. È possibile ottenere questo token usando [MSAL](../../active-directory/develop/msal-overview.md). Seguire i passaggi nella [Guida introduttiva sulla registrazione di un'app](../../active-directory/develop/quickstart-register-app.md), che include:

**Nel portale di Azure**
1.    Registrare l'applicazione in Azure AD come applicazione nativa. Nell'ambito della registrazione, è necessario determinare se l'applicazione deve essere multi-tenant. Sarà inoltre necessario specificare gli URL di reindirizzamento consentiti per l'applicazione.
1.  Passare alla scheda **autorizzazioni API** .
2.  Selezionare **Aggiungi un'autorizzazione** .
    1.  Selezionare **provider di risorse realtà mista** nelle **API utilizzate dall'organizzazione** .
    2.  Selezionare **Autorizzazioni delegate** .
    3.  Selezionare **mixedreality. SignIn** in **mixedreality** .
    4.  Selezionare **Aggiungi autorizzazioni** .
3.  Selezionare **concedi il consenso dell'amministratore** .

2. Concedere all'applicazione o agli utenti l'accesso alla risorsa:
   1.    Passare alla risorsa ancoraggi spaziali nella portale di Azure.
   2.    Passare alla scheda **controllo di accesso (IAM)** .
   3.    Selezionare **Aggiungi un'assegnazione di ruolo** .
   1.    [Selezionare un ruolo](#azure-role-based-access-control).
   2.    Nella casella **Seleziona** immettere i nomi degli utenti, i gruppi e/o le applicazioni a cui si desidera assegnare l'accesso.
   3.    Selezionare **Salva** .

**Nel codice**
1.    Assicurarsi di usare l'ID applicazione e l'URI di reindirizzamento dell'applicazione Azure AD per i parametri **ID client** e **RedirectUri** in MSAL.
2.    Impostare le informazioni relative al tenant:
        1.    Se l'applicazione supporta **solo l'organizzazione** , sostituire questo valore con l' **ID tenant** o il **nome del tenant** . Ad esempio, contoso.microsoft.com.
        2.    Se l'applicazione supporta gli **account in qualsiasi directory organizzativa** , sostituire questo valore con le **organizzazioni** .
        3.    Se l'applicazione supporta **tutti gli utenti account Microsoft** , sostituire questo valore con **comune** .
3.    Nella richiesta di token impostare l' **ambito** su **" `https://sts.<account-domain>//.default` "** , dove `<account-domain>` viene sostituito con il **dominio** dell'account per l'account degli ancoraggi spaziali di Azure. Un ambito di esempio per un account di ancoraggio spaziale di Azure nel dominio dell'account Stati Uniti orientali 2 è **" `https://sts.mixedreality.azure.com//.default` "** . Questo ambito indicherà ad Azure AD che l'applicazione richiede un token per il servizio token di sicurezza (STS) di Realtà mista.

Dopo aver completato questi passaggi, l'applicazione deve essere in grado di ottenere da MSAL un token di Azure AD. È possibile impostare il token Azure AD come nell' `authenticationToken` oggetto di configurazione della sessione cloud:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

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

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Autenticazione per il servizio Azure AD

Per distribuire le app che usano gli ancoraggi spaziali di Azure nell'ambiente di produzione, è consigliabile usare un servizio back-end che effettuerà il broker delle richieste di autenticazione. Ecco una panoramica del processo:

![Diagramma che fornisce una panoramica dell'autenticazione a ancoraggi spaziali di Azure.](./media/spatial-anchors-aad-authentication.png)

In questo caso si presuppone che l'app usi un proprio meccanismo per l'autenticazione nel servizio back-end. (Ad esempio, un account Microsoft, PlayFab, Facebook, un ID Google o un nome utente e una password personalizzati).  Dopo l'autenticazione degli utenti nel servizio back-end, il servizio può recuperare un token di Azure AD, scambiarlo per un token di accesso per gli ancoraggi spaziali di Azure e restituirlo all'applicazione client.

Il token di accesso Azure AD viene recuperato tramite [MSAL](../../active-directory/develop/msal-overview.md). Seguire i passaggi nella [Guida introduttiva per la registrazione di un'app](../../active-directory/develop/quickstart-register-app.md), che include:

**Nel portale di Azure**
1.    Registrare l'applicazione in Azure AD:
        1.    Nella portale di Azure selezionare **Azure Active Directory** , quindi selezionare **registrazioni app** .
        2.    Selezionare **Nuova registrazione** .
        3.    Immettere il nome dell'applicazione, impostare il tipo di applicazione su **App Web/API** e immettere l'URL di autenticazione per il servizio. Selezionare **Crea** .
2.    Nell'applicazione selezionare **Impostazioni** , quindi selezionare la scheda **certificati e segreti** . Creare un nuovo segreto client, selezionare una durata, quindi selezionare **Aggiungi** . Assicurarsi di salvare il valore del segreto. È necessario includerlo nel codice del servizio Web.
3.    Concedere all'applicazione e/o agli utenti l'accesso alla risorsa:
        1.    Passare alla risorsa ancoraggi spaziali nella portale di Azure.
        2.    Passare alla scheda **controllo di accesso (IAM)** .
        3.    Selezionare **Aggiungi un'assegnazione di ruolo** .
        4.    [Selezionare un ruolo](#azure-role-based-access-control).
        5.    Nella casella **Seleziona** immettere il nome o i nomi delle applicazioni a cui si desidera assegnare l'accesso. Se si vuole che gli utenti dell'app abbiano ruoli diversi rispetto all'account degli ancoraggi spaziali, registrare più applicazioni nel Azure AD e assegnare un ruolo separato a ciascuna di esse. Quindi implementare la logica di autorizzazione in modo che venga usato il ruolo degli utenti corretto.

              > [!NOTE]
              > Nel riquadro **Aggiungi assegnazione ruolo** , in **assegna accesso a** Selezionare **Azure ad utente, gruppo o entità servizio** .

        6.    Selezionare **Salva** .

**Nel codice**

>[!NOTE]
> È possibile usare l'esempio di servizio disponibile su GitHub.

1.    Assicurarsi di usare l'ID applicazione, il segreto applicazione e l'URI di reindirizzamento dell'applicazione Azure AD come i parametri **ID client** , **segreto** e **RedirectUri** in MSAL.
2.    Impostare l'ID tenant sul proprio Azure AD ID tenant nel parametro **Authority** in MSAL.
3.    Nella richiesta di token impostare l' **ambito** su **" `https://sts.<account-domain>//.default` "** , dove `<account-domain>` viene sostituito con il **dominio** dell'account per l'account degli ancoraggi spaziali di Azure. Un ambito di esempio per un account di ancoraggio spaziale di Azure nel dominio dell'account Stati Uniti orientali 2 è **" `https://sts.mixedreality.azure.com//.default` "** .

Dopo aver completato questi passaggi, il servizio back-end può recuperare un token di Azure AD. Il token potrà quindi essere scambiato con un token MR che verrà restituito al client. Il recupero di un token MR tramite un token di Azure AD viene eseguito tramite una chiamata REST. Ecco una chiamata di esempio:

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

L'intestazione Authorization è formattata come indicato di seguito: `Bearer <Azure_AD_token>`

La risposta contiene il token MR in testo normale.

Il token MR viene restituito al client. L'app client può quindi impostarla come token di accesso nella configurazione di sessione cloud:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

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

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="azure-role-based-access-control"></a>Controllo degli accessi in base al ruolo di Azure

Per consentire di controllare il livello di accesso concesso a applicazioni, servizi o Azure AD utenti del servizio, è possibile assegnare questi ruoli preesistenti in base alle esigenze per gli account di ancoraggio spaziali di Azure:

- **Proprietario dell'account di ancoraggi spaziali** . Le applicazioni o gli utenti che dispongono di questo ruolo possono creare ancoraggi spaziali, eseguire query per tali elementi ed eliminarli. Quando si esegue l'autenticazione all'account usando le chiavi dell'account, il ruolo di proprietario dell'account di ancoraggi spaziali viene assegnato all'entità autenticata.
- **Collaboratore account di ancoraggi spaziali** . Le applicazioni o gli utenti che dispongono di questo ruolo possono creare ancoraggi spaziali ed eseguire query su di essi, ma non possono eliminarli.
- **Lettore di account di ancoraggi spaziali** . Le applicazioni o gli utenti con questo ruolo possono eseguire query solo per gli ancoraggi spaziali. Non possono crearne di nuovi, eliminare quelli esistenti o aggiornarne i metadati. Questo ruolo viene in genere usato per le applicazioni in cui alcuni utenti curano l'ambiente, ma altri possono richiamare solo gli ancoraggi precedentemente posizionati nell'ambiente.

## <a name="next-steps"></a>Passaggi successivi

Creare la prima app con gli ancoraggi spaziali di Azure:

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
