---
title: Registrare un'app Web che accede agli utenti - Piattaforma di identità Microsoft Azure
description: Informazioni su come registrare un'app Web che accede agli utenti
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
ms.openlocfilehash: f2513b1fee022199f040ba0dcf83110c0b3b1365
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701570"
---
# <a name="web-app-that-signs-in-users-app-registration"></a>App Web che consente l'accesso agli utenti: registrazione dell'app

Questo articolo illustra le specifiche di registrazione dell'app per un'app Web che consente di accedere agli utenti.

Per registrare l'applicazione, è possibile utilizzare:

- [L'app Web inizia rapidamente](#register-an-app-by-using-the-quickstarts). Oltre ad essere un'ottima prima esperienza con la creazione di un'applicazione, le guide introduttive nel portale di Azure contengono un pulsante denominato **Apportare questa modifica per me**. Puoi usare questo pulsante per impostare le proprietà necessarie, anche per un'app esistente. È necessario adattare i valori di queste proprietà al proprio caso. In particolare, l'URL dell'API Web per l'app sarà probabilmente diverso dall'impostazione predefinita proposta, il che influirà anche sull'URI di disconnessione.
- Portale di Azure per [registrare manualmente l'applicazione.](#register-an-app-by-using-the-azure-portal)
- PowerShell e gli strumenti da riga di comando.

## <a name="register-an-app-by-using-the-quickstarts"></a>Registrare un'app tramite le guide introduttive

È possibile utilizzare questi collegamenti per bootstrap per la creazione dell'applicazione web:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-by-using-the-azure-portal"></a>Registrare un'app tramite il portale di AzureRegister an app by using the Azure portal

> [!NOTE]
> Il portale da usare è diverso a seconda che l'applicazione venga eseguita nel cloud pubblico di Microsoft Azure o in un cloud nazionale o sovrano. Per ulteriori informazioni, vedere [Nubi nazionali](./authentication-national-cloud.md#app-registration-endpoints).


1. Accedere al [portale](https://portal.azure.com) di Azure usando un account aziendale o dell'istituto di istruzione o un account Microsoft personale. In alternativa, accedere al portale di Azure preferito per il cloud nazionale.
1. Se l'account consente di accedere a più tenant, selezionarlo nell'angolo superiore destro. Impostare quindi la sessione del portale sul tenant di Azure Active Directory (Azure AD) desiderato.
1. Nel riquadro sinistro selezionare il servizio **Azure Active Directory** e quindi selezionare **Registrazioni** > app**Nuova registrazione**.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

1. Quando viene visualizzata la pagina **Registra un'applicazione,** immettere le informazioni di registrazione dell'applicazione:When the Register an application page appears, enter your application's registration information:
   1. Scegliere i tipi di account supportati per l'applicazione. (Vedere [Tipi di account supportati](./v2-supported-account-types.md).
   1. Nella sezione **Nome** immettere un nome di applicazione significativo che verrà visualizzato agli utenti dell'app. Ad esempio, immettere **AspNetCore-WebApp**.
   1. Per **URI di reindirizzamento**aggiungere il tipo di applicazione e la destinazione URI che accetterà le risposte del token restituite dopo la corretta autenticazione. Ad esempio, **https://localhost:44321**immettere . Selezionare quindi **Registra**.
1. Selezionare il menu **Autenticazione** e quindi aggiungere le informazioni seguenti:
   1. Per URL di **https://localhost:44321/signin-oidc** **risposta**, aggiungere di tipo **Web**.
   1. Nella sezione **Impostazioni avanzate** impostare **URL di disconnessione** su **https://localhost:44321/signout-oidc**.
   1. In **Concessione implicita** selezionare **Token ID**.
   1. Selezionare **Salva**.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

1. Quando viene visualizzata la **pagina Registra un'applicazione,** immettere le informazioni di registrazione dell'applicazione:When the Register an application page appears, enter your application's registration information:
   1. Scegliere i tipi di account supportati per l'applicazione. (Vedere [Tipi di account supportati](./v2-supported-account-types.md).
   1. Nella sezione **Nome** immettere un nome di applicazione significativo che verrà visualizzato agli utenti dell'app. Ad esempio, immettere **MailApp-openidconnect-v2**.
   1. Nella sezione **URI di reindirizzamento (facoltativo)** selezionare **Web** nella **https://localhost:44326/** casella combinata e immettere il seguente URI di reindirizzamento: .
1. Selezionare **Registra** per creare l'applicazione.
1. Selezionare il menu **Autenticazione.**
1. Nella sezione**Concessione implicita** **impostazioni** | avanzate selezionare **Token ID**. Questo esempio richiede che il flusso di [concessione implicita](v2-oauth2-implicit-grant-flow.md) sia abilitato per l'accesso dell'utente.
1. Selezionare **Salva**.

# <a name="java"></a>[Java](#tab/java)

1. Quando viene visualizzata la **pagina Registra un'applicazione,** immettere un nome visualizzato per l'applicazione. Ad esempio, immettere **java-webapp**.
1. Selezionare **Account in qualsiasi directory organizzativa e account Microsoft personali (ad esempio Skype, Xbox, Outlook.com)** e quindi selezionare **App Web/API** per **Tipo di applicazione**.
1. Selezionare **Registra** per registrare l'applicazione.
1. Nel menu a sinistra selezionare **Autenticazione**. In **URI di reindirizzamento**selezionare **Web**.

1. Immettere due URI di reindirizzamento: uno per la pagina di accesso e uno per la pagina del grafico. Per entrambi, utilizzare lo stesso numero di host e di porta, seguito da **/msal4jsample/secure/aad** per la pagina di accesso e **msal4jsample/graph/me** per la pagina delle informazioni utente.

   Per impostazione predefinita, nell'esempio viene utilizzato:By default, the sample uses:

   - **http://localhost:8080/msal4jsample/secure/aad**
   - **http://localhost:8080/msal4jsample/graph/me**

  Selezionare quindi **Salva**.

1. Selezionare **Certificati & segreti** dal menu.
1. Nella sezione **Segreti client** selezionare **Nuovo segreto client**e quindi:

   1. Immettere una descrizione della chiave.
   1. Selezionare la durata della chiave **In 1 anno**.
   1. Selezionare **Aggiungi**.
   1. Quando viene visualizzato il valore della chiave, copiarlo per un secondo momento. Questo valore non verrà visualizzato di nuovo o sarà recuperabile con altri mezzi.

# <a name="python"></a>[Python](#tab/python)

1. Quando viene visualizzata la **pagina Registra un'applicazione,** immettere le informazioni di registrazione dell'applicazione:When the Register an application page appears, enter your application's registration information:
   1. Nella sezione **Nome** immettere un nome di applicazione significativo che verrà visualizzato agli utenti dell'app. Ad esempio, immettere **python-webapp**.
   1. Modificare **Tipi di account supportati** in Account in qualsiasi directory **dell'organizzazione e account Microsoft personali (ad esempio Skype, Xbox Outlook.com).**
   1. Nella sezione **URI di reindirizzamento (facoltativo)** selezionare **Web** nella **http://localhost:5000/getAToken**casella combinata e immettere il seguente URI di reindirizzamento: .
1. Selezionare **Registra** per creare l'applicazione.
1. Nella pagina **Panoramica ** dell'app trovare il valore del campo **ID applicazione (client)** e prenderne nota. Sarà necessario per configurare il file di configurazione di Visual Studio per questo progetto.
1. Nel menu a sinistra selezionare **Certificati & segreti**.
1. Nella sezione **Segreti client** selezionare **Nuovo segreto client**e quindi:

   1. Immettere una descrizione della chiave.
   1. Selezionare **Tra 1 anno** per la durata della chiave.
   1. Selezionare **Aggiungi**.
   1. Quando viene visualizzato il valore della chiave, copiarlo. Sarà necessario più avanti.
---

## <a name="register-an-app-by-using-powershell"></a>Registrare un'app tramite PowerShellRegister an app by using PowerShell

> [!NOTE]
> Attualmente, Azure AD PowerShell crea applicazioni con solo i tipi di account supportati seguenti:Currently, Azure AD PowerShell creates applications with only the following supported account types:
>
> - MyOrg (solo account in questa directory dell'organizzazione)
> - AnyOrg (account in qualsiasi directory organizzativa)
>
> È possibile creare un'applicazione che accede agli utenti con i propri account Microsoft personali (ad esempio, Skype, Xbox o Outlook.com). Creare innanzitutto un'applicazione multi-tenant. I tipi di account supportati sono account in qualsiasi directory dell'organizzazione. Modificare quindi `signInAudience` la proprietà nel manifesto dell'applicazione dal portale di Azure.Then, change the property in the application manifest from the Azure portal. Per altre informazioni, vedere [il passaggio 1.3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) nell'esercitazione ASP.NET Core.For more information, see step 1.3 in the ASP.NET Core tutorial. È possibile generalizzare questo passaggio alle app Web in qualsiasi lingua.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurazione del codice dell'app](scenario-web-app-sign-user-app-configuration.md)
