---
title: Eseguire il test drive di un'app Web abilitata per Azure AD B2C | Microsoft Docs
description: Accedere al test drive, effettuare la registrazione, modificare il profilo e reimpostare i percorsi utente password tramite un ambiente di test di Azure AD B2C
services: active-directory-b2c
documentationcenter: .net
author: saraford
manager: mtillman
editor: PatAltimore
ms.assetid: 2ffb780d-2c51-4c2e-b8d6-39c40a81a77e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/31/2017
ms.author: patricka
ms.openlocfilehash: bc56da695145f396a2899fb9dc7add3af9a549e8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="test-drive-an-azure-ad-b2c-enabled-web-app"></a>Eseguire il test drive di un'app Web abilitata per Azure AD B2C

Azure Active Directory B2C consente la gestione delle identità del cloud per garantire la protezione costante dell'applicazione, delle attività aziendali e dei clienti. Questa guida introduttiva usa un'app di elenco attività di esempio per illustrare:

> [!div class="checklist"]
> * Come accedere da una pagina di accesso personalizzata.
> * Come accedere usando un provider di identità basato su social network.
> * Come creare e gestire l'account e il profilo utente di Azure AD B2C.
> * Come chiamare un'API Web protetta da Azure AD B2C.

## <a name="prerequisites"></a>Prerequisiti

