---
title: 'Esercitazione: Integrazione di Azure Active Directory con iLMS | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e iLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d528195161b987dda783c4b22721e950f702f94
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56984459"
---
# <a name="tutorial-azure-active-directory-integration-with-ilms"></a>Esercitazione: Integrazione di Azure Active Directory con iLMS

Questa esercitazione descrive come integrare iLMS con Azure Active Directory (Azure AD).
L'integrazione di iLMS con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a iLMS.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a iLMS con i loro account Azure AD.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con iLMS sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di iLMS abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* iLMS supporta l'accesso SSO avviato da **SP e IDP**

## <a name="adding-ilms-from-the-gallery"></a>Aggiunta di iLMS dalla raccolta

Per configurare l'integrazione di iLMS in Azure AD, è necessario aggiungere iLMS dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere iLMS dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **iLMS**, selezionare **iLMS** dal pannello dei risultati quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![iLMS nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con iLMS usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in iLMS.

Per configurare e testare l'accesso Single Sign-On di Azure AD con iLMS, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di iLMS](#configure-ilms-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di iLMS](#create-ilms-test-user)**: per avere una controparte di Britta Simon in iLMS collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare Single Sign-On di Azure AD con iLMS, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com/), nella pagina di integrazione dell'applicazione **iLMS**, selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di iLMS](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** incollare il valore **Identificatore** copiato dalla sezione **Provider di servizi** delle impostazioni SAML nel portale di amministrazione iLMS.

    b. Nella casella di testo **URL di risposta** incollare il valore **Endpoint (URL)** copiato dalla sezione **Provider di servizi** delle impostazioni SAML nel portale di amministrazione iLMS con il modello seguente `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di iLMS](common/metadata-upload-additional-signon.png)

    Nella casella di testo **URL di accesso** incollare il valore **Endpoint (URL)** copiato dalla sezione **Provider di servizi** delle impostazioni SAML nel portale di amministrazione iLMS come `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

6. Per abilitare il provisioning JIT, l'applicazione iLMS prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi di token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti. Fare clic su  **Modifica**  per aprire la finestra di dialogo Attributi utente.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > È necessario abilitare **Create Un-recognized User Account** (Crea account utente non riconosciuto) in iLMS per eseguire il mapping di questi attributi. Seguire le istruzioni [qui](http://support.inspiredelearning.com/customer/portal/articles/2204526) per avere un'idea della configurazione degli attributi.

7. Oltre quelli elencati in precedenza, l'applicazione iLMS prevede il passaggio di qualche altro attributo nella risposta SAML. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** eseguire la procedura seguente per aggiungere l'attributo del token SAML come illustrato nella tabella seguente:

    | NOME | Source Attribute|
    | --------|------------- |
    | divisione | user.department |
    | region | user.state |
    | department | user. jobtitle |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    f. Fare clic su **Ok**

    g. Fare clic su **Save**.

8. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

9. Nella sezione **Configura iLMS** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-ilms-single-sign-on"></a>Configurare l'accesso Single Sign-On di iLMS

1. In un'altra finestra del Web browser accedere al **portale di amministrazione di iLMS** come amministratore.

2. Fare clic su **SSO:SAML** nella scheda **Impostazioni** per aprire le impostazioni di SAML ed eseguire la procedura seguente:

    ![Configure Single Sign-On](./media/ilms-tutorial/1.png)

3. Espandere la sezione **Provider di servizi** e copiare l'**Identificatore** e il valore **Endpoint (URL)**.

    ![Configure Single Sign-On](./media/ilms-tutorial/2.png) 

4. Nella sezione **Provider di identità** fare clic su **Import metadata** (Importa metadati).

5. Selezionare il file **metadati federazione** scaricato dal portale di Azure dalla sezione **Certificato di firma SAML**.

    ![Configure Single Sign-On](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png)

6. Se si desidera abilitare il provisioning JIT per creare gli account iLMS per utenti non riconosciuti, seguire questa procedura:

    a. Selezionare **Create Un-recognized User Account** (Crea account utente non riconosciuto).

    ![Configure Single Sign-On](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

    b. Eseguire il mapping degli attributi in Azure AD con gli attributi in iLMS. Nella colonna dell'attributo specificare il nome degli attributi o il valore predefinito.

    c. Passare alla scheda **Business Rules** (Regole Business) ed eseguire la procedura seguente:

    ![Configure Single Sign-On](./media/ilms-tutorial/5.png)

    d. Selezionare **Create Un-recognized Regions, Divisions and Departments** (Crea aree, divisioni e reparti non riconosciuti) per creare aree, divisioni e reparti che non esistono già al momento del Single Sign-on.

    e. Selezionare **Update User Profile During Sign-in** (Aggiorna il profilo utente durante l'accesso) per specificare se il profilo dell'utente viene aggiornato ad ogni Single Sign-on.

    f. Se l'opzione **Update Blank Values for Non Mandatory Fields in User Profile** (Aggiorna i valori vuoti per i campi non obbligatori nel profilo utente) è selezionata, i campi facoltativi del profilo vuoti al momento dell'accesso faranno sì che il profilo iLMS dell'utente contenga valori vuoti per quei campi.

    g. Selezionare **Send Error Notification Email** (Invia una notifica di errore via posta elettronica) e immettere l'indirizzo di posta elettronica dell'utente a cui si desidera ricevere la notifica di errore.

7. Fare clic sul pulsante **Save** (Salva) per salvare le impostazioni.

    ![Configure Single Sign-On](./media/ilms-tutorial/save.png)

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

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a iLMS.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **iLMS**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **iLMS**.

    ![Collegamento a iLMS nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-ilms-test-user"></a>Creare un utente di test di iLMS

L'applicazione supporta il provisioning dell'utente just-in-time e dopo l'autenticazione gli utenti vengono automaticamente creati nell'applicazione. JIT funzionerà se si è fatto clic sulla casella di controllo **Create Un-recognized User Account** (Crea account utente non riconosciuto) durante l'impostazione di configurazione SAML presso il portale di amministrazione di iLMS.

Se è necessario creare manualmente un utente, seguire questa procedura:

1. Accedere al sito aziendale di iLMS come amministratore.

2. Fare clic su **Register User** (Registra utente) nella scheda **Users** (Utenti) per aprire la pagina **Register User** (Registra utente).

   ![Aggiungere un dipendente](./media/ilms-tutorial/3.png)

3. Nella pagina **Register User** (Registra utente) seguire questa procedura.

    ![Aggiungere un dipendente](./media/ilms-tutorial/create_testuser_add.png)

    a. Nella casella di testo **Nome** digitare Britta.

    b. Nella casella di testo **Cognome** digitare Simon.

    c. Nella casella di testo **Email ID** (ID indirizzo di posta elettronica) digitare l'indirizzo di posta elettronica dell'utente nel formato BrittaSimon@contoso.com.

    d. Dall'elenco a discesa **Region** (Area), selezionare il valore per l'area.

    e. Dall'elenco a discesa **Division** (Divisione), selezionare il valore per la divisione.

    f. Dall'elenco a discesa **Department** (Reparto), selezionare il valore per il reparto.

    g. Fare clic su **Save**.

    > [!NOTE]
    > È possibile inviare all'utente un messaggio di registrazione selezionando la casella di controllo **Send Registration Mail** (Invia email di registrazione).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di iLMS nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione iLMS per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)