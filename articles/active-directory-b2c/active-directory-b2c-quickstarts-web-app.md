---
title: Eseguire il test drive di un'applicazione Web di Azure AD B2C | Microsoft Docs
description: Accedere al test drive, effettuare la registrazione, modificare il profilo e reimpostare i percorsi utente password tramite un ambiente di test di Azure AD B2C
services: active-directory-b2c
documentationcenter: .net
author: saraford
manager: krassk
editor: PatAltimore
ms.assetid: 2ffb780d-2c51-4c2e-b8d6-39c40a81a77e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/31/2017
ms.author: saraford
ms.openlocfilehash: 2a51f15fd38a901548dcf4c56922f9715c328463
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2017
---
# <a name="test-drive-a-web-application-configured-with-azure-ad-b2c"></a>Eseguire il test drive di un'applicazione Web configurata con Azure AD B2C

Azure Active Directory B2C consente la gestione delle identità del cloud per garantire la protezione costante dell'applicazione, delle attività aziendali e dei clienti.  Questa guida introduttiva usa un'app di elenco attività di esempio per illustrare:

* L'uso del criterio **Iscrizione o accesso** per creare o accedere con un provider di identità basato su social network o un account locale tramite un indirizzo di posta elettronica. 
* Una chiamata a un'API protetta da parte di Azure AD B2C per creare e modificare gli elementi elenco attività.

## <a name="prerequisites"></a>Prerequisiti

