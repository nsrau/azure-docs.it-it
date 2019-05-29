---
title: 'Esercitazione: Integrazione di Azure Active Directory con Salesforce Sandbox | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Salesforce Sandbox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.openlocfilehash: b2940f3eee3112fe1c6d57cc92157c573ecad109
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65904273"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Esercitazione: Integrazione di Azure Active Directory con Salesforce Sandbox

Questa esercitazione descrive come integrare Salesforce Sandbox con Azure Active Directory (Azure AD).

Sandbox offre la possibilità di creare più copie dell'organizzazione in ambienti distinti per diversi scopi, ad esempio sviluppo, test e formazione, senza compromettere i dati e le applicazioni dell’organizzazione di produzione Salesforce.
Per altre informazioni, vedere [Panoramica di Sandbox](https://help.salesforce.com/articleView?id=create_test_instance.htm&language=en_us&type=5).

L'integrazione di Salesforce Sandbox con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Salesforce Sandbox.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Salesforce Sandbox con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Salesforce Sandbox, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Una sottoscrizione di Salesforce Sandbox abilitata per l'accesso Single Sign-On.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Salesforce Sandbox supporta l'accesso Single Sign-On avviato da **SP e IDP**
* Salesforce Sandbox supporta il provisioning utenti **Just-In-Time** (JIT)
* Salesforce Sandbox supporta il [**provisioning utenti automatico**](salesforce-sandbox-provisioning-tutorial.md)

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Aggiunta di Salesforce Sandbox dalla raccolta

Per configurare l'integrazione di Salesforce Sandbox in Azure AD, è necessario aggiungere Salesforce Sandbox dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Salesforce Sandbox dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Salesforce Sandbox**, selezionare **Salesforce Sandbox** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Salesforce Sandbox nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Salesforce Sandbox usando un utente di test di nome **Britta Simon**.

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Salesforce Sandbox che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Salesforce Sandbox.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Salesforce Sandbox, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Salesforce Sandbox](#configure-salesforce-sandbox-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Salesforce Sandbox](#create-salesforce-sandbox-test-user)** : per avere una controparte di Britta Simon in Salesforce Sandbox collegata alla rappresentazione di Azure AD dell'utente.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare Single Sign-On di Azure AD con Salesforce Sandbox, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Salesforce Sandbox** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si dispone del **file di metadati del provider di servizi** e si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Fare clic su **Carica il file di metadati**.

    ![Caricare file di metadati](common/upload-metadata.png)

    b. Fare clic su **logo cartella** per selezionare il file di metadati e fare quindi clic su **Upload**.

    ![Scegliere file di metadati](common/browse-upload-metadata.png)

    > [!NOTE]
    > Il file di metadati del provider di servizi sarà disponibile nel portale di amministrazione Salesforce Sandbox, come spiegato più avanti nell'esercitazione.

    c. Dopo che è stato caricato il file di metadati, il valore **URL di risposta** viene popolato automaticamente nella casella di testo della sezione **URL di risposta**.

    ![image](common/both-replyurl.png)

    > [!Note]
    > Se il valore di **URL di risposta** non viene popolato automaticamente, inserirlo manualmente in base alle esigenze.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **file XML dei metadati** definito dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura Salesforce Sandbox** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-salesforce-sandbox-single-sign-on"></a>Configurare l'accesso Single Sign-On di Salesforce Sandbox

1. Aprire una nuova scheda del browser e accedere all'account amministratore di Salesforce Sandbox.

2. Fare clic su **Setup** (Configura) sotto l'**icona Settings** (Impostazioni) nell'angolo superiore destro della pagina.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/configure1.png)

3. Scorrere verso il basso fino alla voce **IMPOSTAZIONI** nel riquadro di spostamento a sinistra e fare clic su **Identità** per espandere la sezione corrispondente. Fare quindi clic su **Single Sign-On Settings**.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

4. Nella pagina **Single Sign-On Settings** fare clic su **Edit**.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/configure3.png)

5. Selezionare **SAML Enabled**, quindi fare clic su **Save**.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

6. Per configurare le impostazioni dell'accesso Single Sign-On SAML, fare clic su **New from Metadata File** (Nuovo da file di metadati).

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

7. Fare clic su **Scegli file** per caricare il file XML di metadati scaricato dal portale di Azure e fare clic su **Crea**.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/xmlchoose.png)

8. Nella pagina **SAML Single Sign-On Settings** (Impostazioni SAML Single Sign-On) i campi vengono popolati automaticamente. Quindi fare clic su Salva.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/salesforcexml.png)

