---
title: 'Esercitazione: Integrazione di Azure Active Directory con Zscaler One | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Zscaler One.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f352e00d-68d3-4a77-bb92-717d055da56f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.openlocfilehash: 94682cfae8078a38fff9ef36ccfc619baee2e612
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64694433"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-one"></a>Esercitazione: Integrazione di Azure Active Directory con Zscaler One

Questa esercitazione descrive come integrare Zscaler One con Azure Active Directory (Azure AD).
L'integrazione di Zscaler One con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Zscaler One.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Zscaler One con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Zscaler One, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Zscaler One abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Zscaler One supporta l'accesso SSO avviato da **SP**

* Zscaler One supporta il provisioning utenti **JIT**

## <a name="adding-zscaler-one-from-the-gallery"></a>Aggiunta di Zscaler One dalla raccolta

Per configurare l'integrazione di Zscaler One in Azure AD, è necessario aggiungere Zscaler One dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Zscaler One dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Zscaler One**, selezionare **Zscaler One** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Zscaler One nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Zscaler One usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Zscaler One.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Zscaler One, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Zscaler One](#configure-zscaler-one-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l' di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Zscaler One](#create-zscaler-one-test-user)** : per avere una controparte di Britta Simon in Zscaler One collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Zscaler One, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Zscaler One** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Zscaler One](common/sp-signonurl.png)

    Nella casella di testo dell'**URL di accesso** digitare l'URL usato dagli utenti per accedere all'applicazione Zscaler One.

    > [!NOTE]
    > Aggiornare questo valore con l'URL di accesso effettivo. Per ottenere il valore, contattare il [team di supporto clienti di Zscaler One](https://www.zscaler.com/company/contact). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. L'applicazione Zscaler One prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti. Fare clic sull'icona  **Modifica** per aprire la finestra di dialogo **Attributi utente**. 

    ![image](common/edit-attribute.png)

6. Oltre quelli elencati in precedenza, l'applicazione Zscaler One prevede il passaggio di altri attributi nella risposta SAML. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** eseguire la procedura seguente per aggiungere l'attributo del token SAML come illustrato nella tabella seguente:
    
    | Nome | Attributo di origine |
    | ---------| ------------ |
    | memberOf     | user.assignedroles |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.
    
    f. Fare clic su **Save**.

    > [!NOTE]
    > Fare clic [qui](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) per sapere come configurare un Ruolo in Azure AD

7. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

8. Nella sezione **Configura Zscaler One** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-zscaler-one-single-sign-on"></a>Configurare l'accesso Single Sign-On di Zscaler One

1. Per automatizzare la configurazione all'interno di Zscaler One, è necessario installare l'**estensione del browser per l'accesso sicuro MyApps** facendo clic su **Install the extension** (Installa l'estensione).

    ![Estensione MyApps](common/install-myappssecure-extension.png)

2. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura Zscaler One** per passare direttamente all'applicazione Zscaler One. Nell'applicazione fornire le credenziali di amministratore per accedere a Zscaler One. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 6.

    ![Configurare l'accesso SSO](common/setup-sso.png)

3. Se si vuole configurare manualmente Zscaler One, aprire una nuova finestra del Web browser, accedere al sito aziendale di Zscaler One come amministratore e seguire questa procedura:

4. Passare a **Amministrazione > Autenticazione > Impostazioni di autenticazione** ed eseguire i passaggi seguenti:
   
    ![Amministrazione](./media/zscaler-one-tutorial/ic800206.png "Amministrazione")

    a. In Tipo di autenticazione scegliere **SAML**.

    b. Fare clic su **Configure SAML**.

5. Nella finestra **Modifica SAML** eseguire i passaggi seguenti: e fare clic su Salva.  
            
    ![Gestire utenti e autenticazione](./media/zscaler-one-tutorial/ic800208.png "Gestire utenti e autenticazione")
    
    a. Nella casella di testo **URL portale SAML** incollare il valore di **URL di accesso** copiato dal portale di Azure.

    b. Nella casella di testo **Attributo del nome di accesso** immettere **NameID**.

    c. Fare clic su **Carica** per caricare il certificato di firma di Azure SAML scaricato dal portale di Azure nel **certificato pubblico SSL**.

    d. Selezionare **Abilita provisioning automatico SAML**.

    e. Nella casella di testo **Attributo nome visualizzato dell'utente** immettere **displayName** se si desidera abilitare il provisioning automatico di SAML per gli attributi displayName.

    f. Nella casella di testo **Attributo nome gruppo** immettere **memberOf** se si desidera abilitare il provisioning automatico di SAML per gli attributi memberOf.

    g. Nella casella di testo **Attributo nome reparto** immettere **department** se si desidera abilitare il provisioning automatico di SAML per gli attributi department.

    h. Fare clic su **Save**.

6. Nella pagina della finestra di dialogo **Configure User Authentication** seguire questa procedura:

    ![Administration](./media/zscaler-one-tutorial/ic800207.png)

    a. Passare il mouse sul menu **Attivazione** nella parte inferiore sinistra.

    b. Fare clic su **Attiva**.

## <a name="configuring-proxy-settings"></a>Configurazione delle impostazioni proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Per configurare le impostazioni proxy in Internet Explorer

1. Avviare **Internet Explorer**.

2. Selezionare **Opzioni Internet** dal menu **Strumenti** per aprire la finestra di dialogo **Opzioni Internet**.   
    
     ![Opzioni Internet](./media/zscaler-one-tutorial/ic769492.png "Opzioni Internet")

3. Fare clic sulla scheda **Connessioni** .   
  
     ![Connessioni](./media/zscaler-one-tutorial/ic769493.png "Connessioni")

4. Fare clic su **Impostazioni LAN** per aprire la finestra di dialogo **Impostazioni LAN**.

5. Nella sezione del server proxy seguire questa procedura:   
   
    ![Server proxy](./media/zscaler-one-tutorial/ic769494.png "Server proxy")

    a. Selezionare **Usa un server proxy per la LAN**.

    b. Nella casella di testo Indirizzo digitare **gateway.Zscaler One.net**.

    c. Nella casella di testo Porta digitare **80**.

    d. Selezionare **Ignora server proxy per indirizzi locali**.

    e. Fare clic su **OK** per chiudere la finestra di dialogo **Impostazioni rete locale (LAN)** .

7. Fare clic su **OK** per chiudere la finestra di dialogo **Opzioni Internet**.

### <a name="create-an-azure-ad-test-user"></a>Creare un  di Azure AD 

Questa sezione descrive come creare un  denominato Britta Simon nel portale di Azure.

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

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l' di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Zscaler One.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Zscaler One**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Zscaler One**.

    ![Collegamento di Zscaler One nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare l'utente **Britta Simon** nell'elenco e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

    ![image](./media/zscaler-one-tutorial/tutorial_zscalerone_users.png)

6. Nella finestra di dialogo **Seleziona ruolo** scegliere il ruolo utente appropriato dall'elenco e fare clic sul pulsante **Seleziona** in basso nella schermata.

    ![image](./media/zscaler-one-tutorial/tutorial_zscalerone_roles.png)

7. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.

    ![image](./media/zscaler-one-tutorial/tutorial_zscalerone_assign.png)

### <a name="create-zscaler-one-test-user"></a>Creare l'utente di test di Zscaler One

In questa sezione viene creato un utente di nome Britta Simon in Zscaler One. Zscaler One supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Zscaler One, ne viene creato uno nuovo dopo l'autenticazione.

>[!Note]
>Se è necessario creare un utente manualmente, contattare il [team di supporto di Zscaler One](https://www.zscaler.com/company/contact).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Zscaler One nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Zscaler One per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

