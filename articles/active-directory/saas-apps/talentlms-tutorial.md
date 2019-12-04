---
title: 'Esercitazione: Integrazione di Azure Active Directory con TalentLMS | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e TalentLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c903d20d-18e3-42b0-b997-6349c5412dde
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 970ab9de270b1227884a13ac578d4c439043b20c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233372"
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Esercitazione: Integrazione di Azure Active Directory con TalentLMS

Questa esercitazione descrive come integrare TalentLMS con Azure Active Directory (Azure AD).
L'integrazione di TalentLMS con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a TalentLMS.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a TalentLMS con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con TalentLMS, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Una sottoscrizione di TalentLMS abilitata per l'accesso Single Sign-On.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* TalentLMS supporta l'accesso Single Sign-On avviato da **provider di servizi**

## <a name="adding-talentlms-from-the-gallery"></a>Aggiunta di TalentLMS dalla raccolta

Per configurare l'integrazione di TalentLMS in Azure AD, è necessario aggiungere TalentLMS dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere TalentLMS dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **TalentLMS**, selezionare **TalentLMS** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![TalentLMS nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con TalentLMS usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in TalentLMS.

Per configurare e testare l'accesso Single Sign-On di Azure AD con TalentLMS, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di TalentLMS](#configure-talentlms-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di TalentLMS](#create-talentlms-test-user)** : per avere una controparte di Britta Simon in TalentLMS collegata alla relativa rappresentazione in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con TalentLMS, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **TalentLMS** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di TalentLMS](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<tenant-name>.TalentLMSapp.com`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `http://<tenant-name>.talentlms.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di TalentLMS](https://www.talentlms.com/contact). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella sezione **Certificato di firma SAML**  fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Certificato di firma SAML** .

    ![Modificare il certificato di firma SAML](common/edit-certificate.png)

6. Nella sezione **Certificato di firma SAML** copiare il valore **IDENTIFICAZIONE PERSONALE** e salvarlo nel computer.

    ![Copiare il valore di Identificazione personale](common/copy-thumbprint.png)

7. Nella sezione **Configura TalentLMS** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-talentlms-single-sign-on"></a>Configurare l'accesso Single Sign-On di TalentLMS

1. In un'altra finestra del Web browser accedere al sito aziendale di TalentLMS come amministratore.

1. Nella sezione **Account & Settings** (Account e impostazioni) fare clic sulla scheda **Users** (Utenti).

    ![Account & Settings](./media/talentlms-tutorial/IC777296.png "Account e impostazioni")

1. Fare clic su **Single Sign-On (SSO)** .

1. Nella sezione Single Sign-On seguire questa procedura:

    ![Single Sign-On](./media/talentlms-tutorial/IC777297.png "Single Sign-On")

    a. Dall'elenco **SSO integration type** (Tipo integrazione SSO) selezionare **SAML 2.0**.

    b. Nella casella di testo **Identity Provider (IDP)** (Provider di entità) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    c. Incollare il valore **Identificazione personale** dal portale di Azure nella casella di testo **Certificate Fingerprint** (Impronta digitale certificato).

    d.  Nella casella di testo**Remote sign-in URL** (URL accesso remoto) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    e. Nella casella di testo**Remote sign-out URL** (URL di disconnessione remota) incollare il valore di **URL di disconnessione** copiato dal portale di Azure.

    f. Specificare le informazioni seguenti:

    * Nella casella di testo **TargetedID** (ID destinazione) digitare `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`

    * Nella casella di testo **First name** (Nome) digitare `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    * Nella casella di testo **Last name** (Cognome) digitare `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    * Nella casella di testo **Email** (Posta elettronica) digitare `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

1. Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

Questa sezione descrive come creare un utente di test di nome Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare `brittasimon@yourcompanydomain.extension`. Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a TalentLMS.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **TalentLMS**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **TalentLMS**.

    ![Collegamento a TalentLMS nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-talentlms-test-user"></a>Creare l'utente di test di TalentLMS

Per consentire agli utenti di Azure AD di accedere a TalentLMS, è necessario eseguirne il provisioning in TalentLMS. Nel caso di TalentLMS, il provisioning è un'attività manuale.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere al tenant **TalentLMS**.

1. Fare clic su **Users** (Utenti), quindi fare clic su **Add User** (Aggiungi utente).

1. Nella pagina della finestra di dialogo **Add User** seguire questa procedura:

    ![Add User](./media/talentlms-tutorial/IC777299.png "Aggiunta di un utente")  

    a. Nella casella di testo **First name** (Nome) immettere il nome dell'utente, ad esempio **Britta**.

    b. Nella casella di testo **Last name** (Cognome) immettere il cognome dell'utente, ad esempio **Simon**.
 
    c. Nella casella di testo **Email address** (Indirizzo di posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio `brittasimon\@contoso.com`.

    d. Fare clic su **Add User**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente forniti da TalentLMS per effettuare il provisioning degli account utente di Azure AD.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di TalentLMS nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di TalentLMS per cui si è configurato l'accesso Single Sign-On. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

