---
title: "Esercitazione: Abilitare l'autenticazione in un'applicazione client nativa - Azure Active Directory B2C | Microsoft Docs"
description: Esercitazione su come usare Azure Active Directory B2C per consentire l'accesso degli utenti a un'applicazione desktop .NET.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 3df54c6805c5117e627afe0a2b4caa0ddd94b182
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64723711"
---
# <a name="tutorial-enable-authentication-in-a-native-client-application-using-azure-active-directory-b2c"></a>Esercitazione: Abilitare l'autenticazione in un'applicazione client nativa usando Azure Active Directory B2C

Questa esercitazione illustra come usare Azure Active Directory (Azure AD) B2C per l'iscrizione e l'accesso degli utenti a un'applicazione desktop Windows Presentation Foundation (WPF). Azure AD B2C consente alle applicazioni di eseguire l'autenticazione per account di social network, aziendali e di Azure Active Directory usando protocolli standard aperti.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiungere l'applicazione client nativa
> * Configurare l'esempio per l'uso dell'applicazione
> * Iscriversi usando il flusso utente

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

- [Creare flussi utente](tutorial-create-user-flows.md) per abilitare le esperienze utente nell'applicazione. 
- Installare [Visual Studio 2017](https://www.visualstudio.com/downloads/) con i carichi di lavoro **Sviluppo per desktop .NET** e **Sviluppo ASP.NET e Web**.

## <a name="add-the-native-client-application"></a>Aggiungere l'applicazione client nativa

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul **filtro delle directory e delle sottoscrizioni** nel menu in alto e scegliere la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
4. Selezionare **Applicazioni** e quindi **Aggiungi**.
5. Immettere un nome per l'applicazione. Ad esempio, *nativeapp1*.
6. Per **Includi app Web/API Web**, selezionare **No**.
7. Per **Includi client nativo**, selezionare **Sì**.
8. Per **URI di reindirizzamento**, immettere un URI di reindirizzamento valido con uno schema personalizzato. Quando si sceglie un URI di reindirizzamento, occorre tenere presenti due considerazioni importanti:

    - **Univocità**: lo schema dell'URI di reindirizzamento deve essere univoco per ogni applicazione. Nell'esempio `com.onmicrosoft.contoso.appname://redirect/path`, `com.onmicrosoft.contoso.appname` è lo schema. È necessario seguire questo modello. Se due applicazioni usano lo stesso schema, l'utente ha la possibilità di scegliere una delle due. Se la scelta dell'utente non è corretta, il tentativo di accesso ha esito negativo.
    - **Completezza**: l'URI di reindirizzamento deve avere uno schema e un percorso. Il percorso deve contenere almeno una barra rovesciata dopo il dominio. Ad esempio, `//contoso/` funziona e `//contoso` ha esito negativo. Verificare che l'URI di reindirizzamento non includa caratteri speciali, come quello di sottolineatura.

9. Fare clic su **Create**(Crea).
10. Nella pagina delle proprietà prendere nota dell'ID applicazione, che verrà usato per la configurazione dell'esempio.

## <a name="configure-the-sample"></a>Configurare l'esempio

In questa esercitazione si configura un esempio che è possibile scaricare da GitHub. L'applicazione desktop WPF di esempio dimostra l'iscrizione, l'accesso e la chiamata a un'API Web protetta in Azure AD B2C. [Scaricare un file ZIP](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip), [esplorare il repository](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) o clonare l'esempio da GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

Per cambiare le impostazioni dell'app, sostituire `<your-tenant-name>` con il nome del tenant e `<application-ID`> con l'ID applicazione registrato.

1. Aprire la soluzione `active-directory-b2c-wpf` in Visual Studio.
2. Nel progetto `active-directory-b2c-wpf` aprire il file **App.xaml.cs** ed eseguire gli aggiornamenti seguenti:

    ```C#
    private static string Tenant = "<your-tenant-name>.onmicrosoft.com";
    private static string ClientId = "<application-ID>";
    ```

3. Aggiornare la variabile **PolicySignUpSignIn** con il nome del flusso utente creato.

    ```C#
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>Eseguire l'esempio

Premere **F5** per compilare ed eseguire l'esempio.

### <a name="sign-up-using-an-email-address"></a>Iscriversi usando un indirizzo di posta elettronica

1. Fare clic su **Accedi** per iscriversi come utente. Verrà usato il flusso utente **B2C_1_signupsignin1**.
2. Azure AD B2C presenta una pagina di accesso con un collegamento di iscrizione. Non avendo ancora un account, fare clic sul collegamento **Iscriversi adesso**. 
3. Il flusso di lavoro per l'iscrizione presenta una pagina per raccogliere e verificare l'identità dell'utente usando un indirizzo e-mail. Il flusso di lavoro per l'iscrizione raccoglie anche la password dell'utente e gli attributi richiesti definiti nel flusso utente.

    Usare un indirizzo e-mail valido ed eseguire la convalida usando un codice di verifica. Impostare una password. Immettere i valori per gli attributi richiesti. 

    ![Flusso di lavoro di iscrizione](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. Fare clic su **Crea** per creare un account locale nel tenant di Azure AD B2C.

Ora l'utente può usare il proprio indirizzo e-mail per accedere e usare l'app desktop.

> [!NOTE]
> Se si fa clic sul pulsante **Call API (Chiama API)**, sarà visualizzato l'errore "Non autorizzato". L'errore viene visualizzato perché si sta tentando di accedere a una risorsa dal tenant dimostrativo. Poiché il token di accesso è valido solo per il tenant di Azure AD, la chiamata API non è autorizzata. Continuare con l'esercitazione successiva per la creazione di un'API Web protetta per il proprio tenant.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Aggiungere l'applicazione client nativa
> * Configurare l'esempio per l'uso dell'applicazione
> * Iscriversi usando il flusso utente

> [!div class="nextstepaction"]
> [Esercitazione: Concedere l'accesso a un'API Web Node.js da un'app desktop usando Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md)
