---
title: 'Esercitazione: Integrazione di Azure Active Directory con Mitel Connect | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Mitel Connect.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/31/2020
ms.author: jeedes
ms.openlocfilehash: b89ae74a1f4ead568a5454c9494d4a82b0eeea3e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015180"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect-or-cloudlink-platform"></a>Esercitazione: Integrazione di Azure Active Directory con Mitel MiCloud Connect o CloudLink Platform

Questa esercitazione spiega come usare l'app Mitel Connect per integrare Azure Active Directory (Azure AD) con Mitel MiCloud Connect o CloudLink Platform. L'app Mitel Connect è disponibile nella raccolta di Azure. L'integrazione di Azure AD con MiCloud Connect o CloudLink Platform offre i vantaggi seguenti:

* È possibile controllare gli utenti che accedono alle app di MiCloud Connect e di CloudLink in Azure AD usando le credenziali aziendali.
* È possibile abilitare gli utenti dell'account per l'accesso automatico (Single Sign-On) a MiCloud Connect o CloudLink con gli account Azure AD personali.

Per dettagli sull'integrazione di app SaaS con Azure AD, vedere le [informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di avviare l'integrazione di Azure AD con Mitel MiCloud Connect o CloudLink Platform.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con MiCloud Connect, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Un account Mitel MiCloud Connect o Mitel CloudLink, a seconda dell'applicazione che si vuole configurare.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione verrà configurato e testato l'accesso Single Sign-On (SSO) di Azure AD.

* Mitel Connect supporta l'accesso SSO avviato da **SP**
* Dopo aver configurato Mitel Connect, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-mitel-connect-from-the-gallery"></a>Aggiungere Mitel Connect dalla raccolta

