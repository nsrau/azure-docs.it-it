---
title: App Web di accesso degli utenti (registrazione dell'app) - piattaforma delle identità Microsoft
description: Informazioni su come compilare un'app web di accesso degli utenti (registrazione dell'app)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6f512911811535818f4ad857c5c3b956870f619
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074546"
---
# <a name="web-app-that-signs-in-users---app-registration"></a>App Web di accesso degli utenti - registrazione dell'app

Questa pagina illustra le specifiche di registrazione di app per un'app web che gli utenti esegue l'accesso.

Per registrare l'applicazione, è possibile usare:

- Il [guide introduttive di app web](#register-an-app-using-the-quickstarts) -oltre a essere un'ottima prima esperienza con la creazione di un'applicazione, guide introduttive nel portale di Azure contengono un pulsante denominato **apportare questa modifica per me**. È possibile usare questo pulsante per impostare le proprietà che necessarie, anche per un'app esistente. È necessario adattare i valori di queste proprietà per il proprio caso. In particolare, l'URL dell'API web per l'app verrà probabilmente può essere diverso da quello predefinito proposto, che influirà anche l'URI di disconnessione.
- Portale di Azure per [registrare manualmente l'applicazione](#register-an-app-using-azure-portal)
- Strumenti da riga di comando e PowerShell

## <a name="register-an-app-using-the-quickstarts"></a>Registrare un'app usando le guide introduttive

Se si passa a questo collegamento, è possibile creare bootstrap la creazione dell'applicazione web:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

### <a name="register-an-app-using-azure-portal"></a>Registrare un'app usando il portale di Azure

> [!NOTE]
> usare il portale è diverso a seconda se l'applicazione viene eseguita nel cloud pubblico di Microsoft Azure o in un cloud nazionale o sovrano. Per altre informazioni, vedere [cloud nazionali](./authentication-national-cloud.md#app-registration-endpoints)

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale. In alternativa, accedere al portale di Azure scelta cloud nazionali.
1. Se l'account consente l'accesso a più di un tenant, selezionare il proprio account nell'angolo superiore destro e impostare la sessione del portale di Azure AD desiderato del tenant.
1. Nel riquadro di spostamento a sinistra, selezionare la **Azure Active Directory** del servizio e quindi selezionare **registrazioni per l'App** > **nuova registrazione**.
1. Nella pagina **Registra un'applicazione** visualizzata immettere le informazioni di registrazione dell'applicazione.
   - Scegliere i tipi di account supportato per l'applicazione (vedere [tipi di Account supportati](./v2-supported-account-types.md))
   - Nella sezione **Nome** immettere un nome di applicazione significativo che verrà visualizzato agli utenti dell'app, ad esempio `AspNetCore-WebApp`.
   - Nelle **URL di risposta**, aggiungere l'URL di risposta per l'app, ad esempio `https://localhost:44321/`e selezionare **registrare**.
1. Selezionare il menu **Autenticazione** e quindi aggiungere le informazioni seguenti:
- In **URL di risposta** aggiungere `https://localhost:44321/signin-oidc` e selezionare **Registra**.
- Nel **impostazioni avanzate** sezione, impostare **URL di disconnessione** a `https://localhost:44321/signout-oidc`.
- In **Concessione implicita** selezionare **Token ID**.
- Selezionare **Salva**.

### <a name="register-an-app-using-powershell"></a>Registrare un'app con PowerShell

> [!NOTE]
> Azure AD PowerShell attualmente crea solo applicazioni con i tipi di account supportati seguenti:
>
> - Org (account in questa directory dell'organizzazione solo)
> - AnyOrg (account in qualsiasi directory dell'organizzazione).
>
> Se si desidera creare un'applicazione che esegue l'accesso gli utenti con i propri Accounts Microsoft personale (ad esempio Skype, XBox, Outlook.com), è possibile creare innanzitutto un'applicazione multi-tenant (tipi di account supportati = account in qualsiasi directory dell'organizzazione), quindi modificare il `signInAudience` proprietà nel manifesto dell'applicazione dal portale di Azure. Questa operazione è illustrata nei dettagli nel passaggio [1.3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) dell'esercitazione ASP.NET Core (e possono essere generalizzate alle App web in qualsiasi linguaggio).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurazione del codice dell'App](scenario-web-app-sign-user-app-configuration.md)
