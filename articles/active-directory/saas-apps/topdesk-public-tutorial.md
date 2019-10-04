---
title: 'Esercitazione: Integrazione di Azure Active Directory con TOPdesk - Public | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e TOPdesk - Public.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0873299f-ce70-457b-addc-e57c5801275f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: jeedes
ms.openlocfilehash: a2a0ffd670a03aeaaa262b83127a385be9efc978
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67088487"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Esercitazione: Integrazione di Azure Active Directory con TOPdesk - Public

Questa esercitazione descrive come integrare TOPdesk - Public con Azure Active Directory (Azure AD).
L'integrazione di TOPdesk - Public con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a TOPdesk - Public.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a TOPdesk - Public con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con TOPdesk - Public, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di TOPdesk - Public abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* TOPdesk - Public supporta l'accesso SSO avviato dal **provider di servizi**

## <a name="adding-topdesk---public-from-the-gallery"></a>Aggiunta di TOPdesk - Public dalla raccolta

Per configurare l'integrazione di TOPdesk - Public in Azure AD, è necessario aggiungere TOPdesk - Public dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere TOPdesk - Public dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **TOPdesk - Public**, selezionare **TOPdesk - Public** dal pannello dei risultati quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![TOPdesk - Public nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con TOPdesk - Public usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in TOPdesk - Public.

Per configurare e testare l'accesso Single Sign-On di Azure AD con TOPdesk - Public, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di TOPdesk - Public](#configure-topdesk---public-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test per TOPdesk - Public](#create-topdesk---public-test-user)** : per avere una controparte di Britta Simon in TOPdesk - Public collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con TOPdesk - Public, completare i passaggi seguenti:

