---
title: 'Esercitazione: Integrazione di Azure Active Directory con Proxyclick | Microsoft Docs'
description: In questa esercitazione si apprenderà come configurare l'accesso Single Sign-On tra Azure Active Directory e Proxyclick.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5c58a859-71c2-4542-ae92-e5f16a8e7f18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 6a4d1c8a390ebd1194d14c057bb32d3111bf39be
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67093500"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Esercitazione: Integrazione di Azure Active Directory con Proxyclick

Questa esercitazione descrive come integrare Proxyclick con Azure Active Directory (Azure AD).
Questa integrazione offre i seguenti vantaggi:

* È possibile usare Azure AD per controllare chi può accedere a Proxyclick.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Proxyclick con gli account Azure AD personali.
* È possibile gestire gli account da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Proxyclick, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile iscriversi per ottenere una [versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).
* Sottoscrizione di Proxyclick abilitata per l'accesso Single Sign-On.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione verranno eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Proxyclick supporta l'accesso SSO avviato da SP e IdP.

## <a name="add-proxyclick-from-the-gallery"></a>Aggiungere Proxyclick dalla raccolta

Per configurare l'integrazione di Proxyclick in Azure AD, è necessario aggiungere Proxyclick dalla raccolta al proprio elenco di app SaaS gestite.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** nel riquadro sinistro:

    ![Selezionare Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali** > **Tutte le applicazioni**:

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere un'applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra:

    ![Selezionare Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Proxyclick**. Selezionare **Proxyclick** nei risultati della ricerca e quindi selezionare **Aggiungi**.

     ![Search Results](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione verrà configurato e testato l'accesso Single Sign-On di Azure AD con Proxyclick usando un utente di test di nome Britta Simon.
Per abilitare l'accesso Single Sign-On, è necessario stabilire una relazione tra un utente di Azure AD e l'utente corrispondente in Proxyclick.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Proxyclick, è necessario completare questi passaggi:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** per abilitare questa funzionalità per gli utenti.
2. **[Configurare l'accesso Single Sign-On di Proxyclick](#configure-proxyclick-single-sign-on)** sul lato dell'applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** per abilitare l'accesso Single Sign-On di Azure AD per tale utente.
5. **[Creare un utente di test di Proxyclick](#create-a-proxyclick-test-user)** collegato alla rappresentazione di Azure AD dell'utente.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione verrà abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Proxyclick, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione Proxyclick del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**:

    ![Selezionare Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare l'accesso Single Sign-On:

    ![Selezionare un metodo di accesso Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** selezionare l'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**:

    ![Icona Modifica](common/edit-urls.png)

4. Nella finestra di dialogo **Configurazione SAML di base** seguire questa procedura per configurare l'applicazione nella modalità avviata da IdP.

    ![Finestra di dialogo Configurazione SAML di base](common/idp-intiated.png)

    1. Nella casella **Identificatore** immettere un URL nel formato seguente:
   
       `https://saml.proxyclick.com/init/<companyId>`

    1. Nella casella **URL di risposta** immettere un URL nel formato seguente:

       `https://saml.proxyclick.com/consume/<companyId>`

5. Se si vuole configurare l'applicazione in modalità avviata da SP, selezionare **Impostare URL aggiuntivi**. Nella casella **URL di accesso** immettere un URL nel formato seguente:
   
   `https://saml.proxyclick.com/init/<companyId>`

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Proxyclick](common/metadata-upload-additional-signon.png)

    

    > [!NOTE]
    > Questi valori sono segnaposto. È necessario usare l'identificatore, l'URL di risposta e l'URL di accesso effettivi. La procedura da eseguire per ottenere questi valori è descritta più avanti in questa esercitazione.

6. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** selezionare il collegamento **Scarica** accanto a **Certificato (Base64)** , in base alle esigenze, quindi salvare il certificato nel computer:

    ![Collegamento di download del certificato](common/certificatebase64.png)

7. Nella sezione **Configura Proxyclick** copiare gli URL appropriati in base alle esigenze:

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    1. **URL di accesso**.

    1. **Identificatore Azure AD**.

    1. **URL di disconnessione**.

### <a name="configure-proxyclick-single-sign-on"></a>Configurare l'accesso Single Sign-On di Proxyclick

1. In una nuova finestra del Web browser accedere al sito aziendale di Proxyclick come amministratore.

2. Selezionare **Account & Settings** (Account e impostazioni):

    ![Selezionare Account & Settings (Account e impostazioni)](./media/proxyclick-tutorial/configure1.png)

3. Scorrere verso il basso fino alla sezione **Integrations** (Integrazioni) e selezionare **SAML**:

    ![Selezionare SAML](./media/proxyclick-tutorial/configure2.png)

4. Nella sezione **SAML** seguire questa procedura.

    ![Sezione SAML](./media/proxyclick-tutorial/configure3.png)

    1. Copiare il valore di **SAML Consumer URL** (URL consumer SAML) e incollarlo nella casella **URL di risposta** nella finestra di dialogo **Configurazione SAML di base** del portale di Azure.

    1. Copiare il valore di **SAML SSO Redirect URL** (URL di reindirizzamento SSO SAML) e incollarlo nelle caselle **URL di accesso** e **Identificatore** nella finestra di dialogo **Configurazione SAML di base** del portale di Azure.

    1. Nell'elenco **SAML Request Method** (Metodo di richiesta SAML) selezionare **HTTP Redirect** (Reindirizzamento HTTP).

    1. Nella casella **Issuer** (Autorità di certificazione) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    1. Nella casella **SAML 2.0 Endpoint URL** (URL endpoint SAML 2.0) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    1. Nel Blocco note aprire il file del certificato scaricato dal portale di Azure. Incollare il contenuto di questo file nella casella **Certificate** (Certificato).

    1. Selezionare **Save changes** (Salva modifiche).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione verrà creato un utente di test di nome Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**:

    ![Selezionare Tutti gli utenti](common/users.png)

2. Selezionare **Nuovo utente** nella parte superiore della schermata:

    ![Selezionare Nuovo utente](common/new-user.png)

3. Nella finestra di dialogo **Utente** seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    1. Nella casella **Nome** immettere **BrittaSimon**.
  
    1. Nella casella **Nome utente** immettere **BrittaSimon@\<dominioaziendale>.\<estensione>** , ad esempio BrittaSimon@contoso.com.

    1. Selezionare **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    1. Selezionare **Create** (Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon sarà abilitata per l'uso dell'accesso Single Sign-On di Azure, concedendole l'accesso a Proxyclick.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Proxyclick**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Proxyclick**.

    ![Elenco delle applicazioni](common/all-applications.png)

3. Nel riquadro sinistro selezionare **Utenti e gruppi**:

    ![Selezionare Utenti e gruppi](common/users-groups-blade.png)

4. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Selezionare Aggiungi utente](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco utenti e quindi fare clic sul pulsante **Seleziona** in basso nella finestra.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco. Fare clic sul pulsante **Seleziona** in basso nella finestra.

7. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

### <a name="create-a-proxyclick-test-user"></a>Creare un utente test Proxyclick

Per consentire agli utenti di Azure AD di accedere a Proxyclick, aggiungerli a Proxyclick. È necessario aggiungerli manualmente.

Per creare un account utente, seguire questi passaggi:

1. Accedere al sito aziendale di Proxyclick come amministratore.

1. Selezionare **Colleagues** (Colleghi) nella parte superiore della finestra:

    ![Selezionare Colleagues (Colleghi)](./media/proxyclick-tutorial/user1.png)

1. Selezionare **Add Colleague** (Aggiungi collega):

    ![Selezionare Add Colleague (Aggiungi collega)](./media/proxyclick-tutorial/user2.png)

1. Nella sezione **Add a colleague** (Aggiungi un collega) seguire questa procedura.

    ![Sezione Add a colleague (Aggiungi un collega)](./media/proxyclick-tutorial/user3.png)

    1. Nella casella **Email** immettere l'indirizzo di posta elettronica dell'utente. In questo caso, **brittasimon\@contoso.com**.

    1. Nella casella **First Name** (Nome) immettere il nome dell'utente. In questo caso, **Britta**.

    1. Nella casella **Last Name** (Cognome) immettere il cognome dell'utente. In questo caso, **Simon**.

    1. Selezionare **Add User** (Aggiungi utente).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

Il passaggio finale consiste nel testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro di Proxyclick nel Pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Proxyclick per cui si è configurato l'accesso SSO. Per altre informazioni sul Pannello di accesso, vedere [Accedere e usare le app nel portale App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Esercitazioni per l'integrazione di applicazioni SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