* [Visual Studio 2017](https://www.visualstudio.com/downloads/) con il carico di lavoro **Sviluppo ASP.NET e Web**. 
* Un account di social networking di Facebook, Google, Microsoft o Twitter.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Scaricare l'esempio

[Scaricare o clonare l'applicazione di esempio](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi) da GitHub.

## <a name="run-the-app-in-visual-studio"></a>Eseguire l'app in Visual Studio

Nella cartella del progetto dell'applicazione di esempio aprire la soluzione `B2C-WebAPI-DotNet.sln` in Visual Studio. 

La soluzione è un'applicazione elenco attività di esempio costituita da due progetti:

* **TaskWebApp**: applicazione Web di ASP.NET MVC in cui l'utente può gestire gli elementi elenco attività.  
* **TaskService**: back-end di un'API Web ASP.NET che gestisce le operazioni eseguite sugli elementi elenco attività dell'utente. L'app Web chiama l'API Web e visualizza i risultati.

Per questa guida introduttiva è necessario eseguire entrambi i progetti `TaskWebApp` e `TaskService` contemporaneamente. 

1. Nel menu di Visual Studio scegliere **Progetti > Imposta progetti di avvio**. 
2. Selezionare il pulsante di opzione **Progetti di avvio multipli**.
3. Modificare il valore di **Azione** in entrambi i progetti in **Avvia**. Fare clic su **OK**.

![Impostare la pagina di avvio in Visual Studio](media/active-directory-b2c-quickstarts-web-app/setup-startup-projects.png)

Selezionare **Debug > Avvia debug** per compilare ed eseguire entrambe le applicazioni. Ogni applicazione viene visualizzata in una scheda del browser separata:

`https://localhost:44316/`: questa pagina è l'applicazione Web ASP.NET. Nella guida introduttiva si interagisce direttamente con l'applicazione.
`https://localhost:44332/`: questa pagina è l'API Web chiamata dall'applicazione Web ASP.NET.

## <a name="create-an-account"></a>Creare un account

Fare clic sul collegamento **Sign up / Sign in** (Iscrizione/Accesso) nell'applicazione Web ASP.NET per avviare il flusso di lavoro**Iscrizione o accesso**. Quando si crea un account, è possibile usare un account esistente di un provider di identità basato su social network o un account di posta elettronica. Per questa guida introduttiva, usare un account di provider di identità basato su social network, ovvero un account di Facebook, Google, Microsoft o Twitter.

![App Web ASP.NET di esempio](media/active-directory-b2c-quickstarts-web-app/web-app-sign-in.png)

### <a name="sign-up-using-a-social-identity-provider"></a>Iscriversi usando un provider di identità basato su social network

Per iscriversi usando un provider di identità basato su social network, fare clic sul pulsante del provider di identità che si vuole usare. 

![Provider di accesso o di iscrizione](media/active-directory-b2c-quickstarts-web-app/sign-in-or-sign-up-web.png)

Per leggere le informazioni dell'account di social networking, è necessario eseguire l'autenticazione (accesso) tramite le credenziali di tale account e autorizzare l'applicazione. Dopo la concessione dell'accesso, l'applicazione può recuperare le informazioni sul profilo dall'account, ad esempio il nome e la città dell'utente. 

Completare il processo di accesso per il provider di identità. Se ad esempio si è scelto Twitter, immettere le credenziali di Twitter e fare clic su **Sign in** (Accedi).

![Eseguire l'autenticazione e l'autorizzazione tramite un account di social networking](media/active-directory-b2c-quickstarts-web-app/twitter-authenticate-authorize-web.png)

I dettagli del nuovo profilo dell'account di Azure AD B2C sono già popolati con informazioni derivate dall'account di social networking.

Aggiornare i campi Nome visualizzato, Posizione e Città e fare clic su **Continua**.  I valori immessi vengono usati per il profilo dell'account utente di Azure AD B2C.

![Dettagli del profilo di iscrizione del nuovo account](media/active-directory-b2c-quickstarts-web-app/new-account-sign-up-profile-details-web.png)

Sono state completate queste operazioni:

> [!div class="checklist"]
> * Autenticazione tramite un provider di identità.
> * Creazione di un account utente di Azure AD B2C. 

## <a name="edit-your-profile"></a>Modificare il profilo

Azure Active Directory B2C offre funzionalità che consentono agli utenti di aggiornare il profilo. Sulla barra dei menu dell'applicazione Web fare clic sul nome del profilo e selezionare **Modifica profilo** per modificare il profilo creato.

![Modificare il profilo](media/active-directory-b2c-quickstarts-web-app/edit-profile-web.png)

Modificare **Nome visualizzato** e **Città**.  Fare clic su **Continua** per aggiornare il profilo.

![Update profile (Aggiorna profilo)](media/active-directory-b2c-quickstarts-web-app/update-profile-web.png)

Si noti che il nome visualizzato nella parte superiore destra della pagina risulta aggiornato. 

## <a name="access-a-secured-web-api-resource"></a>Accedere a una risorsa API Web protetta

Fare clic su **Elenco attività** per immettere e modificare gli elementi elenco attività. L'applicazione Web ASP.NET include un token di accesso nella richiesta alla risorsa API Web per ottenere l'autorizzazione per l'esecuzione di operazioni sugli elementi elenco attività dell'utente. 

Immettere il testo nella casella di testo **Nuovo elemento**. Fare clic su **Aggiungi** per chiamare l'API Web protetta di Azure AD B2C che aggiunge un elemento elenco attività.

![Aggiungere un elemento elenco attività](media/active-directory-b2c-quickstarts-web-app/add-todo-item-web.png)

È stato usato l'account utente di Azure AD B2C per eseguire una chiamata autorizzata a un'API Web protetta di Azure AD B2C.

## <a name="next-steps"></a>Passaggi successivi

L'esempio presentato in questa guida introduttiva può essere usato per provare altri scenari di Azure AD B2C, tra cui:

* Creazione di un nuovo account locale tramite un indirizzo di posta elettronica.
* Reimpostazione della password dell'account locale.

Se si è pronti ad approfondire la procedura di creazione del tenant di Azure AD B2C e a configurare l'esempio per l'esecuzione con il proprio tenant, provare l'esercitazione seguente.

> [!div class="nextstepaction"]
> [Creare un'app Web ASP.NET con criteri di iscrizione, accesso, modifica del profilo e reimpostazione della password di Azure Active Directory B2C](active-directory-b2c-devquickstarts-web-dotnet-susi.md)