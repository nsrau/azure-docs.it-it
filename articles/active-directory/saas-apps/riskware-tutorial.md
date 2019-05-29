---
title: 'Esercitazione: Integrazione di Azure Active Directory con Riskware | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Riskware.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 81866167-b163-4695-8978-fd29a25dac7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 08db463c92e4ee34819f446c3827ed8dfddc02d7
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65903943"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>Esercitazione: Integrazione di Azure Active Directory con Riskware

Questa esercitazione descrive come integrare Riskware con Azure Active Directory (Azure AD).
L'integrazione di Riskware con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Riskware.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Riskware con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Riskware, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione abilitata Riskware per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Riskware supporta l'accesso SSO avviato da **SP**

## <a name="adding-riskware-from-the-gallery"></a>Aggiunta di Riskware dalla raccolta

Per configurare l'integrazione di Riskware in Azure AD, è necessario aggiungere Riskware dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Riskware dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Riskware**, selezionare **Riskware** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Riskware nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Riskware in base a un utente test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Riskware.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Riskware, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Riskware](#configure-riskware-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare utente di test di Riskware](#create-riskware-test-user)** : per avere una controparte di Britta Simon in Riskware collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare Single Sign-On di Azure AD con Riskware, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Riskware** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Riskware](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente:
    
    | Environment| Modello URL|
    |--|--|
    | Test di accettazione utente|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | PROD| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | DEMO| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. Nella casella di testo **Identificatore (ID entità)** digitare l' URL:
    
    | Environment| Modello URL|
    |--|--|
    | Test di accettazione utente| `https://riskcloud.net/uat` |
    | PROD| `https://riskcloud.net/prod` |
    | DEMO| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > Il valore dell'URL di accesso non è reale. è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere il valore contattare il [team di supporto client di Riskware](mailto:support@pansoftware.com.au). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura Riskware** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-riskware-single-sign-on"></a>Configurare l'accesso Single Sign-On Riskware

1. In un'altra finestra del Web browser accedere al sito aziendale di Riskware come amministratore.

1. In alto a destra, fare clic su **Manutenzione** per aprire la pagina di manutenzione.

    ![Manutenzione di Configurazione Riskware](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Nella pagina di manutenzione, fare clic su **Autenticazione**.

    ![Autenticazione di Configurazione Riskware](./media/riskware-tutorial/tutorial_riskware_authen.png)

1. Nella pagina **Configurazione di autenticazione** seguire questa procedura:

    ![Configurazione di autenticazione di Configurazione Riskware](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. Selezionare **Tipo** come **SAML** per l'autenticazione.

    b. Nella casella di testo **Codice** digitare il codice come AZURE_UAT.

    c. Nella casella di testo **Descrizione**, digitare una descrizione, ad esempio Configurazione AZURE per SSO.

    d. Nella casella di testo **Pagina Single Sign-On** incollare il valore di **URL di accesso** copiato dal portale di Azure.

    e. Nella casella di testo **Pagina di disconnessione** incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure.

    f. Nella casella di testo **Campo modulo Post** digitare il nome del campo presente nella risposta Post contenente SAML come SAMLResponse.

    g. Nella casella di testo **nome del tag identità XML** scrivere l'attributo che contiene l'identificatore univoco nella risposta SAML come NameID.

    h. Aprire nel Blocco note il  **metadati Xml**  scaricato dal portale di Azure, copiarne il certificato dal file di metadati e quindi incollarlo nella casella di testo **Certificato**

    i. Nella casella di testo **URL consumer**, incollare il valore di **URL di risposta**, che si ottiene dal team di supporto.

    j. Nella casella di testo **Autorità di certificazione**, incollare il valore di **Identificatore**, che si ottiene dal team di supporto.

    > [!Note]
    > Per ottenere tali valori contattare il [team di supporto client di Riskware](mailto:support@pansoftware.com.au)

    k. Selezionare la casella di controllo **Usa POST**.

    l. Selezionare la casella di controllo **Usa richiesta SAML**.

    m. Fare clic su **Save**.

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

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Riskware.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Riskware**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco applicazioni, selezionare **Riskware**.

    ![Collegamento di Riskware nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-riskware-test-user"></a>Creare utente test Riskware

Per consentire agli utenti di Azure AD di accedere a Riskware, è necessario effettuarne il provisioning in Riskware. Nel caso di Riskware il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere a Riskware come amministratore della sicurezza.

1. In alto a destra, fare clic su **Manutenzione** per aprire la pagina di manutenzione. 

    ![Manutenzione di Configurazione Riskware](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Nella pagina di manutenzione, fare clic su **Persone**.

    ![Persone di Configurazione Riskware](./media/riskware-tutorial/tutorial_riskware_people.png)

1. Selezionare la scheda **Dettagli** ed eseguire questa procedura:

    ![Dettagli di Configurazione Riskware](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. Selezionare **Tipo di persona**, ad esempio, dipendente.

    b. Nella casella di testo **Nome** immettere il nome dell'utente, ad esempio **Britta**.

    c. Nella casella di testo **Cognome** immettere il cognome dell'utente, ad esempio **Simon**.

1. Nella scheda **Sicurezza** eseguire la procedura seguente:

    ![Protezione di Configurazione Riskware](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. Nella sezione **Autenticazione**, selezionare la modalità di **Autenticazione**, che è stata predisposta come Configurazione di AZURE per SSO.

    b. Nella sezione **Dettagli di accesso**, nella casella di testo **ID utente** immettere l'indirizzo di posta elettronica dell'utente, ad esempio `brittasimon@contoso.com`.

    c. Nella casella di testo **Password** digitare la password dell'utente.

1. Nella scheda **Organizzazione** eseguire questa procedura:

    ![Organizzazione di Configurazione Riskware](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. Selezionare l'opzione come organizzazione **Level1**.

    b. Nella sezione**Area di lavoro primaria della persona** nella casella di testo **Percorso** digitare il percorso.

    c. Nella sezione **Dipendente**, impostare **Stato dipendente** come generico.

    d. Fare clic su **Save**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Riskware nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Riskware per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
