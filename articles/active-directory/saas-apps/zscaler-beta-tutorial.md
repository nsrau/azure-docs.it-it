---
title: 'Esercitazione: Integrazione di Azure Active Directory con Zscaler Beta | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Zscaler Beta.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 56b846ae-a1e7-45ae-a79d-992a87f075ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07b0f8112f724c857ffb46378f7aa7ef605b9bbb
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68943297"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Esercitazione: Integrazione di Azure Active Directory con Zscaler Beta

Questa esercitazione spiega come integrare Zscaler Beta con Azure Active Directory (Azure AD).
Quando si integra Zscaler Beta con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Zscaler Beta.
* Consentire agli utenti di eseguire l'accesso automatico (Single Sign-On) a Zscaler Beta con gli account Azure AD personali. Questo controllo di accesso è denominato accesso Single Sign-On (SSO).
* È possibile gestire gli account in un'unica posizione centrale usando il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Zscaler Beta, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Zscaler Beta che usa l'accesso Single Sign-On.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Zscaler Beta supporta l'accesso SSO avviato da SP.
* Zscaler Beta supporta il provisioning utenti JIT.

## <a name="add-zscaler-beta-from-the-azure-marketplace"></a>Aggiungere Zscaler Beta da Azure Marketplace

Per configurare l'integrazione di Zscaler Beta in Azure AD, aggiungere Zscaler Beta da Azure Marketplace all'elenco di app SaaS gestite.

