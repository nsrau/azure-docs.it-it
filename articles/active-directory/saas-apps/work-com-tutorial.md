---
title: 'Esercitazione: Integrazione di Azure Active Directory con Work.com | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Work.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 816f9bfe022b4a00c01c3ee1bc243f87ef56817b
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565938"
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Esercitazione: Integrazione di Azure Active Directory con Work.com

Questa esercitazione descrive come integrare Work.com con Azure Active Directory (Azure AD).
L'integrazione di Work.com con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Work.com.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Work.com con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Work.com, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Work.com abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Work.com supporta l'accesso SSO avviato da **SP**

## <a name="adding-workcom-from-the-gallery"></a>Aggiunta di Work.com dalla raccolta

Per configurare l'integrazione di Work.com in Azure AD, è necessario aggiungere Work.com dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Work.com dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Work.com** selezionare **Work.com** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Work.com nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Work.com usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Work.com.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Work.com, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Work.com](#configure-workcom-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Work.com](#create-workcom-test-user)**: per avere una controparte di Britta Simon in Work.com collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

>[!NOTE]
>Per configurare l'accesso Single Sign-On, è necessario configurare un nome di dominio personalizzato Work.com. È necessario definire almeno un nome di dominio, testare il nome di dominio e distribuirlo in tutta l'organizzazione.

