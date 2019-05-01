---
title: Guida introduttiva - Configurare l'accesso per un'applicazione ASP.NET tramite Azure Active Directory B2C | Microsoft Docs
description: Eseguire un'app Web ASP.NET di esempio che usa Azure Active Directory B2C per fornire l'accesso all'account.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 0d82a174f9fcfab84b5caf71853242ce68ebb60d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64699500"
---
# <a name="quickstart-set-up-sign-in-for-an-aspnet-application-using-azure-active-directory-b2c"></a>Guida introduttiva: Configurare l'accesso per un'applicazione ASP.NET tramite Azure Active Directory B2C

Azure Active Directory (Azure AD) B2C consente la gestione delle identità del cloud per garantire la protezione costante dell'applicazione, delle attività aziendali e dei clienti. Azure AD B2C consente alle applicazioni di eseguire l'autenticazione per account di social networking e account aziendali usando protocolli standard aperti. In questa guida introduttiva si usa un'applicazione ASP.NET per eseguire l'accesso con un provider di identità basato su social network e chiamare un'API Web protetta da Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

- [Visual Studio 2017](https://www.visualstudio.com/downloads/) con il carico di lavoro **Sviluppo ASP.NET e Web**. 
- Un account di social networking di Facebook, Google, Microsoft o Twitter.
- [Scaricare un file ZIP](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) o clonare l'applicazione Web di esempio da GitHub.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
    ```

    Nella soluzione di esempio sono inclusi i due progetti seguenti.

    - **TaskWebApp**: applicazione Web che crea e modifica un elenco attività. L'applicazione Web usa il flusso utente di **iscrizione o accesso** per l'iscrizione o l'accesso degli utenti.
    - **TaskService**: API Web che supporta le funzionalità di creazione, lettura, aggiornamento ed eliminazione dell'elenco attività. L'API Web è protetta da Azure AD B2C e viene chiamata dall'applicazione Web.

## <a name="run-the-application-in-visual-studio"></a>Eseguire l'applicazione in Visual Studio

1. Nella cartella del progetto dell'applicazione di esempio aprire la soluzione **B2C-WebAPI-DotNet.sln** in Visual Studio.
2. Per questa guida introduttiva si eseguono contemporaneamente entrambi i progetti **TaskWebApp** e **TaskService**. Fare clic con il pulsante destro del mouse sulla soluzione **B2C-WebAPI-DotNet** in Esplora soluzioni e quindi scegliere **Imposta progetti di avvio**. 
3. Selezionare **Progetti di avvio multipli** e modificare l'impostazione di **Azione** in **Avvia** per entrambi i progetti. 
4. Fare clic su **OK**.
5. Premere **F5** per eseguire il debug di entrambe le applicazioni. Ogni applicazione viene visualizzata in una scheda del browser separata:

    - `https://localhost:44316/`: applicazione Web ASP.NET. Nella guida introduttiva si interagisce direttamente con l'applicazione.
    - `https://localhost:44332/`: API Web chiamata dall'applicazione Web ASP.NET.

## <a name="sign-in-using-your-account"></a>Eseguire l'accesso con il proprio account

1. Fare clic su **Sign up / Sign in** (Iscrizione/Accesso) nell'applicazione Web ASP.NET per avviare il flusso di lavoro.

    ![App Web ASP.NET di esempio](media/active-directory-b2c-quickstarts-web-app/web-app-sign-in.png)

    L'esempio supporta alcune opzioni di iscrizione, tra cui l'uso di un provider di identità di social networking o la creazione di un account locale tramite un indirizzo di posta elettronica. Per questa guida introduttiva, usare un account di provider di identità basato su social network, ovvero un account di Facebook, Google, Microsoft o Twitter.

2. Azure AD B2C presenta una pagina di accesso personalizzata di un marchio fittizio denominato Wingtip Toys per l'applicazione Web di esempio. Per iscriversi usando un provider di identità basato su social network, fare clic sul pulsante del provider di identità che si vuole usare.

    ![Provider di accesso o di iscrizione](media/active-directory-b2c-quickstarts-web-app/sign-in-or-sign-up-web.png)

    Per leggere le informazioni dell'account di social networking, eseguire l'autenticazione (accesso) tramite le credenziali di tale account e autorizzare l'applicazione. Dopo la concessione dell'accesso, l'applicazione può recuperare le informazioni sul profilo dall'account, ad esempio il nome e la città dell'utente. 

3. Completare il processo di accesso per il provider di identità.

## <a name="edit-your-profile"></a>Modificare il profilo

Azure Active Directory B2C offre funzionalità che consentono agli utenti di aggiornare il profilo. L'app Web di esempio usa un flusso utente di modifica dei profili di Azure AD B2C per il flusso di lavoro. 

1. Sulla barra dei menu dell'applicazione fare clic sul nome del profilo e selezionare **Edit profile** (Modifica profilo) per modificare il profilo creato.

    ![Modificare il profilo](media/active-directory-b2c-quickstarts-web-app/edit-profile-web.png)

2. Modificare **Display name** (Nome visualizzato) o **City** (Città) e quindi fare clic su **Continue** (Continua) per aggiornare il profilo. 

    La modifica viene visualizzata in alto a destra nella home page dell'applicazione Web.

## <a name="access-a-protected-api-resource"></a>Accedere a una risorsa API protetta

1. Fare clic su **Elenco attività** per immettere e modificare gli elementi elenco attività. 

2. Immettere il testo nella casella di testo **Nuovo elemento**. Fare clic su **Aggiungi** per chiamare l'API Web protetta di Azure AD B2C che aggiunge un elemento elenco attività.

    ![Aggiungere un elemento elenco attività](media/active-directory-b2c-quickstarts-web-app/add-todo-item-web.png)

    L'applicazione Web ASP.NET include un token di accesso di Azure AD nella richiesta alla risorsa API Web protetta per l'esecuzione di operazioni sugli elementi elenco attività dell'utente.

È stato usato l'account utente Azure AD B2C per eseguire una chiamata autorizzata a un'API Web protetta da Azure AD B2C.

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile usare il tenant di Azure AD B2C se si prevede di provare altre guide introduttive o esercitazioni relative ad Azure AD B2C. Quando non è più necessario, è possibile [eliminare il tenant di Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata usata un'applicazione ASP.NET di esempio per accedere con una pagina di accesso personalizzata e con un provider di identità basato su social network, creare un account Azure AD B2C e chiamare un'API Web protetta da Azure AD B2C. 

Iniziare ora a creare un tenant di Azure AD B2C.

> [!div class="nextstepaction"]
> [Creare un tenant di Azure Active Directory B2C nel portale di Azure](tutorial-create-tenant.md)
