---
title: 'Esercitazione: Integrazione di Azure Active Directory con Clear Review | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Clear Review.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: cc5a214d53441a5e1f30158a0264278b3cdc95a0
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92455920"
---
# <a name="tutorial-azure-active-directory-integration-with-clear-review"></a>Esercitazione: Integrazione di Azure Active Directory con Clear Review

Questa esercitazione descrive come integrare Clear Review con Azure Active Directory (Azure AD).
L'integrazione di Clear Review con Azure AD offre i vantaggi seguenti:

* In Azure AD è possibile controllare chi può accedere a Clear Review.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Clear Review con gli account Azure AD.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisites

Per configurare l'integrazione di Azure AD con Clear Review, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Clear Review abilitata per l'accesso Single Sign-On

> [!NOTE]
> È possibile usare questa integrazione anche dall'ambiente cloud US Government di Azure AD. Questa applicazione è disponibile nella raccolta di applicazioni cloud US Government di Azure AD e la procedura di configurazione è analoga a quella eseguita dal cloud pubblico.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Clear Review supporta l'accesso SSO avviato da **SP e IDP**

## <a name="adding-clear-review-from-the-gallery"></a>Aggiunta di Clear Review dalla raccolta

Per configurare l'integrazione di Clear Review in Azure AD, è necessario aggiungere Clear Review dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Clear Review dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni** .

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Clear Review** , nel pannello dei risultati selezionare **Clear Review** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Clear Review nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Clear Review usando un utente di test di nome **Britta Simon** .
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Clear Review.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Clear Review, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per Clear Review](#configure-clear-review-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Clear Review](#create-clear-review-test-user)** : per avere una controparte di Britta Simon in Clear Review collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Clear Review, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Clear Review** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On** .

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base** .

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP** :

    ![Screenshot che mostra la sezione Configurazione SAML di base in cui è possibile immettere l'identificatore e l'URL di risposta prima di selezionare Salva.](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<customer name>.clearreview.com/sso/metadata/`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<customer name>.clearreview.com/sso/acs/`

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP** :

    ![Screenshot che mostra l'opzione Impostare URL aggiuntivi che consente di immettere un URL di accesso.](common/metadata-upload-additional-signon.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<customer name>.clearreview.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Clear Review](https://clearreview.com/contact/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

6. L'applicazione Clear Review prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. La schermata seguente illustra l'elenco degli attributi predefiniti in cui **nameidentifier** è associato a **user.userprincipalname** . L'applicazione Clear Review prevede che **nameidentifier** sia associato a **user.mail** , di conseguenza è necessario modificare il mapping dell'attributo. A questo scopo, fare clic sull'icona **Modifica** e modificare il mapping.

    ![Screenshot che mostra il riquadro Attributi utente con l'icona di modifica selezionata.](common/edit-attribute.png)

7. Nella finestra di dialogo **Attributi e attestazioni utente** eseguire i passaggi seguenti:

    a. Fare clic sull' **icona Modifica** a destra del **valore identificatore nome** .

    ![Screenshot che mostra il riquadro Attributi utente e attestazioni con l'icona di modifica selezionata.](./media/clearreview-tutorial/attribute02.png)

    ![Screenshot che mostra la finestra di dialogo Gestisci attestazioni utente in cui è possibile immettere i valori descritti.](./media/clearreview-tutorial/attribute01.png)

    b. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo **user.mail** per la riga.

    c. Fare clic su **Salva** .

8. Nella pagina **Configura l'accesso Single Sign-On con SAML** , nella sezione **Certificato di firma SAML** , fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

9. Nella sezione **Set up Clear Review** (Configura Clear Review) copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di disconnessione

### <a name="configure-clear-review-single-sign-on"></a>Configurare l'accesso Single Sign-On per Clear Review

1. Per configurare l'accesso Single Sign-On in **Clear Review** , aprire il portale di **Clear Review** usando le credenziali di amministratore.

2. Selezionare **Admin** (Amministrazione) nel riquadro di spostamento a sinistra.

    ![Screenshot che mostra il portale di Clear Review con l'opzione Admin selezionata.](./media/clearreview-tutorial/tutorial_clearreview_app_admin1.png)

3. Nella sezione **Integrazioni** nella parte inferiore della pagina fare clic sul pulsante **Modifica** a destra di **Impostazioni Single Sign-on** .

    ![Screenshot che mostra il pulsante Change per Single Sign-On.](./media/clearreview-tutorial/tutorial_clearreview_app_admin2.png)

4. Nella pagina **Single Sign-On Settings** (Impostazioni Single Sign-On) seguire questa procedura:

    ![Screenshot che mostra la pagina Single Sign-On Settings in cui è possibile immettere le informazioni in questo passaggio.](./media/clearreview-tutorial/tutorial_clearreview_app_admin3.png)

    a. Nella casella di testo **URL autorità di certificazione** incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    b. Nella casella di testo **SAML Endpoint** (Endpoint SAML) incollare il valore di **URL di accesso** copiato dal portale di Azure.  

    c. Nella casella di testo **SLO Endpoint** (URL SLO) incollare il valore di **URL di disconnessione** copiato dal portale di Azure.  

    d. Aprire il certificato scaricato nel Blocco note e incollare il contenuto nella casella di testo **X.509 Certificate** (Certificato X.509).   

    e. Fare clic su **Salva** .

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD 

Questa sezione descrive come creare un utente di test di nome Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory** , **Utenti** e quindi **Tutti gli utenti** .

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon** .
  
    b. Nel campo **Nome utente** digitare **brittasimon\@dominioaziendale.estensione**  
    Ad esempio, usare BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Crea** .

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Clear Review.

1. Nel portale di Azure selezionare **Applicazioni aziendali** , quindi **Tutte le applicazioni** e infine **Clear Review** .

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Clear Review** .

    ![Collegamento di Clear Review nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione** .

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna** .

### <a name="create-clear-review-test-user"></a>Creare l'utente di test di Clear Review

In questa sezione viene creato un utente chiamato Britta Simon in Clear Review. Collaborare con il [team di supporto di Clear Review](https://clearreview.com/contact/) per aggiungere gli utenti nella piattaforma Clear Review.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Clear Review nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Clear Review per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)