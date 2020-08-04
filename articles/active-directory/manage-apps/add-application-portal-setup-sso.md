---
title: "Avvio rapido: Configurare l'accesso Single Sign-On (SSO) per un'applicazione nel tenant di Azure Active Directory (Azure AD)"
description: Questo argomento di avvio rapido illustra il processo di configurazione dell'accesso Single Sign-On (SSO) per un'applicazione nel tenant di Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f8f19e6b98143bb48430decdd51f5626e72d422
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387286"
---
# <a name="quickstart-set-up-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Avvio rapido: Configurare l'accesso Single Sign-On (SSO) per un'applicazione nel tenant di Azure Active Directory (Azure AD)

Per iniziare a usare account di accesso utente semplificati, configurare l'accesso SSO per un'applicazione aggiunta al tenant di Azure AD. Dopo aver configurato l'accesso SSO, gli utenti potranno accedere a un'applicazione usando le loro credenziali di Azure AD. L'accesso SSO è incluso nell'edizione gratuita di Azure AD.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'accesso SSO per un'applicazione aggiunta al tenant di Azure AD, è necessario avere:

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uno dei ruoli seguenti: amministratore globale, amministratore di applicazioni cloud o amministratore di applicazioni oppure proprietario dell'entità servizio.
- Un'applicazione che supporta l'accesso SSO e che è già stata preconfigurata e aggiunta alla raccolta di Azure AD. La maggior parte delle app può usare Azure AD per l'accesso SSO. Le app della raccolta di Azure AD sono preconfigurate. Se l'app non è inclusa nell'elenco oppure è personalizzata, è comunque possibile usarla con Azure AD. Vedere le esercitazioni e gli altri documenti nel sommario. Questo argomento di avvio rapido illustra le app preconfigurate per l'accesso SSO e aggiunte alla raccolta di Azure AD dagli sviluppatori.
- Facoltativo: completamento di [Visualizzare le app](view-applications-portal.md).
- Facoltativo: completamento di [Aggiungere un'app](add-application-portal.md).
- Facoltativo: completamento di [Configurare un'app](add-application-portal-configure.md).


>[!IMPORTANT]
>Per testare i passaggi di questo argomento di avvio rapido, usare un ambiente non di produzione.


## <a name="enable-single-sign-on-for-an-app"></a>Abilitare l'accesso Single Sign-On per un'app

Una volta completata l'aggiunta di un'applicazione al tenant di Azure AD, viene visualizzata la pagina di panoramica. Se si configura un'applicazione già aggiunta, consultare il primo argomento di avvio rapido, che illustra come visualizzare le applicazioni aggiunte al tenant. 

Per configurare l'accesso Single Sign-On per un'applicazione:

1. Nel portale di Azure AD selezionare **Applicazioni aziendali**. Quindi trovare e selezionare l'applicazione da configurare per l'accesso Single Sign-On.
1. Nella sezione **Gestisci** selezionare **Single Sign-On** per aprire il riquadro **Single Sign-On** per la modifica.

    :::image type="content" source="media/add-application-portal-setup-sso/configure-sso.png" alt-text="Screenshot che mostra la pagina di configurazione dell'accesso Single Sign-On nel portale di Azure AD.":::

1. Selezionare **SAML** per aprire la pagina di configurazione dell'accesso SSO. In questo esempio l'applicazione configurata per l'accesso SSO è GitHub. Dopo aver configurato GitHub, gli utenti potranno accedere a GitHub usando le proprie credenziali del tenant di Azure AD.

    :::image type="content" source="media/add-application-portal-setup-sso/github-sso.png" alt-text="Screenshot che mostra la pagina di configurazione dell'accesso Single Sign-On in GitHub.":::

1. Il processo di configurazione di un'applicazione per l'uso di Azure AD per l'accesso SSO basato su SAML varia a seconda dell'applicazione. È disponibile un collegamento alle indicazioni per GitHub. Per trovare le guide per altre app, vedere [Esercitazioni per l'integrazione di applicazioni SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/).
1. Seguire la guida per configurare l'accesso SSO per l'applicazione. Molte applicazioni prevedono requisiti specifici della sottoscrizione per la funzionalità SSO. GitHub richiede, ad esempio, una sottoscrizione Enterprise.
    > [!TIP]
    > Per altre informazioni sulle opzioni di configurazione SAML, vedere [Configurare l'accesso Single Sign-On basato su SAML](configure-saml-single-sign-on.md).

    :::image type="content" source="media/add-application-portal-setup-sso/github-pricing.png" alt-text="Screenshot che mostra l'opzione Single Sign-On nella sottoscrizione aziendale della pagina di prezzi di GitHub.":::


## <a name="next-step"></a>Passaggio successivo

- [Eliminare un'app](delete-application-portal.md)
