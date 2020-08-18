---
title: 'Esercitazione: Integrazione di Azure Active Directory con PagerDuty | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e PagerDuty.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0410456a-76f7-42a7-9bb5-f767de75a0e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 330066a950165d3424ca7900493ac89ce746b309
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87904548"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pagerduty"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con PagerDuty

Questa esercitazione descrive come integrare PagerDuty con Azure Active Directory (Azure AD). Integrando PagerDuty con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a PagerDuty.
* Abilitare gli utenti per l'accesso automatico a PagerDuty con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di PagerDuty abilitata per l'accesso Single Sign-On (SSO).

> [!NOTE]
> Se si usa l'autenticazione a più fattori o l'autenticazione senza password con Azure AD, disattivare il valore AuthnContext nella richiesta SAML. In caso contrario Azure AD genererà l'errore in caso di mancata corrispondenza di AuthnContext e non invierà di nuovo il token all'applicazione.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* PagerDuty supporta l'accesso SSO avviato da **SP**
* Dopo aver configurato PagerDuty, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-pagerduty-from-the-gallery"></a>Aggiunta di PagerDuty dalla raccolta

Per configurare l'integrazione di PagerDuty in Azure AD, è necessario aggiungere PagerDuty dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **PagerDuty** nella casella di ricerca.
1. Selezionare **PagerDuty** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-pagerduty"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per PagerDuty

Configurare e testare l'accesso SSO di Azure AD con PagerDuty usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in PagerDuty.

Per configurare e testare l'accesso SSO di Azure AD con PagerDuty, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di PagerDuty](#configure-pagerduty-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di PagerDuty](#create-pagerduty-test-user)** : per avere una controparte di B.Simon in PagerDuty collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **PagerDuty** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<tenant-name>.pagerduty.com`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://<tenant-name>.pagerduty.com`

    c. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<tenant-name>.pagerduty.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, Aggiornarli con l'URL di accesso, l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di PagerDuty](https://www.pagerduty.com/support/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura PagerDuty** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a PagerDuty.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **PagerDuty**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-pagerduty-sso"></a>Configurare l'accesso Single Sign-On di PagerDuty

1. In un'altra finestra del Web browser accedere al sito aziendale di Pagerduty come amministratore.

2. Nel menu in alto fare clic su **Impostazioni account**.

    ![Impostazioni dell'account](./media/pagerduty-tutorial/ic778535.png "Impostazioni account")

3. Fare clic su **Single Sign-On**.

    ![Single Sign-On](./media/pagerduty-tutorial/ic778536.png "Single sign-on")

4. Nella pagina **Attiva Single Sign-on (SSO)** eseguire la procedura seguente:

    ![Abilita Single Sign-On](./media/pagerduty-tutorial/ic778537.png "Abilita Single Sign-On")

    a. Aprire nel Blocco note il certificato con codifica Base 64 scaricato dal portale di Azure, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **X.509 Certificate** (Certificato X.509).
  
    b. Nella casella di testo **Login URL** (URL di accesso) incollare l'**URL di accesso** copiato dal portale di Azure.
  
    c. Nella casella di testo **Logout URL** (URL di disconnessione) incollare l'**URL di disconnessione** copiato dal portale di Azure.

    d. Selezionare **Allow username/password login** (Consenti accesso tramite password/nome utente).

    e. Selezionare la casella di testo **Require EXACT authentication context comparison** (Richiedi il confronto ESATTO del contesto di autenticazione).

    f. Fare clic su **Salva modifiche**.

### <a name="create-pagerduty-test-user"></a>Creare un utente di test di PagerDuty

Per consentire agli utenti di Azure AD di accedere a PagerDuty, è necessario effettuarne il provisioning in PagerDuty. Nel caso di PagerDuty, il provisioning è un'attività manuale.

> [!NOTE]
> È possibile usare qualsiasi altro strumento di creazione di account utente di Pagerduty o le API fornite da Pagerduty per effettuare il provisioning degli account utente di Azure Active Directory.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere al tenant **Pagerduty**.

2. Scegliere **Utenti**dal menu in alto.

3. Fare clic su **Aggiungi utenti**.
   
    ![Aggiungere utenti](./media/pagerduty-tutorial/ic778539.png "Aggiungi utenti")

4.  Nella finestra di dialogo **Invite Users** (Invita utenti) seguire questa procedura:
   
    ![Invite your team](./media/pagerduty-tutorial/ic778540.png "Invita il team")

    a. Digitare il **nome e il cognome** dell'utente, ad esempio **B.Simon**. 
   
    b. Immettere l'indirizzo di **posta elettronica** dell'utente, ad esempio **b.simon\@contoso.com**.
   
    c. Fare clic su **Add** (Aggiungi) e quindi su **Send Invites** (Invia inviti).
   
    > [!NOTE]
    > Tutti gli utenti aggiunti riceveranno un invito per creare un account PagerDuty.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di PagerDuty nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di PagerDuty per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare PagerDuty con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessione in Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Come proteggere PagerDuty con visibilità e controlli avanzati](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

