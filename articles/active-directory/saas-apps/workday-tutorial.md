---
title: 'Esercitazione: Integrazione di Azure Active Directory con Workday | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Workday.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: 8451fd692409933803f5f8023f1e1161c3a97daf
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59278531"
---
# <a name="tutorial-azure-active-directory-integration-with-workday"></a>Esercitazione: Integrazione di Azure Active Directory con Workday

Questa esercitazione descrive come integrare Workday con Azure Active Directory (Azure AD).
L'integrazione di Workday con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Workday.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Workday con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Workday, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Workday abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Workday supporta l'accesso SSO avviato da **SP** e **IDP**

## <a name="adding-workday-from-the-gallery"></a>Aggiunta di Workday dalla raccolta

Per configurare l'integrazione di Workday in Azure AD, è necessario aggiungere Workday dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Workday dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Workday**, selezionare **Workday** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Workday nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Workday in base a un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Workday.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Workday, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Workday](#configure-workday-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Workday](#create-workday-test-user)**: per avere una controparte di Britta Simon in Workday collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Workday, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Workday** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Workday](common/sp-identifier.png)

    a. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https:\//impl.workday.com/<tenant>/login-saml2.flex`

    b. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://www.workday.com`

5. Fare clic su **Impostare URL aggiuntivi** e seguire i passaggi seguenti:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Workday](./media/workday-tutorial/reply.png)

    Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https:\//impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'URL di accesso e l'URL di risposta effettivi. L'URL di risposta deve avere un sottodominio, ad esempio www, wd2, wd3, wd3-impl, wd5, wd5-impl.
    > L'uso di qualcosa di simile a `http://www.myworkday.com` funziona, ma `http://myworkday.com` non funziona. Per ottenere tali valori, contattare il [team di supporto clienti di Workday](https://www.workday.com/en-us/partners-services/services/support.html). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

6. L'applicazione Workday prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi token SAML. La schermata seguente illustra l'elenco degli attributi predefiniti in cui **nameidentifier** è associato a **user.userprincipalname**. L'applicazione Workday prevede che **nameidentifier** sia mappato a **user.mail**, **UPN** e così via. Di conseguenza è necessario modificare il mapping dell'attributo. A questo scopo, fare clic sull'icona **Modifica** e cambiare il mapping.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Qui abbiamo mappato l'ID del nome con UPN (user.userprincipalname) come valore predefinito. È necessario mappare l'ID del nome con l'ID utente effettivo nell'account Workday (indirizzo di posta elettronica, UPN, ecc.) per l'utilizzo di SSO.

7. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

8. Nella sezione **Configura Workday** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-workday-single-sign-on"></a>Configurazione di Single Sign-On in Workday

1. In un'altra finestra del Web browser accedere al sito aziendale di Workday come amministratore.

2. Nella **casella di ricerca** in alto a sinistra della home page eseguire una ricerca di **Edit Tenant Setup - Security** (Modifica configurazione tenant - Sicurezza).

    ![Edit Tenant Security](./media/workday-tutorial/IC782925.png "Edit Tenant Security")

