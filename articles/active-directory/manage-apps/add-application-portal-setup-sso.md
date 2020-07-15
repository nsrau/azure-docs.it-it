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
ms.openlocfilehash: f90edf525a8deb35376d6637090e7b424b2fd658
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86038991"
---
# <a name="quickstart-set-up-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Avvio rapido: Configurare l'accesso Single Sign-On (SSO) per un'applicazione nel tenant di Azure Active Directory (Azure AD)

Per iniziare a usare account di accesso utente semplificati, configurare l'accesso SSO per un'applicazione aggiunta al tenant di Azure AD. Dopo aver configurato l'accesso SSO, gli utenti potranno accedere a un'applicazione usando le credenziali di Azure AD. L'accesso SSO è incluso nell'edizione gratuita di Azure AD.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'accesso SSO per un'applicazione aggiunta al tenant di Azure AD, sono necessari:

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uno dei ruoli seguenti: amministratore globale, amministratore di applicazioni cloud o amministratore di applicazioni oppure proprietario dell'entità servizio.
- Un'applicazione che supporta l'accesso Single Sign-On e che è già stata preconfigurata e aggiunta alla raccolta di Azure AD. La maggior parte delle app può usare Azure AD per l'accesso Single Sign-On. Le app nella raccolta di Azure AD sono state preconfigurate. Se l'app non è inclusa nell'elenco oppure è un'app sviluppata personalizzata, è comunque possibile usarla con Azure AD. Vedere le esercitazioni e gli altri documenti nel sommario. Questo argomento di avvio rapido illustra le app preconfigurate per l'accesso SSO e aggiunte alla raccolta di Azure AD dagli sviluppatori di app.
- (Facoltativo: completamento di [Visualizzare le app](view-applications-portal.md)).
- (Facoltativo: completamento di [Aggiungere un'app](add-application-portal.md)).
- (Facoltativo: completamento di [Configurare un'app](add-application-portal-configure.md)).


>[!IMPORTANT]
>Per testare i passaggi di questo argomento di avvio rapido, è consigliabile usare un ambiente non di produzione.


## <a name="enable-single-sign-on-for-an-app"></a>Abilitare l'accesso Single Sign-On per un'app

Dopo aver aggiunto un'applicazione al tenant di Azure AD, viene immediatamente visualizzata la pagina di panoramica di tale applicazione. Se si configura un'applicazione che è già stata aggiunta, esaminare il primo argomento di avvio rapido che spiega come visualizzare le applicazioni aggiunte al tenant. 

Per configurare l'accesso Single Sign-On per un'applicazione:

1. Nel portale di Azure AD selezionare **Applicazioni aziendali** e quindi trovare e selezionare l'applicazione da configurare per l'accesso Single Sign-On.
2. Nella sezione Gestisci selezionare **Single Sign-On** per aprire il riquadro delle proprietà per la modifica.
    :::image type="content" source="media/add-application-portal-setup-sso/configure-sso.png" alt-text="Mostra la pagina di configurazione dell'accesso Single Sign-On nel portale di Azure AD.":::
3. Selezionare SAML per aprire la pagina di configurazione dell'accesso SSO. In questo esempio l'applicazione configurata per l'accesso SSO è GitHub. Dopo aver configurato GitHub, gli utenti potranno accedere a GitHub usando le proprie credenziali dal tenant di Azure AD.
    :::image type="content" source="media/add-application-portal-setup-sso/github-sso.png" alt-text="Mostra la pagina di configurazione dell'accesso Single Sign-On in GitHub.":::
4. Il processo di configurazione di un'applicazione per l'uso di Azure AD per l'accesso SSO basato su SAML varia a seconda dell'applicazione. Si noti che è disponibile un collegamento alle indicazioni per GitHub. Le guide per altre app sono disponibili all'indirizzo: https://docs.microsoft.com/azure/active-directory/saas-apps/
5. Seguire la guida per configurare l'accesso SSO per l'applicazione. Molte applicazioni prevedono requisiti specifici della sottoscrizione per la funzionalità SSO. GitHub richiede, ad esempio, una sottoscrizione Enterprise.
    :::image type="content" source="media/add-application-portal-setup-sso/github-pricing.png" alt-text="Mostra l'opzione Single Sign-On nella sottoscrizione Enterprise della pagina dei prezzi di GitHub.":::


## <a name="next-steps"></a>Passaggi successivi

- [Eliminare un'app](delete-application-portal.md)