Per aggiungere Zscaler Beta da Azure Marketplace, seguire questa procedura.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** nel riquadro di spostamento a sinistra.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Zscaler Beta**. Selezionare **Zscaler Beta** nel pannello dei risultati e quindi selezionare **Aggiungi**.

     ![Zscaler Beta nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Zscaler Beta usando l'utente di test Britta Simon.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Zscaler Beta.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Zscaler Beta, completare le procedure di base seguenti:

- [Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on) per consentire agli utenti di usare questa funzionalità.
- [Configurare l'accesso Single Sign-On di Zscaler Beta](#configure-zscaler-beta-single-sign-on): per configurare le impostazioni di Single Sign-On sul lato applicazione.
- [Creare un utente di test di Azure AD](#create-an-azure-ad-test-user) per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
- [Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user) per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
- [Creare un utente di test di Zscaler Beta](#create-a-zscaler-beta-test-user): per avere una controparte di Britta Simon in Zscaler Beta collegata alla rappresentazione dell'utente in Azure AD.
- [Testare l'accesso Single Sign-On](#test-single-sign-on) per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Zscaler Beta, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Zscaler Beta** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Configurare il collegamento Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare l'accesso Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** selezionare **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** eseguire il passaggio seguente:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Zscaler Beta](common/sp-intiated.png)

    - Nella casella **URL di accesso** digitare l'URL usato dagli utenti per accedere all'applicazione Zscaler Beta.

    > [!NOTE]
    > Poiché il valore non è reale, è necessario aggiornarlo con l'URL di accesso effettivo. Per ottenere il valore, contattare il [team di supporto clienti di Zscaler Beta](https://www.zscaler.com/company/contact).

5. L'applicazione Zscaler Beta prevede un formato specifico per le asserzioni SAML. È necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti. Selezionare **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![Finestra di dialogo Attributi utente](common/edit-attribute.png)

6. L'applicazione Zscaler Beta prevede inoltre il passaggio di qualche altro attributo nella risposta SAML. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** seguire questa procedura per aggiungere l'attributo del token SAML come illustrato nella tabella seguente.
    
    | NOME | Attributo di origine | 
    | ---------------| --------------- |
    | memberOf  | user.assignedroles |

    a. Selezionare **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![Finestra di dialogo Attestazioni utente](common/new-save-attribute.png)

    ![Finestra di dialogo Gestisci attestazioni utente](common/new-attribute-details.png)

    b. Nella casella **Nome** immettere il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per **Origine**, selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** immettere il valore dell'attributo indicato per la riga.

    f. Selezionare **OK**.

    g. Selezionare **Salva**.

    > [!NOTE]
    > Per informazioni su come configurare i ruoli in Azure AD, vedere l'argomento su come [configurare l'attestazione del ruolo](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management).

7. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** selezionare **Scarica** per scaricare il file **Certificato (Base64)** . Salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

8. Nella sezione **Configura Zscaler Beta** copiare gli URL necessari in base alle esigenze:

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    - URL di accesso
    - Identificatore di Azure AD
    - URL di chiusura sessione

### <a name="configure-zscaler-beta-single-sign-on"></a>Configurare l'accesso Single Sign-On di Zscaler Beta

1. Per automatizzare la configurazione all'interno di Zscaler Beta, installare l'estensione del browser **My Apps Secure Sign-in Extension** selezionando **Installa l'estensione**.

    ![Estensione My Apps](common/install-myappssecure-extension.png)

2. Dopo aver aggiunto l'estensione al browser, selezionare **Configura Zscaler Beta** per aprire direttamente l'applicazione Zscaler Beta. Nell'applicazione fornire le credenziali di amministratore per accedere a Zscaler Beta. L'estensione del browser configura automaticamente l'applicazione e automatizza i passaggi da 3 a 6.

    ![Eseguire la configurazione](common/setup-sso.png)

3. Per configurare manualmente Zscaler Beta, aprire una nuova finestra del Web browser. Accedere al sito aziendale di Zscaler Beta come amministratore e seguire questa procedura.

4. Passare ad **Administration** (Amministrazione)  > **Authentication** (Autenticazione)  > **Authentication Settings** (Impostazioni di autenticazione) e seguire questa procedura.
   
    ![Amministrazione](./media/zscaler-beta-tutorial/ic800206.png "Amministrazione")

    a. In **Authentication Type** (Tipo di autenticazione) selezionare **SAML**.

    b. Selezionare **Configure SAML** (Configura SAML).

5. Nella finestra **Edit SAML** (Modifica SAML) seguire questa procedura: 
            
    ![Gestire utenti e autenticazione](./media/zscaler-beta-tutorial/ic800208.png "Gestire utenti e autenticazione")
    
    a. Nella casella **SAML Portal URL** (URL del portale SAML) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    b. Nella casella **Login Name Attribute** (Attributo del nome di accesso) immettere **NameID**.

    c. Nella casella **Public SSL Certificate** (Certificato SSL pubblico) selezionare **Upload** (Carica) per caricare il certificato di firma SAML di Azure scaricato dal portale di Azure.

    d. Attivare/disattivare **Enable SAML Auto-Provisioning** (Abilita provisioning automatico SAML).

    e. Nella casella **User Display Name Attribute** (Attributo del nome visualizzato utente) immettere **displayName** se si vuole abilitare il provisioning automatico SAML per gli attributi displayName.

    f. Nella casella **Group Name Attribute** (Attributo del nome gruppo) immettere **memberOf** se si vuole abilitare il provisioning automatico SAML per gli attributi memberOf.

    g. Nella casella di testo **Department Name Attribute** (Attributo nome reparto) immettere **department** se si vuole abilitare il provisioning automatico SAML per gli attributi department.

    h. Selezionare **Salva**.

6. Nella pagina della finestra di dialogo **Configure User Authentication** (Configura autenticazione utente) seguire questa procedura:

    ![Menu Activation e pulsante Activate](./media/zscaler-beta-tutorial/ic800207.png)

    a. Passare il puntatore sul menu **Activation** (Attivazione) in basso a sinistra.

    b. Selezionare **Attiva**.

## <a name="configure-proxy-settings"></a>Configurare le impostazioni proxy
Per configurare le impostazioni proxy in Internet Explorer, seguire questa procedura.

1. Avviare **Internet Explorer**.

2. Selezionare **Opzioni Internet** dal menu **Strumenti** per aprire la finestra di dialogo **Opzioni Internet**. 
    
     ![Finestra di dialogo Opzioni Internet](./media/zscaler-beta-tutorial/ic769492.png "Opzioni Internet")

3. Selezionare la scheda **Connessioni**. 
  
     ![Scheda Connessioni](./media/zscaler-beta-tutorial/ic769493.png "Connessioni")

4. Selezionare **Impostazioni LAN** per aprire la finestra di dialogo **Impostazione rete locale (LAN)** .

5. Nella sezione **Server proxy** seguire questa procedura: 
   
    ![Sezione Server proxy](./media/zscaler-beta-tutorial/ic769494.png "Server proxy")

    a. Selezionare la casella di controllo **Usa un server proxy per le connessioni LAN**.

    b. Nella casella **Indirizzo** immettere **gateway.Zscaler Beta.net**.

    c. Nella casella **Porta** immettere **80**.

    d. Selezionare la casella di controllo **Ignora server proxy per indirizzi locali**.

    e. Selezionare **OK** per chiudere la finestra di dialogo **Impostazioni rete locale (LAN)** .

6. Selezionare **OK** per chiudere la finestra di dialogo **Opzioni Internet**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

Nel portale di Azure creare un utente di test chiamato Britta Simon.

1. Nel riquadro di sinistra del portale di Azure selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti**.

    ![Collegamenti Utenti e Tutti gli utenti](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nella casella **Nome** immettere **BrittaSimon**.
  
    b. Nella casella **Nome utente** immettere `brittasimon@yourcompanydomain.extension`. Un esempio è BrittaSimon@contoso.com.

    c. Selezionare la casella di controllo **Mostra password**. Prendere quindi nota del valore visualizzato nella casella **Password**.

    d. Selezionare **Create** (Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

Abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Zscaler Beta.

1. Nel portale di Azure selezionare **Applicazioni aziendali** > **Tutte le applicazioni** > **Zscaler Beta**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni immettere e selezionare **Zscaler Beta**.

    ![Collegamento di Zscaler Beta nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento Utenti e gruppi](common/users-groups-blade.png)

4. Selezionare **Aggiungi utente**. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Utenti e gruppi**.

    ![Pulsante Aggiungi utente](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare l'utente **Britta Simon** nell'elenco. Scegliere quindi **Seleziona** nella parte inferiore della schermata.

    ![Finestra di dialogo Utenti e gruppi](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_users.png)

6. Nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo utente appropriato nell'elenco. Scegliere quindi **Seleziona** nella parte inferiore della schermata.

    ![Finestra di dialogo Selezionare un ruolo](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_roles.png)

7. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

    ![Finestra di dialogo Aggiungi assegnazione](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_assign.png)

### <a name="create-a-zscaler-beta-test-user"></a>Creare l'utente di test di Zscaler Beta

In questa sezione viene creato l'utente Britta Simon in Zscaler Beta. Zscaler Beta supporta il **provisioning utenti JIT**, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Zscaler Beta, ne viene creato uno nuovo dopo l'autenticazione.

>[!Note]
>Per creare un utente manualmente, contattare il [team di supporto di Zscaler Beta](https://www.zscaler.com/company/contact).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

Testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro di Zscaler Beta nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Zscaler Beta per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