* Installare [Visual Studio 2017](https://www.visualstudio.com/downloads/) con i carichi di lavoro seguenti:
    - **Sviluppo Web e ASP.NET**

* Un account di social networking di Facebook, Google, Microsoft o Twitter. Se non si ha un account di social networking, è necessario un indirizzo di posta elettronica valido.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Scaricare l'esempio

[Scaricare o clonare l'applicazione di esempio](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi) da GitHub.

## <a name="run-the-app-in-visual-studio"></a>Eseguire l'app in Visual Studio

Nella cartella del progetto dell'applicazione di esempio aprire la soluzione `B2C-WebAPI-DotNet.sln` in Visual Studio. 

La soluzione è costituita da due progetti:

* **TaskWebApp**: applicazione Web di ASP.NET MVC in cui l'utente può gestire gli elementi elenco attività.  
* **TaskService**: back-end di un'API Web ASP.NET che gestisce tutte le operazioni CRUD eseguite sugli elementi elenco attività dell'utente. L'app Web chiama l'API e visualizza i risultati.

Per questa guida introduttiva è necessario eseguire entrambi i progetti `TaskWebApp` e `TaskService` contemporaneamente. 

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e selezionare **Imposta progetti di avvio...**. 
2. Selezionare il pulsante di opzione **Progetti di avvio multipli**.
3. Modificare il valore di **Azione** in entrambi i progetti in **Avvia**. Fare clic su **OK**.

![Impostare la pagina di avvio in Visual Studio](media/active-directory-b2c-quickstarts-web-app/setup-startup-projects.png)

Selezionare **Debug > Avvia debug** per compilare ed eseguire entrambe le applicazioni. Ogni applicazione viene visualizzata in una scheda del browser separata:

* `https://localhost:44316/`: questa pagina è l'applicazione Web ASP.NET. Nella guida introduttiva si interagisce direttamente con l'applicazione.
* `https://localhost:44332/`: questa pagina è l'API Web chiamata dall'applicazione Web ASP.NET.

## <a name="create-an-account"></a>Creare un account

Fare clic sul collegamento **Sign up / Sign in** (Iscrizione/Accesso) nell'applicazione Web ASP.NET per avviare il flusso di lavoro**Iscrizione o accesso**. Quando si crea un account, è possibile usare un account esistente di un provider di identità basato su social network o un account di posta elettronica.

![App Web ASP.NET di esempio](media/active-directory-b2c-quickstarts-web-app/web-app-sign-in.png)

### <a name="sign-up-using-a-social-identity-provider"></a>Iscriversi usando un provider di identità basato su social network

Per iscriversi usando un provider di identità basato su social network, fare clic sul pulsante del provider di identità che si vuole usare. Se si preferisce usare un indirizzo di posta elettronica, passare alla sezione [Iscriversi usando un indirizzo di posta elettronica](#sign-up-using-an-email-address).

![Provider di accesso o di iscrizione](media/active-directory-b2c-quickstarts-web-app/sign-in-or-sign-up-web.png)

Per leggere le informazioni dell'account di social networking, è necessario eseguire l'autenticazione (accesso) tramite le credenziali di tale account e autorizzare l'applicazione. Dopo la concessione dell'accesso, l'applicazione può recuperare le informazioni sul profilo dall'account, ad esempio il nome e la città dell'utente. 

![Eseguire l'autenticazione e l'autorizzazione tramite un account di social networking](media/active-directory-b2c-quickstarts-web-app/twitter-authenticate-authorize-web.png)

Completare il processo di accesso per il provider di identità. Ad esempio, fare clic sul pulsante **Accedi** per Twitter.

I dettagli del nuovo profilo dell'account sono già popolati con informazioni derivate dall'account di social networking.

![Dettagli del profilo di iscrizione del nuovo account](media/active-directory-b2c-quickstarts-web-app/new-account-sign-up-profile-details-web.png)

Aggiornare i campi Nome visualizzato, Posizione e Città e fare clic su **Continua**.  I valori immessi vengono usati per il profilo dell'account utente di Azure AD B2C.

È stato creato un nuovo account utente di Azure AD B2C che usa un provider di identità. 

Passaggio successivo: sezione [Visualizzare le attestazioni](#view-your-claims).

### <a name="sign-up-using-an-email-address"></a>Iscriversi usando un indirizzo di posta elettronica

Se si sceglie di non usare un account di social networking per l'autenticazione, è possibile creare un account utente di Azure AD B2C usando un indirizzo di posta elettronica valido. Un account utente locale di Azure AD B2C usa Azure Active Directory come provider di identità. Per usare l'indirizzo di posta elettronica, fare clic sul collegamento **Non si ha un account? Iscriversi adesso**.

![Accedere o iscriversi tramite indirizzo di posta elettronica](media/active-directory-b2c-quickstarts-web-app/sign-in-or-sign-up-email-web.png)

Immettere un indirizzo di posta elettronica valido e fare clic su **Invia codice di verifica**. Per ricevere il codice di verifica da Azure AD B2C, è necessario un indirizzo di posta elettronica valido. 

Immettere il codice di verifica ricevuto nel messaggio di posta elettronica e fare clic su **Verifica codice**.

Aggiungere le informazioni sul profilo e fare clic su **Crea**.

![Iscriversi con il nuovo account tramite posta elettronica](media/active-directory-b2c-quickstarts-web-app/sign-up-new-account-profile-email-web.png)

È stato creato un nuovo account utente locale di Azure AD B2C.

## <a name="reset-your-password"></a>Reimpostare la password

Se l'account è stato creato usando un indirizzo di posta elettronica, Azure AD B2C offre la funzionalità che consente agli utenti di reimpostare la password. Per modificare il profilo creato, fare clic sul nome del profilo sulla barra dei menu e selezionare **Reimposta password**.

Per verificare l'indirizzo di posta elettronica, immetterlo e fare clic su **Invia codice di verifica**. Un codice di verifica viene inviato all'indirizzo di posta elettronica specificato.

Immettere il codice di verifica ricevuto nel messaggio di posta elettronica e fare clic su **Verifica codice**.

Dopo aver verificato l'indirizzo di posta elettronica, fare clic su **Continua**.

Immettere la nuova password e fare clic su **Continua**.

## <a name="view-your-claims"></a>Visualizzare le attestazioni

Fare clic su **Attestazioni** sulla barra dei menu dell'applicazione Web per visualizzare le attestazioni associate all'ultima azione. 

![Iscriversi con il nuovo account tramite posta elettronica](media/active-directory-b2c-quickstarts-web-app/view-claims-sign-up-web.png)

In questo esempio l'ultima azione è stata quella relativa all'esperienza di *accesso o iscrizione*. Si noti che il **tipo di attestazione** `http://schemas.microsoft.com/claims/authnclassreference` è `b2c_1_susi` che indica che l'ultima azione è stata l'iscrizione o l'accesso. Se l'ultima azione fosse stata la reimpostazione della password, il **tipo di attestazione** sarebbe `b2c_1_reset`.

## <a name="edit-your-profile"></a>Modificare il profilo

Azure Active Directory B2C offre funzionalità che consentono agli utenti di aggiornare il profilo. Sulla barra dei menu dell'applicazione Web fare clic sul nome del profilo e selezionare **Modifica profilo** per modificare il profilo creato.

![Modificare il profilo](media/active-directory-b2c-quickstarts-web-app/edit-profile-web.png)

Modificare **Nome visualizzato** e **Città**.  Fare clic su **Continua** per aggiornare il profilo.

![Update profile (Aggiorna profilo)](media/active-directory-b2c-quickstarts-web-app/update-profile-web.png)

Si notino gli aggiornamenti del nome visualizzato nella parte superiore destra della pagina dopo la modifica del nome. 

Fare clic su **Attestazioni**. Le modifiche apportate a **Nome visualizzato** e a **Città** si riflettono sulle attestazioni.

![Visualizzare le attestazioni](media/active-directory-b2c-quickstarts-web-app/view-claims-update-web.png)

 Si noti che il **tipo di attestazione** `http://schemas.microsoft.com/claims/authnclassreference` è stato aggiornato in `b2c_1_edit_profile`, che indica che l'ultima azione eseguita è stata la modifica del profilo. Si noti anche che i nuovi valori per il nome e la città sono *Sara S.* e *Seattle*.

## <a name="access-a-resource"></a>Accedere a una risorsa

Fare clic su **Elenco attività** per immettere e modificare gli elementi elenco attività. L'applicazione Web ASP.NET include un token di accesso nella richiesta alla risorsa API Web per ottenere l'autorizzazione per l'esecuzione di operazioni sugli elementi elenco attività dell'utente. 

Immettere il testo nella casella di testo **Nuovo elemento**. Fare clic su **Aggiungi** per chiamare l'API Web protetta di Azure AD B2C che aggiunge un elemento elenco attività.

![Aggiungere un elemento elenco attività](media/active-directory-b2c-quickstarts-web-app/add-todo-item-web.png)

## <a name="other-scenarios"></a>Altri scenari

Altri scenari di test drive sono i seguenti:

* Disconnessione dall'applicazione e clic su **Elenco attività**. Si noti come viene richiesto di accedere e come vengono mantenuti gli elementi elenco. 
* Creazione di un nuovo account usando un tipo di account diverso. Se in precedenza è stato creato un account con un indirizzo di posta elettronica, ad esempio, usare un provider di identità basato su social network.

## <a name="next-steps"></a>Passaggi successivi

Il passaggio successivo consiste nel creare un tenant di Azure AD B2C e nel configurare l'esempio per l'esecuzione con il tenant. 

> [!div class="nextstepaction"]
> [Creare un tenant di Azure Active Directory B2C nel portale di Azure](active-directory-b2c-get-started.md)