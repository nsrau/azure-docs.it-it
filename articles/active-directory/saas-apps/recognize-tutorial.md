---
title: 'Esercitazione: Integrazione di Azure Active Directory con Recognize | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Recognize.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: c0a513ad8fe772839813615d065616f2c911a031
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68943348"
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Esercitazione: Integrazione di Azure Active Directory con Recognize

Questa esercitazione descrive come integrare Recognize con Azure Active Directory (Azure AD).
L'integrazione di Recognize con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Recognize.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Recognize con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Recognize, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Recognize abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Recognize supporta l'accesso SSO avviato da **SP**

## <a name="adding-recognize-from-the-gallery"></a>Aggiunta di Recognize dalla raccolta

Per configurare l'integrazione di Recognize in Azure AD, è necessario aggiungere Recognize dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Recognize dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Recognize**, selezionare **Recognize** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Recognize nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Recognize in base a un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Recognize.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Recognize, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Recognize](#configure-recognize-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Recognize](#create-recognize-test-user)** : per avere una controparte di Britta Simon in Recognize collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Recognize, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione [Recognize](https://portal.azure.com/) del **portale di Azure** selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base**, se si dispone di un **file di metadati di un provider di servizi**, seguire questa procedura:

    >[!NOTE]
    >Si otterrà il **file di metadati del provider di servizi** dalla sezione dell'esercitazione **Configurare l'accesso Single Sign-On di Recognize**.

    a. Fare clic su **Carica il file di metadati**.

    ![Caricare file di metadati](common/upload-metadata.png)

    b. Fare clic su **logo cartella** per selezionare il file di metadati e fare quindi clic su **Upload**.

    ![Scegliere file di metadati](common/browse-upload-metadata.png)

    c. Dopo il caricamento del file di metadati, il valore di **Identificatore** viene inserito automaticamente nella sezione Configurazione SAML di base.

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Recognize](common/sp-identifier.png)

     Nella casella di testo **URL di accesso** digitare un URL usando il modello seguente: `https://recognizeapp.com/<your-domain>/saml/sso`

    > [!Note]
    > Se il valore **Identificatore** non viene popolato automaticamente, si otterrà il valore Identificatore aprendo l'URL dei metadati del provider di servizi dalla sezione Impostazioni SSO descritta più avanti nella sezione dell'esercitazione **Configurare l'accesso Single Sign-On di Recognize**. Poiché il valore di URL accesso non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere il valore contattare il [team di supporto client di Recognize](mailto:support@recognizeapp.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Recognize** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-recognize-single-sign-on"></a>Configurare l'accesso Single Sign-On di Recognize

1. In un'altra finestra del browser Web accedere al tenant Recognize come amministratore.

2. In alto a destra fare clic su **Menu**. Passare a **Company Admin** (Amministrazione società).
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/recognize-tutorial/tutorial_recognize_000.png)

3. Nella barra di spostamento a sinistra fare clic su **Settings**(Impostazioni).
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/recognize-tutorial/tutorial_recognize_001.png)

4. Nella sezione **SSO Settings** (Impostazioni SSO) seguire questa procedura.
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/recognize-tutorial/tutorial_recognize_002.png)
    
    a. In **Enable SSO** (Abilita SSO) selezionare **ON**.

    b. Nella casella di testo **IdP Entity ID** (ID entità provider di identità) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.
    
    c. Nella casella di testo **Sso target url** (URL di destinazione SSO) incollare il valore di **URL di accesso** copiato dal portale di Azure.
    
    d. Nella casella di testo **Slo target url** (URL di destinazione SLO) incollare il valore di **URL di accesso** copiato dal portale di Azure. 
    
    e. Aprire il file **Certificate (Base64)** scaricato nel Blocco note, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **Certificato**.
    
    f. Fare clic sul pulsante **Save settings** (Salva impostazioni). 

5. Accanto alla sezione **SSO Settings** (Impostazioni Single Sign-On) copiare l'URL in **Service Provider Metadata url** (URL metadati del provider di servizio).
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/recognize-tutorial/tutorial_recognize_003.png)

6. In una pagina del browser fare clic sul collegamento **Metadata URL** (URL metadati) per scaricare il documento di metadati. Quindi copiare il valore EntityDescriptor (entityID) dal file e incollarlo nella casella di testo **Identificatore** nella sezione **Configurazione SAML di base** del portale di Azure.
    
    ![Configurazione accesso Single Sign-On sul lato app](./media/recognize-tutorial/tutorial_recognize_004.png)

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

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a Recognize.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Recognize**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Recognize**.

    ![Collegamento Recognize nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-recognize-test-user"></a>Creare l'utente di test di Recognize

Per consentire agli utenti di Azure AD di accedere a Recognize, è necessario eseguirne il provisioning in Recognize. Nel caso di Recognize, il provisioning è un'attività manuale.

Questa app non supporta il provisioning SCIM, ma dispone di una sincronizzazione utente alternativa che esegue il provisioning degli utenti. 

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di Recognize come amministratore.

2. In alto a destra fare clic su **Menu**. Passare a **Company Admin** (Amministrazione società).

3. Nella barra di spostamento a sinistra fare clic su **Settings**(Impostazioni).

4. Nella sezione **User Sync** (Sincronizzazione utente) seguire questa procedura.
   
    ![Nuovo utente](./media/recognize-tutorial/tutorial_recognize_005.png "Nuovo utente")
   
    a. In **Sync Enabled** (Sincronizzazione abilitata) selezionare **ON**.
   
    b. In **Choose sync provider** (Scegli provider di sincronizzazione) selezionare **Microsoft/Office 365**.
   
    c. Fare clic su **Run User Sync** (Esegui sincronizzazione utente).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Recognize nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Recognize per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

