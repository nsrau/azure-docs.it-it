---
title: "Esercitazione: Autenticare gli utenti in un'applicazione client nativa - Azure Active Directory B2C"
description: Esercitazione su come usare Azure Active Directory B2C per consentire l'accesso degli utenti a un'applicazione desktop .NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 6d1c2eff9bc9ffe8a0fb2166d39c16360c0a79cc
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538932"
---
# <a name="tutorial-authenticate-users-in-a-native-desktop-client-using-azure-active-directory-b2c"></a>Esercitazione: Autenticare gli utenti in un client desktop nativo con Azure Active Directory B2C

Questa esercitazione illustra come usare Azure Active Directory B2C (Azure AD B2C) per l'iscrizione e l'accesso degli utenti a un'applicazione desktop Windows Presentation Foundation (WPF). Azure AD B2C consente alle applicazioni di eseguire l'autenticazione per account di social network, aziendali e di Azure Active Directory usando protocolli standard aperti.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiungere l'applicazione client nativa
> * Configurare l'esempio per l'uso dell'applicazione
> * Iscriversi usando il flusso utente

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

- [Creare flussi utente](tutorial-create-user-flows.md) per abilitare le esperienze utente nell'applicazione.
- Installare [Visual Studio 2019](https://www.visualstudio.com/downloads/) con i carichi di lavoro **Sviluppo per desktop .NET** e **Sviluppo ASP.NET e Web**.

## <a name="add-the-native-client-application"></a>Aggiungere l'applicazione client nativa

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Prendere nota del valore di **ID applicazione (client)** , che sarà necessario in un passaggio successivo.

## <a name="configure-the-sample"></a>Configurare l'esempio

In questa esercitazione si configura un esempio che è possibile scaricare da GitHub. L'applicazione desktop WPF di esempio illustra l'iscrizione e l'accesso ed è in grado di chiamare un'API Web protetta in Azure AD B2C. [Scaricare un file ZIP](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/msalv3.zip), [esplorare il repository](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) o clonare l'esempio da GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

Per aggiornare l'applicazione in modo che funzioni con il tenant di Azure AD B2C e richiami i flussi utente invece di quelli nel tenant predefinito della demo:

1. Aprire la soluzione **active-directory-b2c-wpf** (`active-directory-b2c-wpf.sln`) in Visual Studio.
2. Nel progetto **active-directory-b2c-wpf** aprire il file *App.xaml.cs* e trovare le definizioni delle variabili seguenti. Sostituire `{your-tenant-name}` con il nome del tenant di Azure AD B2C e `{application-ID}` con l'ID applicazione annotato in precedenza.

    ```csharp
    private static readonly string Tenant = "{your-tenant-name}.onmicrosoft.com";
    private static readonly string AzureAdB2CHostname = "{your-tenant-name}.b2clogin.com";
    private static readonly string ClientId = "{application-ID}";
    ```

3. Aggiornare le variabili dei nomi dei criteri con i nomi dei flussi utente creati per i prerequisiti. Ad esempio:

    ```csharp
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    public static string PolicyEditProfile = "B2C_1_profileediting1";
    public static string PolicyResetPassword = "B2C_1_passwordreset1";
    ```

## <a name="run-the-sample"></a>Eseguire l'esempio

Premere **F5** per compilare ed eseguire l'esempio.

### <a name="sign-up-using-an-email-address"></a>Iscriversi usando un indirizzo di posta elettronica

1. Selezionare **Accedi** per iscriversi come utente. Verrà usato il flusso utente **B2C_1_signupsignin1**.
2. Azure AD B2C presenta una pagina di accesso con un collegamento per **eseguire l'iscrizione**. Non avendo ancora un account, selezionare il collegamento **Iscriversi adesso**.
3. Il flusso di lavoro per l'iscrizione presenta una pagina per raccogliere e verificare l'identità dell'utente usando un indirizzo e-mail. Il flusso di lavoro per l'iscrizione raccoglie anche la password dell'utente e gli attributi richiesti definiti nel flusso utente.

    Usare un indirizzo e-mail valido ed eseguire la convalida usando un codice di verifica. Impostare una password. Immettere i valori per gli attributi richiesti.

    ![Pagina di iscrizione visualizzata nel flusso di lavoro di accesso/iscrizione](media/active-directory-b2c-tutorials-desktop-app/azure-ad-b2c-sign-up-workflow.png)

4. Selezionare **Crea** per creare un account locale nel tenant di Azure AD B2C.

L'utente può ora usare il proprio indirizzo di posta elettronica per accedere e usare l'applicazione desktop. Dopo l'iscrizione o l'accesso i dettagli del token vengono visualizzati nel riquadro inferiore dell'app WPF.

![Dettagli del token visualizzati nel riquadro inferiore dell'applicazione desktop WPF](media/active-directory-b2c-tutorials-desktop-app/desktop-app-01-post-signin.png)

Se si seleziona il pulsante dell'**API di chiamata**, viene visualizzato un **messaggio di errore**. L'errore si verifica perché, nello stato corrente, l'applicazione sta provando ad accedere a un'API protetta dal tenant della demo, ovvero `fabrikamb2c.onmicrosoft.com`. Poiché il token di accesso è valido solo per il tenant di Azure AD B2C, la chiamata API non è autorizzata.

Continuare con l'esercitazione successiva per registrare un'API Web protetta nel tenant e abilitare la funzionalità **Chiamata di API**.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Aggiungere l'applicazione client nativa
> * Configurare l'esempio per l'uso dell'applicazione
> * Iscriversi usando il flusso utente

A questo punto, per abilitare la funzionalità del pulsante **Chiamata di API**, concedere all'applicazione desktop WPF l'accesso a un'API Web registrata nel tenant di Azure AD B2C:

> [!div class="nextstepaction"]
> [Esercitazione: Concedere l'accesso a un'API Web Node.js da un'app desktop](active-directory-b2c-tutorials-desktop-app-webapi.md)
