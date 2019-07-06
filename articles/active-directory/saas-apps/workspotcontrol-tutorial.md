---
title: 'Esercitazione: integrazione di Azure Active Directory con Workspot Control | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On per Azure Active Directory e Workspot Control.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3ea8e4e9-f61f-4f45-b635-b0e306eda3d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: jeedes
ms.openlocfilehash: 086ec95531b01477be56d4b1a19d189f167a020f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086671"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>Esercitazione: integrazione di Azure Active Directory con Workspot Control

Questa esercitazione descrive come integrare Workspot Control con Azure Active Directory (Azure AD). Integrando Workspot Control con Azure AD:

* È possibile usare Azure AD per controllare chi ha accesso a Workspot Control.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Workspot Control con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Workspot Control, sono necessari i requisiti seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).

* Una sottoscrizione di Workspot Control abilitata per l'accesso Single Sign-On.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

> [!Note]
> Workspot Control supporta il Single Sign-On avviato da SP e avviato da IDP.


## <a name="adding-workspot-control-from-the-gallery"></a>Aggiunta di Workspot Control dalla raccolta

Per configurare l'integrazione di Workspot Control in Azure AD, è necessario aggiungere Workspot Control dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Workspot Control dalla raccolta, seguire questa procedura:**

1. Nel riquadro sinistro del [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e selezionare **Tutte le applicazioni**.

    ![Riquadro Applicazioni aziendali](common/enterprise-applications.png)

3. Selezionare **Nuova applicazione** nella parte superiore della finestra.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Workspot Control**, selezionare **Workspot Control** nel pannello dei risultati e quindi selezionare **Aggiungi**.

     ![Finestra "Aggiungi dalla raccolta"](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Workspot Control per un utente di test di nome Britta Simon.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilito un collegamento tra un utente di Azure AD e l'utente correlato in Workspot Control.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Workspot Control, è necessario completare le attività seguenti:

1. [Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on) per consentire agli utenti di usare questa funzionalità.
2. [Configurare l'accesso Single Sign-On di Workspot Control](#configure-workspot-control-single-sign-on) per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. [Creare un utente di test di Azure AD](#create-an-azure-ad-test-user) per testare l'accesso Single Sign-On di Azure AD per l'utente Britta Simon.
4. [Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user) per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. [Creare un utente di test di Workspot Control](#create-a-workspot-control-test-user): per stabilire una controparte di Britta Simon in Workspot Control collegata alla rappresentazione di Azure AD dell'utente.
6. [Testare l'accesso Single Sign-On](#test-single-sign-on) per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Workspot Control, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione [Workspot Control](https://portal.azure.com/) nel **portale di Azure** selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML** per abilitare l'accesso Single Sign-On.

    ![Finestra Selezionare un metodo di accesso Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** selezionare l'icona **Modifica** (la matita) per aprire **Configurazione SAML di base**.

    ![Icona Modifica evidenziata in "Configurazione SAML di base"](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione nella modalità avviata da IDP:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Workspot Control](common/idp-intiated.png)

    1. Nella casella di testo **Identificatore** immettere un URL nel formato seguente:<br/>
    ***https://<<i></i>NOMEISTANZA>-saml.workspot.com/saml/metadata***

    1. Nella casella di testo **URL di risposta** immettere un URL nel formato seguente:<br/>
    ***https://<<i></i>NOMEISTANZA>-saml.workspot.com/saml/assertion***

5. Se si vuole configurare l'applicazione in modalità avviata da SP, selezionare **Impostare URL aggiuntivi**.

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Workspot Control](common/metadata-upload-additional-signon.png)

    Nella casella di testo **URL di accesso** immettere un URL nel formato seguente:<br/>
    ***https://<<i></i>NOMEISTANZA>-saml.workspot.com/***

    > [!NOTE]
    > Poiché questi non sono i valori reali, sostituirli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Workspot Control](mailto:support@workspot.com). È anche possibile fare riferimento ai modelli disponibili nella sezione **Configurazione SAML di base** del portale di Azure.

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, selezionare **Scarica** per scaricare il file **Certificato (Base64)** dalle opzioni disponibili in base alle esigenze. Salvarlo nel computer.

    ![Collegamento di download del certificato (Base64)](common/certificatebase64.png)

7. Nella sezione **Configura Workspot Control** copiare gli URL appropriati in base alle esigenze:

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    - **URL di accesso**

    - **Identificatore Azure AD**

    - **URL di disconnessione**

### <a name="configure-workspot-control-single-sign-on"></a>Configurare l'accesso Single Sign-On di Workspot Control

1. In un'altra finestra del Web browser accedere a Workspot Control come amministratore della sicurezza.

2. Sulla barra degli strumenti nella parte superiore della pagina fare clic su **Setup** (Configura) e quindi su **SAML**.

    ![Opzioni di configurazione](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

3. Nella finestra **Security Assertion Markup Language Configuration** (Configurazione di Security Assertion Markup Language) seguire questa procedura:
 
    ![Finestra Security Assertion Markup Language Configuration](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    1. Nella casella **Entity ID** (ID entità) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    1. Nella casella **Signon Service URL** (URL del servizio di accesso) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    1. Nella casella **Logout Service URL** (URL servizio di disconnessione) incollare il valore di **URL di disconnessione** copiato dal portale di Azure.

    1. Selezionare **Update File** (Aggiorna file) per caricare nel certificato X.509 il certificato codificato in base 64 scaricato dal portale di Azure.

    1. Selezionare **Salva**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione viene creato un utente di test nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** nella parte superiore della finestra.

    ![Pulsante "Nuovo utente"](common/new-user.png)

3. Nella finestra delle proprietà dell'utente eseguire questa procedura:

    ![Finestra delle proprietà dell'utente](common/user-properties.png)

    1. Nel campo **Nome** immettere **BrittaSimon**.
  
    1. Nel campo **Nome utente** immettere **brittasimon@* dominioaziendale.estensione***. Ad esempio, immettere **BrittaSimon@contoso.<i></i>com**.

    1. Selezionare la casella di controllo **Mostra password**. Prendere quindi nota del valore visualizzato nella casella **Password**.

    1. Selezionare **Create** (Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione viene concesso a Britta Simon l'accesso a Workspot Control per consentirle di usare l'accesso Single Sign-On di Azure.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, **Tutte le applicazioni** e quindi **Workspot Control**.

    ![Riquadro Applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Workspot Control**.

    ![Collegamento a Workspot Control nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Selezionare il pulsante **Aggiungi utente**. Quindi selezionare **Utenti e gruppi** nella finestra **Aggiungi assegnazione**.

    ![Finestra "Aggiungi assegnazione"](common/add-assign-user.png)

5. Nella finestra **Utenti e gruppi** selezionare **Britta Simon** nell'elenco **Utenti**. Quindi fare clic su **Seleziona**.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco. Quindi fare clic su **Seleziona** nella parte inferiore.

7. Nella finestra **Aggiungi assegnazione** selezionare **Assegna**.

### <a name="create-a-workspot-control-test-user"></a>Creare un utente di test di Workspot Control

Per consentire agli utenti di Azure AD di accedere a Workspot Control, è necessario effettuarne il provisioning in Workspot Control. Il provisioning è un'attività manuale.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere a Workspot Control come amministratore della sicurezza.

2. Sulla barra degli strumenti nella parte superiore della pagina selezionare **Users** (Utenti) e quindi **Add User** (Aggiungi utente).

    ![Opzioni di "Utenti"](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. Nella finestra **Add a New User** (Aggiungi nuovo utente) seguire questa procedura:

    ![Finestra "Add a New User"](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    1. Nella casella **First Name** (Nome) immettere il nome dell'utente, ad esempio **Britta**.

    1. Nella casella di testo **Last Name** (Cognome) immettere il cognome dell'utente, ad esempio **Simon**.

    1. Nella casella **Email** (Posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio **Brittasimon@contoso.<i></i>com**.

    1. Selezionare il ruolo utente appropriato nell'elenco a discesa **Role** (Ruolo).

    1. Selezionare il gruppo utenti appropriato nell'elenco a discesa **Group** (Gruppo).

    1. Selezionare **Add User** (Aggiungi utente).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD tramite il *pannello di accesso*.

Quando si fa clic sul riquadro di **Workspot Control** nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Workspot Control per cui si è configurato l'accesso Single Sign-On. Per altre informazioni, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Esercitazioni per l'integrazione di applicazioni SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

- [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
