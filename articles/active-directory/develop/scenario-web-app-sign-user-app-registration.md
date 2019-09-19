---
title: App Web per l'accesso degli utenti (registrazione dell'app)-piattaforma di identità Microsoft
description: Informazioni su come creare un'app Web per l'accesso degli utenti (registrazione dell'app)
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
ms.openlocfilehash: 0bdf04014d7b0382913c0a4094f7474686658441
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086696"
---
# <a name="web-app-that-signs-in-users---app-registration"></a>App Web che esegue l'accesso degli utenti-registrazione dell'app

Questa pagina illustra le specifiche di registrazione delle app per un'app Web che esegue l'accesso agli utenti.

Per registrare l'applicazione, è possibile usare:

- Le [guide introduttive per le app Web](#register-an-app-using-the-quickstarts) , oltre a essere un'ottima prima esperienza nella creazione di un'applicazione, le guide introduttive nell'portale di Azure contengono un pulsante denominato **apportare questa modifica**. È possibile usare questo pulsante per impostare le proprietà necessarie, anche per un'app esistente. È necessario adattare i valori di queste proprietà al proprio caso. In particolare, l'URL dell'API Web per l'app è probabilmente diverso dal valore predefinito proposto, che avrà un effetto anche sull'URI di disconnessione.
- Portale di Azure per [registrare l'applicazione manualmente](#register-an-app-using-azure-portal)
- PowerShell e gli strumenti da riga di comando

## <a name="register-an-app-using-the-quickstarts"></a>Registrare un'app usando le guide introduttive

Se si passa a questo collegamento, è possibile creare bootstrap per la creazione dell'applicazione Web:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

### <a name="register-an-app-using-azure-portal"></a>Registrare un'app usando portale di Azure

> [!NOTE]
> il portale da usare varia a seconda che l'applicazione venga eseguita nel cloud pubblico Microsoft Azure o in un cloud nazionale o sovrano. Per altre informazioni, vedere [cloud nazionali](./authentication-national-cloud.md#app-registration-endpoints)

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale. In alternativa, accedere al portale di Azure cloud nazionale preferito.
1. Se l'account consente di accedere a più tenant, selezionare l'account nell'angolo in alto a destra e impostare la sessione del portale sul tenant di Azure AD desiderato.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory** e quindi selezionare **registrazioni app** > **nuova registrazione**.
1. Nella pagina **Registra un'applicazione** visualizzata immettere le informazioni di registrazione dell'applicazione.
   1. scegliere i tipi di account supportati per l'applicazione (vedere [tipi di account supportati](./v2-supported-account-types.md))
   1. Nella sezione **Nome** immettere un nome di applicazione significativo che verrà visualizzato agli utenti dell'app, ad esempio `AspNetCore-WebApp`.
   1. In **URI di reindirizzamento**aggiungere il tipo di applicazione e la destinazione URI che accetterà le risposte del token restituito dopo l'autenticazione. Ad esempio `https://localhost:44321/`.  Selezionare **Registra**.
1. Selezionare il menu **Autenticazione** e quindi aggiungere le informazioni seguenti:
   1. In **URL di risposta**aggiungere `https://localhost:44321/signin-oidc`.
   1. Nella sezione **Impostazioni avanzate** impostare **URL disconnessione** su `https://localhost:44321/signout-oidc`.
   1. In **Concessione implicita** selezionare **Token ID**.
   1. Selezionare **Salva**.

### <a name="register-an-app-using-powershell"></a>Registrare un'app con PowerShell

> [!NOTE]
> Attualmente Azure AD PowerShell crea applicazioni solo con i tipi di account supportati seguenti:
>
> - MyOrg (solo account in questa directory organizzativa)
> - AnyOrg (account in qualsiasi directory dell'organizzazione).
>
> Se si vuole creare un'applicazione che esegua l'accesso agli utenti con gli account Microsoft personali (ad esempio Skype, XBox, Outlook.com), è possibile creare prima un'applicazione multi-tenant (tipi di account supportati = account in qualsiasi directory organizzativa) e quindi modificare `signInAudience` proprietà nel manifesto dell'applicazione dal portale di Azure. Questa procedura è illustrata in dettaglio nel passaggio [1,3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) dell'esercitazione ASP.NET Core (e può essere generalizzata in app Web in qualsiasi linguaggio).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurazione del codice dell'app](scenario-web-app-sign-user-app-configuration.md)
