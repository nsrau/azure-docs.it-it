---
title: 'Esercitazione: Integrazione di Azure Active Directory con Samanage | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Samanage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: f0db4fb0-7eec-48c2-9c7a-beab1ab49bc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 714e67bc7cf9e9c928eb4fd0764619041c9c1424
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092602"
---
# <a name="tutorial-azure-active-directory-integration-with-samanage"></a>Esercitazione: Integrazione di Azure Active Directory con Samanage

Questa esercitazione descrive come integrare Samanage con Azure Active Directory (Azure AD).
L'integrazione di Samanage con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Samanage.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Samanage con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Samanage, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Samanage abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Samanage supporta l'accesso SSO avviato da **SP**

## <a name="adding-samanage-from-the-gallery"></a>Aggiunta di Samanage dalla raccolta

Per configurare l'integrazione di Samanage in Azure AD, è necessario aggiungerla dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Samanage dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Samanage**, selezionare **Samanage** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Samanage nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Samanage usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Samanage.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Samanage, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Samanage](#configure-samanage-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Samanage](#create-samanage-test-user)** : per avere una controparte di Britta Simon in Samanage collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Samanage, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Samanage** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Samanage](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL usando il modello seguente: `https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di accesso effettivi. La procedura è descritta più avanti nell'esercitazione. Per altri dettagli, contattare il [team di supporto clienti di Samanage](https://www.samanage.com/support). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Samanage** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-samanage-single-sign-on"></a>Configurare l'accesso Single Sign-On per Samanage

1. In un'altra finestra del Web browser accedere al sito aziendale di Samanage come amministratore.

2. Fare clic su **Dashboard** e selezionare **Setup** (Configurazione) nel riquadro di spostamento a sinistra.
   
    ![Dashboard](./media/samanage-tutorial/tutorial_samanage_001.png "Dashboard")

3. Fare clic su **Single Sign-On**.
   
    ![Single Sign-On](./media/samanage-tutorial/tutorial_samanage_002.png "Single Sign-On")

4. Nella sezione **Login using SAML** (Accesso tramite SAML) seguire questa procedura:
   
    ![Login using SAML](./media/samanage-tutorial/tutorial_samanage_003.png "Login using SAML")
 
    a. Fare clic su **Enable Single Sign-On with SAML**(Abilita Single Sign-On con SAML).  
 
    b. Nella casella di testo **Identity Provider URL** (URL del provider di identità) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.    
 
    c. Verificare che il valore di **URL di accesso** corrisponda a quello di **URL di accesso** della sezione **Configurazione SAML di base** nel portale di Azure.
 
    d. Nella casella di testo **URL disconnessione** immettere il valore di **URL disconnessione** copiato dal portale di Azure.
 
    e. Nella casella di testo **SAML Issuer** (Autorità di certificazione SAML) digitare l'URI ID app impostato nel provider di identità.
 
    f. Aprire il certificato con codifica Base 64 scaricato dal portale di Azure nel Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **Paste your Identity Provider x.509 Certificate below** (Incollare il certificato X.509 del provider di identità di seguito).
 
    g. Fare clic su **Create users if they do not exist in Samanage**(Crea utenti se non presenti in Samanage).
 
    h. Fare clic su **Update**.

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

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Samanage.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Samanage**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Samanage**.

    ![Collegamento di Samanage nell'elenco Applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-samanage-test-user"></a>Creare l'utente di test di Samanage

Per consentire agli utenti di Azure AD di accedere a Samanage, è necessario effettuarne il provisioning in Samanage.  
Nel caso di Samanage, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di Samanage come amministratore.

2. Fare clic su **Dashboard** e selezionare **Setup** (Configurazione) nel riquadro di spostamento a sinistra.
   
    ![Installazione](./media/samanage-tutorial/tutorial_samanage_001.png "Installazione")

3. Fare clic sulla scheda **Users** .
   
    ![Utenti](./media/samanage-tutorial/tutorial_samanage_006.png "Utenti")

4. Fare clic su **Nuovo utente**.
   
    ![Nuovo utente](./media/samanage-tutorial/tutorial_samanage_007.png "Nuovo utente")

5. In **Name** (Nome) e **Email Address** (Indirizzo di posta elettronica) digitare nome e indirizzo di posta elettronica di un account Azure Active Directory di cui si vuole effettuare il provisioning e fare clic su **Create user** (Crea utente).
   
    ![Crea utente](./media/samanage-tutorial/tutorial_samanage_008.png "Crea utente")
   
   >[!NOTE]
   >Il titolare dell'account Azure Active Directory riceverà un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo. È possibile usare qualsiasi altro strumento o API di creazione di account utente offerti da Samanage per eseguire il provisioning degli account utente di Azure Active Directory.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Samanage nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Samanage per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

