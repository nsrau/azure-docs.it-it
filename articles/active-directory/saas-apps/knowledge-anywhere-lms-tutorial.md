---
title: 'Esercitazione: Integrazione di Azure Active Directory con Knowledge Anywhere LMS | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Knowledge Anywhere LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5cfa07b1-a792-4f0a-8c6f-1a13142193d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/14/2019
ms.author: jeedes
ms.openlocfilehash: f39952c74006964155fd23920c85506cac13a878
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57892719"
---
# <a name="tutorial-azure-active-directory-integration-with-knowledge-anywhere-lms"></a>Esercitazione: Integrazione di Azure Active Directory con Knowledge Anywhere LMS

Questa esercitazione descrive come integrare Knowledge Anywhere LMS con Azure Active Directory (Azure AD).
L'integrazione di Knowledge Anywhere LMS con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Knowledge Anywhere LMS.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Knowledge Anywhere LMS con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Knowledge Anywhere LMS, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Knowledge Anywhere LMS abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Knowledge Anywhere LMS supporta l'accesso SSO avviato da **SP e IDP**
* Knowledge Anywhere LMS supporta il provisioning utenti **JIT**

## <a name="adding-knowledge-anywhere-lms-from-the-gallery"></a>Aggiunta di Knowledge Anywhere LMS dalla raccolta

Per configurare l'integrazione di Knowledge Anywhere LMS in Azure AD, è necessario aggiungere Knowledge Anywhere LMS dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Knowledge Anywhere LMS dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Knowledge Anywhere LMS**, selezionare **Knowledge Anywhere LMS** nel pannello dei risultati, quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Knowledge Anywhere LMS nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Knowledge Anywhere LMS usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Knowledge Anywhere LMS.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Knowledge Anywhere LMS, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per Knowledge Anywhere LMS](#configure-knowledge-anywhere-lms-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Knowledge Anywhere LMS](#create-knowledge-anywhere-lms-test-user)**: per avere una controparte di Britta Simon in Knowledge Anywhere LMS collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Knowledge Anywhere LMS, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Knowledge Anywhere LMS** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Knowledge Anywhere LMS](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<CLIENTNAME>.knowledgeanywhere.com/`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<CLIENTNAME>.knowledgeanywhere.com/SSO/SAML/Response.aspx?<IDPNAME>`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore e l'URL di risposta effettivi, come illustrato più avanti in questa esercitazione.

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Knowledge Anywhere LMS](common/metadata-upload-additional-signon.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<CLIENTNAME>.knowledgeanywhere.com/`

    > [!NOTE]
    > Poiché il valore dell'URL di accesso non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere questo valore, contattare il [team di supporto clienti di Knowledge Anywhere LMS](https://knowany.zendesk.com/hc/en-us/articles/360000469034-SAML-2-0-Single-Sign-On-SSO-Set-Up-Guide). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

7. Nella sezione **Configura Knowledge Anywhere LMS** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-knowledge-anywhere-lms-single-sign-on"></a>Configurare l'accesso Single Sign-On per Knowledge Anywhere LMS

1. In un'altra finestra del browser aprire il portale di amministrazione di Knowledge Anywhere LMS.

2. Selezionare la scheda **Site** (Sito).

    ![Configurazione di Knowledge Anywhere LMS](./media/knowledge-anywhere-lms-tutorial/configure1.png)

3. Selezionare la scheda **SAML Settings** (Impostazioni SAML).

    ![Configurazione di Knowledge Anywhere LMS](./media/knowledge-anywhere-lms-tutorial/configure2.png)

4. Fare clic su **Add New** (Aggiungi nuova).

    ![Configurazione di Knowledge Anywhere LMS](./media/knowledge-anywhere-lms-tutorial/configure3.png)

5. Nella pagina **Add/Update SAML Settings** (Aggiungi/Aggiorna impostazioni SAML) seguire questa procedura:

    ![Configurazione di Knowledge Anywhere LMS](./media/knowledge-anywhere-lms-tutorial/configure4.png)

    a. Immettere il nome IDP previsto dall'organizzazione, ad esempio `Azure`.

    b. Nella casella di testo **IDP Entity ID** (ID entità IDP) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    c. Nella casella di testo **IDP URL** (URL IDP) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    d. Aprire nel Blocco note il file di certificato scaricato dal portale di Azure, copiare il contenuto del certificato e incollarlo nella casella di testo **Certificate** (Certificato).

    e. Nella casella di testo **Logout URL** (URL di disconnessione) incollare il valore di **URL disconnessione** copiato dal portale di Azure.

    f. Selezionare **Main Site** (Sito principale) nell'elenco a discesa **Domain** (Dominio).

    g. Copiare il valore di **SP Entity ID** (ID entità SP) e incollarlo nella casella di testo **Identificatore** nella sezione **Configurazione SAML di base** del portale di Azure.

    h. Copiare il valore di **SP Response(ACS) URL** (URL risposta SP - ACS) e incollarlo nella casella di testo **URL di risposta** nella sezione **Configurazione SAML di base** del portale di Azure.

    i. Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

Questa sezione descrive come creare un utente di test denominato Britta Simon nel portale di Azure.

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

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Knowledge Anywhere LMS.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Knowledge Anywhere LMS**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Knowledge Anywhere LMS**.

    ![Collegamento di Knowledge Anywhere LMS nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-knowledge-anywhere-lms-test-user"></a>Creare l'utente di test di Knowledge Anywhere LMS

In questa sezione viene creato un utente di nome Britta Simon in Knowledge Anywhere LMS. Knowledge Anywhere LMS supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Knowledge Anywhere LMS, ne viene creato uno nuovo dopo l'autenticazione.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Knowledge Anywhere LMS nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Knowledge Anywhere LMS per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)