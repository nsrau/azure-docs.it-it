---
title: 'Esercitazione: Integrazione di Azure Active Directory con Mitel Connect | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Mitel Connect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 204f540b-09f1-452b-a52f-78143710ef76
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.openlocfilehash: e03870322df94a4c9587a3395c70925d2d2e838d
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67588213"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect"></a>Esercitazione: Integrazione di Azure Active Directory con Mitel MiCloud Connect

Questa esercitazione descrive come integrare Mitel MiCloud Connect con Azure Active Directory (Azure AD). L'integrazione di MiCloud Connect con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi ha accesso alle app MiCloud Connect usando le credenziali aziendali.
* È possibile abilitare gli utenti dell'account per l'accesso automatico (Single Sign-On) a MiCloud Connect con gli account Azure AD personali.


Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con MiCloud Connect, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD

  Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Un account di Mitel MiCloud Connect

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione verrà configurato e testato l'accesso Single Sign-On (SSO) di Azure AD.

* Mitel Connect supporta l'accesso SSO avviato da **SP**

## <a name="adding-mitel-connect-from-the-gallery"></a>Aggiunta di Mitel Connect dalla raccolta

Per configurare l'integrazione di Mitel Connect in Azure AD, è necessario aggiungere Mitel Connect dalla raccolta all'elenco di app SaaS gestite nel portale di Azure.

