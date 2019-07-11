---
title: 'Esercitazione: Integrazione di Azure Active Directory con Cisco Webex Meetings| Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Cisco Webex Meetings.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 498650ce8a22f4804773bb66db3d640cd63bab8c
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655827"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex-meetings"></a>Esercitazione: Integrazione di Azure Active Directory con Cisco Webex Meetings

Questa esercitazione descrive come integrare Cisco Webex Meetings con Azure Active Directory (Azure AD).
L'integrazione di Cisco Webex Meetings con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Cisco Webex Meetings.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Cisco Webex Meetings con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Cisco Webex Meetings, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Cisco Webex Meetings abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Cisco Webex Meetings supporta l'accesso SSO avviato da **SP**

* Cisco Webex Meetings supporta il provisioning utenti **Just-In-Time**

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>Aggiunta di Cisco Webex Meetings dalla raccolta

Per configurare l'integrazione di Cisco Webex Meetings in Azure AD, è necessario aggiungere Cisco Webex Meetings dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Cisco Webex Meetings dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Cisco Webex Meetings**, selezionare **Cisco Webex Meetings** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Cisco Webex Meetings nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Cisco Webex Meetings mediante un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Cisco Webex Meetings.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Cisco Webex Meetings, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Cisco Webex Meetings](#configure-cisco-webex-meetings-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Cisco Webex Meetings](#create-cisco-webex-meetings-test-user)** : per avere una controparte di Britta Simon in Cisco Webex Meetings collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Cisco Webex Meetings, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Cisco Webex Meetings** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** del portale di Azure fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** caricare il **file di metadati del provider di servizi** e configurare l'applicazione seguendo questa procedura:

    >[!Note]
    >Si otterrà il file di metadati del provider di servizi, la procedura è descritta più avanti nella sezione dell'esercitazione **Configurare Single Sign-On in Cisco Webex Meetings**. 

    a. Fare clic su **Carica il file di metadati**.

    ![Caricare file di metadati](common/upload-metadata.png)

    b. Fare clic su **logo cartella** per selezionare il file di metadati e fare quindi clic su **Upload**.

    ![Scegliere file di metadati](common/browse-upload-metadata.png)

    c. Al termine del caricamento del file di metadati del provider di servizi, i valori dell'**Identificatore** e dell'**URL di risposta** vengono popolati automaticamente nella sezione **Configurazione SAML di base**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Cisco Webex Meetings](common/sp-identifier-reply.png)

    Nella casella di testo **URL di accesso**, incollare il valore **URL di risposta** che viene compilato automaticamente dal caricamento dei file di metadati SP.

5. L'applicazione Cisco Webex Meetings prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti. Fare clic su **Modifica** per aggiungere gli attributi.

    ![image](common/edit-attribute.png)

6. Eliminare gli attributi predefiniti dalla sezione **Attestazioni utente** e l'applicazione Cisco Webex Meetings prevede che alcuni altri attributi vengano ritrasferiti nella risposta SAML. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** eseguire la procedura seguente per aggiungere l'attributo del token SAML come illustrato nella tabella seguente:
    
    | Nome | Attributo di origine|
    | ---------------|  --------- |
    |   firstname    | user.givenname |
    |   lastname    | user.surname |
    |   email       | user.mail |
    |   uid    | user.mail |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](./media/cisco-webex-tutorial/tutorial-cisco-webex-addnewclaim.png)

    ![image](common/new-attribute-details.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    f. Fare clic su **Ok**

    g. Fare clic su **Save**.

7. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

8. Nella sezione **Configura Cisco Webex Meetings** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-cisco-webex-meetings-single-sign-on"></a>Configurare l'accesso Single Sign-On di Cisco Webex Meetings

1. Passare a [Cisco Cloud Collaboration Management](https://www.webex.com/go/connectadmin) con le credenziali di amministratore.

2. Passare a **Impostazioni di sicurezza** e passare alla **Federated Web SSO Configuration** (Configurazione SSO Web federato).
 
    ![Configure Single Sign-On](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

3. In **Federated Web SSO Configuration** (Configurazione SSO Web federato) seguire questa procedura:

    ![Configure Single Sign-On](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

    a. Nella casella di testo Federation Protocol (protocollo di federazione), digitare il nome del protocollo.

    b. Fare clic sul link **Import SAML Metadata** (Importa metadati SAML) per caricare il file di metadati scaricato dal portale di Azure.

    c. Fare clic su **Esporta** per scaricare il file di metadati del provider di servizi e caricarlo nella sezione **Configurazione SAML di base** del portale di Azure.

    d. Nella casella di testo **AuthContextClassRef**, digitare `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified` e se si desidera abilitare l'autenticazione a più fattori con Azure AD digitare i due valori, ad esempio `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`

    e. Selezionare **Auto Account Creation** (Creazione automatica dell'account).

    >[!NOTE]
    >Per l'abilitazione del provisioning utenti **just-in-time** è necessario controllare la **Auto Account Creation** (Creazione automatica dell'account). Inoltre, gli attributi token SAML devono essere passati nella risposta SAML.

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
  
    b. Nel campo **Nome utente** digitare **brittasimon\@dominioaziendale.estensione**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Cisco Webex Meetings.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Cisco Webex Meetings**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Cisco Webex Meetings**.

    ![Collegamento di Cisco Webex Meetings nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-cisco-webex-meetings-test-user"></a>Creare un utente di test per Cisco Webex Meetings

Questa sezione descrive come creare un utente denominato Britta Simon in Cisco Webex Meetings. Cisco Webex Meetings supporta il provisioning **just-in-time**, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se un utente non esiste in Cisco Webex Meetings, ne viene creato uno nuovo quando si tenta di accedere a Cisco Webex Meetings.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Cisco Webex Meetings nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Cisco Webex Meetings per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

