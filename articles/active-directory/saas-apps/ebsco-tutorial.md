---
title: 'Esercitazione: Integrazione di Azure Active Directory con EBSCO | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory ed EBSCO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 2753daf225016d3bd8e07383193a1260b40a36d5
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2019
ms.locfileid: "59564976"
---
# <a name="tutorial-azure-active-directory-integration-with-ebsco"></a>Esercitazione: Integrazione di Azure Active Directory con EBSCO

Questa esercitazione descrive come integrare EBSCO con Azure Active Directory (Azure AD).
L'integrazione di EBSCO con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a EBSCO.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a EBSCO con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con EBSCO, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di EBSCO abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* EBSCO supporta l'accesso SSO avviato da **SP** e **IDP**

* EBSCO supporta il provisioning utenti **JIT**

## <a name="adding-ebsco-from-the-gallery"></a>Aggiunta di EBSCO dalla raccolta

Per configurare l'integrazione di EBSCO in Azure AD, è necessario aggiungere EBSCO dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere EBSCO dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel pannello di spostamento a sinistra.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **EBSCO**, selezionare **EBSCO** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![EBSCO nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con EBSCO con un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in EBSCO.

Per configurare e testare l'accesso Single Sign-On di Azure AD con EBSCO, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di EBSCO](#configure-ebsco-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di EBSCO](#create-ebsco-test-user)**: per avere una controparte di Britta Simon in EBSCO collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con EBSCO, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione [EBSCO](https://portal.azure.com/) nel **portale di Azure** selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni Single Sign-On per URL e dominio di EBSCO](common/idp-identifier.png)

    Nella casella di testo **Identificatore** digitare un URL: `pingsso.ebscohost.com`

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![image](common/both-preintegrated-signon.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`

    > [!NOTE]
    > Poiché il valore di URL accesso non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere tali valori, contattare il [team di supporto clienti EBSCO](mailto:sso@ebsco.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

    o   **Elementi univoci:**  

    o   **Custid** = Immettere l'ID cliente EBSCO univoco 

    o   **Profile** = I clienti possono personalizzare il collegamento in modo da indirizzare gli utenti a uno profilo specifico, in base al prodotto acquistato da EBSCO. È possibile immettere un ID di profilo specifico. I principali ID sono eds (EBSCO Discovery Service) ed ehost (database EBSCOhost). Istruzioni specifiche sull'argomento sono disponibili in [questa pagina](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile).

6. L'applicazione EBSCO prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti. Fare clic sull'icona  **Modifica** per aprire la finestra di dialogo **Attributi utente**. 

    ![image](common/edit-attribute.png)

     > [!Note]
    > L'attributo **name** è obbligatorio ed è mappato al **valore identificatore nome** nell'applicazione EBSCO. Viene aggiunto per impostazione predefinita e non è quindi necessario inserirlo manualmente.

7. Oltre quelli elencati in precedenza, l'applicazione EBSCO prevede il passaggio di qualche altro attributo nella risposta SAML. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** eseguire la procedura seguente per aggiungere l'attributo del token SAML come illustrato nella tabella seguente: 

    | Nome | Source Attribute|
    | ---------------| --------------- |    
    | FirstName   | user.givenname |
    | LastName   | user.surname |
    | Email   | user.mail |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    f. Fare clic su **Save**.

8. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

9. Nella sezione **Configura EBSCO** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-ebsco-single-sign-on"></a>Configurare l'accesso Single Sign-On di EBSCO

Per configurare l'accesso Single Sign-On sul lato **EBSCO**, è necessario inviare il file di **XML metadati** scaricato e gli URL appropriati copiati dal portale di Azure al [team di supporto di EBSCO](mailto:sso@ebsco.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

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

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a EBSCO.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **EBSCO**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **EBSCO**.

    ![Collegamento di EBSCO nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-ebsco-test-user"></a>Creare l'utente di test di EBSCO

Nel caso di EBSCO, il provisioning utenti viene eseguito automaticamente.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

Azure AD passa i dati necessari all'applicazione EBSCO. Il provisioning utenti di EBSCO può essere eseguito automaticamente o richiedere la compilazione di un modulo unico, a seconda che il cliente abbia o meno molti account EBSCOhost preesistenti con impostazioni personali salvate. Questo aspetto può essere esaminato con il [team di supporto EBSCO](mailto:sso@ebsco.com) durante l'implementazione. In entrambi i casi, il cliente non deve creare alcun account EBSCOhost prima del test.

   >[!Note]
   >È possibile automatizzare la personalizzazione o il provisioning utenti EBSCOhost. Per informazioni sul provisioning utenti JIT (Just-In-Time), contattare il [team di supporto EBSCO](mailto:sso@ebsco.com). 

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

1. Quando si fa clic sul riquadro EBSCO nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione EBSCO.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

2. Dopo aver eseguito l'accesso all'applicazione, fare clic sul pulsante **Sign In** (Accedi) nell'angolo in alto a destra.

    ![Accesso a EBSCO nell'elenco delle applicazioni](./media/ebsco-tutorial/tutorial_ebsco_signin.png)
 
3. Si riceverà un messaggio unico in cui viene richiesto di associare i dati di accesso istituzionali/SAML con l'opzione **Link your existing MyEBSCOhost account to your institution account now** (Collega l'account MyEBSCOhost esistente all'account dell'istituto ora) oppure **Create a new MyEBSCOhost account and link it to your institution account** (Crea un nuovo account MyEBSCOhost e collegalo all'account dell'istituto). L'account viene usato per la personalizzazione nell'applicazione EBSCOhost. Selezionare l'opzione per **creare un nuovo account** e si noterà che il modulo per la personalizzazione è precompilato con i valori della risposta SAML, come illustrato nella schermata seguente. Fare clic su "**Continue**" (Continua) per salvare la selezione.
    
     ![Utente EBSCO nell'elenco delle applicazioni](./media/ebsco-tutorial/tutorial_ebsco_user.png)

1. Dopo aver completato la configurazione precedente, cancellare nuovamente i cookie, la cache e i dati di accesso. Le impostazioni di personalizzazione vengono memorizzate e non sarà necessario eseguire nuovamente l'accesso manuale.

## <a name="additional-sesources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

