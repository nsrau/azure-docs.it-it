---
title: 'Esercitazione: integrazione di Azure Active Directory con RingCentral | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e RingCentral.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/09/2019
ms.author: jeedes
ms.openlocfilehash: c374c9e8fd91d50b7e6589f22f9bed09fbe0de39
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092864"
---
# <a name="tutorial-integrate-ringcentral-with-azure-active-directory"></a>Esercitazione: Integrare RingCentral con Azure Active Directory

In questa esercitazione si apprenderà come integrare RingCentral con Azure Active Directory (Azure AD). Integrando RingCentral con Azure AD è possibile:

* Controllare in Azure AD chi può accedere a RingCentral.
* Abilitare gli utenti per l'accesso automatico a RingCentral con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere una versione di valutazione gratuita per un mese [qui](https://azure.microsoft.com/pricing/free-trial/).
* Una sottoscrizione di RingCentral abilitata per l'accesso Single Sign-On.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. RingCentral supporta l'accesso SSO avviato da **IDP**

## <a name="adding-ringcentral-from-the-gallery"></a>Aggiunta di RingCentral dalla raccolta

Per configurare l'integrazione di RingCentral in Azure AD, è necessario aggiungere RingCentral dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **RingCentral** nella casella di ricerca.
1. Selezionare **RingCentral** nel riquadro dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

Configurare e testare l'accesso Single Sign-On di Azure AD con RingCentral usando un utente di test di nome **Britta Simon**. Per consentire il funzionamento del Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in RingCentral.

Per configurare e testare l'accesso Single Sign-On di Azure AD con RingCentral, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di RingCentral](#configure-ringcentral-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di RingCentral](#create-ringcentral-test-user)** : per avere una controparte di Britta Simon in RingCentral collegata alla relativa rappresentazione in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **RingCentral** del [portale di Azure](https://portal.azure.com/) trovare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base**, se si dispone di un **file di metadati di un provider di servizi**, seguire questa procedura:

    1. Fare clic su **Carica il file di metadati**.
    1. Fare clic su **logo cartella** per selezionare il file di metadati e fare quindi clic su **Upload**.
    1. Dopo il caricamento del file di metadati, il valore di **Identificatore** e **URL di risposta** viene inserito automaticamente nella sezione **Configurazione SAML di base**.

    > [!Note]
    > Il **file di metadati del provider di servizi** sarà disponibile nella pagina di configurazione SSO di RingCentral, come spiegato più avanti nell'esercitazione.

1. Se non si dispone del **file di metadati del provider di servizi**, immettere i valori per i campi seguenti:

    a. Nella casella di testo **Identificatore** digitare un URL:

    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    b. Nella casella di testo **URL di risposta** digitare un URL:

    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

1. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)

### <a name="configure-ringcentral-sso"></a>Configurare l'accesso Single Sign-On di RingCentral

1. In un'altra finestra del Web browser accedere a RingCentral come amministratore della sicurezza.

1. In alto fare clic su **Tools** (Strumenti).

    ![image](./media/ringcentral-tutorial/ringcentral1.png)

1. Passare a **Single Sign-on**.

    ![image](./media/ringcentral-tutorial/ringcentral2.png)

1. Nella pagina **Single Sign-on**, nella sezione **SSO Configuration** (Configurazione SSO), fare clic sul pulsante **Edit** (Modifica) relativo a **Step 1** (Passaggio 1) e seguire questa procedura:

    ![image](./media/ringcentral-tutorial/ringcentral3.png)

1. Nella pagina **Set up Single Sign-on** (Configura Single Sign-On) seguire questa procedura:

    ![image](./media/ringcentral-tutorial/ringcentral4.png)

    a. Fare clic su **Browse** (Sfoglia) per caricare il file di metadati scaricato dal portale di Azure.

    b. Dopo il caricamento dei metadati, i valori vengono popolati automaticamente nella sezione **SSO General Information** (Informazioni generali SSO).

    c. Nella sezione **Attribute Mapping** (Mapping attributi), in **Map Email Attribute to** (Esegui mapping attributo posta elettronica in) selezionare `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Fare clic su **Save**.

    e. In **Step 2** (Passaggio 2) fare clic su **Download** (Scarica) per scaricare il **file di metadati del provider di servizi** e caricarlo nella sezione **Configurazione SAML di base** per popolare automaticamente i valori **Identificatore** e **URL di risposta** nel portale di Azure.

    ![image](./media/ringcentral-tutorial/ringcentral6.png) 

    f. Nella stessa pagina, passare alla sezione **Enable SSO** (Abilita SSO) e seguire questa procedura:

    ![image](./media/ringcentral-tutorial/ringcentral5.png)

    * Selezionare **Enable SSO Service** (Abilita servizio SSO).

    * Selezionare **Allow users to log in with SSO or RingCentral credential** (Consenti agli utenti di accedere con le credenziali SSO o RingCentral).

    * Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione verrà creato un utente di test di nome Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `Britta Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `BrittaSimon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon verrà abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a RingCentral.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco di applicazioni selezionare **RingCentral**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-ringcentral-test-user"></a>Creare un utente di test di RingCentral

In questa sezione viene creato un utente chiamato Britta Simon in RingCentral. Collaborare con il  [team di supporto clienti di RingCentral](https://success.ringcentral.com/RCContactSupp)  per aggiungere gli utenti alla piattaforma RingCentral. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="test-sso"></a>Testare l'accesso SSO

Quando si seleziona il riquadro di RingCentral nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione RingCentral per cui si è configurato l'accesso Single Sign-On. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
