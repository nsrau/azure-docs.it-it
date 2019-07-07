---
title: 'Esercitazione: Integrazione di Azure Active Directory con iQualify LMS | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e iQualify LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8a3caaff-dd8d-4afd-badf-a0fd60db3d2c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: f7ca163b93a68525e80018051baa626bc378c200
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67099761"
---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>Esercitazione: Integrazione di Azure Active Directory con iQualify LMS

Questa esercitazione descrive come integrare iQualify LMS con Azure Active Directory (Azure AD).
L'integrazione di iQualify LMS con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a iQualify LMS.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a iQualify LMS con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con iQualify LMS, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di iQualify LMS abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* iQualify LMS supporta l'accesso SSO avviato da **SP e IDP**
* iQualify LMS supporta il provisioning utenti **JIT**

## <a name="adding-iqualify-lms-from-the-gallery"></a>Aggiunta di iQualify LMS dalla raccolta

Per configurare l'integrazione di iQualify LMS in Azure AD, è necessario aggiungere iQualify LMS dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere iQualify LMS dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **iQualify LMS**, nel pannello dei risultati selezionare **iQualify LMS** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![iQualify LMS nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con iQualify LMS usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in iQualify LMS.

Per configurare e testare l'accesso Single Sign-On di Azure AD con iQualify LMS, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di iQualify LMS](#configure-iqualify-lms-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di iQualify LMS](#create-iqualify-lms-test-user)** : per avere una controparte di Britta Simon in iQualify LMS collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con iQualify LMS, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **iQualify LMS** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di iQualify LMS](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente:
    | |
    |--|--|
    | Ambiente di produzione: `https://<yourorg>.iqualify.com/`|
    | Ambiente di test: `https://<yourorg>.iqualify.io`|

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente:
    | |
    |--|--|
    | Ambiente di produzione: `https://<yourorg>.iqualify.com/auth/saml2/callback` |
    | Ambiente di test: `https://<yourorg>.iqualify.io/auth/saml2/callback` |

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di iQualify LMS](common/metadata-upload-additional-signon.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente:
    | |
    |--|--|
    | Ambiente di produzione: `https://<yourorg>.iqualify.com/login` |
    | Ambiente di test: `https://<yourorg>.iqualify.io/login` |

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di iQualify LMS](https://www.iqualify.com/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

6. L'applicazione iQualify LMS prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti. Fare clic sull'icona  **Modifica** per aprire la finestra di dialogo **Attributi utente**. 

    ![image](common/edit-attribute.png)

7. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** modificare le attestazioni usando l'**icona Modifica** o aggiungere le attestazioni usando l'opzione **Aggiungi nuova attestazione** per configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura:

    | Nome | Attributo di origine|
    | --- | --- |
    | email | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | person_id | "your attribute" |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    f. Fare clic su **Ok**

    g. Fare clic su **Save**.

    > [!Note]
    > L'attributo **person_id** è **facoltativo**

8. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

9. Nella sezione **Configura iQualify LMS** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-iqualify-lms-single-sign-on"></a>Configurare l'accesso Single Sign-On di iQualify LMS

1. Aprire una nuova finestra del browser e quindi accedere all'ambiente di iQualify come amministratore.

1. Una volta connessi, fare clic sul proprio avatar in alto a destra e quindi fare clic su **Account settings** (Impostazioni account)

    ![Impostazioni dell'account](./media/iqualify-tutorial/setting1.png)

1. Nell'area delle impostazioni dell'account fare clic sul menu a sinistra sulla barra multifunzione e fare clic su **INTEGRATIONS** (INTEGRAZIONI)

    ![INTEGRAZIONI](./media/iqualify-tutorial/setting2.png)

1. In INTEGRATIONS (INTEGRAZIONI) fare clic sull'icona **SAML**.

    ![Icona SAML](./media/iqualify-tutorial/setting3.png)

1. Nella finestra di dialogo **SAML Authentication Settings** (Impostazioni di autenticazione SAML) seguire questa procedura:

    ![Impostazioni di autenticazione SAML](./media/iqualify-tutorial/setting4.png)

    a. Nella casella **SAML SINGLE SIGN-ON SERVICE URL** (URL SERVIZIO SINGLE SIGN-ON SAML) incollare il valore dell'**URL di accesso** copiato dalla finestra di configurazione dell'applicazione di Azure AD.

    b. Nella casella **SAML LOGOUT URL** (URL DI DISCONNESSIONE SAML) incollare il valore di **URL di disconnessione** copiato dalla finestra di configurazione dell'applicazione di Azure AD.

    c. Aprire il file del certificato scaricato nel Blocco note, copiarne il contenuto e quindi incollarlo nella casella **PUBLIC CERTIFICATE** (CERTIFICATO PUBBLICO).

    d. In **LOGIN BUTTON LABEL** (ETICHETTA DEL PULSANTE DI ACCESSO) immettere il nome del pulsante da visualizzare nella pagina di accesso.

    e. Fare clic su **SAVE**.

    f. Fare clic su **UPDATE** (AGGIORNA).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

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

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a iQualify LMS.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **iQualify LMS**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **iQualify LMS**.

    ![Collegamento di iQualify LMS nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-iqualify-lms-test-user"></a>Creare l'utente di test di iQualify LMS

In questa sezione viene creato un utente di nome Britta Simon in iQualify LMS. iQualify LMS supporta il provisioning JIT, abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in iQualify LMS, ne viene creato uno nuovo dopo l'autenticazione.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro iQualify LMS nel pannello di accesso, dovrebbe venire visualizzata la pagina di accesso dell'applicazione iQualify LMS. 

   ![pagina di accesso](./media/iqualify-tutorial/login.png) 

Fare clic sul pulsante **Sign in with Azure AD** (Accedi con Azure AD) e si dovrebbe accedere automaticamente all'applicazione iQualify LMS.

Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)