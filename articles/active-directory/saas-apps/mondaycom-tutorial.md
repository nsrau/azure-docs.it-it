---
title: 'Esercitazione: Integrazione di Azure Active Directory con monday.com | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e monday.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9e8ad807-0664-4e31-91de-731097c768e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/08/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 771b7a07416458fd8523223ca57d3575ae2c85ff
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69033645"
---
# <a name="tutorial-integrate-mondaycom-with-azure-active-directory"></a>Esercitazione: Integrare monday.com con Azure Active Directory

Questa esercitazione descrive come integrare monday.com con Azure Active Directory (Azure AD). Integrando monday.com con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a monday.com.
* Abilitare gli utenti per l'accesso automatico a monday.com con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di monday.com abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. monday.com supporta l'accesso SSO avviato da **SP e IDP** e il provisioning utenti **JIT**.

## <a name="adding-mondaycom-from-the-gallery"></a>Aggiunta di monday.com dalla raccolta

Per configurare l'integrazione di monday.com in Azure AD, è necessario aggiungere monday.com dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **monday.com** nella casella di ricerca.
1. Selezionare **monday.com** nel riquadro dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

Configurare e testare l'accesso SSO di Azure AD con monday.com usando un utente di test di nome **B. Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in monday.com.

Per configurare e testare l'accesso SSO di Azure AD con monday.com, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** per consentire agli utenti di usare questa funzionalità.
2. **[Configurare monday.com](#configure-mondaycom)** per configurare le impostazioni dell'accesso Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di monday.com](#create-mondaycom-test-user)** per avere una controparte di B. Simon in monday.com collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-sso)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **monday.com** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nel riquadro **Configurazione SAML di base** seguire questa procedura se è disponibile un file di metadati del provider di servizi e si vuole eseguire la configurazione in modalità **avviata da IDP**:

    1. Selezionare **Carica il file di metadati**.

    1. Per selezionare il file di metadati, selezionare l'icona della cartella, quindi **Carica**.

    1. Dopo il caricamento del file di metadati, i valori per **Identificatore** e **URL di risposta** vengono inseriti automaticamente nel riquadro **Configurazione SAML di base**:

       > [!Note]
       > Se i valori di **Identificatore** e **URL di risposta** non vengono popolati automaticamente, inserirli manualmente. **Identificatore** e **URL di risposta** sono uguali e il valore è nel formato seguente: `https://<your-domain>.monday.com/saml/saml_callback`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<your-domain>.monday.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di monday.com](mailto:dev@food.ee). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione monday.com prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti. Fare clic su **Modifica** per aprire la finestra di dialogo Attributi utente.

    ![Riquadro Attributi utente](common/edit-attribute.png)

1. Oltre a quelli elencati in precedenza, l'applicazione monday.com prevede il passaggio di qualche altro attributo nella risposta SAML. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** eseguire la procedura seguente per aggiungere l'attributo del token SAML come illustrato nella tabella seguente:

    | Nome | Attributo di origine|
    | ---------------| --------------- |
    | Email | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Rimuovere **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    f. Fare clic su **OK**.

    g. Fare clic su **Save**.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML**, trovare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer in uso.

   ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura monday.com** copiare gli URL appropriati in base alle esigenze.

   ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="configure-mondaycom"></a>Configurare monday.com

1. Per automatizzare la configurazione all'interno di monday.com, è necessario installare l'**estensione del browser per l'accesso sicuro alle app personali** facendo clic su **Installa l'estensione**.

    ![Estensione My Apps](common/install-myappssecure-extension.png)

1. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura monday.com** per passare direttamente all'applicazione monday.com. Specificare quindi le credenziali di amministratore per accedere a monday.com. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 6.

    ![Eseguire la configurazione](common/setup-sso.png)

1. Se si vuole configurare manualmente monday.com, aprire una nuova finestra del Web browser, accedere a monday.com come amministratore e seguire questa procedura:

1. Passare a **Profile** (Profilo) nell'angolo in alto a destra della pagina e fare clic su **Admin** (Amministratore).

    ![Configurazione di monday.com](./media/mondaycom-tutorial/configuration01.png)

1. Selezionare **Security** (Sicurezza) e assicurarsi di fare clic su **Open** (Apri) accanto a SAML.

    ![Configurazione di monday.com](./media/mondaycom-tutorial/configuration02.png)

1. Specificare i dettagli seguenti dell'IDP.

    ![Configurazione di monday.com](./media/mondaycom-tutorial/configuration03.png)

    >[!NOTE]
    >Per altri dettagli, vedere [questo](https://support.monday.com/hc/articles/360000460605-SAML-Single-Sign-on?abcb=34642) articolo

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abiliterà B. Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a monday.com.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **monday.com**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-mondaycom-test-user"></a>Creare l'utente di test di monday.com

In questa sezione viene creato un utente di nome B. Simon in monday.com. monday.com supporta il provisioning JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in monday.com, ne viene creato uno nuovo quando si prova ad accedere a monday.com.

### <a name="test-sso"></a>Testare l'accesso SSO

Quando si seleziona il riquadro di monday.com nel Pannello di accesso, si dovrebbe accedere automaticamente all'applicazione monday.com per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