Per configurare l'integrazione di Mitel Connect in Azure AD, è necessario aggiungere Mitel Connect dalla raccolta all'elenco di app SaaS gestite nel portale di Azure.

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Selezionare **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Selezionare **Nuova applicazione**.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Digitare **Mitel Connect** nel campo di ricerca, selezionare **Mitel Connect** nel riquadro dei risultati e quindi selezionare **Aggiungi**.

     ![Mitel Connect nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con MiCloud Connect o CloudLink Platform usando un utente di test di nome **_Britta Simon_**. Per il corretto funzionamento di Single Sign-On, è necessario stabilire un collegamento tra l'utente nel portale di Azure AD e l'utente corrispondente nella piattaforma Mitel. Vedere le sezioni seguenti per informazioni sulla configurazione e il test dell'accesso SSO di Azure AD con MiCloud Connect o CloudLink Platform.
* Configurare e testare l'accesso SSO di Azure AD con MiCloud Connect
* Configurare e testare l'accesso SSO di Azure AD con CloudLink Platform

## <a name="configure-and-test-azure-ad-sso-with-micloud-connect"></a>Configurare e testare l'accesso SSO di Azure AD con MiCloud Connect

Per configurare e testare l'accesso Single Sign-On di Azure AD con MiCloud Connect:

1. **[Configurare MiCloud Connect per l'accesso SSO con Azure AD](#configure-micloud-connect-for-sso-with-azure-ad)**: per abilitare gli utenti per l'uso di questa funzionalità e configurare le impostazioni di SSO sul lato applicazione.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
4. **[Creare un utente di test di Mitel MiCloud Connect](#create-a-mitel-micloud-connect-test-user)**: per avere una controparte di Britta Simon in MiCloud Connect collegata alla relativa rappresentazione in Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

## <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>Configurare MiCloud Connect per l'accesso SSO con Azure AD

In questa sezione verrà abilitato l'accesso Single Sign-On di Azure AD per MiCloud Connect nel portale di Azure e verrà configurato l'account di MiCloud Connect per consentire l'accesso SSO con Azure AD.

Per configurare MiCloud Connect con l'accesso SSO per Azure AD, è consigliabile aprire il portale di Azure e il portale dell'account Mitel affiancati. Sarà necessario copiare alcune informazioni dal portale di Azure al portale dell'account Mitel e altre dal portale dell'account Mitel al portale di Azure.


1. Per aprire la pagina di configurazione nel [portale di Azure](https://portal.azure.com/):

    1. Nella pagina di integrazione di **Mitel Connect** selezionare **Single Sign-On**.

       ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

    1. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
    
       ![Selezione della modalità Single Sign-On](common/select-saml-option.png)
    
       Viene visualizzata la pagina dell'accesso basato su SAML.

2. Per aprire la finestra di dialogo di configurazione nel portale dell'account Mitel:

    1. Nel menu **Phone System** (Sistema telefonico) selezionare **Add-On Features** (Funzionalità componenti aggiuntivi).

    1. A destra di **Single Sign-On** selezionare **Activate** (Attiva) o **Settings** (Impostazioni).
    
    Viene visualizzata la finestra di dialogo Connect Single Sign-On Settings (Impostazioni di connessione Single Sign-On).
    
3. Selezionare la casella di controllo **Enable Single Sign-On** (Abilita Single Sign-On).
    
    ![Screenshot che mostra la pagina delle impostazioni per l'accesso Single Sign-On di Mitel Connect con la casella di controllo Enable Single Sign-On selezionata.](./media/mitel-connect-tutorial/mitel-connect-enable.png)

4. Nel portale di Azure selezionare l'icona **Modifica** nella sezione **Configurazione SAML di base**.
   
    ![Screenshot che mostra la pagina Set up Single Sign-On with SAML con l'icona di modifica selezionata.](common/edit-urls.png)

    Viene visualizzata la finestra di dialogo Configurazione SAML di base.

5.  Copiare l'URL dal campo **Mitel Identifier (Entity ID)** (Identificatore Mitel - ID entità) nel portale dell'account Mitel e incollarlo nel campo **Identificatore (ID entità)** del portale di Azure.

6. Copiare l'URL dal campo **Reply URL (Assertion Consumer Service URL)** (URL di risposta - URL del servizio consumer di asserzione) nel campo **URL di risposta (URL del servizio consumer di asserzione)** del portale di Azure.

   ![Screenshot che mostra la Configurazione SAML di base nel portale di Azure e la sezione Set Up Identity Provider nel portale dell'account Mitel con righe indicanti la relazione tra di esse.](./media/mitel-connect-tutorial/mitel-azure-basic-configuration.png)

7. Nella casella di testo **URL di accesso** digitare uno degli URL seguenti:

    1. **https://portal.shoretelsky.com** : per usare il portale dell'account Mitel come applicazione Mitel predefinita
    1. **https://teamwork.shoretel.com** : per usare Teamwork come applicazione Mitel predefinita

    > [!NOTE]
    > L'applicazione Mitel predefinita è quella a cui l'utente accede quando seleziona il riquadro di Mitel Connect nel pannello di accesso. È anche l'applicazione a cui si accede quando si esegue una configurazione di test da Azure AD.

8. Selezionare **Salva** nella finestra di dialogo **Configurazione SAML di base** del portale di Azure.

9. Nella sezione **Certificato di firma SAML** della pagina **Accesso basato su SAML** del portale di Azure selezionare **Scarica** accanto a **Certificato (Base64)** per scaricare il **certificato di firma** e salvarlo nel computer.

    ![Screenshot che mostra il riquadro Certificato di firma SAML in cui è possibile scaricare un certificato.](./media/mitel-connect-tutorial/azure-signing-certificate.png)

10. Aprire il file del certificato di firma in un editor di testo, copiare tutti i dati al suo interno e quindi incollarli nel campo **Signing Certificate** (Certificato di firma) del portale dell'account Mitel. 

      ![Screenshot che mostra il campo Certificato di firma.](./media/mitel-connect-tutorial/mitel-connect-signing-certificate.png)

11. Nella sezione **Configura Mitel Connect** della pagina **Accesso basato su SAML** del portale di Azure:

     1. Copiare l'URL dal campo **URL di accesso** e copiarlo nel campo **Sign-in URL** (URL di accesso) del portale dell'account Mitel.

     1. Copiare l'URL dal campo **Identificatore Azure AD** e copiarlo nel campo **Entity ID** (ID entità) del portale dell'account Mitel.
         
         ![Screenshot che mostra la relazione tra la pagina di accesso basata su SAML del portale di Azure e il portale dell'account Mitel.](./media/mitel-connect-tutorial/mitel-azure-set-up-connect.png)

12. Selezionare **Save** (Salva) nella finestra di dialogo **Connect Single Sign-On Settings** del portale dell'account Mitel.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD 

In questa sezione verrà creato un utente di test di nome Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. Nella finestra di dialogo Proprietà utente procedere come segue:

    ![Finestra di dialogo Utente](common/user-properties.png)

    1. Nel campo **Nome** digitare **BrittaSimon**.
  
    1. Nel campo **Nome utente** digitare brittasimon@\<yourcompanydomain\>.\<extension\>.  Ad esempio: BrittaSimon@contoso.com.

    1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    1. Selezionare **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Mitel Connect.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Mitel Connect**.

    ![Collegamento di Mitel Connect nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco **Utenti** e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Seleziona ruolo** selezionare il ruolo appropriato per l'utente nell'elenco e quindi fare clic su **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

### <a name="create-a-mitel-micloud-connect-test-user"></a>Creare un utente di test di Mitel MiCloud Connect

In questa sezione viene creato un utente di nome Britta Simon nell'account di MiCloud Connect. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

Per informazioni sull'aggiunta di utenti nel portale dell'account Mitel, vedere l'articolo su come [aggiungere utenti](https://shoretelcommunity.force.com/s/article/Adding-Users-092815) nella knowledge base Mitel.

Creare un utente nell'account MiCloud Connect con i dettagli seguenti:

* **Nome:** Britta Simon
* **Indirizzo di posta elettronica aziendale:** `brittasimon@<yourcompanydomain>.<extension>`   
  Esempio: [brittasimon@contoso.com](mailto:brittasimon@contoso.com)
* **Nome utente:** `brittasimon@<yourcompanydomain>.<extension>`  
  Esempio: [brittasimon@contoso.com](mailto:brittasimon@contoso.com); il nome utente corrisponde in genere all'indirizzo di posta elettronica aziendale

> [!NOTE]
> il nome utente di MiCloud Connect deve essere identico all'indirizzo di posta elettronica dell'utente in Azure.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro di Mitel Connect nel pannello di accesso, si dovrebbe essere automaticamente reindirizzati nella pagina di accesso all'applicazione MiCloud Connect configurata come predefinita nel campo **URL di accesso**. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="configure-and-test-azure-ad-sso-with-cloudlink-platform"></a>Configurare e testare l'accesso SSO di Azure AD con CloudLink Platform

Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD per la piattaforma CloudLink nel portale di Azure e come configurare l'account della piattaforma CloudLink per consentire l'accesso Single Sign-On con Azure AD.

Per configurare la piattaforma CloudLink con l'accesso Single Sign-On per Azure AD, è consigliabile aprire affiancati il portale di Azure e il portale di CloudLink Accounts, in quanto sarà necessario copiare alcune informazioni dal portale di Azure al portale di CloudLink Accounts e viceversa.

1. Per aprire la pagina di configurazione nel [portale di Azure](https://portal.azure.com/):

    1. Nella pagina di integrazione di **Mitel Connect** selezionare **Single Sign-On**.

       ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

    1. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.

       ![Selezione della modalità Single Sign-On](common/select-saml-option.png)
    
       Viene visualizzata la pagina **Accesso basato su SAML** in cui è presente la sezione **Configurazione SAML di base**.

       ![Screenshot che mostra la pagina di accesso basato su SAML con la configurazione SAML di base.](./media/mitel-connect-tutorial/mitel-azure-saml-settings.png)

2. Per accedere al pannello di configurazione di **Azure AD Single Sign On** (Accesso Single Sign-On di Azure AD) nel portale di CloudLink Accounts:

    1. Passare alla pagina **Account Information** (Informazioni sull'account) dell'account del cliente con cui abilitare l'integrazione.

    1. Nella sezione **Integrations** (Integrazioni) selezionare **+ Add new** (+ Aggiungi nuova). In una schermata popup viene visualizzato il pannello **Integrations** (Integrazioni).

    1. Selezionare la scheda **3rd party** (Terze parti). Viene visualizzato un elenco delle applicazioni di terze parti supportate. Selezionare il pulsante **Add** (Aggiungi) associato a **Azure AD Single Sign On** (Accesso Single Sign-On di Azure AD) e quindi **Done** (Fine).

       ![Screenshot che mostra la pagina Integrations in cui è possibile aggiungere Azure A D Single Sign-On.](./media/mitel-connect-tutorial/mitel-cloudlink-integrations.png)

       La funzionalità **Azure AD Single Sign On** (Accesso Single Sign-On di Azure AD) è abilitata per l'account del cliente ed è stata aggiunta alla sezione **Integrations** (Integrazioni) della pagina **Account Information** (Informazioni sull'account).   

   1. Selezionare **Complete Setup** (Completa configurazione).
    
      ![Screenshot che mostra l'opzione Complete Setup per Azure A D Single Sign-On.](./media/mitel-connect-tutorial/mitel-cloudlink-complete-setup.png)
      
      Viene aperto il pannello di configurazione di **Azure AD Single Sign On** (Accesso Single Sign-On di Azure AD).
      
       ![Screenshot che mostra la configurazione di Azure A D Single Sign-On.](./media/mitel-connect-tutorial/mitel-cloudlink-sso-setup.png)
       
       Mitel consiglia di non selezionare la casella di controllo **Enable Mitel Credentials (Optional)** (Abilita credenziali di Mitel (facoltativo)) nella sezione **Optional Mitel credentials** (Credenziali di Mitel facoltative). Selezionare questa casella di controllo solo se si vuole che l'utente possa accedere all'applicazione CloudLink usando le credenziali di Mitel, in aggiunta all'opzione per l'accesso Single Sign-On.

3. Nella pagina **Accesso basato su SAML** del portale di Azure selezionare l'icona **Modifica** nella sezione **Configurazione SAML di base**. Viene aperto il pannello **Configurazione SAML di base**.

    ![Screenshot che mostra la sezione Configurazione SAML di base con l'icona Modifica selezionata.](./media/mitel-connect-tutorial/mitel-azure-saml-basic.png)
 
 4. Copiare l'URL dal campo **Mitel Identifier (Entity ID)** (Identificatore Mitel - ID entità) nel portale di CloudLink Accounts e incollarlo nel campo **Identificatore (ID entità)** del portale di Azure.

 5. Copiare l'URL dal campo **Reply URL (Assertion Consumer Service URL)** (URL di risposta - URL del servizio consumer di asserzione) nel portale di CloudLink Accounts e incollarlo nel campo **URL di risposta (URL del servizio consumer di asserzione)** del portale di Azure.  
    
    ![Screenshot che mostra la relazione tra le pagine del portale di CloudLink Accounts e il portale di Azure.](./media/mitel-connect-tutorial/mitel-cloudlink-saml-mapping.png) 

 6. Nella casella di testo **URL di accesso** digitare l'URL `https://accounts.mitel.io` per usare il portale di CloudLink Accounts come applicazione Mitel predefinita.
     
     ![Screenshot che mostra la casella di testo U R L di accesso.](./media/mitel-connect-tutorial/mitel-cloudlink-sign-on-url.png)
  
     > [!NOTE]
     > L'applicazione Mitel predefinita è quella che viene aperta quando l'utente seleziona il riquadro di Mitel Connect nel pannello di accesso. È anche l'applicazione a cui si accede quando l'utente imposta una configurazione di test da Azure AD.

7. Selezionare **Salva** nella finestra di dialogo **Configurazione SAML di base**.

8. Nella sezione **Certificato di firma SAML** della pagina **Accesso basato su SAML** del portale di Azure selezionare **Scarica** accanto a **Certificato (Base64)** per scaricare il **certificato di firma**. Salvare il certificato nel computer.
  
    ![Screenshot che mostra la sezione Certificato di firma SAML in cui è possibile scaricare un certificato Base 64.](./media/mitel-connect-tutorial/mitel-cloudlink-save-certificate.png)

9. Aprire il file del certificato di firma in un editor di testo, copiare tutti i dati al suo interno e quindi incollarli nel campo **Signing Certificate** (Certificato di firma) del portale di CloudLink Accounts.  

    > [!NOTE]
    > Se sono disponibili più certificati, è consigliabile incollarli uno dopo l'altro. 
       
    ![Screenshot che mostra il secondo passaggio della procedura in cui si compilano i valori dell'integrazione di Azure A D.](./media/mitel-connect-tutorial/mitel-cloudlink-enter-certificate.png)

10. Nella sezione **Configura Mitel Connect** della pagina **Accesso basato su SAML** del portale di Azure:

     1. Copiare l'URL dal campo **URL di accesso** e copiarlo nel campo **Sign-in URL** (URL di accesso) del portale di CloudLink Accounts.

     1. Copiare l'URL dal campo **Identificatore Azure AD** e copiarlo nel campo **IDP Identifier (Entity ID)** (Identificatore IDP (ID entità)) del portale di CloudLink Accounts.
     
        ![Screenshot che mostra l'origine dei valori qui descritti in Mintel Connect.](./media/mitel-connect-tutorial/mitel-cloudlink-copy-settings.png)

11. Selezionare **Save**  (Salva) nel pannello **Azure AD Single Sign On** (Accesso Single Sign-On di Azure AD) nel portale di CloudLink Accounts.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD 

In questa sezione verrà creato un utente di test di nome Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. Nella finestra di dialogo Proprietà utente procedere come segue:

    ![Finestra di dialogo Utente](common/user-properties.png)

    1. Nel campo **Nome** digitare **BrittaSimon**.
  
    1. Nel campo **Nome utente** digitare brittasimon@\<yourcompanydomain\>.\<extension\>.  Ad esempio: BrittaSimon@contoso.com.

    1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    1. Selezionare **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Mitel Connect.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Mitel Connect**.

    ![Collegamento di Mitel Connect nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco **Utenti** e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Seleziona ruolo** selezionare il ruolo appropriato per l'utente nell'elenco e quindi fare clic su **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

### <a name="create-a-cloudlink-test-user"></a>Creare un utente di test di CloudLink

Questa sezione descrive come creare un utente di test di nome **_Britta Simon_** nella piattaforma CloudLink. Per consentire agli utenti di usare l'accesso Single Sign-On, è necessario prima crearli e attivarli.

Per informazioni dettagliate sull'aggiunta di utenti nel portale degli account CloudLink, vedere l'argomento sulla **_gestione degli utenti_** nella [documentazione di CloudLink Accounts](https://www.mitel.com/document-center/technology/cloudlink/all-releases/en/cloudlink-accounts-html).

Creare un utente nel portale di CloudLink Accounts con i dettagli seguenti:

* Nome: Britta Simon
* Nome: Britta
* Cognome: Simon
* Posta elettronica: BrittaSimon@contoso.com

> [!NOTE]
> L'indirizzo di posta elettronica CloudLink dell'utente deve essere identico a quello indicato in **Nome entità utente** nel portale di Azure.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro di Mitel Connect nel pannello di accesso, si verrà automaticamente reindirizzati nella pagina di accesso all'applicazione CloudLink configurata come predefinita nel campo **URL di accesso**. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)