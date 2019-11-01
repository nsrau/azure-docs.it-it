---
title: 'Esercitazione: Integrazione di Azure Active Directory con Adobe Experience Manager | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Adobe Experience Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f39751f40b32c5da24e13d75d2607d7da0a57ad3
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73154106"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Esercitazione: Integrazione di Azure Active Directory con Adobe Experience Manager

Questa esercitazione descrive come integrare Adobe Experience Manager con Azure Active Directory (Azure AD).
L'integrazione di Adobe Experience Manager con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere ad Adobe Experience Manager.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) ad Adobe Experience Manager con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Adobe Experience Manager, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Adobe Experience Manager abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Adobe Experience Manager supporta l'accesso SSO avviato da **SP e IDP**

* Adobe Experience Manager supporta il provisioning utenti **Just In Time** (JIT)

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>Aggiunta di Adobe Experience Manager dalla raccolta

Per configurare l'integrazione di Adobe Experience Manager in Azure AD, è necessario aggiungere Adobe Experience Manager dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Adobe Experience Manager dalla raccolta, completare questi passaggi:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Adobe Experience Manager**, selezionare **Adobe Experience Manager** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Adobe Experience Manager nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con [Nome applicazione] usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in [Nome applicazione].

Per configurare e testare l'accesso Single Sign-On di Azure AD con [Nome applicazione], è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Adobe Experience Manager](#configure-adobe-experience-manager-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Adobe Experience Manager](#create-adobe-experience-manager-test-user)** : per avere una controparte di Britta Simon in Adobe Experience Manager collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con [Nome applicazione], seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Adobe Experience Manager** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Adobe Experience Manager](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un valore univoco definito anche nel server Adobe Experience Manager.

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<AEM Server Url>/saml_login`

    > [!NOTE]
    > Il valore di URL di risposta non è reale. Aggiornare questo valore con l'URL di risposta effettivo. Per ottenere tale valore, contattare il [team di supporto clienti di Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Adobe Experience Manager](common/metadata-upload-additional-signon.png)

    Nella casella di testo **URL di accesso** digitare l'URL del server Adobe Experience Manager.

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

7. Nella sezione **Configura Adobe Experience Manager** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-adobe-experience-manager-single-sign-on"></a>Configurare l'accesso Single Sign-On di Adobe Experience Manager

1. In un'altra finestra del browser, aprire il portale di amministrazione di **Adobe Experience Manager**.

2. Selezionare **Settings** (Impostazioni) > **Security** (Sicurezza) > **Users** (Utenti).

    ![Pulsante di salvataggio per la configurazione dell'accesso Single Sign-On](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

3. Selezionare **Administrator** (Amministratore) o un altro utente rilevante.

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

4. Selezionare **Account Settings** (Impostazioni account) > **Manage TrustStore** (Gestisci archivio trust).

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

5. In **Add Certificate from CER file** (Aggiungi certificato da file CER), fare clic su **Select Certificate File** (Seleziona file certificato). Individuare e selezionare il file di certificato, scaricato in precedenza dal portale di Azure.

    ![Pulsante di salvataggio per la configurazione dell'accesso Single Sign-On](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

6. Il certificato viene aggiunto all'archivio trust. Notare l'alias del certificato.

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

7. Nella pagina **Users** (Utenti) selezionare **authentication-service**.

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

8. Selezionare **Account Settings** (Impostazioni account) > **Create/Manage KeyStore** (Crea/Gestisci archivio chiavi). Creare l'archivio chiavi specificando una password.

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

9. Tornare alla schermata di amministrazione e quindi selezionare **Settings** (Impostazioni) > **Operations** (Operazioni) > **Web Console** (Console Web).

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    Viene aperta la pagina di configurazione.

    ![Pulsante di salvataggio per la configurazione dell'accesso Single Sign-On](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

10. Cercare **Adobe Granite SAML 2.0 Authentication Handler** e quindi selezionare l'icona **Aggiungi**.

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

11. In questa pagina eseguire le azioni seguenti.

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. Nella casella **Path** (Percorso) immettere **/** .

    b. Nella casella **IDP URL** (URL IDP) immettere il valore **URL di accesso** copiato dal portale di Azure.

    c. Nella casella **IDP Certificate Alias** (Alias certificato IDP) immettere il valore di **Certificate Alias** (Alias certificato) aggiunto nell'archivio trust.

    d. Nella casella **Security Provided Entity ID** (ID entità provider di servizi) immettere il valore **Identificatore Azure AD**  univoco configurato nel portale di Azure.

    e. Nella casella **Assertion Consumer Service URL** (URL del servizio consumer di asserzione) immettere il valore di **URL di risposta** configurato nel portale di Azure.

    f. Nella casella **Password of Key Store** (Password archivio chiavi) immettere la **Password** impostata nell'archivio chiavi.

    g. Nella casella **User Attribute ID** (ID attributo utente) immettere l'**ID nome** o un altro ID utente pertinente.

    h. Selezionare **Autocreate CRX Users** (Creazione automatica utenti CRX).

    i. Nella casella **Logout URL** (URL di disconnessione) incollare il valore **URL di disconnessione** ottenuto dal portale di Azure.

    j. Selezionare **Salva**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

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

    d. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione viene concesso a Britta Simon l'accesso ad Adobe Experience Manager per consentirle di usare l'accesso Single Sign-On di Azure.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Adobe Experience Manager**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Adobe Experience Manager**.

    ![Collegamento di Adobe Experience Manager nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-adobe-experience-manager-test-user"></a>Creare un utente di test di Adobe Experience Manager

In questa sezione viene creato un utente di nome Britta Simon in Adobe Experience Manager. Se è stata selezionata l'opzione **Autocreate CRX Users** (Creazione automatica utenti CRX), gli utenti vengono creati automaticamente dopo l'autenticazione.

Se si vogliono creare utenti manualmente, collaborare con il [team di supporto di Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html)  per aggiungere gli utenti alla piattaforma Adobe Experience Manager.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Adobe Experience Manager nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Adobe Experience Manager per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
