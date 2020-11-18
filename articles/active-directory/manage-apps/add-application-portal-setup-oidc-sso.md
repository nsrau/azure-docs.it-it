---
title: "Avvio rapido: Configurare l'accesso Single Sign-On (SSO) basato su OIDC per un'applicazione nel tenant di Azure Active Directory (Azure AD)"
description: Questa guida di avvio rapido illustra il processo di configurazione dell'accesso Single Sign-On (SSO) basato su OIDC per un'applicazione nel tenant di Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.openlocfilehash: 9ea4ec748ca37f93e9711970b10746a009543d00
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94656599"
---
# <a name="quickstart-set-up-oidc-based-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Avvio rapido: Configurare l'accesso Single Sign-On (SSO) basato su OIDC per un'applicazione nel tenant di Azure Active Directory (Azure AD)

Per iniziare a usare account di accesso utente semplificati, configurare l'accesso SSO per un'applicazione aggiunta al tenant di Azure AD. Dopo aver configurato l'accesso SSO, gli utenti potranno accedere a un'applicazione usando le loro credenziali di Azure AD. L'accesso SSO è incluso nell'edizione gratuita di Azure AD.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'accesso SSO per un'applicazione aggiunta al tenant di Azure AD, è necessario avere:

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uno dei ruoli seguenti: amministratore globale, amministratore di applicazioni cloud o amministratore di applicazioni oppure proprietario dell'entità servizio.
- Un'applicazione che supporta l'accesso SSO e che è già stata preconfigurata e aggiunta alla raccolta di Azure AD. La maggior parte delle app può usare Azure AD per l'accesso SSO. Le app della raccolta di Azure AD sono preconfigurate. Se l'app non è inclusa nell'elenco oppure è personalizzata, è comunque possibile usarla con Azure AD. Vedere le esercitazioni e gli altri documenti nel sommario. Questo argomento di avvio rapido illustra le app preconfigurate per l'accesso SSO e aggiunte alla raccolta di Azure AD dagli sviluppatori.
- Facoltativo: completamento di [Visualizzare le app](view-applications-portal.md).
- Facoltativo: completamento di [Aggiungere un'app](add-application-portal.md).
- Facoltativo: completamento di [Configurare un'app](add-application-portal-configure.md).
- Facoltativo: completamento di [Assegnare utenti a un'app](add-application-portal-assign-users.md).

>[!IMPORTANT]
>Per testare i passaggi di questo argomento di avvio rapido, usare un ambiente non di produzione.

## <a name="enable-single-sign-on-for-an-app"></a>Abilitare l'accesso Single Sign-On per un'app

Se si aggiunge un'app che usa lo standard OIDC per l'accesso SSO, è disponibile un pulsante di configurazione. Selezionando il pulsante, si passa al sito di applicazioni e si completa il processo di iscrizione per l'app. Il processo di aggiunta di un'app è descritto nella precedente guida di avvio rapido di questa serie, Aggiungere un'app. Se si configura un'applicazione già aggiunta, consultare il primo argomento di avvio rapido, che illustra come visualizzare le applicazioni già presenti nel tenant. 

Per configurare l'accesso Single Sign-On per un'applicazione:

1. Nella precedente guida di avvio rapido di questa serie si è appreso come aggiungere un'app che userà il tenant di Azure AD per la gestione delle identità. Se lo sviluppatore dell'app ha usato lo standard OIDC per implementare l'accesso SSO, viene visualizzato un pulsante per l'iscrizione quando si aggiunge l'app. 

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/sign-up-oidc-sso.png" alt-text="Screenshot che mostra l'opzione Single Sign-On e il pulsante per l'iscrizione." lightbox="media/add-application-portal-setup-oidc-sso/sign-up-oidc-sso.png":::


2. Selezionare **Iscrizione** per passare alla pagina di accesso dello sviluppatore dell'app. Accedere con le credenziali di Azure Active Directory. 

   > [!IMPORTANT]
    > Se si ha già una sottoscrizione dell'applicazione, verrà eseguita la convalida dei dettagli dell'utente e delle informazioni relative a tenant/directory. Se l'applicazione non è in grado di verificare l'utente, quest'ultimo verrà reindirizzato alla pagina di iscrizione al servizio dell'applicazione o alla pagina di errore.

3. Al termine dell'autenticazione, viene visualizzata una finestra di dialogo che chiede il consenso dell'amministratore. Selezionare **Acconsenti per conto dell'organizzazione** e quindi **Accetta**. 

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/consent.png" alt-text="Screenshot che mostra la schermata di consenso per un'app." lightbox="media/add-application-portal-setup-oidc-sso/consent.png":::

4. L'applicazione viene aggiunta al tenant e viene visualizzata la relativa home page.


> [!TIP]
> È possibile automatizzare la gestione delle app usando l'API Graph. Vedere [Automatizzare la gestione delle app con l'API Microsoft Graph](/graph/application-saml-sso-configure-api).


## <a name="clean-up-resources"></a>Pulire le risorse

Al termine di questa serie di guide di avvio rapido, è consigliabile eliminare l'app per pulire il tenant di test. La procedura di eliminazione dell'app è illustrata nell'ultimo argomento di avvio rapido di questa serie. Vedere [Eliminare un'app](delete-application-portal.md).

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo per informazioni su come eliminare un'app.
> [!div class="nextstepaction"]
> [Eliminare un'app](delete-application-portal.md)