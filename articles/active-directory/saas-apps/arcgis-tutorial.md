---
title: 'Esercitazione: Integrazione di Azure Active Directory con ArcGIS Online | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e ArcGIS Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74cf0c1e055570df7702465df79dcdfbd8ea9e9f
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232100"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Esercitazione: Integrazione di Azure Active Directory con ArcGIS Online

Questa esercitazione descrive come integrare ArcGIS Online con Azure Active Directory (Azure AD).
L'integrazione di ArcGIS Online con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere ad ArcGIS Online.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) ad ArcGIS Online con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con ArcGIS Online, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di ArcGIS Online abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* ArcGIS Online supporta accesso SSO avviato da **provider di servizi**

## <a name="adding-arcgis-online-from-the-gallery"></a>Aggiunta di ArcGIS Online dalla raccolta

Per configurare l'integrazione di ArcGIS Online in Azure AD, è necessario aggiungere ArcGIS Online dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere ArcGIS Online dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **ArcGIS Online** selezionare **ArcGIS Online** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![ArcGIS Online nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con ArcGIS Online usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in ArcGIS Online.

Per configurare e testare l'accesso Single Sign-On di Azure AD con ArcGIS Online, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di ArcGIS Online](#configure-arcgis-online-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di ArcGIS Online](#create-arcgis-online-test-user)** : per avere una controparte di Britta Simon in ArcGIS Online collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con ArcGIS Online, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **ArcGIS Online** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di ArcGIS Online](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<companyname>.maps.arcgis.com`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `<companyname>.maps.arcgis.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di ArcGIS Online](https://support.esri.com/en/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Per automatizzare la configurazione all'interno di **ArcGIS Online**, è necessario installare l'**estensione del browser per l'accesso sicuro alle app personali** facendo clic su **Install the extension** (Installa estensione).

    ![image](./media/arcgis-tutorial/install_extension.png)

7. Dopo aver aggiunto l'estensione al browser, fare clic su **setup ArcGIS Online** (Configura ArcGIS Online) per passare direttamente all'applicazione ArcGIS Online. Da qui, fornire le credenziali di amministratore per accedere ad ArcGIS Online. L'estensione del browser configurerà automaticamente l'applicazione per l'utente e automatizzerà i passaggi nella sezione **Configurare il Single Sign-On di ArcGIS Online**.

### <a name="configure-arcgis-online-single-sign-on"></a>Configurare il Single Sign-On di ArcGIS Online

1. Se si vuole configurare manualmente ArcGIS Online, aprire una nuova finestra del Web browser, accedere al sito aziendale di ArcGIS come amministratore e seguire questa procedura:

2. Fare clic su **EDIT SETTINGS** (Modifica impostazioni).

    ![Edit Settings](./media/arcgis-tutorial/ic784742.png "Edit Settings")

3. Fare clic su **Security**.

    ![Sicurezza](./media/arcgis-tutorial/ic784743.png "Security")

4. In **Enterprise Logins** (Accessi aziendali) fare clic su **SET IDENTITY PROVIDER** (Imposta provider di identità).

    ![Enterprise Logins](./media/arcgis-tutorial/ic784744.png "Enterprise Logins")

5. Nella pagina di configurazione **Set Identity Provider** seguire questa procedura:

    ![Set Identity Provider](./media/arcgis-tutorial/ic784745.png "Set Identity Provider")

    a. Nella casella di testo **Name** (Nome) digitare il nome della propria organizzazione.

    b. In **I metadati per il provider di identità dell'organizzazione verranno forniti mediante**, selezionare **Un File**.

    c. Per caricare il file di metadati scaricato, fare clic su **Seleziona file**.

    d. Fare clic su **SET IDENTITY PROVIDER** (Imposta provider di identità).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD 

Questa sezione descrive come creare un utente di test di nome Britta Simon nel portale di Azure.

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

    d. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad ArcGIS Online.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **ArcGIS Online**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni, digitare e selezionare **ArcGIS Online**.

    ![Collegamento di ArcGIS Online nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-arcgis-online-test-user"></a>Creare l'utente di test di ArcGIS Online

Per consentire agli utenti di Azure AD di accedere ad ArcGIS Online, è necessario effettuarne il provisioning in ArcGIS Online.  
Nel caso di ArcGIS Online, il provisioning è un'attività manuale.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere al tenant **ArcGIS** .

2. Fare clic su **INVITE MEMBERS** (Invita membri).
   
    ![Invite Members](./media/arcgis-tutorial/ic784747.png "Invita Membri")

3. Selezionare **Add members automatically without sending an email** (Aggiungi membri automaticamente senza inviare un'e-mail) e quindi fare clic su **NEXT** (Avanti).
   
    ![Add Members Automatically](./media/arcgis-tutorial/ic784748.png "Add Members Automatically")

4. Nella finestra di dialogo **Membri** seguire questa procedura:
   
     ![Add and review](./media/arcgis-tutorial/ic784749.png "Aggiungi e verifica")
    
     a. Nelle caselle di testo **Email** (Indirizzo di posta elettronica), **First name** (Nome) e **Last name** (Cognome) digitare rispettivamente l'indirizzo di posta elettronica, il nome e il cognome relativi a un account Azure AD valido di cui si vuole effettuare il provisioning.
  
     b. Fare clic su **ADD AND REVIEW** (Aggiungi e verifica).
5. Verificare i dati immessi e quindi fare clic su **ADD MEMBERS** (Aggiungi membri).
   
    ![Add member](./media/arcgis-tutorial/ic784750.png "Add member")
        
    > [!NOTE]
    > Il titolare dell'account Azure Active Directory riceverà un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro ArcGIS Online nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di ArcGIS Online per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

