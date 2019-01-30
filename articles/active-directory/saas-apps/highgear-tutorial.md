---
title: 'Esercitazione: Integrazione di Azure Active Directory con HighGear | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e HighGear.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 55dcd2fb-96b7-46ec-9e69-eee71c535773
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.openlocfilehash: 71e95e0c4197ba53eaf641259858d3229036ee0c
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54808767"
---
# <a name="tutorial-azure-active-directory-integration-with-highgear"></a>Esercitazione: Integrazione di Azure Active Directory con HighGear

Questa esercitazione descrive come integrare HighGear con Azure Active Directory (Azure AD).
L'integrazione di HighGear con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a HighGear.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a HighGear con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con HighGear, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sistema HighGear con licenza Enterprise o Unlimited

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione si apprende come eseguire la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* HighGear supporta l'accesso SSO avviato da **SP e IdP**

## <a name="adding-highgear-from-the-gallery"></a>Aggiunta di HighGear dalla raccolta

Per configurare l'integrazione di HighGear in Azure AD, è necessario aggiungere HighGear dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere HighGear dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel pannello di spostamento a sinistra.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **HighGear**, selezionare **HighGear** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![HighGear nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione si apprende come configurare e testare l'accesso Single Sign-On di Azure AD con il sistema HighGear usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato nel sistema HighGear.