Per configurare l'accesso Single Sign-On di Azure AD con Work.com, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Work.com** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Work.com](common/sp-signonurl.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `http://<companyname>.my.salesforce.com`

    > [!NOTE]
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere il valore, contattare il [team di supporto clienti di Work.com](https://help.salesforce.com/articleView?id=000159855&type=3). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Work.com** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-workcom-single-sign-on"></a>Configurare l'accesso Single Sign-On di Work.com

1. Accedere al tenant di Work.com come amministratore.

2. Passare a **Setup**.
   
    ![Installazione](./media/work-com-tutorial/ic794108.png "Installazione")

3. Nella sezione **Administer** (Amministra) del riquadro di spostamento sinistro fare clic su **Domain Management** (Gestione dominio) per espandere la sezione correlata e quindi fare clic su **My Domain** (Dominio personale) per aprire la pagina **My Domain** (Dominio personale). 
   
    ![My Domain](./media/work-com-tutorial/ic767825.png "My Domain")

4. Per verificare che il dominio sia stato configurato correttamente, verificare che sia presente in "**Step 4 Deployed to Users**" (Passaggio 4 Distribuzione agli utenti) e quindi verificare le selezioni in "**My Domain Settings**" (Impostazioni dominio personale).
   
    ![Domain Deployed to User](./media/work-com-tutorial/ic784377.png "Domain Deployed to User")

5. Accedere al tenant di Work.com.

6. Passare a **Setup**.
    
    ![Installazione](./media/work-com-tutorial/ic794108.png "Installazione")

7. Espandere il menu **Security Controls** (Controlli di sicurezza) e quindi fare clic su **Single Sign-On Settings** (Impostazioni Single Sign-On).
    
    ![Single Sign-On Settings](./media/work-com-tutorial/ic794113.png "Single Sign-On Settings")

8. Nella finestra di dialogo **Single Sign-On Settings** seguire questa procedura:
    
    ![SAML Enabled](./media/work-com-tutorial/ic781026.png "SAML Enabled")
    
    a. Selezionare **Abilitato SAML**.
    
    b. Fare clic su **Nuovo**.

9. Nella sezione **SAML Single Sign-On Setting** seguire questa procedura:
    
    ![SAML Single Sign-On Setting](./media/work-com-tutorial/ic794114.png "SAML Single Sign-On Setting")
    
    a. Nella casella di testo **Name** digitare un nome per la configurazione.  
       
    > [!NOTE]
    > Se si specifica un valore per **Name** (Nome) verrà popolata automaticamente la casella di testo **API Name** (Nome API).
    
    b. Nella casella di testo **Issuer** (Autorità di certificazione) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.
    
    c. Per caricare il certificato scaricato dal portale di Azure, fare clic su **Browse** (Sfoglia).
    
    d. Nella casella di testo **Entity Id** (ID entità) digitare `https://salesforce-work.com`.
    
    e. In **SAML Identity Type** (Tipo di identità SAML) selezionare **Assertion contains the Federation ID from the User object** (L'asserzione contiene l'ID federazione dell'oggetto utente).
    
    f. In **SAML Identity Location** (Percorso identità SAML) selezionare **Identity is in the NameIdentfier element of the Subject statement** (L'identità è nell'elemento NameIdentfier dell'istruzione Subject).
    
    g. Nella casella di testo **Identity Provider Login URL** (URL di accesso provider di identità) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    h. Nella casella di testo **Identity Provider Logout URL** (URL di disconnessione provider di identità) incollare il valore di **URL di disconnessione** copiato dal portale di Azure.
    
    i. In **Service Provider Initiated Request Binding** (Binding richiesta avviato dal provider di servizi) selezionare **HTTP Post**.
    
    j. Fare clic su **Save**.

10. Nel pannello di navigazione a sinistra del portale di Work.com classico fare clic su **Domain Management** (Gestione dominio) per espandere la sezione correlata e quindi fare clic su **My Domain** (Dominio personale) per aprire la pagina **My Domain** (Dominio personale). 
    
    ![My Domain](./media/work-com-tutorial/ic794115.png "My Domain")

11. Nella sezione **Login Page Branding** (Personalizzazione pagina di accesso) della pagina **My Domain** (Dominio personale) fare clic su **Edit** (Modifica).
    
    ![Login Page Branding](./media/work-com-tutorial/ic767826.png "Login Page Branding")

12. Nella sezione**Authentication Service** (Servizio autenticazione) della pagina **Login Page Branding** (Personalizzazione pagina di accesso) viene visualizzato il nome delle **impostazioni SAML SSO**. Selezionarlo e quindi fare clic su **Save**.
    
    ![Login Page Branding](./media/work-com-tutorial/ic784366.png "Login Page Branding")

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare `brittasimon@yourcompanydomain.extension`. Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Work.com.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Work.com**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Work.com**.

    ![Collegamento di Work.com nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-workcom-test-user"></a>Creare l'utente di test di Work.com

Per consentire l'accesso agli utenti di Azure Active Directory, è necessario che eseguano il provisioning a Work.com. Nel caso di Work.com, il provisioning è un'attività manuale.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning utente, eseguire la procedura seguente:

1. Accedere al sito aziendale di Work.com come amministratore.

2. Passare a **Setup**.
   
    ![Installazione](./media/work-com-tutorial/IC794108.png "Installazione")

3. Passare a **Manage Users (Gestisci utenti) \> Users (Utenti)**.
   
    ![Gestione utenti](./media/work-com-tutorial/IC784369.png "Gestione utenti")

4. Fare clic su **Nuovo utente**.
   
    ![Tutti gli utenti](./media/work-com-tutorial/IC794117.png "Tutti gli utenti")

5. Nella sezione relativa alle modifiche utente eseguire i passaggi seguenti inserendo nelle caselle di testo correlate gli attributi di un account Azure AD valido di cui si vuole effettuare il provisioning:
   
    ![User Edit](./media/work-com-tutorial/ic794118.png "User Edit")
   
    a. Nella casella di testo **First Name** (Nome) digitare il **nome** dell'utente **Britta**.
    
    b. Nella casella di testo **Last Name** (Cognome) digitare il **cognome** dell'utente **Simon**.
    
    c. Nella casella di testo **Alias** digitare il **nome** dell'utente **BrittaS**.
    
    d. Nella casella di testo **Email** (Indirizzo di posta elettronica) digitare l'**indirizzo di posta elettronica** dell'utente Brittasimon@contoso.com.
    
    e. Nella casella di testo **User Name** (Nome utente) digitare il nome utente dell'utente, ad esempio Brittasimon@contoso.com.
    
    f. Nella casella di testo **Nick Name** (Nome alternativo) digitare un **nome alternativo** dell'utente **Simon**.
    
    g. Selezionare **Role** (Ruolo), **User License**(Licenza utente) e **Profile** (Profilo).
    
    h. Fare clic su **Save**.  
      
    > [!NOTE]
    > Il titolare dell'account Azure AD riceverà un messaggio di posta elettronica con un collegamento per confermare l'account e attivarlo.
    > 

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Work.com nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Work.com per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