1. Nella pagina di integrazione dell'applicazione [TOPdesk - Public](https://portal.azure.com/) del **portale di Azure** fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4.  Nella sezione **Configurazione SAML di base**, se si dispone di un **file di metadati di un provider di servizi**, seguire questa procedura:

    >[!NOTE]
    >Si otterrà il **file di metadati del provider di servizi** in base alla procedura descritta più avanti nella sezione **Configurare l'accesso Single Sign-On di TOPdesk - Public** dell'esercitazione.

    a. Fare clic su **Carica il file di metadati**.
    
    ![Caricare file di metadati](common/upload-metadata.png)

    b. Fare clic su **logo cartella** per selezionare il file di metadati e fare quindi clic su **Upload**.

    ![Scegliere file di metadati](common/browse-upload-metadata.png)

    c. Dopo il caricamento del file di metadati, il valore di **Identificatore** e **URL di risposta** viene inserito automaticamente nella sezione Configurazione SAML di base.

    ![Informazioni sull'accesso Single Sign-On per URL e dominio di TOPdesk - Public](common/sp-identifier-reply.png)

    d. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<companyname>.topdesk.net`

    e. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<companyname>.topdesk.net/tas/public/login/verify`

    > [!NOTE] 
    > Se i valori di **Identificatore** e **URL di risposta** non vengono immessi automaticamente, è necessario specificarli manualmente. Per Identificatore, seguire il modello descritto sopra e ottenere il valore di URL di risposta come indicato più avanti nella sezione **Configurare l'accesso Single Sign-On di TOPdesk - Public** dell'esercitazione. Poiché il valore di **URL accesso** non è reale, è necessario aggiornarlo con l'effettivo URL di accesso. Per ottenere questo valore, contattare il [team di supporto clienti di TOPdesk - Public](https://help.topdesk.com/saas/enterprise/user/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura TOPdesk - Public** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-topdesk---public-single-sign-on"></a>Configurare l'accesso Single Sign-On di TOPdesk - Public

1. Accedere al sito aziendale di **TOPdesk - Public** come un amministratore.

2. Nel menu **TOPdesk** fare clic su **Settings** (Impostazioni).
   
    ![Impostazioni](./media/topdesk-public-tutorial/ic790598.png "Impostazioni")

3. Fare clic su **Login Settings**.
   
    ![Login Settings](./media/topdesk-public-tutorial/ic790599.png "Login Settings")

4. Espandere il menu **Login Settings** (Impostazioni accesso) e quindi fare clic su **General** (Generale).
   
    ![General](./media/topdesk-public-tutorial/ic790600.png "General")

5. Nell'area **Public** (Publico) della sezione di configurazione **SAML login** (Accesso SAML) seguire questa procedura:
   
    ![Technical Settings](./media/topdesk-public-tutorial/ic790601.png "Technical Settings")
   
    a. Fare clic su **Download** per scaricare il file di metadati pubblici e quindi salvarlo in locale nel computer.
   
    b. Aprire il file dei metadati scaricato e quindi individuare il nodo **AssertionConsumerService** .

    ![AssertionConsumerService](./media/topdesk-public-tutorial/ic790619.png "AssertionConsumerService")
   
    c. Copiare il valore **AssertionConsumerService** e incollarlo nella casella di testo **URL di risposta** nella sezione **Configurazione SAML di base**.      
   
6. Per creare un file del certificato, seguire questa procedura:
    
    ![Certificate](./media/topdesk-public-tutorial/ic790606.png "Certificate")
    
    a. Aprire il file di metadati scaricato dal portale di Azure.
    
    b. Espandere il nodo **RoleDescriptor** con **xsi:type** corrispondente a **fed:ApplicationServiceType**.
    
    c. Copiare il valore del nodo **X509Certificate** .
    
    d. Salvare il valore copiato di **X509Certificate** in un file locale nel computer.

7. Nell'area **Public** (Pubblica) fare clic su **Add** (Aggiungi).
    
    ![Login SAML](./media/topdesk-public-tutorial/ic790625.png "Login SAML")

8. Nella pagina **SAML configuration assistant** seguire questa procedura:
    
    ![SAML Configuration Assistant](./media/topdesk-public-tutorial/ic790608.png "SAML Configuration Assistant")
    
    a. Per caricare il file di metadati scaricato dal portale di Azure, in **Metadati della federazione** fare clic su **Sfoglia**.

    b. Per caricare il file del certificato, in **Certificate (RSA)** (Certificato RSA) fare clic su **Browse** (Sfoglia).

    c. Per caricare il file del logo ottenuto dal team di supporto del team di TOPdesk, in **Logo icon** (Icona logo) fare clic su **Browse** (Sfoglia).

    d. Nella casella di testo **User name attribute** (Attributo nome utente) digitare `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. Nella casella di testo **Display name** digitare un nome per la configurazione.

    f. Fare clic su **Save**.

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

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a TOPdesk - Public.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **TOPdesk - Public**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **TOPdesk - Public**.

    ![Collegamento TOPdesk - Public nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-topdesk---public-test-user"></a>Creare un utente di test di TOPdesk - Public

Per consentire agli utenti di Azure AD di accedere a TOPdesk - Public, è necessario effettuarne il provisioning in TOPdesk - Public. Nel caso di TOPdesk - Public, il provisioning è un'attività manuale.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning utente, eseguire la procedura seguente:

1. Accedere al sito aziendale di **TOPdesk - Public** come amministratore.

2. Nel menu presente sulla parte superiore fare clic su **TOPdesk \> New (Nuovo) \> Support Files (File di supporto) \> Person (Persona)** .
   
    ![Person](./media/topdesk-public-tutorial/ic790628.png "Person")

3. Nella finestra di dialogo New Person seguire questa procedura:
   
    ![New Person](./media/topdesk-public-tutorial/ic790629.png "New Person")
   
    a. Fare clic sulla scheda General.

    b. Digitare il cognome dell'utente, ad esempio Simon, nella casella di testo **Surname** (Cognome)
 
    c. Selezionare un **Site** per l'account.
 
    d. Fare clic su **Save**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione account utente fornita da TOPdesk - Public per eseguire il provisioning degli account utente Azure AD.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro TOPdesk - Public nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione TOPdesk - Public per cui è stato configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
