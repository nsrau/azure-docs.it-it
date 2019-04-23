---
title: 'Esercitazione: Integrazione di Azure Active Directory con Vidyard | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Vidyard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bed7df23-6e13-4e7c-b4cc-53ed4804664d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 94e5893caa4dc19702f45e9b42727aab4f884bcd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59273856"
---
# <a name="tutorial-azure-active-directory-integration-with-vidyard"></a>Esercitazione: Integrazione di Azure Active Directory con Vidyard

Questa esercitazione descrive come integrare Vidyard con Azure Active Directory (Azure AD).
L'integrazione di Vidyard con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Vidyard.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Vidyard con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Vidyard, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Vidyard abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Vidyard supporta l'accesso SSO avviato da **SP** e **IDP**

* Vidyard supporta il provisioning utenti **JIT**

## <a name="adding-vidyard-from-the-gallery"></a>Aggiunta di Vidyard dalla raccolta

Per configurare l'integrazione di Vidyard in Azure AD, è necessario aggiungere Vidyard dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Vidyard dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Vidyard**, selezionare **Vidyard** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Vidyard nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Vidyard usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Vidyard.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Vidyard, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Vidyard](#configure-vidyard-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Vidyard](#create-vidyard-test-user)**: per avere una controparte di Britta Simon in Vidyard collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Vidyard, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Vidyard** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Vidyard](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://secure.vidyard.com/sso/saml/<unique id>/metadata`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://secure.vidyard.com/sso/saml/<unique id>/consume`

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Vidyard](common/metadata-upload-additional-signon.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://secure.vidyard.com/sso/saml/<unique id>/login`

    > [!NOTE]
    > Poiché questi non sono i valori reali, È necessario aggiornare tali valori con i valori reali di identificatore, URL di risposta e URL di accesso. La procedura è descritta più avanti nell'esercitazione. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

7. Nella sezione **Configura Vidyard** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-vidyard-single-sign-on"></a>Configurare l'accesso Single Sign-On di Vidyard

1. In un'altra finestra del Web browser accedere al sito aziendale di Vidyard Software come amministratore.

2. Nel dashboard di Vidyard selezionare **Group** (Gruppo) > **Security** (Sicurezza)

    ![Configurazione Vidyard](./media/vidyard-tutorial/configure1.png)

3. Fare clic su **New Profile** (Nuovo Profilo).

    ![Configurazione Vidyard](./media/vidyard-tutorial/configure2.png)

4. Nella sezione **SAML Configuration** (Configurazione SAML) seguire questa procedura:

    ![Configurazione Vidyard](./media/vidyard-tutorial/configure3.png)

    a. Immettere il nome del profilo generale nella casella di testo **Profile Name** (Nome profilo).

    b. Copiare il valore di **SSO User Login Page** (Pagina di accesso utente SSO) e incollarlo nella casella di testo **URL di accesso** nella sezione **Configurazione SAML di base** del portale di Azure.

    c. Copiare il valore di **ACS URL** (URL ACS) e incollarlo nella casella di testo **URL di risposta** nella sezione **Configurazione SAML di base** del portale di Azure.

    d. Copiare il valore di **Issuer/Metadata URL** (URL autorità di certificazione/metadati) e incollarlo nella casella di testo **Identificatore** nella sezione **Configurazione SAML di base** del portale di Azure.

    e. Aprire il certificato scaricato dal portale di Azure nel Blocco note, copiarne il contenuto e incollarlo nella casella di testo **X.509 Certificate** (Certificato X.509).

    f. Nella casella **SAML Endpoint URL** (URL endpoint SAML) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    g. Fare clic su **Conferma**.

5. Nella scheda Single Sign-On selezionare **Assign** (Assegna) accanto a un profilo esistente

    ![Configurazione Vidyard](./media/vidyard-tutorial/configure4.png)

    > [!NOTE]
    > Dopo aver creato un profilo SSO, è possibile assegnarlo a qualsiasi gruppo per i quali gli utenti richiederanno l'accesso tramite Azure. Se l'utente non esiste all'interno del gruppo a cui sono stati assegnati, Vidyard creerà automaticamente un account utente e assegnerà loro un ruolo in tempo reale.

6. Selezionare il gruppo dell'organizzazione, visibile in **Groups Available to Assign** (Gruppi disponibili per l'assegnazione).

    ![Configurazione Vidyard](./media/vidyard-tutorial/configure5.png)

7. È possibile visualizzare i gruppi assegnati in **Groups Currently Assigned** (Gruppi attualmente assegnati). Selezionare un ruolo per il gruppo in base alla propria organizzazione e fare clic su **Confirm** (Conferma).

    ![Configurazione Vidyard](./media/vidyard-tutorial/configure6.png)

    > [!NOTE]
    > Per altre informazioni, vedere [questo documento](https://knowledge.vidyard.com/saml-single-sign-on-authentication/saml-based-single-sign-on-sso-in-vidyard).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare brittasimon@yourcompanydomain.extension. Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Vidyard.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Vidyard**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Vidyard**.

    ![Collegamento di Vidyard nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-vidyard-test-user"></a>Creare l'utente di test di Vidyard

In questa sezione viene creato un utente di nome Britta Simon in Vidyard. Vidyard supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Vidyard, ne viene creato uno nuovo dopo l'autenticazione.

>[!Note]
>Se è necessario creare un utente manualmente, contattare il [team di supporto di Vidyard](mailto:support@vidyard.com).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Vidyard nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Vidyard per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

