---
title: Aggiungere un'applicazione non della raccolta - Microsoft Identity Platform | Microsoft Docs
description: Aggiungere un'applicazione non della raccolta al tenant di Azure Active Directory.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5db8aed0a47e7d8d928ef3287010d60efbc5e5da
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86200452"
---
# <a name="add-an-unlisted-non-gallery-application-to-your-azure-ad-organization"></a>Aggiungere un'applicazione non in elenco (non della raccolta) all'organizzazione di Azure AD

Oltre alle opzioni disponibili nella [raccolta di applicazioni di Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/), è possibile aggiungere un'**applicazione non della raccolta**. È possibile aggiungere qualsiasi applicazione che esiste già nell'organizzazione o qualsiasi applicazione di terze parti di un fornitore che non fa parte della raccolta di Azure AD. A seconda del [contratto di licenza](https://azure.microsoft.com/pricing/details/active-directory/) stipulato, sono disponibili le funzionalità seguenti:

- Integrazione self-service di qualsiasi applicazione che supporta i provider di identità [SAML (Security Assertion Markup Language) 2.0](https://wikipedia.org/wiki/SAML_2.0) (avviata dal provider di servizi o dal provider di identità)
- Integrazione self-service di qualsiasi applicazione Web con una pagina di accesso basata su HTML con [SSO basato su password](what-is-single-sign-on.md#password-based-sso)
- Connessione self-service di applicazioni che usano il [protocollo SCIM (System for Cross-domain Identity Management) per il provisioning utenti](../app-provisioning/use-scim-to-provision-users-and-groups.md)
- Possibilità di aggiungere collegamenti a qualsiasi applicazione nell'[icona di avvio delle app di Office 365](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) o nel [riquadro di accesso di Azure AD](what-is-single-sign-on.md#linked-sign-on)

Questo articolo descrive come aggiungere un'applicazione non della raccolta ad **Applicazioni aziendali** nel portale di Azure senza scrivere codice. Se si cercano invece indicazioni per gli sviluppatori sull'integrazione delle app personalizzate con Azure AD, vedere [Scenari di autenticazione per Azure AD](../develop/authentication-scenarios.md). Quando si sviluppa un'app che usa un protocollo moderno, ad esempio [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md) per autenticare gli utenti, è possibile registrarlo con Microsoft Identity Platform usando l'esperienza [Registrazioni app](../develop/quickstart-register-app.md) nel portale di Azure.

## <a name="add-a-non-gallery-application"></a>Aggiungere un'applicazione non inclusa nella raccolta

1. Accedere al [portale di Azure Active Directory](https://aad.portal.azure.com/) usando l'account amministratore di Microsoft Identity Platform.

2. Selezionare **Applicazioni aziendali** > **Nuova applicazione**.

3. (Facoltativo ma consigliato) Nella casella di ricerca **Esplora la raccolta di Azure AD** immettere il nome visualizzato dell'applicazione. 

4. Selezionare **Crea un'applicazione personalizzata**. Viene visualizzata la pagina **Crea un'applicazione personalizzata**.

   ![Aggiunta di un'applicazione](media/add-non-gallery-app/create-your-own-application.png)

5. Iniziare a digitare il nome visualizzato della nuova applicazione. Se sono presenti applicazioni della raccolta con nomi simili, verranno visualizzate nell'elenco dei risultati della ricerca.

   > [!NOTE]
   > Quando possibile, è consigliabile usare la versione dell'applicazione inclusa nella raccolta. Se l'applicazione da aggiungere viene visualizzata nei risultati della ricerca, selezionarla e ignorare il resto della procedura.

6. In **Che cosa si vuole fare con l'applicazione?** scegliere **Consente di integrare qualsiasi altra applicazione non trovata nella raccolta**. Questa opzione viene in genere usata per applicazioni SAML e WS-Fed.

   > [!NOTE]
   > Le altre due opzioni vengono usate negli scenari seguenti:
   >* **Configurare il proxy applicazione per l'accesso remoto sicuro a un'applicazione locale** apre la pagina di configurazione per il proxy e i connettori di Azure AD.
   >* **Consente di registrare l'applicazione che si sta sviluppando per integrarla con Azure AD** apre la pagina **Registrazioni app**. Questa opzione viene in genere usata per applicazioni OpenID Connect.

7. Selezionare **Crea**. Viene visualizzata la pagina **Panoramica** dell'applicazione.

## <a name="configure-user-sign-in-properties"></a>Configurare le proprietà di accesso degli utenti

1. Selezionare **Proprietà** per aprire il riquadro delle proprietà per la modifica.

    ![Riquadro Modifica proprietà](media/add-non-gallery-app/edit-properties.png)

2. Impostare le opzioni seguenti per determinare il modo in cui gli utenti assegnati o non assegnati all'applicazione possono accedere all'applicazione e se un utente può visualizzare l'applicazione nel pannello di accesso.

    - **Abilitata per l'accesso degli utenti** determina se gli utenti assegnati all'applicazione potranno eseguire l'accesso.
    - **Assegnazione di utenti obbligatoria** determina se gli utenti non assegnati all'applicazione potranno eseguire l'accesso.
    - **Visibile agli utenti** determina se un'app verrà visualizzata agli utenti assegnati nel pannello di accesso e nell'icona di avvio delle app di O365.

      Comportamento per gli utenti **assegnati**:

       | Proprietà dell'applicazione | Proprietà dell'applicazione | Proprietà dell'applicazione | Esperienza degli utenti assegnati | Esperienza degli utenti assegnati |
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

       | Proprietà dell'applicazione | Proprietà dell'applicazione | Proprietà dell'applicazione | Esperienza degli utenti non assegnati | Esperienza degli utenti non assegnati |
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

3. Per usare un logo personalizzato, crearne uno di 215 x 215 pixel e salvarlo in formato PNG. Quindi selezionare il logo e caricarlo.

    ![Modificare il logo](media/add-non-gallery-app/change-logo.png)

4. Al termine, fare clic su **Salva**.

## <a name="next-steps"></a>Passaggi successivi

Ora che l'applicazione è stata aggiunta all'organizzazione di Azure AD, [scegliere un metodo di Single Sign-On](what-is-single-sign-on.md#choosing-a-single-sign-on-method) da usare e fare riferimento all'articolo appropriato di seguito:

- [Configurare l'accesso Single Sign-On basato su SAML](configure-single-sign-on-non-gallery-applications.md)
- [Configurare l'accesso Single Sign-On tramite password](configure-password-single-sign-on-non-gallery-applications.md)
- [Configurare l'accesso collegato](configure-linked-sign-on.md)
