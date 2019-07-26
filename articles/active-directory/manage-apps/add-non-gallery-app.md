---
title: Aggiungere un'applicazione non della raccolta-piattaforma di identità Microsoft | Microsoft Docs
description: Aggiungere un'applicazione non della raccolta al tenant di Azure AD.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 06/18/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: db8d8d6df16c5df7e29d8bb870c5d5eda6d8a2d3
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68477269"
---
# <a name="add-an-unlisted-non-gallery-application-to-your-azure-ad-organization"></a>Aggiungere un'applicazione non in elenco (non di raccolta) all'organizzazione Azure AD

Oltre alle opzioni disponibili nella raccolta di [applicazioni di Azure ad](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/), è possibile aggiungere un' **applicazione non della raccolta**. È possibile aggiungere qualsiasi applicazione già esistente nell'organizzazione o qualsiasi applicazione di terze parti da un fornitore che non fa già parte della raccolta di Azure AD. A seconda del [contratto di licenza](https://azure.microsoft.com/pricing/details/active-directory/), sono disponibili le seguenti funzionalità:

- Integrazione self-service di qualsiasi applicazione che supporta i provider di identità di [Security Assertion Markup Language (SAML) 2,0](https://wikipedia.org/wiki/SAML_2.0) (avviata da SP o IDP)
- Integrazione self-service di qualsiasi applicazione Web con una pagina di accesso basata su HTML con [SSO basato su password](what-is-single-sign-on.md#password-based-sso)
- Connessione self-service di applicazioni che usano il [sistema per il protocollo di gestione delle identità tra domini (SCIM) per il provisioning degli utenti](use-scim-to-provision-users-and-groups.md)
- Possibilità di aggiungere collegamenti a qualsiasi applicazione nell'[icona di avvio delle app di Office 365](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) o nel [riquadro di accesso di Azure AD](what-is-single-sign-on.md#linked-sign-on)

Questo articolo descrive come aggiungere un'applicazione non della raccolta alle **applicazioni aziendali** nel portale di Azure senza scrivere codice. Se invece si sta cercando informazioni aggiuntive per gli sviluppatori su come integrare app personalizzate con Azure AD, vedere [scenari di autenticazione per Azure ad](../develop/authentication-scenarios.md). Quando si sviluppa un'app che usa un protocollo moderno come [OpenID Connect/OAuth](../develop/active-directory-v2-protocols.md) per autenticare gli utenti, è possibile registrarlo con la piattaforma di identità Microsoft usando l'esperienza [registrazioni app](../develop/quickstart-register-app.md) nel portale di Azure.

## <a name="add-a-non-gallery-application"></a>Aggiungere un'applicazione non inclusa nella raccolta

1. Accedere al portale di [Azure Active Directory](https://aad.portal.azure.com/) usando l'account amministratore di Microsoft Identity Platform.
1. Selezionare **applicazioni** > aziendali**nuova applicazione**.
2. (Facoltativo ma consigliato) Nella casella di ricerca **Aggiungi dalla raccolta** immettere il nome visualizzato dell'applicazione. Se l'applicazione viene visualizzata nei risultati della ricerca, selezionarla e ignorare il resto di questa procedura.
3. Selezionare l' **applicazione non della raccolta**. Verrà visualizzata la pagina **Aggiungi applicazione personalizzata** .

   ![Aggiungi applicazione](./media/configure-single-sign-on-non-gallery-applications/add-your-own-application.png)
5. Immettere il nome visualizzato per la nuova applicazione.
6. Selezionare **Aggiungi**. Verrà visualizzata la pagina **Panoramica** dell'applicazione.

## <a name="configure-user-sign-in-properties"></a>Configurare le proprietà di accesso degli utenti

1. Selezionare **Proprietà** per aprire il riquadro delle proprietà per la modifica.

    ![Riquadro Modifica proprietà](media/add-application-portal/edit-properties.png)

1. Impostare le opzioni seguenti per determinare il modo in cui gli utenti assegnati o non assegnati all'applicazione possono accedere all'applicazione e se un utente può visualizzare l'applicazione nel pannello di accesso.

    - **Abilitata per l'accesso degli utenti** determina se gli utenti assegnati all'applicazione potranno eseguire l'accesso.
    - **Assegnazione di utenti obbligatoria** determina se gli utenti non assegnati all'applicazione potranno eseguire l'accesso.
    - **Visibile agli utenti** determina se un'app verrà visualizzata agli utenti assegnati nel pannello di accesso e nell'icona di avvio delle app di O365.

      Comportamento per gli utenti **assegnati**:

       | Impostazioni delle proprietà dell'applicazione | | | Esperienza degli utenti assegnati | |
       |---|---|---|---|---|
       | Abilitata per l'accesso degli utenti? | Assegnazione utenti obbligatoria | Visibile agli utenti? | Gli utenti assegnati possono eseguire l'accesso? | L'applicazione viene visualizzata agli utenti assegnati?* |
       | sì | sì | sì | sì | sì  |
       | sì | sì | no  | sì | no   |
       | sì | no  | sì | sì | sì  |
       | sì | no  | no  | sì | no   |
       | no  | sì | sì | no  | no   |
       | no  | sì | no  | no  | no   |
       | no  | no  | sì | no  | no   |
       | no  | no  | no  | no  | no   |

      Comportamento per gli utenti **non assegnati**:

       | Impostazioni delle proprietà dell'applicazione | | | Esperienza degli utenti non assegnati | |
       |---|---|---|---|---|
       | Abilitata per l'accesso degli utenti? | Assegnazione utenti obbligatoria | Visibile agli utenti? | Gli utenti non assegnati possono eseguire l'accesso? | L'applicazione viene visualizzata agli utenti non assegnati?* |
       | sì | sì | sì | no  | no   |
       | sì | sì | no  | no  | no   |
       | sì | no  | sì | sì | no   |
       | sì | no  | no  | sì | no   |
       | no  | sì | sì | no  | no   |
       | no  | sì | no  | no  | no   |
       | no  | no  | sì | no  | no   |
       | no  | no  | no  | no  | no   |

     *L'applicazione viene visualizzata agli utenti nel pannello di accesso e nell'icona di avvio delle app di Office 365?

1. Per usare un logo personalizzato, creare un logo 215 di 215 pixel e salvarlo in formato PNG. Individuare quindi il logo e caricarlo.

    ![Modificare il logo](media/add-application-portal/change-logo.png)

1. Al termine, fare clic su **Salva**.

## <a name="next-steps"></a>Passaggi successivi

Ora che l'applicazione è stata aggiunta all'organizzazione Azure AD, [scegliere un metodo Single Sign-on](what-is-single-sign-on.md#choosing-a-single-sign-on-method) che si vuole usare e fare riferimento all'articolo appropriato di seguito:

- [Configurare l'accesso Single Sign-on basato su SAML](configure-single-sign-on-non-gallery-applications.md)
- [Configurare l'accesso Single Sign-on basato su password](configure-password-single-sign-on-non-gallery-applications.md)
- [Configurare l'accesso collegato](configure-linked-sign-on.md)