9. Nella pagina **Impostazioni Single Sign-on** fare clic su **Scarica metadati** per scaricare il file di metadati del provider di servizi. Usare il file nella sezione **Configurazione SAML di base** del portale di Azure per configurare gli URL necessari come spiegato in precedenza.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/configure4.png)

10. Per configurare l'applicazione in modalità avviata da **SP**, è necessario soddisfare i prerequisiti seguenti:

    a. È necessario un dominio verificato.

    b. È necessario configurare e abilitare il dominio in Salesforce Sandbox. La relativa procedura è illustrata più avanti in questa esercitazione.

    c. Nella sezione **Configurazione SAML di base** del portale di Azure, fare clic su **Impostare URL aggiuntivi** e seguire questa procedura:
  
    ![Informazioni sull'accesso Single Sign-On per URL e dominio di Salesforce Sandbox](common/both-signonurl.png)

    Nella casella di testo **URL di accesso** digitare il valore usando il modello seguente: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > Questo valore deve essere copiato dal portale di Sandbox Salesforce dopo avere abilitato il dominio.

11. Nella sezione **Certificato di firma SAML** fare clic su **XML metadati federazione** e quindi salvare il file XML nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

12. Aprire una nuova scheda del browser e accedere all'account amministratore di Salesforce Sandbox.

13. Fare clic su **Setup** (Configura) sotto l'**icona Settings** (Impostazioni) nell'angolo superiore destro della pagina.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/configure1.png)

14. Scorrere verso il basso fino alla voce **IMPOSTAZIONI** nel riquadro di spostamento a sinistra e fare clic su **Identità** per espandere la sezione corrispondente. Fare quindi clic su **Single Sign-On Settings**.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

15. Nella pagina **Single Sign-On Settings** fare clic su **Edit**.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/configure3.png)

16. Selezionare **SAML Enabled**, quindi fare clic su **Save**.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

17. Per configurare le impostazioni dell'accesso Single Sign-On SAML, fare clic su **New from Metadata File** (Nuovo da file di metadati).

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

18. Per caricare il file di metadati, fare clic su **Scegli file** e quindi scegliere **Crea**.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/xmlchoose.png)

19. Nella pagina **Impostazioni SAML Single Sign-On** i campi vengono popolati automaticamente. Digitare il nome della configurazione (ad esempio, *SPSSOWAAD_Test*) nella casella di testo **Nome** e quindi fare clic su Salva.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

20. Per abilitare il dominio su Salesforce Sandbox, eseguire la procedura seguente:

    > [!NOTE]
    > Prima di abilitare il dominio è necessario creare lo stesso in Salesforce Sandbox. Per altre informazioni, vedere [Definizione del nome di dominio](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US). Dopo aver creato il dominio, assicurarsi che sia configurato correttamente.

21. Nel pannello di navigazione sinistro in Salesforce Sandbox fare clic su **Company Settings** (Impostazioni aziendali) per espandere la sezione correlata e quindi fare clic su **My Domain** (Dominio personale).

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

22. Nella sezione **Configurazione autenticazione** fare clic su **Criterio di autenticazione**.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

23. Nella sezione **Configurazione autenticazione**, per **Servizio di autenticazione** selezionare il nome dell'impostazione Single Sign-On SAML definita durante la configurazione SSO in Salesforce Sandbox e quindi fare clic su **Salva**.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare `brittasimon@yourcompanydomain.extension`. Ad esempio: BrittaSimon@contoso.com.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abilita Britta Simon per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a Salesforce Sandbox.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Salesforce Sandbox**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Salesforce Sandbox**.

    ![Collegamento Salesforce Sandbox nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-salesforce-sandbox-test-user"></a>Creare l'utente di test di Salesforce Sandbox

In questa sezione si crea un utente di nome Britta Simon in Salesforce Sandbox. Salesforce Sandbox supporta il provisioning JIT (Just-In-Time) che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se un utente non esiste in Salesforce Sandbox, ne viene creato uno nuovo quando si tenta di accedere a Salesforce Sandbox. Salesforce Sandbox supporta anche il provisioning utenti automatico; [qui](salesforce-sandbox-provisioning-tutorial.md) è possibile trovare altre informazioni su come configurare il provisioning utenti automatico.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Salesforce Sandbox nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Salesforce Sandbox per cui si è configurato l'accesso Single Sign-On. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

* [Configura provisioning utenti](salesforce-sandbox-provisioning-tutorial.md)
