---
title: 'Esercitazione: Integrazione di Azure Active Directory con NetSuite | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e NetSuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab329574ef425e8133ac746c185050efcc8bc15a
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59261446"
---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>Esercitazione: Integrazione di Azure Active Directory con NetSuite

Questa esercitazione descrive come integrare NetSuite con Azure Active Directory (Azure AD).
L'integrazione di NetSuite con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a NetSuite.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a NetSuite con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con NetSuite, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di NetSuite abilitata per il Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* NetSuite supporta l'accesso SSO avviato da **IdP**
* NetSuite supporta il provisioning **Just-In-Time** (JIT) degli utenti
* NetSuite supporta il [provisioning utenti automatico](NetSuite-provisioning-tutorial.md)

## <a name="adding-netsuite-from-the-gallery"></a>Aggiunta di NetSuite dalla raccolta

Per configurare l'integrazione di NetSuite in Azure AD, è necessario aggiungere NetSuite dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere NetSuite dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **NetSuite**, selezionare **NetSuite** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![NetSuite nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con NetSuite usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in NetSuite.

Per configurare e testare l'accesso Single Sign-On di Azure AD con NetSuite, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di NetSuite](#configure-netsuite-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di NetSuite](#create-netsuite-test-user)**: per avere una controparte di Britta Simon in NetSuite collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con NetSuite, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **NetSuite** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di NetSuite](common/idp-reply.png)

    Nella casella di testo **URL di risposta** digitare un URL nel formato seguente:

    `https://<tenant-name>.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.NetSuite.com/saml2/acs`

    `https://<tenant-name>.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs`

    > [!NOTE]
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di risposta effettivo. Per ottenere il valore contattare il [team di supporto di NetSuite](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. L'applicazione NetSuite prevede un formato specifico per le asserzioni SAML. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![image](common/edit-attribute.png)

6. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** modificare le attestazioni usando l'**icona Modifica** o aggiungere le attestazioni usando l'opzione **Aggiungi nuova attestazione** per configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura:
    
    | Nome | Attributo di origine | 
    | ---------------| --------------- |
    | account  | `account id` |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    f. Fare clic su **Ok**

    g. Fare clic su **Save**.

    >[!NOTE]
    >Il valore dell'attributo account non è reale. È necessario aggiornarlo, come descritto più avanti nell'esercitazione.

4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **file XML dei metadati** definito dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura NetSuite** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-netsuite-single-sign-on"></a>Configurare l'accesso Single Sign-On di NetSuite

1. Aprire una nuova scheda nel browser e accedere al sito della società NetSuite come amministratore.

2. Sulla barra degli strumenti nella parte superiore della pagina, fare clic su **Setup** (Installazione), quindi passare a **Company** (Società) e fare clic su **Enable Features** (Attiva funzionalità).

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Sulla barra degli strumenti al centro della pagina, fare clic su **SuiteCloud**.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-suitecloud.png)

4. Nella sezione **Manage Authentication** (Gestisci autenticazione) selezionare **SAML SINGLE SIGN-ON** per abilitare l'opzione SAML SINGLE SIGN-ON in NetSuite.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-ticksaml.png)

5. Sulla barra degli strumenti nella parte superiore della pagina fare clic su **Setup** (Configurazione).

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-setup.png)

6. Dall'elenco **SETUP TASKS** (Configura attività) selezionare **Integration** (Integrazione).

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-integration.png)

7. Nella sezione **MANAGE AUTHENTICATION** (Gestione autenticazione) fare clic su **SAML Single Sign-on**.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-saml.png)

8. Nella pagina **SAML Setup** (Configurazione SAML), nella sezione **NetSuite Configuration** (Configurazione NetSuite) seguire questa procedura:

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Selezionare **PRIMARY AUTHENTICATION METHOD** (Metodo di autenticazione primario).

    b. Per il campo con etichetta **SAMLV2 IDENTITY PROVIDER METADATA** (Metadati provider identità SAMLV2) selezionare **UPLOAD IDP METADATA FILE** (Carica file di metadati IDP). Fare quindi clic su **Browse** (Sfoglia) per caricare il file di metadati scaricato dal portale di Azure.

    c. Fare clic su **Submit**.

9. In NetSuite fare clic su **Setup** (Configurazione) quindi passare a **Company** (Società) e fare clic su **Company Information** (Informazioni sulla società) dal menu di spostamento superiore.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-com.png)

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-account-id.png)

    b. Nella pagina **Company Information** (Informazioni sulla società) sulla colonna destra copiare **ID ACCOUNT**.

    c. Incollare l'**ID account** copiato dall'account NetSuite nel campo **Valore attributo** in Azure AD. 

10. Per consentire l'accesso Single Sign-On a NetSuite, è prima di tutto necessario che le autorizzazioni appropriate vengano assegnate agli utenti in NetSuite. Per assegnare le autorizzazioni, seguire le istruzioni seguenti.

    a. Dal menu di navigazione principale fare clic su **Setup** (Configurazione).

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-setup.png)

    b. Scegliere **Users/Roles** (Utenti/Ruoli) dal menu di navigazione a sinistra, quindi fare clic su **Manage Roles** (Gestisci ruoli).

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Fare clic su **New Role**.

    d. Digitare un **nome** per il nuovo ruolo.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-new-role.png)

    e. Fare clic su **Save**.

    f. Scegliere **Permissions**dal menu disponibile nella parte superiore. Fare quindi clic su **Setup**.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-sso.png)

    g. Selezionare **SAML Single Sign-on**, quindi fare clic su **Aggiungi**.

    h. Fare clic su **Save**.

    i. Scegliere **Setup** (Configurazione) dal menu di navigazione principale, quindi fare clic su **Setup Manager** (Configura gestore).

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-setup.png)

    j. Scegliere **Users/Roles** (Utenti/Ruoli) dal menu di navigazione a sinistra, quindi fare clic su **Manage Users** (Gestisci utenti).

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Selezionare un utente di test. Quindi fare clic su **Edit** (Modifica) e passare alla scheda **Access** (Accesso).

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-edit-user.png)

    l. Nella finestra di dialogo Roles (Ruoli) selezionare il ruolo creato.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-add-role.png)

    m. Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD 

Questa sezione descrive come creare un utente di test denominato Britta Simon nel portale di Azure.

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

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a NetSuite.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **NetSuite**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni digitare e selezionare **NetSuite**.

    ![Collegamento a NetSuite nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-netsuite-test-user"></a>Creare l'utente di test di NetSuite

In questa sezione viene creato un utente di nome Britta Simon in NetSuite. NetSuite supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in NetSuite, ne viene creato uno nuovo dopo l'autenticazione.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di NetSuite nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione NetSuite per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configura provisioning utenti](NetSuite-provisioning-tutorial.md)

