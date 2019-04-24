---
title: 'Esercitazione: integrazione di Azure Active Directory con RingCentral | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e RingCentral.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.openlocfilehash: 7cd20397c122834a23586bf51af6467f58531094
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "59997241"
---
# <a name="tutorial-azure-active-directory-integration-with-ringcentral"></a>Esercitazione: integrazione di Azure Active Directory con RingCentral

Questa esercitazione descrive come integrare RingCentral con Azure Active Directory (Azure AD).
L'integrazione di RingCentral con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a RingCentral.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a RingCentral con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con RingCentral, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di RingCentral abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* RingCentral supporta l'accesso SSO avviato da **SP**

## <a name="adding-ringcentral-from-the-gallery"></a>Aggiunta di RingCentral dalla raccolta

Per configurare l'integrazione di RingCentral in Azure AD, è necessario aggiungere RingCentral dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere RingCentral dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **RingCentral**, selezionare **RingCentral** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![RingCentral nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con RingCentral usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in RingCentral.

Per configurare e testare l'accesso Single Sign-On di Azure AD con RingCentral, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di RingCentral](#configure-ringcentral-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di RingCentral](#create-ringcentral-test-user)**: per avere una controparte di Britta Simon in RingCentral collegata alla relativa rappresentazione in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare il Single Sign-On di Azure AD con RingCentral, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **RingCentral** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base**, se si dispone di un **file di metadati di un provider di servizi**, seguire questa procedura:

    a. Fare clic su **Carica il file di metadati**.

    ![Caricare file di metadati](common/upload-metadata.png)

    b. Fare clic su **logo cartella** per selezionare il file di metadati e fare quindi clic su **Upload**.

    ![Scegliere file di metadati](common/browse-upload-metadata.png)

    c. Dopo il caricamento del file di metadati, il valore di **Identificatore** e **URL di risposta** viene inserito automaticamente nella sezione **Configurazione SAML di base**.

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di RingCentral](common/sp-identifier-reply.png)

    Nella casella di testo **URL di accesso** digitare un URL:
    
    | |
    |--|
    | `https://service.ringcentral.com`|
    | `https://service.ringcentral.com.au`|
    | `https://service.ringcentral.co.uk`|
    | `https://service.ringcentral.eu`|

    > [!Note]
    > Il **file di metadati del provider di servizi** sarà disponibile nella pagina di configurazione SSO di RingCentral, come spiegato più avanti nell'esercitazione.

5. Se non si dispone del **file di metadati del provider di servizi**, seguire questa procedura:

    a. Nella casella di testo **URL di accesso** digitare un URL:

    | |
    |--|
    | `https://service.ringcentral.com` |
    | `https://service.ringcentral.com.au` |
    | `https://service.ringcentral.co.uk` |
    | `https://service.ringcentral.eu` |

    b. Nella casella di testo **Identificatore** digitare un URL:

    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    c. Nella casella di testo **URL di risposta** digitare un URL:

    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di RingCentral](common/sp-identifier-reply.png)

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)

### <a name="configure-ringcentral-single-sign-on"></a>Configurare l'accesso Single Sign-On di RingCentral

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

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare `brittasimon@yourcompanydomain.extension`.  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a RingCentral.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **RingCentral**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **RingCentral**.

    ![Collegamento a RingCentral nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-ringcentral-test-user"></a>Creare un utente di test di RingCentral

In questa sezione viene creato un utente chiamato Britta Simon in RingCentral. Collaborare con il  [team di supporto clienti di RingCentral](https://success.ringcentral.com/RCContactSupp)  per aggiungere gli utenti alla piattaforma RingCentral. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di RingCentral nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione RingCentral per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
