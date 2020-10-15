---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con ContractSafe Saml2 SSO | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e ContractSafe Saml2 SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: jeedes
ms.openlocfilehash: 9459d13e8943f7e9177d2ad493873257f46f6649
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88544386"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-sso-with-contractsafe-saml2-sso"></a>Esercitazione: Integrare l'accesso Single Sign-On (SSO) di Azure Active Directory con ContractSafe Saml2 SSO

Questa esercitazione descrive come integrare ContractSafe Saml2 SSO con Azure Active Directory (Azure AD). Integrando ContractSafe Saml2 SSO con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a ContractSafe Saml2 SSO.
* Abilitare gli utenti per l'accesso automatico a ContractSafe Saml2 SSO con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di ContractSafe Saml2 SSO con l'accesso Single Sign-On abilitato.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. ContractSafe Saml2 SSO supporta l'accesso SSO avviato da **IDP**.

## <a name="add-contractsafe-saml2-sso-from-the-gallery"></a>Aggiungere ContractSafe Saml2 SSO dalla raccolta

Per configurare l'integrazione di ContractSafe Saml2 SSO in Azure AD, è necessario aggiungere ContractSafe Saml2 SSO dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **ContractSafe Saml2 SSO** nella casella di ricerca.
1. Selezionare **ContractSafe Saml2 SSO** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-contractsafe-saml2-sso"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per ContractSafe Saml2 SSO

Configurare e testare l'accesso Single Sign-On di Azure AD con ContractSafe Saml2 SSO usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in ContractSafe Saml2 SSO.

Per configurare e testare l'accesso SSO di Azure AD con ContractSafe Saml2 SSO, completare le procedure di base seguenti:

1. [Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso): per consentire agli utenti di usare questa funzionalità.
   * [Creare un utente di test di Azure AD](#create-an-azure-ad-test-user): per testare l'accesso Single Sign-On di Azure AD con l'account di **B.Simon**.
   * [Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user): per consentire a **B.Simon** di usare l'accesso Single Sign-On di Azure AD.

1. [Configurare l'accesso Single Sign-On di ContractSafe Saml2](#configure-contractsafe-saml2-sso): per configurare le impostazioni dell'accesso Single Sign-On sul lato applicazione.
   * [Creare un utente di test di ContractSafe Saml2 SSO](#create-a-contractsafe-saml2-sso-test-user): per avere una controparte di **B.Simon** in ContractSafe Saml2 SSO collegata alla rappresentazione dell'utente in Azure AD.
2. [Testare l'accesso Single Sign-On](#test-sso): per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **ContractSafe Saml2 SSO** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona di modifica (penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella pagina **Configura l'accesso Single Sign-On con SAML** immettere i valori seguenti per i campi corrispondenti:

    a. Nella casella di testo **Identificatore** immettere un URL nel formato seguente: `https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    b. Nella casella di testo **URL di risposta** immettere un URL nel formato seguente: `https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di ContractSafe Saml2 SSO](mailto:support@contractsafe.com). È anche possibile fare riferimento ai formati illustrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. ContractSafe Saml2 SSO prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![Attributi predefiniti comuni](common/default-attributes.png)

1. Oltre a quelli predefiniti, l'applicazione ContractSafe Saml2 SSO prevede il passaggio di altri attributi nella risposta SAML. Questi attributi vengono prepopolati, ma è possibile modificarli in base alle esigenze. L'elenco seguente mostra gli attributi aggiuntivi disponibili.

    | Nome | Attributo di origine|
    | ---------------| --------------- |
    | emailname | user.userprincipalname |
    | email | user.onpremisesuserprincipalname |

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione**. Selezionare **Scarica** per scaricare il certificato e quindi salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. In the **Configura ContractSafe Saml2 SSO** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

## <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome **B.Simon** nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**. Selezionare **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere un indirizzo di posta elettronica nel formato `username@companydomain.extension`. Un esempio è `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Selezionare **Create** (Crea).

## <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà **B.Simon** all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a ContractSafe Saml2 SSO.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **ContractSafe Saml2 SSO**.
1. Nella pagina di panoramica dell'app individuare la sezione **Gestione** e quindi selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

   ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** nell'elenco **Utenti**. Fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco. Fare quindi clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.

## <a name="configure-contractsafe-saml2-sso"></a>Configurare l'accesso Single Sign-On di ContractSafe Saml2

Per configurare l'accesso Single Sign-On sul lato **ContractSafe Saml2 SSO**, è necessario inviare il file **XML dei metadati della federazione** scaricato e gli URL appropriati, copiati dal portale di Azure, al [team di supporto di ContractSafe Saml2 SSO](mailto:support@contractsafe.com). Il team è responsabile della corretta impostazione della connessione SSO SAML su entrambi i lati.

## <a name="create-a-contractsafe-saml2-sso-test-user"></a>Creare un utente di test di ContractSafe Saml2 SSO

Creare un utente di nome B.Simon in ContractSafe Saml2 SSO. Collaborare con il [team di supporto di ContractSafe Saml2 SSO](mailto:support@contractsafe.com) per aggiungere gli utenti alla piattaforma ContractSafe Saml2 SSO. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO

Testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso. Quando si seleziona il riquadro di ContractSafe Saml2 SSO nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di ContractSafe Saml2 SSO per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare ContractSafe Saml2 SSO con Azure AD](https://aad.portal.azure.com/)
