---
title: 'Esercitazione: Integrazione di Azure Active Directory con DocuSign | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 607e5aded52375190878de6b48ffa4aa2ab49767
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67104094"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Esercitazione: Integrazione di Azure Active Directory con DocuSign

Questa esercitazione descrive come integrare DocuSign con Azure Active Directory (Azure AD).
L'integrazione di DocuSign con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a DocuSign.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a DocuSign con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con DocuSign, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di DocuSign abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* DocuSign supporta l'accesso SSO avviato da **SP**

* DocuSign supporta il provisioning utenti **JIT**

## <a name="adding-docusign-from-the-gallery"></a>Aggiunta di DocuSign dalla raccolta

Per configurare l'integrazione di DocuSign in Azure AD, è necessario aggiungere DocuSign dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere DocuSign dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **DocuSign**, selezionare **DocuSign** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![DocuSign nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con DocuSign in base a un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in DocuSign.

Per configurare e testare l'accesso Single Sign-On di Azure AD con DocuSign, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di DocuSign](#configure-docusign-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente test di DocuSign](#create-docusign-test-user)** : per avere una controparte di Britta Simon in DocuSign collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con DocuSign, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione [DocuSign](https://portal.azure.com/) del **portale di Azure** selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di DocuSign](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL usando il modello seguente: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di accesso effettivi. La procedura a questo scopo viene descritta più avanti nella sezione **Visualizzare endpoint SAML 2.0** dell'esercitazione.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura DocuSign** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-docusign-single-sign-on"></a>Configurare l'accesso Single Sign-On di DocuSign

1. In un'altra finestra del Web browser accedere al **portale di amministrazione di DocuSign** come amministratore.

2. Nell'angolo in alto a destra della pagina fare clic sul **logo** del profilo e quindi su **Go to Admin** (Vai ad amministrazione).
  
    ![Configurazione dell'accesso Single Sign-On][51]

3. Nella pagina delle soluzioni di dominio fare clic su **Domini**

    ![Configurazione dell'accesso Single Sign-On][50]

4. Nella sezione **Domini** fare clic su **CLAIM DOMAIN** (RICHIEDI DOMINIO).

    ![Configurazione dell'accesso Single Sign-On][52]

5. Nella finestra di dialogo **Claim a domain** (Richiedi un dominio) digitare il dominio aziendale nella casella di testo **Nome dominio** e quindi fare clic su **CLAIM** (RICHIEDI). Verificare il dominio e che lo stato sia attivo.

    ![Configurazione dell'accesso Single Sign-On][53]

6. Nella pagina delle soluzioni di dominio fare clic su **Provider di identità**.
  
    ![Configurazione dell'accesso Single Sign-On][54]

7. Nella sezione **Provider di identità** fare clic su **AGGIUNGI PROVIDER DI IDENTITÀ**. 

    ![Configurazione dell'accesso Single Sign-On][55]

8. Nella pagina **Identity Provider Settings** (Impostazioni provider di identità) eseguire questa procedura:

    ![Configurazione dell'accesso Single Sign-On][56]

    a. Nella casella di testo **Name** (Nome) digitare un nome per la configurazione. Non usare spazi.

    b. Nella casella di testo **Autorità di certificazione del provider di identità** incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    c. Nella casella di testo **URL di accesso provider di identità** incollare il valore di **URL di accesso** copiato dal portale di Azure.

    d. Nella casella di testo **URL di disconnessione provider di identità** incollare il valore di **URL disconnessione** copiato dal portale di Azure.

    e. Selezionare **Sign AuthN Request** (Firma richiesta di autenticazione).

    f. Per **Invia richiesta di autenticazione da** selezionare **POST**.

    g. Per **Invia richiesta di disconnessione da** selezionare **GET**.

    h. Nella sezione **Custom Attribute Mapping** (Mapping attributi personalizzati) fare clic su **AGGIUNGI NUOVO MAPPING**.

    ![Configurazione dell'accesso Single Sign-On][62]

    i. Scegliere il campo di cui si vuole eseguire il mapping all'attestazione di Azure AD. In questo esempio, viene eseguito il mapping dell'attestazione **emailaddress** con il valore di **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** . Si tratta del nome di attestazione predefinito di Azure AD per l'attestazione basata su posta elettronica. Fare clic su **SALVA**.

    ![Configurazione dell'accesso Single Sign-On][57]

    > [!NOTE]
    > Usare l' **Identificatore utente** appropriato per associare l'utente da Azure AD al mapping degli utenti di DocuSign. Selezionare il campo corretto e immettere il valore appropriato in base alle impostazioni dell'organizzazione.

    j. Nella sezione **Certificato provider di identità** fare clic su **AGGIUNGI CERTIFICATO**, caricare il certificato scaricato dal portale di Azure AD e quindi fare clic su **SALVA**.

    ![Configurazione dell'accesso Single Sign-On][58]

    k. Nella sezione **Provider di identità** fare clic su **AZIONI** e quindi su **Endpoint**.

    ![Configurazione dell'accesso Single Sign-On][59]

    l. Nella sezione **View SAML 2.0 Endpoints** (Visualizza endpoint SAML 2.0) del **portale di amministrazione di DocuSign** seguire questa procedura:

    ![Configurazione dell'accesso Single Sign-On][60]

    * Copiare il valore di **Service Provider Issuer URL** (URL autorità di certificazione provider di servizi) e quindi incollarlo nella casella di testo **Identificatore** nella sezione **Configurazione SAML di base** del portale di Azure.

    * Copiare il valore di **Service Provider Login URL** (URL autorità di certificazione provider di servizi) e quindi incollarlo nella casella di testo **URL di accesso** nella sezione **Configurazione SAML di base** del portale di Azure.

    * Fare clic su **Close**

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare brittasimon@yourcompanydomain.extension. Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a DocuSign.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **DocuSign**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **DocuSign**.

    ![Collegamento di DocuSign nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-docusign-test-user"></a>Creare un utente test di DocuSign

In questa sezione viene creato un utente di nome Britta Simon in DocuSign. DocuSign supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in DocuSign, ne viene creato uno nuovo dopo l'autenticazione.

>[!Note]
>Per creare un utente manualmente, contattare il [team di supporto di DocuSign](https://support.docusign.com/).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di DocuSign nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione DocuSign per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png