**Per aggiungere Mitel Connect dalla raccolta, eseguire i passaggi seguenti:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic su **Azure Active Directory** nel pannello di spostamento a sinistra.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Fare clic su **Applicazioni aziendali** e quindi su **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic su **Nuova applicazione**.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Digitare **Mitel Connect** ne campo di ricerca, fare clic su **Mitel Connect** nel riquadro dei risultati e quindi fare clic su **Aggiungi**.

     ![Mitel Connect nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con MiCloud Connect usando un utente di test di nome **Britta Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in MiCloud Connect.

Per configurare e testare l'accesso Single Sign-On di Azure Ad con MiCloud Connect, è necessario completare i passaggi seguenti:

1. **[Configurare MiCloud Connect per l'accesso SSO con Azure AD](#configure-micloud-connect-for-sso-with-azure-ad)** : per abilitare gli utenti per l'uso di questa funzionalità e configurare le impostazioni di SSO sul lato applicazione.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
4. **[Creare un utente di test di Mitel MiCloud Connect](#create-a-mitel-micloud-connect-test-user)** : per avere una controparte di Britta Simon in MiCloud Connect collegata alla relativa rappresentazione in Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>Configurare MiCloud Connect per l'accesso SSO con Azure AD

In questa sezione verrà abilitato l'accesso Single Sign-On di Azure AD per MiCloud Connect nel portale di Azure e verrà configurato l'account di MiCloud Connect per consentire l'accesso SSO con Azure AD.

Per configurare MiCloud Connect con l'accesso SSO per Azure AD, è consigliabile aprire il portale di Azure e il portale dell'account Mitel affiancati. Sarà necessario copiare alcune informazioni dal portale di Azure al portale dell'account Mitel e altre dal portale dell'account Mitel al portale di Azure.


1. Per aprire la pagina di configurazione nel [portale di Azure](https://portal.azure.com/), procedere come segue:

    a. Nella pagina di integrazione di **Mitel Connect** fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

    b. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** fare clic su **SAML**.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)
    
    Viene visualizzata la pagina dell'accesso basato su SAML.

2. Per aprire la finestra di dialogo di configurazione nel portale dell'account Mitel, procedere come segue:

    a. Nel menu **Phone System** (Sistema telefonico) fare clic su **Add-On Features** (Funzionalità aggiuntive).

    b. A destra di **Single Sign-On** fare clic su **Activate** (Attiva) o su **Settings** (Impostazioni).
    
    Viene visualizzata la finestra di dialogo Connect Single Sign-On Settings (Impostazioni di connessione Single Sign-On).
    
3. Selezionare la casella di controllo **Enable Single Sign-On** (Abilita Single Sign-On).
    ![image](./media/mitel-connect-tutorial/Mitel_Connect_Enable.png)


4. Nel portale di Azure fare clic sull'icona **Modifica** nella sezione **Configurazione SAML di base**.
    ![image](common/edit-urls.png)

    Viene visualizzata la finestra di dialogo Configurazione SAML di base.

5.  Copiare l'URL dal campo **Mitel Identifier (Entity ID)** (Identificatore Mitel - ID entità) nel portale dell'account Mitel e incollarlo nel campo **Identificatore (ID entità)** del portale di Azure.

6. Copiare l'URL dal campo **Reply URL (Assertion Consumer Service URL)** (URL di risposta - URL del servizio consumer di asserzione) nel campo **URL di risposta (URL del servizio consumer di asserzione)** del portale di Azure.  
   ![image](./media/mitel-connect-tutorial/Mitel_Azure_BasicConfig.png)

7. Nella casella di testo **URL di accesso** digitare uno degli URL seguenti:

    * **https://portal.shoretelsky.com** : per usare il portale dell'account Mitel come applicazione Mitel predefinita
    * **https://teamwork.shoretel.com** : per usare Teamwork come applicazione Mitel predefinita

    **NOTA**: L'applicazione Mitel predefinita è quella a cui l'utente accede quando fa clic sul riquadro Mitel Connect nel pannello di accesso. È anche l'applicazione a cui si accede quando si esegue una configurazione di test da Azure AD.

8. Fare clic su **Salva** nella finestra di dialogo **Configurazione SAML di base** del portale di Azure.

9. Nella sezione **Certificato di firma SAML** della pagina **Accesso basato su SAML** del portale di Azure fare clic su **Scarica** accanto a **Certificato (Base64)** per scaricare il **Certificato di firma** e salvarlo nel computer.
    ![image](./media/mitel-connect-tutorial/Azure_SigningCert.png)

10. Aprire il file del certificato di firma in un editor di testo, copiare tutti i dati al suo interno e quindi incollarli nel campo **Signing Certificate** (Certificato di firma) del portale dell'account Mitel. 
    ![image](./media/mitel-connect-tutorial/Mitel_Connect_SigningCert.png)

11. Nella sezione **Configura Mitel Connect** della pagina **Accesso basato su SAML** del portale di Azure, procedere come segue:

    a. Copiare l'URL dal campo **URL di accesso** e copiarlo nel campo **Sign-in URL** (URL di accesso) del portale dell'account Mitel.

    b. Copiare l'URL dal campo **Identificatore Azure AD** e copiarlo nel campo **Entity ID** (ID entità) del portale dell'account Mitel.
    ![image](./media/mitel-connect-tutorial/Mitel_Azure_SetupConnect.png)

12. Fare clic su **Save** (Salva) nella finestra di dialogo **Connect Single Sign-On Settings** del portale dell'account Mitel.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

In questa sezione verrà creato un utente di test di nome Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure fare clic su **Azure Active Directory**, quindi su **Utenti** e infine su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Fare clic su **Nuovo utente** nella parte superiore dello schermo.

    ![Pulsante Nuovo utente](common/new-user.png)

3. Nella finestra di dialogo delle proprietà Utente procedere come segue:

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** digitare **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare brittasimon@\<yourcompanydomain\>.\<extension\>.  
Ad esempio: BrittaSimon@contoso.com.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Mitel Connect.

1. Nel portale di Azure fare clic su **Applicazioni aziendali** e quindi su **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni fare clic su **Mitel Connect**.

    ![Collegamento di Mitel Connect nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic su **Aggiungi utente** e quindi su **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco **Utenti** e quindi fare clic su **Seleziona** nella parte inferiore dello schermo.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Seleziona ruolo** selezionare il ruolo appropriato per l'utente nell'elenco e quindi fare clic su **Seleziona** nella parte inferiore dello schermo.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic su **Assegna**.

### <a name="create-a-mitel-micloud-connect-test-user"></a>Creare un utente di test di Mitel MiCloud Connect

In questa sezione viene creato un utente di nome Britta Simon nell'account di MiCloud Connect. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

Per informazioni sull'aggiunta di utenti nel portale dell'account Mitel, vedere l'articolo su come [aggiungere utenti](https://docs.shoretel.com/connectcloud/Account/Users/AddUser) nella knowledge base Mitel.

Creare un utente nell'account MiCloud Connect con i dettagli seguenti:

  * **Nome:** Britta Simon

* **Indirizzo di posta elettronica aziendale:** `brittasimon@<yourcompanydomain>.<extension>`   
Esempio: [brittasimon@contoso.com](mailto:brittasimon@contoso.com)

* **Nome utente:** `brittasimon@<yourcompanydomain>.<extension>`  
Esempio: [brittasimon@contoso.com](mailto:brittasimon@contoso.com); il nome utente corrisponde in genere all'indirizzo di posta elettronica aziendale

**NOTA:** il nome utente di MiCloud Connect deve essere identico all'indirizzo di posta elettronica dell'utente in Azure.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Mitel Connect nel pannello di accesso, si dovrebbe essere automaticamente reindirizzati nella pagina di accesso all'applicazione MiCloud Connect configurata come predefinita nel campo **URL di accesso**. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
