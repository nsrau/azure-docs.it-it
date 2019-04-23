---
title: 'Esercitazione: integrazione di Azure Active Directory con iPass SmartConnect | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e iPass SmartConnect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dee6d039-f9bb-49a2-a408-5ed40ef17d9f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: bf8c7e76aa6dd69cc505be237e47d8e10e7e8db5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59264863"
---
# <a name="tutorial-azure-active-directory-integration-with-ipass-smartconnect"></a>Esercitazione: integrazione di Azure Active Directory con iPass SmartConnect

Questa esercitazione descrive come integrare iPass SmartConnect con Azure Active Directory (Azure AD).
L'integrazione di iPass SmartConnect con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a iPass SmartConnect.
* È possibile abilitare gli utenti per l'accesso automatico a iPass SmartConnect (Single Sign-On) con i propri account Azure AD.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con iPass SmartConnect, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di iPass SmartConnect abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* iPass SmartConnect supporta l'accesso SSO avviato da **SP e IDP**
* iPass SmartConnect supporta il provisioning utenti **JIT**

## <a name="adding-ipass-smartconnect-from-the-gallery"></a>Aggiunta di iPass SmartConnect dalla raccolta

Per configurare l'integrazione di iPass SmartConnect in Azure AD, è necessario aggiungere iPass SmartConnect dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere iPass SmartConnect dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **iPass SmartConnect**, selezionare **iPass SmartConnect** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![iPass SmartConnect nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con iPass SmartConnect in base a un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in iPass SmartConnect.

Per configurare e testare l'accesso Single Sign-On di Azure AD con iPass SmartConnect, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di iPass SmartConnect](#configure-ipass-smartconnect-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di iPass SmartConnect](#create-ipass-smartconnect-test-user)**: per avere una controparte di Britta Simon in iPass SmartConnect collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con iPass SmartConnect, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **iPass SmartConnect** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** l'utente non deve eseguire alcuna operazione perché l'app è già preintegrata in Azure.

    ![Informazioni su URL e dominio per Single Sign-On di iPass SmartConnect](common/preintegrated.png)

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per Single Sign-On di iPass SmartConnect](common/metadata-upload-additional-signon.png)

    Nella casella di testo **URL di accesso** digitare un URL: `https://om-activation.ipass.com/ClientActivation/ssolanding.go`

6. L'applicazione iPass SmartConnect prevede che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![image](common/edit-attribute.png)

7. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** modificare le attestazioni usando l'**icona Modifica** o aggiungere le attestazioni usando l'opzione **Aggiungi nuova attestazione** per configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura:

    | Nome |  Attributo di origine|
    | ---------------| ----------|
    | firstName | user.givenname |
    | lastName | user.surname |
    | email | user.userprincipalname |
    | username | user.userprincipalname |
    | | |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    f. Fare clic su **Ok**

    g. Fare clic su **Save**.

8. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

9. Nella sezione **Configura iPass SmartConnect** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-ipass-smartconnect-single-sign-on"></a>Configurare l'accesso Single Sign-On di iPass SmartConnect

Per configurare l'accesso Single Sign-On sul lato **iPass SmartConnect**, è necessario inviare il file **XML dei metadati della federazione** scaricato e gli URL appropriati copiati dal portale di Azure al [team di supporto di iPass SmartConnect](mailto:help@ipass.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a iPass SmartConnect.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **iPass SmartConnect**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **iPass SmartConnect**.

    ![Collegamento iPass SmartConnect nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-ipass-smartconnect-test-user"></a>Creare l'utente di test di iPass SmartConnect

In questa sezione viene creato un utente chiamato Britta Simon in iPass SmartConnect. Rivolgersi al  [team di supporto di iPass SmartConnect](mailto:help@ipass.com)  per aggiungere gli utenti o il dominio necessario per essere inclusi nella piattaforma iPass SmartConnect. Se il dominio viene aggiunto dal team, verrà automaticamente effettuato il provisioning degli utenti per la piattaforma iPass SmartConnect. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

**Per testare l'applicazione nel flusso avviato da SP, seguire questa procedura:**

a. Scaricare il client iPass SmartConnect per Windows [qui](https://om-activation.ipass.com/ClientActivation/ssolanding.go).

![Collegamento iPass SmartConnect nell'elenco delle applicazioni](./media/ipasssmartconnect-tutorial/testing3.png)

b. Installare il client e avviarlo.

c. Fare clic su **Get Started** (Inizia).

![Collegamento iPass SmartConnect nell'elenco delle applicazioni](./media/ipasssmartconnect-tutorial/testing1.png) 

d. Immettere il nome utente di Azure con il dominio. Fare clic su **Continue** (Continua). Si verrà reindirizzati alla pagina di accesso di Azure

![Collegamento iPass SmartConnect nell'elenco delle applicazioni](./media/ipasssmartconnect-tutorial/testing2.png) 

e. Al termine dell'autenticazione, verrà avviata l'attivazione del client. Il client verrà attivato.

**Per testare l'applicazione nel flusso avviato dal provider di identità, seguire questa procedura:**

a. Accedere a [https://myapps.microsoft.com](https://myapps.microsoft.com).

b. Fare clic sull'app iPass SmartConnect.

c. Viene aperta la pagina SSA. Fare clic su **Download App for Windows** (Scarica app per Windows) per installare il client iPass SmartConnect.

![Collegamento iPass SmartConnect nell'elenco delle applicazioni](./media/ipasssmartconnect-tutorial/testing4.png)

d. Dopo l'installazione, il client, al primo avvio, avvierà automaticamente l'attivazione dopo l'accettazione dei termini e delle condizioni.

e. Se l'attivazione non viene avviata, fare clic sul pulsante Activate (Attiva) nella pagina SSA per avviare l'attivazione.

f. Il client verrà attivato.

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)