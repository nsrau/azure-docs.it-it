---
title: 'Esercitazione: Integrazione di Azure Active Directory con Citrix ShareFile | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Citrix ShareFile.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: e14fc310-bac4-4f09-99ef-87e5c77288b6
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc89475ea0338165e7a4477e861ddb199bd73de7
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56879901"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Esercitazione: Integrazione di Azure Active Directory con Citrix ShareFile

Questa esercitazione descrive come integrare Citrix ShareFile con Azure Active Directory (Azure AD).
L'integrazione di Citrix ShareFile con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Citrix ShareFile.
* È possibile consentire agli utenti l'accesso automatico a Citrix ShareFile (Single Sign-On) con i rispettivi account Azure AD.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Citrix ShareFile, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Citrix ShareFile abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Citrix ShareFile supporta l'accesso SSO avviato da **provider di servizi**

## <a name="adding-citrix-sharefile-from-the-gallery"></a>Aggiunta di Citrix ShareFile dalla raccolta

Per configurare l'integrazione di Citrix ShareFile in Azure AD, è necessario aggiungere Citrix ShareFile dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Citrix ShareFile dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Citrix ShareFile**, selezionare **Citrix ShareFile** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Citrix ShareFile nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Citrix ShareFile con un utente di test di nome **Britta Simon**.
Per il funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Citrix ShareFile.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Citrix ShareFile, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Citrix ShareFile](#configure-citrix-sharefile-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Citrix ShareFile](#create-citrix-sharefile-test-user)**: per avere una controparte di Britta Simon in Citrix ShareFile collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Citrix ShareFile, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Citrix ShareFile** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Citrix ShareFile](common/sp-identifier-reply.png)

    a. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<tenant-name>.sharefile.com/saml/login`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL usando il criterio seguente:

    | |
    |---|
    | `https://<tenant-name>.sharefile.com`|
    | `https://<tenant-name>.sharefile.com/saml/info`|
    | `https://<tenant-name>.sharefile1.com/saml/info`|
    | `https://<tenant-name>.sharefile1.eu/saml/info`|
    | `https://<tenant-name>.sharefile.eu/saml/info`|
    | |
    
    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente:
    | |
    |---|
    | `https://<tenant-name>.sharefile.com/saml/acs`|
    | `https://<tenant-name>.sharefile.eu/saml/<URL path>`|
    | `https://<tenant-name>.sharefile.com/saml/<URL path>`|

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team del supporto clienti di Citrix ShareFile](https://www.citrix.co.in/products/citrix-content-collaboration/support.html). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Citrix ShareFile** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-citrix-sharefile-single-sign-on"></a>Configurare l'accesso Single Sign-On di Citrix ShareFile

1. In un'altra finestra del Web browser accedere al sito aziendale di **Citrix ShareFile** come amministratore.

2. Nel barra degli strumenti in alto fare clic su **Admin**.

3. Nel riquadro di spostamento sinistro selezionare **Configure Single Sign-On**.
   
    ![Account amministratore](./media/sharefile-tutorial/ic773627.png "Account amministratore")

4. In **Basic Settings** della finestra di dialogo **Single Sign-On/ SAML 2.0 Configuration** seguire questa procedura:
   
    ![Single Sign-On](./media/sharefile-tutorial/ic773628.png "Single Sign-On")
   
    a. Fare clic su **Enable SAML**.
    
    b. Nella casella di testo **Your IDP Issuer/ Entity ID** (Autorità di certificazione IdP/ID entità) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    c. Fare clic su **Change** (Cambia) accanto al campo **X.509 Certificate** (Certificato X.509) e quindi caricare il certificato scaricato dal portale di Azure.
    
    d. Nella casella di testo **Login URL** (URL di accesso) incollare il valore di **URL di accesso** copiato dal portale di Azure.
    
    e. Nella casella di testo **Logout URL** (URL disconnessione) incollare il valore di **URL disconnessione** copiato dal portale di Azure.

5. Fare clic su **Save** nel portale di gestione di Citrix ShareFile.

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

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure, tramite la concessione dell'accesso a Citrix ShareFile.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Citrix ShareFile**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni digitare e selezionare **Citrix ShareFile**.

    ![Collegamento di Citrix ShareFile nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-citrix-sharefile-test-user"></a>Creare un utente di test di Citrix ShareFile

Per consentire agli utenti di Azure AD di accedere a Citrix ShareFile, è necessario eseguirne il provisioning in Citrix ShareFile. Nel caso di Citrix ShareFile, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al tenant di **Citrix ShareFile** .

2. Fare clic su **Manage Users \> Manage Users Home \> + Create Employee** (Gestisci utenti > Gestisci home utenti > + Crea dipendente).
   
    ![Crea dipendente](./media/sharefile-tutorial/IC781050.png "Crea dipendente")

3. Nella sezione **Informazioni di base** seguire questa procedura:
   
    ![Informazioni di base](./media/sharefile-tutorial/IC799951.png "Informazioni di base")
   
    a. Nella casella di testo **Email Address** (Indirizzo di posta elettronica) digitare l'indirizzo di posta elettronica di Britta Simon come **brittasimon@contoso.com**.
   
    b. Nella casella di testo **First Name** (Nome) digitare il **nome** dell'utente come **Britta**.
   
    c. Nella casella di testo **Last Name** (Cognome) digitare il **cognome** dell'utente come **Simon**.

4. Fare clic su **Add User**.
  
    >[!NOTE]
    >Il titolare dell'account Azure AD riceverà un messaggio di posta elettronica e selezionerà un collegamento per confermare il proprio account prima che venga attivato. È possibile usare qualsiasi altro strumento di creazione di account utente Citrix ShareFile o qualsiasi API fornita da Citrix ShareFile per effettuare il provisioning degli account utente di Azure AD.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Facendo clic sul riquadro Citrix ShareFile nel pannello di accesso si dovrebbe accedere automaticamente all'applicazione Citrix ShareFile per cui è stato configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

