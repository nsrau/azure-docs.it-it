---
title: Aggiungere un'app della raccolta - Azure Active Directory | Microsoft Docs
description: Informazioni su come aggiungere un'app dalla raccolta di Azure AD alle applicazioni aziendali di Azure.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 10/29/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: af0a826c499790c86a20ea7061075a6c3e66b3a4
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73062607"
---
# <a name="add-a-gallery-app-to-your-azure-ad-organization"></a>Aggiungere un'app della raccolta all'organizzazione di Azure AD

Azure Active Directory (Azure AD) offre una raccolta contenente migliaia di applicazioni preintegrate, abilitate con Enterprise Single Sign-On. Questo articolo descrive i passaggi generali per l'aggiunta di un'app della raccolta all'organizzazione di Azure AD.

> [!IMPORTANT]
> Cercare prima di tutto l'app nell'[elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/). È probabile che siano disponibili istruzioni dettagliate per l'aggiunta e la configurazione dell'app della raccolta che si vuole aggiungere.

## <a name="add-a-gallery-application"></a>Aggiungere un'app della raccolta

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale per il tenant di Azure AD, amministratore applicazione cloud o amministratore applicazione.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.

1. Nel riquadro **Azure Active Directory** selezionare **Applicazioni aziendali**.

    ![Aprire Applicazioni aziendali](media/add-gallery-app/open-enterprise-apps.png)


3. Per aggiungere un'app della raccolta al tenant, selezionare **Nuova applicazione**.

    ![Selezionare Nuova applicazione per aggiungere un'app della raccolta al tenant](media/add-gallery-app/new-application.png)

 4. Passare alla nuova esperienza di anteprima della raccolta: Nel banner nella parte superiore della **pagina Aggiungi applicazione** selezionare il collegamento **Fare clic qui per provare la nuova raccolta di app migliorata**.

5. Viene visualizzato il riquadro **Esplora la raccolta di Azure AD**, contenente sezioni per le piattaforme cloud, le applicazioni locali e le applicazioni in primo piano. Si noti che per le applicazioni elencate nella sezione **Applicazioni in primo piano** vengono visualizzate icone che indicano se sono supportati l'accesso Single Sign-On (SSO) federato e il provisioning.

    ![Cercare un'app per nome o categoria](media/add-gallery-app/browse-gallery.png)

6. Cercare nella raccolta l'applicazione che si vuole aggiungere o cercare l'applicazione immettendone il nome nella casella di ricerca. Selezionare quindi l'applicazione nei risultati. (Facoltativo) Nel modulo è possibile modificare il nome dell'applicazione in base alle esigenze dell'organizzazione.

    ![Mostra come aggiungere un'applicazione dalla raccolta](media/add-gallery-app/create-application.png)

7. Selezionare **Create** (Crea). Verrà visualizzata una pagina Attività iniziali con le opzioni per configurare l'applicazione per l'organizzazione.

## <a name="configure-user-sign-in-properties"></a>Configurare le proprietà di accesso degli utenti

1. Selezionare **Proprietà** per aprire il riquadro delle proprietà per la modifica.

    ![Riquadro Modifica proprietà](media/add-gallery-app/edit-properties.png)

1. Impostare le opzioni seguenti per determinare il modo in cui gli utenti assegnati o non assegnati all'applicazione possono accedere all'applicazione e se un utente può visualizzare l'applicazione nel pannello di accesso.

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

1. Per usare un logo personalizzato, crearne uno di 215 x 215 pixel e salvarlo in formato PNG. Quindi selezionare il logo e caricarlo.

    ![Modificare il logo](media/add-gallery-app/change-logo.png)

1. Al termine, fare clic su **Salva**.

## <a name="next-steps"></a>Passaggi successivi

Ora che l'applicazione è stata aggiunta all'organizzazione di Azure AD, [scegliere un metodo di Single Sign-On](what-is-single-sign-on.md#choosing-a-single-sign-on-method) da usare e fare riferimento all'articolo appropriato di seguito:

- [Configurare l'accesso Single Sign-On basato su SAML](configure-single-sign-on-non-gallery-applications.md)
- [Configurare l'accesso Single Sign-On tramite password](configure-password-single-sign-on-non-gallery-applications.md)
- [Configurare l'accesso collegato](configure-linked-sign-on.md)

