---
title: 'Esercitazione: Integrazione di Azure Active Directory con Slack | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Slack.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.openlocfilehash: fd868e02987c61e6bdd992c45a95cb775d1778ca
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2019
ms.locfileid: "53969547"
---
# <a name="tutorial-azure-active-directory-integration-with-slack"></a>Esercitazione: Integrazione di Azure Active Directory con Slack

Questa esercitazione descrive come integrare Slack con Azure Active Directory (Azure AD).
L'integrazione di Slack con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Slack.
* È possibile consentire agli utenti l'accesso automatico a Slack (Single Sign-On) con i rispettivi account Azure AD.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Slack, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Slack abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Slack supporta l'accesso SSO avviato da **provider di servizi**
* Slack supporta il provisioning utenti **JIT**
* Slack supporta il [provisioning utenti **automatico**](slack-provisioning-tutorial.md)

## <a name="adding-slack-from-the-gallery"></a>Aggiunta di Slack dalla raccolta

Per configurare l'integrazione di Slack in Azure AD, è necessario aggiungere Slack dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Slack dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Slack**, selezionare **Slack** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Slack nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Slack con un utente di test di nome **Britta Simon**.
Per il funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Slack.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Slack, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Slack](#configure-slack-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Slack](#create-slack-test-user)**: per avere una controparte di Britta Simon in Slack collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Slack, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Slack** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Slack](common/sp-identifier.png)

    a. Nella casella di testo **URL accesso** digitare l'URL usando il modello seguente: `https://<companyname>.slack.com`.

    b. Nella casella di testo **Identificatore (ID entità)** aggiornare il valore con l'URL di accesso. Questo è il dominio dell'area di lavoro. Ad esempio: `https://contoso.slack.com`.

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori contattare il [team di supporto clienti di Slack](https://slack.com/help/contact). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. L'applicazione Slack prevede che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![image](common/edit-attribute.png)

    > [!NOTE] 
    > Se l'**indirizzo di posta elettronica** di uno o più utenti non è compreso da una licenza di Office 365, l'attestazione **User.Email** non viene visualizzata nel token SAML. In questi casi, è invece consigliabile usare **user.userprincipalname** come valore dell'attributo **User.Email** per eseguire il mapping come **Identificatore univoco**.

6. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura:

    | NOME | Source Attribute |
    | --- | --- |
    | first_name | user.givenname |
    | last_name | user.surname |
    | User.Email | user.mail |
    | User.Username | user.userprincipalname |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    f. Fare clic su **Ok**

    g. Fare clic su **Save**.

7. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

8. Nella sezione **Configura Slack** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-slack-single-sign-on"></a>Configurare l'accesso Single Sign-On di Slack

1. In un'altra finestra del browser Web accedere al sito aziendale di Slack come amministratore.

2. Accedere a **Microsoft Azure AD**, quindi andare su **Impostazioni team**.

     ![Configurazione accesso Single Sign-On sul lato app](./media/slack-tutorial/tutorial_slack_001.png)

3. Nella sezione **Impostazioni team** fare clic sulla scheda **Autenticazione**, quindi fare clic su **Cambia impostazioni**.

    ![Configurazione accesso Single Sign-On sul lato app](./media/slack-tutorial/tutorial_slack_002.png)

4. Nella finestra di dialogo **Impostazioni di autenticazione SAML** , eseguire la procedura seguente:

    ![Configurazione accesso Single Sign-On sul lato app](./media/slack-tutorial/tutorial_slack_003.png)

    a.  Nella casella di testo **SAML 2.0 Endpoint (HTTP)** (Endpoint SAML 2.0 - HTTP) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    b.  Nella casella di testo **Identity Provider Issuer** (Autorità di certificazione del provider di identità) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    c.  Aprire il certificato scaricato nel Blocco note, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **Certificato pubblico**.

    d. Configurare le tre impostazioni riportate sopra nel modo appropriato per il proprio team Slack. Per altre informazioni sulle impostazioni, vedere la **guida alla configurazione dell'accesso SSO di Slack** disponibile a questo indirizzo: `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    e.  Fare clic su **Salva configurazione**.

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

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a Slack.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Slack**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Slack**.

    ![Collegamento di Slack nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-slack-test-user"></a>Creare un utente di test di Slack

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in Slack. Slack supporta il provisioning JIT, abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso a Slack viene creato un nuovo utente, se non esiste ancora. Slack supporta anche il provisioning utenti automatico; [qui](slack-provisioning-tutorial.md) è possibile trovare altre informazioni su come configurare il provisioning utenti automatico.

> [!NOTE]
> Per creare un utente manualmente, è necessario contattare il [team di supporto di Slack](https://slack.com/help/contact).

> [!NOTE]
> Azure AD Connect è lo strumento capace di sincronizzare le identità di Active Directory locali con Azure AD. Questi utenti sincronizzati possono quindi usare le applicazioni come gli altri utenti del cloud.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Facendo clic sul riquadro Slack nel pannello di accesso si dovrebbe accedere automaticamente all'applicazione Slack per cui è stato configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

* [Configura provisioning utenti](slack-provisioning-tutorial.md)