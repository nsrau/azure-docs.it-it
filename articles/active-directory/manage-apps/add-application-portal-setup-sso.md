---
title: "Avvio rapido: Configurare l'accesso Single Sign-On (SSO) basato su SAML per un'applicazione nel tenant di Azure Active Directory (Azure AD)"
description: Questa guida di avvio rapido illustra il processo di configurazione dell'accesso Single Sign-On (SSO) basato su SAML per un'applicazione nel tenant di Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.openlocfilehash: d9ef069291f010db510d626ceda959a0342c39e2
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91305784"
---
# <a name="quickstart-set-up-saml-based-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Avvio rapido: Configurare l'accesso Single Sign-On (SSO) basato su SAML per un'applicazione nel tenant di Azure Active Directory (Azure AD)

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

Una volta completata l'aggiunta di un'applicazione al tenant di Azure AD, viene visualizzata la pagina di panoramica. Se si configura un'applicazione già aggiunta, consultare il primo argomento di avvio rapido, che illustra come visualizzare le applicazioni aggiunte al tenant. 

Per configurare l'accesso Single Sign-On per un'applicazione:

1. Nel portale di Azure AD selezionare **Applicazioni aziendali**. Quindi trovare e selezionare l'applicazione da configurare per l'accesso Single Sign-On.
1. Nella sezione **Gestisci** selezionare **Single Sign-On** per aprire il riquadro **Single Sign-On** per la modifica.

    > [!IMPORTANT]
    > Se l'app usa lo standard OIDC (OpenID Connect) per l'accesso SSO, l'opzione Single Sign-On non sarà visibile sulla barra di spostamento. Per informazioni su come configurarla, vedere la guida di avvio rapido sull'accesso SSO basato su OIDC.

    :::image type="content" source="media/add-application-portal-setup-sso/configure-sso.png" alt-text="Screenshot che mostra la pagina di configurazione dell'accesso Single Sign-On nel portale di Azure AD.":::

1. Selezionare **SAML** per aprire la pagina di configurazione dell'accesso SSO. In questo esempio l'applicazione configurata per l'accesso SSO è GitHub. Dopo aver configurato GitHub, gli utenti potranno accedere a GitHub usando le proprie credenziali del tenant di Azure AD.

    :::image type="content" source="media/add-application-portal-setup-sso/github-sso.png" alt-text="Screenshot che mostra la pagina di configurazione dell'accesso Single Sign-On nel portale di Azure AD.":::

1. Il processo di configurazione di un'applicazione per l'uso di Azure AD per l'accesso SSO basato su SAML varia a seconda dell'applicazione. È disponibile un collegamento alle indicazioni per GitHub. Per trovare le guide per altre app, vedere [Esercitazioni per l'integrazione di applicazioni SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/).
1. Seguire la guida per configurare l'accesso SSO per l'applicazione. Molte applicazioni prevedono requisiti specifici della sottoscrizione per la funzionalità SSO. GitHub richiede, ad esempio, una sottoscrizione Enterprise.
    > [!TIP]
    > Per altre informazioni sulle opzioni di configurazione SAML, vedere [Configurare l'accesso Single Sign-On basato su SAML](configure-saml-single-sign-on.md).

    :::image type="content" source="media/add-application-portal-setup-sso/github-pricing.png" alt-text="Screenshot che mostra la pagina di configurazione dell'accesso Single Sign-On nel portale di Azure AD.":::

> [!TIP]
> È possibile automatizzare la gestione delle app usando l'API Graph. Vedere [Automatizzare la gestione delle app con l'API Microsoft Graph](https://docs.microsoft.com/graph/application-saml-sso-configure-api).


## <a name="clean-up-resources"></a>Pulire le risorse

Al termine di questa serie di argomenti di avvio rapido, è consigliabile eliminare l'app per pulire il tenant di test. La procedura di eliminazione dell'app è illustrata nell'ultimo argomento di avvio rapido di questa serie. Vedere [Eliminare un'app](delete-application-portal.md).

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo per informazioni su come eliminare un'app.
> [!div class="nextstepaction"]
> [Eliminare un'app](delete-application-portal.md)
