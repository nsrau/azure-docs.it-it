---
title: 'Esercitazione: Integrazione di Azure Active Directory con MOVEit Transfer - Integrazione di Azure AD | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e MOVEit Transfer - Azure AD integration.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: 0f6b9c85fa3462486a32cf55b10cb06bb84b0072
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57853053"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Esercitazione: Integrazione di Azure Active Directory con MOVEit Transfer - Integrazione di Azure AD

Questa esercitazione descrive come integrare MOVEit Transfer - Azure AD integration con Azure Active Directory (Azure AD).
L'integrazione di MOVEit Transfer - Azure AD integration con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a MOVEit Transfer - Azure AD integration.
* È possibile abilitare gli utenti per l'accesso automatico a MOVEit Transfer - Azure AD integration (Single Sign-On) con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con MOVEit Transfer - Azure AD integration, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di MOVEit Transfer - Azure AD integration abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* MOVEit Transfer - Azure AD integration supporta l'accesso SSO avviato da **SP**

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Aggiunta di MOVEit Transfer - Azure AD integration dalla raccolta

Per configurare l'integrazione di MOVEit Transfer - Azure AD integration in Azure AD, è necessario aggiungere MOVEit Transfer - Azure AD integration dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere MOVEit Transfer - Azure AD integration dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **MOVEit Transfer - Azure AD integration**, selezionare **MOVEit Transfer - Azure AD integration** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![MOVEit Transfer - Azure AD integration nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con MOVEit Transfer - Azure AD integration usando un utente di test di nome **Britta Simon**.
Per il funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in MOVEit Transfer - Azure AD integration.

Per configurare e testare l'accesso Single Sign-On di Azure AD con MOVEit Transfer - Azure AD integration, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per MOVEit Transfer - Azure AD integration](#configure-moveit-transfer---azure-ad-integration-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di MOVEit Transfer - Azure AD integration](#create-moveit-transfer---azure-ad-integration-test-user)**: per avere una controparte di Britta Simon in MOVEit Transfer - Azure AD integration collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con MOVEit Transfer - Azure AD integration, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione [MOVEit Transfer - Azure AD integration](https://portal.azure.com/) del **portale di Azure** selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base**, se si dispone di un **file di metadati di un provider di servizi**, seguire questa procedura:

    a. Fare clic su **Carica il file di metadati**.

    ![Caricare file di metadati](common/upload-metadata.png)

    b. Fare clic su **logo cartella** per selezionare il file di metadati e fare quindi clic su **Upload**.

    ![Scegliere file di metadati](common/browse-upload-metadata.png)

    c. Dopo il caricamento del file di metadati, il valore di **Identificatore** e **URL di risposta** viene inserito automaticamente nella sezione **Configurazione SAML di base**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di MOVEit Transfer - Azure AD integration](common/sp-identifier-reply.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://contoso.com`

    > [!NOTE]
    > Poiché il valore **URL accesso** non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere tale valore, contattare il [team di clienti di MOVEit Transfer - Azure AD integration](https://community.ipswitch.com/s/support). È possibile scaricare il **file di metadati del provider di servizi** dall'**URL dei metadati del provider di servizi**, come descritto più avanti nella sezione **Configurare l'accesso Single Sign-On per MOVEit Transfer - Azure AD integration** dell'esercitazione. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura MOVEit Transfer - Azure AD integration** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-moveit-transfer---azure-ad-integration-single-sign-on"></a>Configurare l'accesso Single Sign-On per MOVEit Transfer - Azure AD integration

1. Accedere al tenant di MOVEit Transfer come amministratore.

2. Nella barra di spostamento a sinistra fare clic su **Settings**(Impostazioni).

    ![Sezione delle impostazioni sul lato dell'app](./media/moveittransfer-tutorial/tutorial_moveittransfer_000.png)

3. Fare clic sul collegamento **Single Signon** (Single Sign-On) in **Security Policies -> User Auth** (Criteri di sicurezza -> Autenticazione utente).

    ![Criteri di sicurezza sul lato dell'app](./media/moveittransfer-tutorial/tutorial_moveittransfer_001.png)

4. Fare clic sul collegamento con l'URL dei metadati per scaricare il documento di metadati.

    ![URL dei metadati del provider di servizi](./media/moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
   * Verificare che **entityID** corrisponda al valore di **Identificatore** nella sezione **Configurazione SAML di base**.
   * Verificare che l'URL del percorso di **AssertionConsumerService** corrisponda all'**URL DI RISPOSTA** nella sezione **Configurazione SAML di base**.
    
     ![Configurazione accesso Single Sign-On sul lato app](./media/moveittransfer-tutorial/tutorial_moveittransfer_007.png)

5. Fare clic sul pulsante **Add Identity Provider** (Aggiungi provider di identità) per aggiungere un nuovo provider di identità federato.

    ![Aggiungi provider di identità](./media/moveittransfer-tutorial/tutorial_moveittransfer_003.png)

6. Fare clic su **Browse** (Sfoglia) per selezionare il file di metadati scaricato dal portale di Azure e quindi fare clic su **Add Identity Provider** (Aggiungi provider di identità) per caricare il file scaricato.

    ![Provider di identità SAML](./media/moveittransfer-tutorial/tutorial_moveittransfer_004.png)

7. Selezionare "**Yes**" (Sì) per **Enabled** (Abilitato) nella pagina **Edit Federated Identity Provider Settings** (Modifica impostazioni provider di identità federato) e fare clic su **Save** (Salva).

    ![Impostazioni provider di identità federato](./media/moveittransfer-tutorial/tutorial_moveittransfer_005.png)

8. Nella pagina **Edit Federated Identity Provider User Settings** (Modifica impostazioni utente del provider di identità federato) eseguire queste operazioni:
    
    ![Modifica delle impostazioni del provider di identità federato](./media/moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. Selezionare **SAML NameID** (ID nome SAML) per **Login name** (Nome di accesso).
    
    b. Selezionare **Other** (Altro) per **Full name** (Nome completo) e nella casella di testo **Attribute name** (Nome attributo) immettere il valore: `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. Selezionare **Other** (Altro) per **Email** (Posta elettronica) e nella casella di testo **Attribute name** (Nome attributo) immettere il valore: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    d. Selezionare **Yes** (Sì) per **Auto-create account on signon** (Crea automaticamente account all'accesso).
    
    e. Fare clic sul pulsante **Salva** .

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon\@dominioaziendale.estensione**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a MOVEit Transfer - Azure AD integration.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **MOVEit Transfer - Azure AD integration**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **MOVEit Transfer - Azure AD integration**.

    ![Collegamento di MOVEit Transfer - Azure AD integration nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>Creare un utente di test di MOVEit Transfer - Azure AD integration

Questa sezione descrive come creare un utente di test di nome Britta Simon in MOVEit Transfer - Azure AD integration. MOVEit Transfer - Azure AD integration supporta il provisioning JIT, che è stato abilitato. Non è necessario alcun intervento dell'utente in questa sezione. Durante il tentativo di accesso a MOVEit Transfer - Azure AD integration viene creato un nuovo utente, se questo non esiste già.

>[!NOTE]
>Per creare un utente manualmente, contattare il [team di supporto clienti di MOVEit Transfer - Azure AD integration](https://community.ipswitch.com/s/support).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro MOVEit Transfer - Azure AD integration nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione MOVEit Transfer - Azure AD integration per cui è stato configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

