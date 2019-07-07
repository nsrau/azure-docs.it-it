---
title: 'Esercitazione: Integrazione di Azure Active Directory con Envi MMIS | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory ed Envi MMIS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab89f8ee-2507-4625-94bc-b24ef3d5e006
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff60e378e900d618cfc07f53959aa2d64518353c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67103358"
---
# <a name="tutorial-azure-active-directory-integration-with-envi-mmis"></a>Esercitazione: Integrazione di Azure Active Directory con Envi MMIS

Questa esercitazione descrive come integrare Envi MMIS con Azure Active Directory (Azure AD).
L'integrazione di Envi MMIS con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Envi MMIS.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Envi MMIS con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Envi MMIS, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Envi MMIS abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Envi MMIS supporta l'accesso SSO avviato da **SP** e **IDP**

## <a name="adding-envi-mmis-from-the-gallery"></a>Aggiunta di Envi MMIS dalla raccolta

Per configurare l'integrazione di Envi MMIS in Azure AD, è necessario aggiungere Envi MMIS dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Envi MMIS dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Envi MMIS**, selezionare **Envi MMIS** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Envi MMIS nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Envi MMIS usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Envi MMIS.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Envi MMIS, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per Envi MMIS](#configure-envi-mmis-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Envi MMIS](#create-envi-mmis-test-user)** : per avere una controparte di Britta Simon in Envi MMIS collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Envi MMIS, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Envi MMIS** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni sull'accesso Single Sign-On per URL e dominio di Envi MMIS](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://www.<CUSTOMER DOMAIN>.com/Account`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://www.<CUSTOMER DOMAIN>.com/Account/Acs`

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni sull'accesso Single Sign-On per URL e dominio di Envi MMIS](common/metadata-upload-additional-signon.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://www.<CUSTOMER DOMAIN>.com/Account`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori contattare il [team di supporto clienti di Envi MMIS](mailto:support@ioscorp.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

6. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

7. Nella sezione **Configura Envi MMIS** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-envi-mmis-single-sign-on"></a>Configurare l'accesso Single Sign-On per Envi MMIS

1. In una finestra del Web browser accedere al sito di Envi MMIS come amministratore.

2. Fare clic sulla scheda **My Domain** (Dominio personale).

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/envimmis-tutorial/configure1.png)

3. Fare clic su **Modifica**.

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/envimmis-tutorial/configure2.png)

4. Selezionare la casella di controllo **Use remote authentication** (Usa autenticazione remota) e quindi selezionare **HTTP Redirect** (Reindirizzamento HTTP) dall'elenco a discesa **Authentication Type** (Tipo di autenticazione).

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/envimmis-tutorial/configure3.png)

5. Selezionare la scheda **Resources** (Risorse) e quindi fare clic su **Upload Metadata** (Carica metadati).

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/envimmis-tutorial/configure4.png)

6. Nel popup **Upload Metadata** (Carica metadati) seguire questa procedura:

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/envimmis-tutorial/configure5.png)

    a. Selezionare l'opzione **File** dall'elenco a discesa **Upload From** (Carica da).

    b. Caricare il file di metadati scaricato dal portale di Azure selezionando l'**icona di selezione file**.

    c. Fare clic su **OK**.

7. Dopo aver caricato il file di metadati scaricato, i campi verranno popolati automaticamente. Fare clic su **Update** (Aggiorna).

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/envimmis-tutorial/configure6.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon\@dominioaziendale.estensione**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Envi MMIS.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Envi MMIS**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Envi MMIS**.

    ![Collegamento di Envi MMIS nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-envi-mmis-test-user"></a>Creare l'utente di test di Envi MMIS

Per consentire agli utenti di Azure AD di accedere a Envi MMIS, è necessario effettuarne il provisioning in Envi MMIS. Nel caso di Envi MMIS, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di Envi MMIS come amministratore.

2. Fare clic sulla scheda **User Access** (Accesso utente).

    ![Aggiungere un dipendente](./media/envimmis-tutorial/user1.png)

3. Fare clic sul pulsante **Add User** (Aggiungi utente).

    ![Aggiungere un dipendente](./media/envimmis-tutorial/user2.png)

4. Nella sezione **Add User** seguire questa procedura:

    ![Aggiungere un dipendente](./media/envimmis-tutorial/user3.png)

    a. Digitare il nome utente dell'account Britta Simon nella casella di testo **Nome utente** come **brittasimon\@contoso.com**.
    
    b. Nella casella di testo **First Name** (Nome) digitare il nome di BrittaSimon come **Britta**.

    c. Nella casella di testo **Last Name** (Cognome) digitare il cognome di BrittaSimon come **Simon**.

    d. Immettere la posizione dell'utente nella sezione **Title** (Titolo) della casella di testo.
    
    e. Digitare l'indirizzo e-mail dell'account Britta Simon nella casella di testo **Indirizzo e-mail** come **brittasimon\@contoso.com**.

    f. Digitare il nome utente dell'account Britta Simon nella casella di testo **Nome utente SSO** come **brittasimon\@contoso.com**.

    g. Fare clic su **Save**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Envi MMIS nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Envi MMIS per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

