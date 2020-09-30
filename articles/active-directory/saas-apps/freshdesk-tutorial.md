---
title: 'Esercitazione: Integrazione di Azure Active Directory con FreshDesk | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e FreshDesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/24/2020
ms.author: jeedes
ms.openlocfilehash: 0bbfdc2463795770b52f5008fc633fe9e95244a0
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2020
ms.locfileid: "90056524"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Esercitazione: Integrazione di Azure Active Directory con FreshDesk

Questa esercitazione descrive come integrare FreshDesk con Azure Active Directory (Azure AD).
L'integrazione di FreshDesk con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a FreshDesk.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a FreshDesk con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisites

Per configurare l'integrazione di Azure AD con FreshDesk, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di FreshDesk abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* FreshDesk supporta l'accesso SSO avviato da **SP**
* Dopo aver configurato FreshDesk, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-freshdesk-from-the-gallery"></a>Aggiunta di FreshDesk dalla raccolta

Per configurare l'integrazione di FreshDesk in Azure AD, è necessario aggiungerla dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **FreshDesk** nella casella di ricerca.
1. Selezionare **FreshDesk** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-freshdesk"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per FreshDesk

Configurare e testare l'accesso SSO di Azure AD con FreshDesk usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in FreshDesk.

Per configurare e testare l'accesso SSO di Azure AD con FreshDesk, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di FreshDesk](#configure-freshdesk-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare un utente di test di FreshDesk](#create-freshdesk-test-user)** : per avere una controparte di Britta Simon in FreshDesk collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

## <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

1. Nella pagina di integrazione dell'applicazione **FreshDesk** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello `https://<tenant-name>.freshdesk.com` o qualsiasi altro valore suggerito da FreshDesk.

    b. Nella casella di testo **Identificatore (ID entità)** digitare l'URL usando il modello `https://<tenant-name>.freshdesk.com` o qualsiasi altro valore suggerito da FreshDesk.
     
    c. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<tenant-name>.freshdesk.com/login/saml`
    
    > [!NOTE]
    > Poiché questi non sono i valori reali, Aggiornarli con l'URL di accesso, l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto di FreshDesk](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione FreshDesk prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti. **Identificatore univoco dell'utente** è mappato con **user.userprincipalname** ma FreshDesk si aspetta che questa attestazione sia mappata con **user.mail**, quindi è necessario modificare il mapping degli attributi facendo clic sull'icona Modifica e cambiando il mapping degli attributi.

    ![image](common/edit-attribute.png)

1. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura FreshDesk** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

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
    Ad esempio, usare BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Crea**.


### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a FreshDesk.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **FreshDesk**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni digitare e selezionare **FreshDesk**.

    ![Collegamento di FreshDesk nell'elenco Applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-freshdesk-single-sign-on"></a>Configurare l'accesso Single Sign-On per FreshDesk

1. In un'altra finestra del Web browser accedere al sito aziendale di Freshdesk come amministratore.

2. Selezionare l'**icona Security** (Sicurezza) e nella sezione **Security** (Sicurezza) seguire questa procedura:

    ![Single Sign-On](./media/freshdesk-tutorial/configure-1.png "Single Sign On")
  
    a. Per **Single Sign On** selezionare **On**.

    b. In **Login Method** (Metodo di accesso) selezionare **SAML SSO** (SSO SAML).

    c. Nella casella di testo **Entity ID provided by the IdP** (ID entità fornito dall'IdP) incollare il valore di **ID entità**, copiato dal portale di Azure.

    d. Nella casella di testo **SAML SSO URL** (URL SSO SAML) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    e. In **Signing Options** (Opzioni di firma) selezionare **Only Signed Assertions** (Solo asserzioni firmate) dall'elenco a discesa.

    f. Nella casella di testo **Logout URL** (URL disconnessione) incollare il valore di **URL disconnessione** copiato dal portale di Azure.

    g. Nella casella di testo **Security Certificate** (Certificato di sicurezza) incollare il valore di **Certificato (Base64)** ottenuto in precedenza.
  
    h. Fare clic su **Salva**.

## <a name="create-freshdesk-test-user"></a>Creare l'utente di test di FreshDesk

Per consentire agli utenti di Azure AD di accedere a FreshDesk, è necessario eseguirne il provisioning in FreshDesk.  
Nel caso di FreshDesk, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al tenant di **Freshdesk** .

1. Nel menu a sinistra fare clic su **Admin** (Amministratore) e nella scheda **General Settings** (Impostazioni generali) fare clic su **Agents** (Agenti).
  
    ![Agenti](./media/freshdesk-tutorial/create-user-1.png "Agenti")

1. Fare clic su **New Agent**.

    ![Nuovo agente](./media/freshdesk-tutorial/create-user-2.png "New Agent")

1. Nella finestra di dialogo delle informazioni sull'agente immettere i campi necessari e fare clic su **Create agent** (Crea agente).

    ![Informazioni dell'agente](./media/freshdesk-tutorial/create-user-3.png "Agent Information")

    >[!NOTE]
    >Il titolare dell'account AD riceverà un messaggio di posta elettronica contenente un collegamento da selezionare per confermare e attivare l'account.
    >
    >[!NOTE]
    >È possibile usare qualsiasi altro strumento o API di creazione di account utente fornito da Freshdesk per eseguire il provisioning degli account utente Azure AD in FreshDesk.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di FreshDesk nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione FreshDesk per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

