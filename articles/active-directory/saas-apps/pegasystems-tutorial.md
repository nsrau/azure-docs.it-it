---
title: 'Esercitazione: Integrazione di Azure Active Directory con Pegasystems | Microsoft Docs'
description: In questa esercitazione si apprenderà come configurare l'accesso Single Sign-On tra Azure Active Directory e Pegasystems.
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
ms.openlocfilehash: bd54466e054055ff84cd5bb2b28c5cc074ac0017
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026811"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Esercitazione: Integrazione di Azure Active Directory con Pegasystems

Questa esercitazione descrive come integrare Pegasystems con Azure Active Directory (Azure AD).

Questa integrazione offre i vantaggi seguenti:

* È possibile usare Azure AD per controllare chi può accedere a Pegasystems.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Pegasystems con gli account Azure AD personali.
* È possibile gestire gli account da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Pegasystems, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile iscriversi per ottenere una [versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).
* Sottoscrizione di Pegasystems abilitata per l'accesso Single Sign-On.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione verranno eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Pegasystems supporta l'accesso SSO avviato da SP e IdP.

## <a name="add-pega-systems-from-the-gallery"></a>Aggiungere Pegasystems dalla raccolta

Per configurare l'integrazione di Pegasystems in Azure AD, è necessario aggiungere Pegasystems dalla raccolta al proprio elenco di app SaaS gestite.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** nel riquadro sinistro:

    ![Selezionare Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere un'applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra:

    ![Selezionare Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Pegasystems**. Selezionare **Pegasystems** nei risultati della ricerca e quindi selezionare **Aggiungi**.

     ![Search Results](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione verrà configurato e testato l'accesso Single Sign-On di Azure AD con Pegasystems usando un utente di test di nome Britta Simon.
Per abilitare l'accesso Single Sign-On, è necessario stabilire una relazione tra un utente di Azure AD e l'utente corrispondente in Pegasystems.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Pegasystems, è necessario completare questi passaggi:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** per abilitare questa funzionalità per gli utenti.
2. **[Configurare l'accesso Single Sign-On di Pegasystems](#configure-pega-systems-single-sign-on)** sul lato dell'applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** per abilitare l'accesso Single Sign-On di Azure AD per tale utente.
5. **[Creare un utente di test di Pegasystems](#create-a-pega-systems-test-user)** collegato alla rappresentazione di Azure AD dell'utente.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione verrà abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Pegasystems, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Pegasystems** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**:

    ![Selezionare Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare l'accesso Single Sign-On:

    ![Selezionare un metodo di accesso Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** selezionare l'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**:

    ![Icona Modifica](common/edit-urls.png)

4. Nella finestra di dialogo **Configurazione SAML di base** completare la seguente procedura se si vuole configurare l'applicazione nella modalità avviata da IdP.

    ![Finestra di dialogo Configurazione SAML di base](common/idp-intiated.png)

    1. Nella casella **Identificatore** immettere un URL nel formato seguente:

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. Nella casella **URL di risposta** immettere un URL nel formato seguente:

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Se si desidera configurare l'applicazione in modalità avviata da SP, selezionare **Imposta URL aggiuntivi** e completare la seguente procedura.

    ![Informazioni sull'accesso Single Sign-On di URL e dominio di Pegasystems](common/both-advanced-urls.png)

    1. Nella casella **URL di accesso** immettere il valore dell'URL di accesso.

    1. Nella casella **Stato dell'inoltro** immettere un URL nel formato seguente: `https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > I valori qui forniti sono segnaposto. È necessario usare l'identificatore, l'URL di risposta, l'URL di accesso e l'URL dello stato dell'inoltro effettivi. È possibile ottenere i valori dell'identificatore e dell'URL di risposta in un'applicazione Pega illustrata più avanti in questa esercitazione. Per ottenere il valore dello stato dell'inoltro, contattare il [team di supporto di Pegasystems](https://www.pega.com/contact-us). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

6. L'applicazione Pegasystems ha bisogno di un formato specifico per le asserzioni SAML. Per visualizzarle nel formato corretto, è necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi dei token SAML. Lo screenshot seguente mostra gli attributi predefiniti. Selezionare l'icona **Modifica** per aprire la finestra di dialogo **Attributi utente**:

    ![Attributi utente](common/edit-attribute.png)

7. Oltre agli attributi illustrati nello screenshot precedente, l'applicazione Pegasystems richiede il passaggio di alcuni altri attributi nella risposta SAML. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** completare questa procedura per aggiungere gli attributi del token SAML:

    
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

    > [!NOTE]
    > Questi valori sono specifici dell'organizzazione. Fornire i valori appropriati.

    1. Selezionare **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**:

    ![Selezionare Aggiungi nuova attestazione](common/new-save-attribute.png)

    ![Finestra di dialogo Gestisci attestazioni utente](common/new-attribute-details.png)

    1. Nella casella **Nome** immettere il nome dell'attributo indicato per la riga.

    1. Lasciare vuota la casella **Spazio dei nomi**.

    1. Per **Origine**, selezionare **Attributo**.

    1. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    1. Selezionare **OK**.

    1. Selezionare **Salva**.

8. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** selezionare il collegamento **Scarica** accanto a **XML metadati federazione**, in base ai propri requisiti, quindi salvare il certificato nel computer:

    ![Collegamento di download del certificato](common/metadataxml.png)

9. Nella sezione **Configura Pegasystems** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    1. **URL di accesso**.

    1. **Identificatore Azure AD**.

    1. **URL di disconnessione**.

### <a name="configure-pega-systems-single-sign-on"></a>Configurare l'accesso Single Sign-On di Pegasystems

1. Per configurare l'accesso Single Sign-On sul lato **Pegasystems**, accedere al portale Pega usando un account amministratore in un'altra finestra del browser.

2. Selezionare **Create** > **SysAdmin** > **Authentication Service** (Crea > Amministrazione del sistema > Servizio di autenticazione):

    ![Selezionare Authentication Service (Servizio di autenticazione)](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. Completare i passaggi seguenti nella schermata **Create Authentication Service** (Crea servizio di autenticazione).

    ![Schermata di creazione del servizio di autenticazione](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    1. Nell'elenco **Type** (Tipo) selezionare **SAML 2.0**.

    1. Nella casella **Name** (Nome) digitare un nome qualsiasi, ad esempio **Azure AD SSO**.

    1. Nella casella **Short description** (Descrizione breve) immettere una descrizione.  

    1. Selezionare **Create and open** (Crea e apri).
    
4. Nella sezione **Identity Provider (IdP) information** (Informazioni sul provider di identità) selezionare **Import IdP metadata** (Importa dati IdP) e cercare il file di metadati scaricato dal portale di Azure. Fare clic su **Submit** (Invia) per caricare i metadati:

    ![Sezione delle informazioni sul provider di identità (IdP)](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
    L'importazione inserirà i dati del provider di identità come illustrato nella seguente immagine:

    ![Dati IdP importati](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. Completare la seguente procedura nella sezione **Service provider settings** (Impostazioni provider di servizi).

    ![Impostazioni provider di servizi](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    1. Copiare il valore di **Entity Identification** (Identificazione entità) e incollarlo nella casella **Identificatore** nella sezione **Configurazione SAML di base** del portale di Azure.

    1. Copiare il valore di **Assertion Consumer Service (ACS) location** (Posizione servizio consumer di asserzione) e incollarlo nella casella **URL di risposta** nella sezione **Configurazione SAML di base** del portale di Azure.

    1. Selezionare **Disable request signing** (Disabilita firma richiesta).

7. Selezionare **Salva**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**:

    ![Selezionare Tutti gli utenti](common/users.png)

2. Selezionare **Nuovo utente** nella parte superiore della schermata:

    ![Selezionare Nuovo utente](common/new-user.png)

3. Nella finestra di dialogo **Utente** completare la procedura seguente.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nella casella **Nome** immettere **BrittaSimon**.
  
    b. Nella casella **Nome utente** immettere **brittasimon@\<dominioazienda>.\<estensione>** . ad esempio BrittaSimon@contoso.com.

    c. Selezionare **Mostra password**, quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Selezionare **Create** (Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione Britta Simon sarà abilitata per l'uso dell'accesso Single Sign-On di Azure, concedendole l'accesso a Pegasystems.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Pegasystems**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Pegasystems**.

    ![Elenco delle applicazioni](common/all-applications.png)

3. Nel riquadro sinistro selezionare **Utenti e gruppi**:

    ![Selezionare Utenti e gruppi](common/users-groups-blade.png)

4. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Selezionare Aggiungi utente](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco di utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco. Fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

### <a name="create-a-pega-systems-test-user"></a>Creare un utente di test per Pegasystems

A questo punto occorre creare un utente di nome Britta Simon in Pegasystems. Collaborare con il [team di supporto di Pegasystems](https://www.pega.com/contact-us) per creare gli utenti.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

Il passaggio finale consiste nel testare la configurazione dell'accesso Single Sign-On di Azure AD usando il Pannello di accesso.

Quando si seleziona il riquadro di Pegasystems nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza Pegasystems per cui si è configurato l'accesso SSO. Per altre informazioni, vedere [Accedere e usare le app nel portale App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Esercitazioni per l'integrazione di applicazioni SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)