Per configurare e testare l'accesso Single Sign-On di Azure AD con il sistema HighGear, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per HighGear](#configure-highgear-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione HighGear.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di HighGear](#create-highgear-test-user)**: per avere una controparte di Britta Simon in HighGear collegata alla rappresentazione dell'utente in Azure AD. 
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione si apprende come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con il sistema HighGear, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **HighGear** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di HighGear](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** incollare il valore del campo **Service Provider Entity ID** (ID entità provider di servizi) visualizzato nella pagina Single Sign-On Settings (Impostazioni di Single Sign-On) nel sistema HighGear.

    ![Campo Service Provider Entity ID](media/highgear-tutorial/service-provider-entity-id-field.png)
    
    > [!NOTE]
    > Per accedere alla pagina Single Sign-On Settings (Impostazioni di Single Sign-On), è necessario eseguire l'accesso al sistema HighGear. Dopo aver eseguito l'accesso passare con il puntatore del mouse sulla scheda Administration (Amministrazione) in HighGear e fare clic sulla voce di menu Single Sign-On Settings (Impostazioni di Single Sign-On).
    
    ![Voce di menu Single Sign-On Settings](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

    b. Nella casella di testo **URL di risposta** incollare il valore di **Assertion Consumer Service (ACS) URL** (URL ACS) della pagina Single Sign-On Settings (Impostazioni di Single Sign-On) nel sistema HighGear.

    ![Campo Assertion Consumer Service (ACS) URL](media/highgear-tutorial/assertion-consumer-service-url-field.png)

    c. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

     ![Informazioni su URL e dominio per l'accesso Single Sign-On di HighGear](common/metadata-upload-additional-signon.png)

     Nella casella di testo **URL di accesso** incollare il valore del campo **Service Provider Entity ID** (ID entità provider di servizi) visualizzato nella pagina Single Sign-On Settings (Impostazioni di Single Sign-On) nel sistema HighGear. Questo ID entità è anche l'URL di base del sistema HighGear da usare per l'accesso avviato da SP.

    ![Campo Service Provider Entity ID](media/highgear-tutorial/service-provider-entity-id-field.png)

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi indicati nella pagina **Single Sign-On Settings** (Impostazioni Single Sign-On) nel sistema HighGear. Per richiedere assistenza, contattare il [supporto tecnico di HighGear](mailto:support@highgear.com).

4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML** fare clic su **Scarica** per scaricare il file **Certificato (Base64)** e salvarlo nel computer in uso. Sarà necessario in un passaggio successivo della configurazione di Single Sign-On.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura HighGear** prendere nota del percorso degli URL seguenti.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso. Questo valore sarà necessario nel passaggio 2 della procedura **Configurare l'accesso Single Sign-On per HighGear**.

    b. Identificatore di Azure AD. Questo valore sarà necessario nel passaggio 3 della procedura **Configurare l'accesso Single Sign-On per HighGear**.

    c. URL di disconnessione. Questo valore sarà necessario nel passaggio 4 della procedura **Configurare l'accesso Single Sign-On per HighGear**.

### <a name="configure-highgear-single-sign-on"></a>Configurare l'accesso Single Sign-On per HighGear

Per configurare HighGear per Single Sign-On, accedere al sistema HighGear. Dopo aver eseguito l'accesso passare con il puntatore del mouse sulla scheda Administration (Amministrazione) in HighGear e fare clic sulla voce di menu Single Sign-On Settings (Impostazioni di Single Sign-On).

![Voce di menu Single Sign-On Settings](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

1. In **Nome del provider di identità** digitare una breve descrizione che verrà visualizzata sul pulsante Single Sign-On di HighGear nella pagina di accesso. Ad esempio: Azure AD

2. Nel campo **Single Sign-On (SSO) URL** (URL Single Sign-On (SSO)) in HighGear incollare il valore del campo **URL di accesso** presente nella sezione **Configura HighGear** in Azure.

3. Nel campo **Identity Provider Entity ID** (ID entità provider di identità) in HighGear incollare il valore del campo **Identificatore Azure AD** presente nella sezione **Configura HighGear** in Azure.

4. Nel campo **Single Logout (SLO) URL** (URL Single Log-Out (SLO)) in HighGear incollare il valore del campo **URL di disconnessione** presente nella sezione **Configura HighGear** in Azure.

5. Usare il Blocco note per aprire il certificato scaricato dalla sezione **Certificato di firma SAML** in Azure. Il formato scaricato dovrebbe essere **Certificato (Base64)**. Copiare il contenuto del certificato dal Blocco note e incollarlo nel campo **Identity Provider Certificate** (Certificato provider di identità) in HighGear.

6. Inviare un messaggio di posta elettronica al [team di supporto di HighGear](mailto:support@highgear.com) per richiedere il certificato HighGear. Seguire le istruzioni ricevute da HighGear per compilare i campi **HighGear Certificate** (Certificato HighGear) e **HighGear Certificate Password** (Password del certificato HighGear).

7. Fare clic sul pulsante **Save** (Salva) per salvare la configurazione Single Sign-On di HighGear.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD 

Questa sezione descrive come creare un utente di test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a HighGear.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **HighGear**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **HighGear**.

    ![Collegamento di HighGear nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-highgear-test-user"></a>Creare l'utente di test di HighGear

Per creare un utente di test di HighGear per testare la configurazione di Single Sign-On, accedere al sistema HighGear.

1. Fare clic sul pulsante **Create New Contact** (Crea nuovo contatto).

    ![Pulsante Create New Contact](media/highgear-tutorial/create-new-contact-button.png)

    Verrà visualizzato un menu che consente di scegliere il tipo di contatto da creare.

2. Fare clic sulla voce di menu **Individual** (Singolo) per creare un utente di HighGear.

    Sulla destra apparirà un riquadro in cui digitare le informazioni relative al nuovo utente.  
    ![Modulo New Contact](media/highgear-tutorial/new-contact-form.png)

3. Nel campo **Name** (Nome) digitare un nome per il contatto. Ad esempio: Britta Simon

4. Fare clic sul menu **More Options** (Altre opzioni) e selezionare la voce di menu **Account Info** (Informazioni account).

    ![Selezione della voce di menu Account Info](media/highgear-tutorial/account-info-menu-item.png)

5. Impostare il campo **Can Log In** (Può accedere) su Yes (Sì).

    Anche il campo **Enable Single Sign-On** (Abilita Single Sign-On) verrà impostato automaticamente su Yes (Sì).

6. Nel campo **Single Sign-On User Id** (ID utente Single Sign-On) digitare l'ID dell'utente. Ad esempio: BrittaSimon@contoso.com

    La sezione Account Info (Informazioni account) dovrebbe essere simile a quella seguente:  
    ![Sezione Account Info compilata](media/highgear-tutorial/finished-account-info-section.png)

7. Per salvare il contatto, fare clic sul pulsante **Save** (Salva) nella parte inferiore del riquadro.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di HighGear nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione HighGear per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

