---
title: Registrare un'app Web che esegue l'accesso agli utenti-piattaforma di identità Microsoft | Azure
description: Informazioni su come registrare un'app Web per l'accesso degli utenti
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b08dd5602196bae0e250953c83320e69ddc6da83
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964821"
---
# <a name="web-app-that-signs-in-users-app-registration"></a>App Web che esegue l'accesso degli utenti: registrazione dell'app

Questo articolo illustra le specifiche di registrazione delle app per un'app Web che esegue l'accesso agli utenti.

Per registrare l'applicazione, è possibile usare:

- [Guide introduttive per le app Web](#register-an-app-by-using-the-quickstarts). Oltre a essere un'ottima esperienza per la creazione di un'applicazione, le guide introduttive nell'portale di Azure contengono un pulsante denominato **apportare questa modifica**. È possibile usare questo pulsante per impostare le proprietà necessarie, anche per un'app esistente. È necessario adattare i valori di queste proprietà al proprio caso. In particolare, l'URL dell'API Web per l'app è probabilmente diverso dal valore predefinito proposto, che influirà anche sull'URI di disconnessione.
- Portale di Azure per [registrare manualmente l'applicazione](#register-an-app-by-using-the-azure-portal).
- PowerShell e gli strumenti da riga di comando.

## <a name="register-an-app-by-using-the-quickstarts"></a>Registrare un'app usando le guide introduttive

È possibile utilizzare questi collegamenti per avviare la creazione dell'applicazione Web:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-by-using-the-azure-portal"></a>Registrare un'app usando il portale di Azure

> [!NOTE]
> Il portale da usare varia a seconda che l'applicazione venga eseguita nel cloud pubblico Microsoft Azure o in un cloud nazionale o sovrano. Per altre informazioni, vedere [cloud nazionali](./authentication-national-cloud.md#app-registration-endpoints).


1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale. In alternativa, accedere al portale di Azure di scelta per il cloud nazionale.
1. Se l'account consente di accedere a più di un tenant, selezionare l'account nell'angolo in alto a destra. Quindi, impostare la sessione del portale sul tenant di Azure Active Directory (Azure AD) desiderato.
1. Nel riquadro sinistro selezionare il servizio **Azure Active Directory** e quindi selezionare **registrazioni app** > **nuova registrazione**.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

1. Nella pagina **Registra un'applicazione** visualizzata immettere le informazioni di registrazione dell'applicazione.
   1. Scegliere i tipi di account supportati per l'applicazione. Vedere [tipi di account supportati](./v2-supported-account-types.md).
   1. Nella sezione **nome** immettere un nome di applicazione significativo che verrà visualizzato agli utenti dell'app. Ad esempio, immettere **AspNetCore-webapp**.
   1. Per **URI di reindirizzamento**aggiungere il tipo di applicazione e la destinazione URI che accetterà le risposte del token restituito dopo l'autenticazione. Ad esempio, immettere **https://localhost:44321** . Selezionare quindi **Registra**.
1. Selezionare il menu **Autenticazione** e quindi aggiungere le informazioni seguenti:
   1. Per **URL di risposta**aggiungere **https://localhost:44321/signin-oidc** di tipo **Web**.
   1. Nella sezione **Impostazioni avanzate** impostare **disconnetti URL** su **https://localhost:44321/signout-oidc** .
   1. In **Concessione implicita** selezionare **Token ID**.
   1. Selezionare **Salva**.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

1. Nella pagina **Registra un'applicazione** visualizzata immettere le informazioni di registrazione dell'applicazione:
   1. Scegliere i tipi di account supportati per l'applicazione. Vedere [tipi di account supportati](./v2-supported-account-types.md).
   1. Nella sezione **nome** immettere un nome di applicazione significativo che verrà visualizzato agli utenti dell'app. Ad esempio, immettere **MailApp-openidconnect-V2**.
   1. Nella sezione **URI di reindirizzamento (facoltativo)** selezionare **Web** nella casella combinata e immettere l'URI di reindirizzamento seguente: **https://localhost:44326/** .
1. Selezionare **Registra** per creare l'applicazione.
1. Selezionare il menu **Authentication** .
1. Nella sezione **Impostazioni avanzate** | **concessione implicita** Selezionare **token ID**. Questo esempio richiede che il [flusso di concessione implicita](v2-oauth2-implicit-grant-flow.md) sia abilitato per l'accesso dell'utente.
1. Selezionare **Salva**.

# <a name="javatabjava"></a>[Java](#tab/java)

1. Quando viene visualizzata la **pagina registra un'applicazione** , immettere un nome visualizzato per l'applicazione. Ad esempio, immettere **Java-webapp**.
1. Selezionare **account in qualsiasi directory organizzativa e account Microsoft personali (ad esempio Skype, Xbox, Outlook.com)** , quindi selezionare **app Web/API** per **tipo di applicazione**.
1. Selezionare **Register (registra** ) per registrare l'applicazione.
1. Scegliere **autenticazione**dal menu a sinistra. In **URI di reindirizzamento**selezionare **Web**.

1. Immettere due URI di reindirizzamento: uno per la pagina di accesso e uno per la pagina Graph. Per entrambi, usare lo stesso host e lo stesso numero di porta, seguito da **/msal4jsample/Secure/AAD** per la pagina di accesso e **msal4jsample/Graph/me** per la pagina informazioni utente.

   Per impostazione predefinita, l'esempio USA:

   - **http://localhost:8080/msal4jsample/secure/aad**
   - **http://localhost:8080/msal4jsample/graph/me**

  Selezionare quindi **Salva**.

1. Selezionare **certificati & Secrets** dal menu.
1. Nella sezione **segreti client** selezionare **nuovo segreto client**, quindi:

   1. Immettere una descrizione della chiave.
   1. Selezionare la durata della chiave **in 1 anno**.
   1. Selezionare **Aggiungi**.
   1. Quando viene visualizzato il valore della chiave, copiarlo per un momento successivo. Questo valore non verrà visualizzato di nuovo o potrà essere recuperato in qualsiasi altro modo.

# <a name="pythontabpython"></a>[Python](#tab/python)

1. Nella pagina **Registra un'applicazione** visualizzata immettere le informazioni di registrazione dell'applicazione:
   1. Nella sezione **nome** immettere un nome di applicazione significativo che verrà visualizzato agli utenti dell'app. Ad esempio, immettere **Python-webapp**.
   1. Modificare i **tipi di account supportati** **in account in qualsiasi directory organizzativa e account Microsoft personali (ad esempio Skype, Xbox, Outlook.com)** .
   1. Nella sezione **URI di reindirizzamento (facoltativo)** selezionare **Web** nella casella combinata e immettere l'URI di reindirizzamento seguente: **http://localhost:5000/getAToken** .
1. Selezionare **Registra** per creare l'applicazione.
1. Nella pagina **Panoramica**  dell'app trovare il valore del campo **ID applicazione (client)** e prenderne nota. Sarà necessario per configurare il file di configurazione di Visual Studio per questo progetto.
1. Nel menu a sinistra selezionare **certificati & segreti**.
1. Nella sezione **segreti client** selezionare **nuovo segreto client**, quindi:

   1. Immettere una descrizione della chiave.
   1. Selezionare **Tra 1 anno** per la durata della chiave.
   1. Selezionare **Aggiungi**.
   1. Quando viene visualizzato il valore della chiave, copiarlo. Sarà necessario più avanti.
---

## <a name="register-an-app-by-using-powershell"></a>Registrare un'app usando PowerShell

> [!NOTE]
> Attualmente, Azure AD PowerShell crea applicazioni con solo i tipi di conto supportati seguenti:
>
> - MyOrg (solo account in questa directory organizzativa)
> - AnyOrg (account in qualsiasi directory organizzativa)
>
> È possibile creare un'applicazione che esegua l'accesso degli utenti con gli account Microsoft personali (ad esempio, Skype, Xbox o Outlook.com). Per prima cosa, creare un'applicazione multi-tenant. I tipi di account supportati sono account in qualsiasi directory dell'organizzazione. Modificare quindi la proprietà `signInAudience` nel manifesto dell'applicazione dall'portale di Azure. Per ulteriori informazioni, vedere il [passaggio 1,3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) nell'esercitazione ASP.NET Core. È possibile generalizzare questo passaggio per le app Web in qualsiasi linguaggio.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurazione del codice dell'app](scenario-web-app-sign-user-app-configuration.md)
