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
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6656361fd4634c46cd5216b57eb8465536319f09
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73062793"
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

2. Selezionare **applicazioni aziendali** > **nuova applicazione**.

3. (Facoltativo ma consigliato) Nella casella di ricerca **sfoglia Azure ad Gallery** immettere il nome visualizzato dell'applicazione. 

4. Selezionare **Crea un'applicazione personalizzata**. Verrà visualizzata la pagina **Crea applicazione personalizzata** .

   ![Aggiunta di un'applicazione](media/add-non-gallery-app/create-your-own-application.png)

5. Iniziare a digitare il nome visualizzato per la nuova applicazione. Se sono presenti applicazioni della raccolta con nomi simili, verranno visualizzate nell'elenco dei risultati della ricerca.

   > [!NOTE]
   > Quando possibile, è consigliabile usare la versione della raccolta dell'applicazione. Se l'applicazione che si desidera aggiungere viene visualizzata nei risultati della ricerca, selezionare l'applicazione e ignorare il resto della procedura.

6. In **che cosa si vuole fare con l'applicazione?** scegliere **integra qualsiasi altra applicazione che non si trova nella raccolta**. Questa opzione viene in genere utilizzata per le applicazioni SAML e WS-Fed.

   > [!NOTE]
   > Le altre due opzioni vengono usate negli scenari seguenti:
   >* **Configurare il proxy di applicazione per l'accesso remoto sicuro a un'applicazione locale** apre la pagina di configurazione per Azure ad proxy di applicazione e i connettori.
   >* **Registrare un'applicazione a cui si sta lavorando per l'integrazione con Azure ad** apre la pagina di **registrazioni app** . Questa opzione viene in genere usata per le applicazioni OpenID Connect.

7. Selezionare **Create** (Crea). Viene visualizzata la pagina **Panoramica** dell'applicazione.

## <a name="configure-user-sign-in-properties"></a>Configurare le proprietà di accesso degli utenti

1. Selezionare **Proprietà** per aprire il riquadro delle proprietà per la modifica.

    ![Riquadro Modifica proprietà](media/add-non-gallery-app/edit-properties.png)

2. Impostare le opzioni seguenti per determinare il modo in cui gli utenti assegnati o non assegnati all'applicazione possono accedere all'applicazione e se un utente può visualizzare l'applicazione nel pannello di accesso.

    - **Abilitata per l'accesso degli utenti** determina se gli utenti assegnati all'applicazione potranno eseguire l'accesso.
    - **Assegnazione di utenti obbligatoria** determina se gli utenti non assegnati all'applicazione potranno eseguire l'accesso.
    - **Visibile agli utenti** determina se un'app verrà visualizzata agli utenti assegnati nel pannello di accesso e nell'icona di avvio delle app di O365.

      Comportamento per gli utenti **assegnati**:

       | Impostazioni delle proprietà dell'applicazione | | | Esperienza degli utenti assegnati | |
       |---|---|---|---|---|
       | Abilitata per l'accesso degli utenti? | Assegnazione utenti obbligatoria | Visibile agli utenti? | Gli utenti assegnati possono eseguire l'accesso? | L'applicazione viene visualizzata agli utenti assegnati?* |
       | Sì | Sì | Sì | Sì | Sì  |
       | Sì | Sì | no  | Sì | no   |
       | Sì | no  | Sì | Sì | Sì  |
       | Sì | no  | no  | Sì | no   |
       | no  | Sì | Sì | no  | no   |
       | no  | Sì | no  | no  | no   |
       | no  | no  | Sì | no  | no   |
       | no  | no  | no  | no  | no   |

      Comportamento per gli utenti **non assegnati**:

       | Impostazioni delle proprietà dell'applicazione | | | Esperienza degli utenti non assegnati | |
       |---|---|---|---|---|
       | Abilitata per l'accesso degli utenti? | Assegnazione utenti obbligatoria | Visibile agli utenti? | Gli utenti non assegnati possono eseguire l'accesso? | L'applicazione viene visualizzata agli utenti non assegnati?* |
       | Sì | Sì | Sì | no  | no   |
       | Sì | Sì | no  | no  | no   |
       | Sì | no  | Sì | Sì | no   |
       | Sì | no  | no  | Sì | no   |
       | no  | Sì | Sì | no  | no   |
       | no  | Sì | no  | no  | no   |
       | no  | no  | Sì | no  | no   |
       | no  | no  | no  | no  | no   |

     *L'applicazione viene visualizzata agli utenti nel pannello di accesso e nell'icona di avvio delle app di Office 365?

3. Per usare un logo personalizzato, crearne uno di 215 x 215 pixel e salvarlo in formato PNG. Quindi selezionare il logo e caricarlo.

    ![Modificare il logo](media/add-non-gallery-app/change-logo.png)

4. Al termine, fare clic su **Salva**.

## <a name="next-steps"></a>Passaggi successivi

Ora che l'applicazione è stata aggiunta all'organizzazione di Azure AD, [scegliere un metodo di Single Sign-On](what-is-single-sign-on.md#choosing-a-single-sign-on-method) da usare e fare riferimento all'articolo appropriato di seguito:

- [Configurare l'accesso Single Sign-On basato su SAML](configure-single-sign-on-non-gallery-applications.md)
- [Configurare l'accesso Single Sign-On tramite password](configure-password-single-sign-on-non-gallery-applications.md)
- [Configurare l'accesso collegato](configure-linked-sign-on.md)