3. Nella sezione **Redirection URLs** seguire questa procedura:

    ![Redirection URLs](./media/workday-tutorial/IC7829581.png "Redirection URLs")

    a. Fare clic su **Aggiungi riga**.

    b. Nelle caselle di testo **URL di reindirizzamento dell'accesso** e **URL di reindirizzamento dispositivi mobili** digitare l'**URL di accesso** immesso nella sezione **Configurazione SAML di base** del portale di Azure.

    c. Nella sezione **Configurazione Workday** del portale di Azure copiare l'**URL di disconnessione** e incollarlo nella casella di testo **URL di reindirizzamento disconnessione**.

    d. Nella casella di testo **Used for Environments** (Usato per gli ambienti) selezionare il nome dell'ambiente.  

   > [!NOTE]
   > Il valore dell'attributo Environment è collegato al valore dell'URL del tenant:  
   > -Se il nome di dominio dell'URL tenant di Workday inizia con impl (ad esempio *https:\//impl.workday.com/\<tenant\>/login-saml2.flex*), l'attributo **Environment** deve essere impostato su Implementation.  
   > -Se il nome di dominio inizia con altro, è necessario contattare il [team di supporto clienti di Workday](https://www.workday.com/en-us/partners-services/services/support.html) per ottenere il valore **Environment** corrispondente.

4. Nella sezione **SAML Setup** seguire questa procedura:

    ![Configurazione SAML](./media/workday-tutorial/IC782926.png "Configurazione SAML")

    a.  Selezionare **Enable SAML authentication**.

    b.  Fare clic su **Aggiungi riga**.

5. Nella sezione **SAML Identity Providers** (Provider di identità SAML) seguire questa procedura:

    ![SAML Identity Providers](./media/workday-tutorial/IC7829271.png "SAML Identity Providers")

    a. Nella casella di testo **Identity Provider Name** (Nome provider di identità) digitare il nome del provider, ad esempio *SPInitiatedSSO*).

    b. Nella sezione **Configurazione Workday** del portale di Azure copiare il valore dell'**identificatore Azure AD** e incollarlo nella casella di testo **Autorità di certificazione**.

    ![SAML Identity Providers](./media/workday-tutorial/IC7829272.png "SAML Identity Providers")

    c. Nella sezione **Configurazione Workday** del portale di Azure copiare il valore dell'**URL di disconnessione** e incollarlo nella casella di testo **URL di risposta disconnessione**.

    d. Nella sezione **Configurazione Workday** del portale di Azure copiare il valore dell'**URL di accesso** e incollarlo nella casella di testo **URL del servizio SSO IdP**.

    e. Nella casella di testo **Used for Environments** (Usato per gli ambienti) selezionare il nome dell'ambiente.

    f. Fare clic su **Certificato di chiave pubblica del provider di identità** e quindi su **Crea**.

    ![Creare](./media/workday-tutorial/IC782928.png "Creare")

    g. Fare clic su **Crea chiave pubblica x509**.

    ![Creare](./media/workday-tutorial/IC782929.png "Creare")

6. Nella sezione **Visualizza chiave pubblica x509** eseguire la procedura seguente:

    ![View x509 Public Key](./media/workday-tutorial/IC782930.png "View x509 Public Key")

    a. Nella casella di testo **Name** (Nome) digitare un nome per il certificato, ad esempio *PPE\_SP*).

    b. Nella casella di testo **Valid From** digitare il valore dell'attributo di inizio validità del certificato.

    c.  Nella casella di testo **Valid To** digitare il valore dell'attributo di fine validità del certificato.

    > [!NOTE]
    > Per individuare la data di inizio e di fine validità, fare doppio clic sul certificato scaricato.  Le date sono elencate nella scheda **Details** .
    >
    >

    d.  Aprire il certificato con codifica Base 64 nel Blocco note e quindi copiarne il contenuto.

    e.  Nella casella di testo **Certificate** incollare il valore copiato negli Appunti.

    f.  Fare clic su **OK**.

7. Eseguire la procedura seguente:

    ![SSO configuration](./media/workday-tutorial/WorkdaySSOConfiguratio.png "SSO configuration")

    a.  Nella casella di testo **Service Provider ID** (ID provider di servizi) digitare **https://www.workday.com**.

    b. Selezionare **Do Not Deflate SP-initiated Authentication Request**.

    c. In **Authentication Request Signature Method** selezionare **SHA256**.

    ![Authentication Request Signature Method](./media/workday-tutorial/WorkdaySSOConfiguration.png "Authentication Request Signature Method") 

    d. Fare clic su **OK**.

    ![OK](./media/workday-tutorial/IC782933.png "OK")

    > [!NOTE]
    > Assicurarsi di aver configurato il single sign-on in modo corretto. Nel caso in cui il single sign-on sia stato configurato in modo errato, potrebbe non essere possibile accedere all'applicazione con le proprie credenziali. In questo caso, Workday fornisce un backup dell’url del log-in da cui gli utenti possono accedere usando il normale nome utente e la password nel formato seguente: [Your Workday URL]/login.flex?redirect=n

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

In questa sezione si abilita Britta Simon per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Workday.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Workday**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Workday**.

    ![Collegamento Workday nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-workday-test-user"></a>Creare un utente di test di Workday

In questa sezione viene creato un utente chiamato Britta Simon in Workday. Collaborare con il [team di supporto clienti di Workday](https://www.workday.com/en-us/partners-services/services/support.html) per aggiungere gli utenti nella piattaforma Workday. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Workday nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Workday per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

