---
title: 'Esercitazione: Integrazione di Azure Active Directory con Trello | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Trello.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: cd5ae365-9ed6-43a6-920b-f7814b993949
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 453827b42d12333fd4f27761e7f73484fb749532
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2019
ms.locfileid: "59578425"
---
# <a name="tutorial-azure-active-directory-integration-with-trello"></a>Esercitazione: Integrazione di Azure Active Directory con Trello

Questa esercitazione descrive come integrare Trello con Azure Active Directory (Azure AD).
L'integrazione di Trello con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Trello.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Trello con gli account Azure AD personali.
* È possibile gestire gli account da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Trello, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere una [versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).
* Una sottoscrizione di Trello abilitata per l'accesso Single Sign-On.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Trello supporta l'accesso SSO avviato da SP e IDP

* Trello supporta il provisioning utenti Just-In-Time

## <a name="add-trello-from-the-gallery"></a>Aggiungere Trello dalla raccolta

Per configurare l'integrazione di Trello in Azure AD, è necessario prima aggiungere Trello dalla raccolta al proprio elenco di app SaaS gestite.

Per aggiungere Trello dalla raccolta, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com) fare clic sull'icona **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Selezionare **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Trello** e quindi selezionare **Trello** nel riquadro dei risultati.

5. Selezionare quindi il pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Trello nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Trello usando un utente di test di nome **Britta Simon**.

Per il funzionamento dell'accesso Single Sign-On, deve essere stabilito un collegamento tra un utente di Azure AD e l'utente correlato in Trello.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Trello, è necessario completare i blocchi predefiniti seguenti:

1. [Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on) per consentire agli utenti di usare questa funzionalità.
2. [Configurare l'accesso Single Sign-On di Trello](#configure-trello-single-sign-on) per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. [Creare un utente di test di Azure AD](#create-an-azure-ad-test-user) per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. [Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user) per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. [Creare un utente di test di Trello](#create-a-trello-test-user) per avere una controparte di Britta Simon in Trello collegata alla rappresentazione dell'utente in Azure AD.
6. [Testare l'accesso Single Sign-On](#test-single-sign-on) per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

> [!NOTE]
> È necessario ottenere il campo dati dinamico **\<enterprise\>** da Trello. Se non si conosce il valore del campo dati dinamico, contattare il [team di supporto di Trello](mailto:support@trello.com) per ottenere il campo dati dinamico relativo all'organizzazione.

Per configurare l'accesso Single Sign-On di Azure AD con Trello, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Trello** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML** per abilitare l'accesso Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** selezionare l'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione nella modalità avviata da IDP:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Trello](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** immettere un URL usando il modello seguente: `https://trello.com/auth/saml/metadata`

    b. Nella casella di testo **URL di risposta** immettere un URL usando il modello seguente: `https://trello.com/auth/saml/consume/<enterprise>`

5. Selezionare **Impostare URL aggiuntivi** e quindi seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da SP:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Trello](common/metadata-upload-additional-signon.png)

    Nella casella di testo **URL di accesso** immettere un URL usando il modello seguente: `https://trello.com/auth/saml/login/<enterprise>`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Trello](mailto:support@trello.com). È anche possibile fare riferimento ai modelli nella sezione **Configurazione SAML di base** del portale di Azure.

6. L'applicazione Trello prevede un formato specifico per le asserzioni SAML. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** selezionare il pulsante **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![Finestra di dialogo Attributi utente](common/edit-attribute.png)

7. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** configurare l'attributo del token SAML come illustrato nell'immagine precedente. Seguire quindi questa procedura:

    | NOME |  Source Attribute|
    | --- | --- |
    | User.Email | user.mail |
    | User.FirstName | user.givenname |
    | User.LastName | user.surname |

    a. Selezionare **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![Finestra di dialogo Attestazioni utente](common/new-save-attribute.png)

    ![Gestisci attestazioni utente](common/new-attribute-details.png)

    b. Nella casella **Nome** immettere il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per **Origine**, selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** immettere il valore dell'attributo indicato per la riga.

    f. Selezionare **OK**.

    g. Selezionare **Salva**.

8. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, selezionare **Scarica** per scaricare il file di **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti. Salvare quindi il file nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

9. Nella sezione **Configura Trello** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-trello-single-sign-on"></a>Configurare l'accesso Single Sign-On di Trello

Per configurare l'accesso Single Sign-On sul lato Trello, inviare prima il file di **Certificato (Base64)** scaricato e gli URL copiati dal portale di Azure al [team di supporto di Trello](mailto:support@trello.com). Questi dati garantiscono che la connessione Single Sign-On SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. Nella finestra di dialogo **Utente** seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** immettere "brittasimon@yourcompanydomain.extension". In questo caso, ad esempio, è possibile immettere "BrittaSimon@contoso.com".

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Selezionare **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a Trello.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Trello**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Trello**.

    ![Collegamento di Trello nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Selezionare il pulsante **Aggiungi utente**. Nella finestra di dialogo **Aggiungi assegnazione** selezionare quindi **Utenti e gruppi**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Users and groups** (Utenti e gruppi) selezionare **Britta Simon** nell'elenco di utenti. Fare quindi clic sul pulsante **Seleziona** nella parte inferiore della schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco. Fare quindi clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.

### <a name="create-a-trello-test-user"></a>Creare un utente di test di Trello

In questa sezione viene creato un utente di nome Britta Simon in Trello. Trello supporta il provisioning utenti Just-In-Time, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Trello, ne viene creato uno nuovo dopo l'autenticazione.

> [!NOTE]
> Per creare un utente manualmente, contattare il  [team di supporto di Trello](mailto:support@trello.com).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD tramite il portale App personali.

Quando si seleziona il riquadro di Trello nel portale App personali, si dovrebbe accedere automaticamente a Trello. Per altre informazioni sul portale App personali, vedere [Accedere e usare le app nel portale App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

