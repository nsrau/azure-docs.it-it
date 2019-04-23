---
title: 'Esercitazione: Integrazione di Azure Active Directory con Pegasystems | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Pegasystems.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 34fe5d85829d909989513214406ba96ea5be0aa8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59271102"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Esercitazione: Integrazione di Azure Active Directory con Pegasystems

Questa esercitazione descrive come integrare Pegasystems con Azure Active Directory (Azure AD).
L'integrazione di Pegasystems con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Pegasystems.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Pega Systems con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Pegasystems, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Pega Systems abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Pega Systems supporta l'accesso SSO avviato da **SP** e **IDP**

## <a name="adding-pega-systems-from-the-gallery"></a>Aggiunta di Pegasystems dalla raccolta

Per configurare l'integrazione di Pegasystems in Azure AD, è necessario aggiungere Pegasystems dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Pegasystems dalla raccolta, eseguire la procedura seguente:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Pegasystems**, selezionare **Pegasystems** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Pegasystems nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Pega Systems usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Pega Systems.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Pegasystems, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Pega Systems](#configure-pega-systems-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Pega Systems](#create-pega-systems-test-user)**: per avere una controparte di Britta Simon in Pega Systems collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Pega Systems, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Pega Systems** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni sull'accesso Single Sign-On di URL e dominio di Pegasystems](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/sp/<INSTANCEID>`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni sull'accesso Single Sign-On di URL e dominio di Pegasystems](common/both-advanced-urls.png)

    a. Nella casella di testo **URL di accesso** digitare il valore dell'URL di accesso.

    b. Nella casella di testo **Stato dell'inoltro** digitare un URL nel formato seguente: `https://<CUSTOMERNAME>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta, l'URL di accesso e l'URL dello stato dell'inoltro effettivi. È possibile trovare i valori dell'identificatore e dell'URL di risposta nell'applicazione Pegasystems illustrata più avanti in questa esercitazione. Per ottenere il valore dello stato dell'inoltro, contattare il [team di supporto clienti di Pega Systems](https://www.pega.com/contact-us). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

6. L'applicazione Pega Systems prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti. Fare clic sull'icona  **Modifica** per aprire la finestra di dialogo **Attributi utente**. 

    ![image](common/edit-attribute.png)

7. Oltre quelli elencati in precedenza, l'applicazione Pega Systems prevede il passaggio di qualche altro attributo nella risposta SAML. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** eseguire la procedura seguente per aggiungere l'attributo del token SAML come illustrato nella tabella seguente:

    | Nome | Attributo di origine|
    | ------------------- | -------------------- |
    | uid | *********** |
    | cn  | *********** |
    | mail | *********** |
    | accessgroup | *********** |
    | Organizzazione | *********** |
    | orgdivision | *********** |
    | orgunit | *********** |
    | workgroup | *********** |
    | Telefono | *********** |

    > [!NOTE]
    > Si tratta di valori specifici del cliente. Specificare i valori appropriati.

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

9. Nella sezione **Configura Pega Systems** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-pega-systems-single-sign-on"></a>Configurare l'accesso Single Sign-On di Pega Systems

1. Per configurare l'accesso Single Sign-On sul lato **Pegasystems**, aprire il **portale Pega** usando l'account amministratore in un'altra finestra del browser.

2. Selezionare **Create** -> **SysAdmin** -> **Authentication Service** (Crea > Amministrazione del sistema > Servizio di autenticazione).

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. Eseguire le operazioni seguenti nella schermata **Create Authentication Service** (Crea servizio di autenticazione):

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    a. Selezionare **SAML 2.0** nella casella Type (Tipo)

    b. Nella casella di testo **Name** (Nome) digitare un nome qualsiasi, ad esempio Azure AD SSO

    c. Nella casella di testo **Short Description** (Descrizione breve) digitare una descrizione  

    d. Fare clic su **Create and open** (Crea e apri) 
    
4. Nella sezione **Identity Provider (IdP) information** (Informazioni sul provider di identità) fare clic su **Import IdP metadata** (Importa dati IdP) e cercare il file di metadati precedentemente scaricato dal portale di Azure. Fare clic su **Submit** (Invia) per caricare i metadati.

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
5. Questa operazione inserirà i dati del provider di identità come illustrato di seguito.

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. Eseguire le azioni seguenti nella sezione **Service Provider (SP) settings** (Impostazioni del provider del servizio):

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    a. Copiare il valore di **Entity Identification** (Identificazione entità) e incollarlo nella casella di testo **Identificatore** nella sezione **Configurazione SAML di base** del portale di Azure.

    b. Copiare il valore di **Assertion Consumer Service (ACS) location** (Percorso del servizio consumer di asserzione) e incollarlo nella casella di testo **URL di risposta** nella sezione **Configurazione SAML di base** del portale di Azure.

    c. Selezionare **Disable request signing** (Disabilita firma richiesta).

7. Fare clic su **Save** (Salva).

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

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Pegasystems.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Pega Systems**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Pegasystems**.

    ![Collegamento Pegasystems nell'elenco Applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-pega-systems-test-user"></a>Creare l'utente di test di Pega Systems

Questa sezione descrive come creare un utente chiamato Britta Simon in Pegasystems. Rivolgersi [team di supporto clienti di Pega Systems](https://www.pega.com/contact-us) per creare utenti in Pega Systems.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Pega Systems nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Pega Systems per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)