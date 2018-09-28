---
title: Guida introduttiva della piattaforma UWP di Windows di Azure AD v2 | Microsoft Docs
description: Altre informazioni su come un'applicazione della piattaforma UWP (Universal Windows Platform) (XAML) può ottenere un token di accesso e chiamare un'API protetta da un endpoint di Azure Active Directory v2.0.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 5241df87297fb2e293e2cc828821e66d6f2837b0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46970825"
---
# <a name="call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Chiamare l'API Microsoft Graph da un'applicazione della piattaforma UWP (Universal Windows Platform)

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Questa guida introduttiva contiene un esempio di codice che illustra come un'applicazione Universal Windows Platform (UWP) può concedere l'accesso agli utenti con account personali, di lavoro o dell'istituto di istruzione, ottenere un token di accesso e chiamare l'API Graph di Microsoft.

![Funzionamento dell'app di esempio generata da questa Guida introduttiva](media/quickstart-v2-uwp/uwp-intro.png)

> [!div renderon="docs"]
> ## <a name="register-and-download"></a>Registrazione e download
> ### <a name="register-and-configure-your-application-and-code-sample"></a>Registrare e configurare l'applicazione e il codice di esempio
> #### <a name="step-1-register-your-application"></a>Passaggio 1: Registrare l'applicazione
> Per registrare l'applicazione e aggiungere le relative informazioni di registrazione alla soluzione, seguire questa procedura:
> 1. Passare al [portale di registrazione delle applicazioni Microsoft](https://apps.dev.microsoft.com/portal/register-app) per registrare un'applicazione.
> 1. Nella casella **Application Name** (Nome applicazione) immettere un nome per l'applicazione.
> 1. Verificare che la casella di controllo **Guided Setup** (Configurazione guidata) sia deselezionata e quindi selezionare **Create** (Crea).
> 1. Selezionare **Aggiungi piattaforma**, **Applicazione nativa** e quindi **Salva**.

> [!div renderon="portal" class="sxs-lookup alert alert-info"]
> #### <a name="step-1-configure-your-application"></a>Passaggio 1: Configurare l'applicazione
> Per l'esempio di codice di questa guida introduttiva è necessario aggiungere un URL di reindirizzamento come **urn:ietf:wg:oauth:2.0:oob**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporta questa modifica per me]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Già configurata](media/quickstart-v2-uwp/green-check.png) l'applicazione è configurata con questi attributi

#### <a name="step-2-download-your-visual-studio-project"></a>Passaggio 2: Scaricare il progetto di Visual Studio

 - [Scaricare il progetto di Visual Studio 2017](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/master.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Passaggio 3: Configurare il progetto di Visual Studio

1. Estrarre il file con estensione zip in una cartella locale, ad esempio **C:\Azure-Samples**.
1. Aprire il progetto in Visual Studio
1. Modificare **App.Xaml.cs** e sostituire la riga che inizia con `private static string ClientId` con:

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```

## <a name="more-information"></a>Altre informazioni

Di seguito una panoramica di questa guida introduttiva:

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) è la libreria usata per concedere l'accesso agli utenti e richiedere i token usati per accedere a un'API protetta da Microsoft Azure Active Directory. È possibile installarla eseguendo il comando seguente nella *Console di Gestione pacchetti* di Visual Studio:

```powershell
Install-Package Microsoft.Identity.Client -Pre
```

### <a name="msal-initialization"></a>Inizializzazione della libreria MSAL

È possibile aggiungere il riferimento per MSAL aggiungendo la riga seguente:

```csharp
using Microsoft.Identity.Client;
```

Quindi inizializzare MSAL usando la riga seguente:

```csharp
public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);
```

> |Dove: ||
> |---------|---------|
> |ClientId | L'ID dell'applicazione registrata in *portal.microsoft.com* |

### <a name="requesting-tokens"></a>Richiesta di token

In Msal sono disponibili due metodi per acquisire i token: `AcquireTokenAsync` e `AcquireTokenSilentAsync`:

#### <a name="get-a-user-token-interactively"></a>Ottenere un token utente in modo interattivo

 Alcune situazioni richiedono di forzare gli utenti a interagire con l'endpoint di Azure Active Directory v2 tramite una finestra popup per convalidare le proprie credenziali o concedere il consenso. Alcuni esempi includono:

- La prima volta che gli utenti accedono all'applicazione
- Potrebbe essere necessario che gli utenti reimmettano le proprie credenziali perché la password è scaduta
- L'applicazione richiede l'accesso a una risorsa per cui è necessario il consenso dell'utente
- È necessaria l'autenticazione a due fattori

```csharp
authResult = await App.PublicClientApp.AcquireTokenAsync(scopes);
```

> |Dove:||
> |---------|---------|
> |ambiti | Contiene gli ambiti richiesti (vale a dire `{ "user.read" }` per Microsoft Graph o `{ "api://<Application ID>/access_as_user" }` per le API Web personalizzate) |

#### <a name="get-a-user-token-silently"></a>Ottenere un token utente in modo automatico

Se non si desidera richiedere agli utenti di convalidare le proprie credenziali ogni volta che devono accedere a una risorsa e se si desidera principalmente che l'acquisizione e il rinnovo dei token avvenga senza alcuna interazione da parte degli utenti, `AcquireTokenSilentAsync` è il metodo comunemente usato per ottenere i token per accedere alle risorse protette dopo il metodo `AcquireTokenAsync` iniziale:

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
authResult = await App.PublicClientApp.AcquireTokenSilentAsync(scopes, accounts.FirstOrDefault());
```

> |Dove: ||
> |---------|---------|
> |ambiti | Contiene gli ambiti richiesti (vale a dire `{ "user.read" }` per Microsoft Graph o `{ "api://<Application ID>/access_as_user" }` per le API Web personalizzate) |
> |accounts.FirstOrDefault() | Il primo utente nella cache (MSAL supporta più utenti in una singola app) |

## <a name="next-steps"></a>Passaggi successivi

Provare l'esercitazione Desktop di Windows per una guida dettagliata completa sulla creazione di applicazioni e di nuove funzionalità, tra cui una spiegazione completa di questa guida introduttiva:

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Informazioni sulla procedura per creare l'applicazione usata in questa guida introduttiva

> [!div class="nextstepaction"]
> [Piattaforma UWP (Universal Windows Platform): esercitazione sulla chiamata dell'API Graph](tutorial-v2-windows-uwp.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]