---
title: 'Esercitazione: Integrazione di Azure Active Directory con Salesforce Sandbox | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Salesforce Sandbox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: feb6cebca0fe165315215813eb939dc2d5d120cf
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56177333"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Esercitazione: Integrazione di Azure Active Directory con Salesforce Sandbox

Questa esercitazione descrive come integrare Salesforce Sandbox con Azure Active Directory (Azure AD).

Sandbox offre la possibilità di creare più copie dell'organizzazione in ambienti distinti per diversi scopi, ad esempio sviluppo, test e formazione, senza compromettere i dati e le applicazioni dell’organizzazione di produzione Salesforce.
Per altre informazioni, vedere una  [panoramica di Sandbox](https://help.salesforce.com/articleView?id=create_test_instance.htm&language=en_us&type=5).

L'integrazione di Salesforce Sandbox con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Salesforce Sandbox.
- È possibile abilitare gli utenti per l'accesso automatico a Salesforce Sandbox (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Salesforce Sandbox, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Salesforce Sandbox abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Salesforce Sandbox dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Aggiunta di Salesforce Sandbox dalla raccolta

Per configurare l'integrazione di Salesforce Sandbox in Azure AD, è necessario aggiungere Salesforce Sandbox dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Salesforce Sandbox dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Salesforce Sandbox**, selezionare **Salesforce Sandbox** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Salesforce Sandbox nell'elenco risultati](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione si configura e si testa l'accesso Single Sign-On di Azure AD con Salesforce Sandbox in base a un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Salesforce Sandbox che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Salesforce Sandbox.

Per stabilire la relazione di collegamento, in Salesforce Sandbox assegnare il valore di **nome utente** di Azure AD come valore dell'attributo **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Salesforce Sandbox, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Salesforce Sandbox](#create-a-salesforce-sandbox-test-user)**: per avere una controparte di Britta Simon in Salesforce Sandbox collegata alla rappresentazione in Azure AD dell'utente.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione si abilita l'accesso Single Sign-On di Azure AD nel portale di Azure e si configura l'accesso Single Sign-On nell'applicazione Salesforce Sandbox.

**Per configurare Single Sign-On di Azure AD con Salesforce Sandbox, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Salesforce Sandbox** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Fare clic su **Modifica modalità Single Sign-On** nella parte superiore della schermata per selezionare la modalità **SAML**.

      ![Collegamento Configura accesso Single Sign-On](./media/salesforce-sandbox-tutorial/tutorial_general_300.png)

3. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** fare clic su **Seleziona** per la modalità **SAML** per abilitare l'accesso Single Sign-On.

    ![Collegamento Configura accesso Single Sign-On](./media/salesforce-sandbox-tutorial/tutorial_general_301.png)

4. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.
   
    ![Collegamento Configura accesso Single Sign-On](./media/salesforce-sandbox-tutorial/tutorial_general_302.png)

5. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Fare clic su **Carica il file di metadati**.

    ![Caricare file di metadati](./media/salesforce-sandbox-tutorial/upload_metadata.png)

    b. Fare clic su **logo cartella** per selezionare il file di metadati e fare quindi clic su **Upload**.

    ![Scegliere file di metadati](./media/salesforce-sandbox-tutorial/browse_upload_metadata.png)

    > [!NOTE]
    > Il file di metadati del provider di servizi sarà disponibile nel portale di amministrazione Salesforce Sandbox, come spiegato più avanti nell'esercitazione.

    c. Dopo che è stato caricato il file di metadati, il valore **URL di risposta** viene popolato automaticamente nella casella di testo della sezione **URL di risposta**.

    ![Informazioni sull'accesso Single Sign-On per URL e dominio di Salesforce Sandbox](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url1.png)

6. Nella sezione **Certificato di firma SAML** fare clic su **Scarica** per scaricare **XML metadati federazione** e quindi salvare il file XML nel computer.

    ![Collegamento di download del certificato](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

7. Aprire una nuova scheda del browser e accedere all'account di amministratore di Salesforce Sandbox.

8. Fare clic su **Setup** (Configura) sotto l'**icona Settings** (Impostazioni) nell'angolo superiore destro della pagina.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/configure1.png)

9. Scorrere verso il basso fino alla voce **IMPOSTAZIONI** nel riquadro di spostamento a sinistra e fare clic su **Identità** per espandere la sezione corrispondente. Fare quindi clic su **Single Sign-On Settings**.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

10. Nella pagina **Single Sign-On Settings** fare clic su **Edit**.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/configure3.png)

11. Selezionare **SAML Enabled**, quindi fare clic su **Save**.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

12. Per configurare le impostazioni dell'accesso Single Sign-On SAML, fare clic su **New from Metadata File** (Nuovo da file di metadati).

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

13. Fare clic su **Scegli file** per caricare il file XML di metadati scaricato dal portale di Azure e fare clic su **Crea**.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/xmlchoose.png)

14. Nella pagina **SAML Single Sign-On Settings** (Impostazioni SAML Single Sign-On) i campi vengono popolati automaticamente. Quindi fare clic su Salva.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/salesforcexml.png)

15. Nella pagina **Impostazioni Single Sign-on** fare clic su **Scarica metadati** per scaricare il file di metadati del provider di servizi. Usare il file nella sezione **Configurazione SAML di base** del portale di Azure per configurare gli URL necessari come spiegato in precedenza.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/configure4.png)

16. Per configurare l'applicazione in modalità avviata da **SP**, è necessario soddisfare i prerequisiti seguenti:

    a. È necessario un dominio verificato.

    b. È necessario configurare e abilitare il dominio in Salesforce Sandbox. La relativa procedura è illustrata più avanti in questa esercitazione.

    c. Nella sezione **Configurazione SAML di base** del portale di Azure, fare clic su **Impostare URL aggiuntivi** e seguire questa procedura:
  
    ![Informazioni sull'accesso Single Sign-On per URL e dominio di Salesforce Sandbox](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url.png)

    Nella casella di testo **URL di accesso** digitare il valore usando il modello seguente: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > Questo valore deve essere copiato dal portale di Sandbox Salesforce dopo avere abilitato il dominio.

17. Nella sezione **Certificato di firma SAML** fare clic su **XML metadati federazione** e quindi salvare il file XML nel computer.

    ![Collegamento di download del certificato](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

18. Aprire una nuova scheda del browser e accedere all'account di amministratore di Salesforce Sandbox.

19. Fare clic su **Setup** (Configura) sotto l'**icona Settings** (Impostazioni) nell'angolo superiore destro della pagina.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/configure1.png)

20. Scorrere verso il basso fino alla voce **IMPOSTAZIONI** nel riquadro di spostamento a sinistra e fare clic su **Identità** per espandere la sezione corrispondente. Fare quindi clic su **Single Sign-On Settings**.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

21. Nella pagina **Single Sign-On Settings** fare clic su **Edit**.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/configure3.png)

22. Selezionare **SAML Enabled**, quindi fare clic su **Save**.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

23. Per configurare le impostazioni dell'accesso Single Sign-On SAML, fare clic su **New from Metadata File** (Nuovo da file di metadati).

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

24. Per caricare il file di metadati, fare clic su **Scegli file** e quindi scegliere **Crea**.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/xmlchoose.png)

25. Nella pagina **Impostazioni SAML Single Sign-On** i campi vengono popolati automaticamente. Digitare il nome della configurazione (ad esempio, *SPSSOWAAD_Test*) nella casella di testo **Nome** e quindi fare clic su Salva.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

26. Per abilitare il dominio su Salesforce Sandbox, eseguire la procedura seguente:

    > [!NOTE]
    > Prima di abilitare il dominio è necessario creare lo stesso in Salesforce Sandbox. Per altre informazioni, vedere [Definizione del nome di dominio](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US). Dopo aver creato il dominio, assicurarsi che sia configurato correttamente.

    * Nel pannello di navigazione sinistro in Salesforce Sandbox fare clic su **Company Settings** (Impostazioni aziendali) per espandere la sezione correlata e quindi fare clic su **My Domain** (Dominio personale).

         ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

    * Nella sezione **Configurazione autenticazione** fare clic su **Criterio di autenticazione**.

        ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

    * Nella sezione **Configurazione autenticazione**, per **Servizio di autenticazione** selezionare il nome dell'impostazione Single Sign-On SAML definita durante la configurazione SSO in Salesforce Sandbox e quindi fare clic su **Salva**.

        ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Creare un utente di Azure AD][100]

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Creazione di un utente test di Azure AD](./media/salesforce-sandbox-tutorial/create_aaduser_01.png) 

3. In Proprietà utente seguire questa procedura.

    ![Creazione di un utente test di Azure AD](./media/salesforce-sandbox-tutorial/create_aaduser_02.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare **Proprietà**, selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Selezionare **Create**.

### <a name="create-a-salesforce-sandbox-test-user"></a>Creare un utente di test di Salesforce Sandbox

In questa sezione si crea un utente di nome Britta Simon in Salesforce Sandbox. Salesforce Sandbox supporta il provisioning JIT (Just-In-Time) che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se un utente non esiste in Salesforce Sandbox, ne viene creato uno nuovo quando si tenta di accedere a Salesforce Sandbox. Salesforce Sandbox supporta anche il provisioning utenti automatico; [qui](salesforce-sandbox-provisioning-tutorial.md) è possibile trovare altre informazioni su come configurare il provisioning utenti automatico.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abilita Britta Simon per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a Salesforce Sandbox.

![Assegnare il ruolo utente][200]

**Per assegnare Britta Simon a Salesforce Sandbox, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Salesforce Sandbox**.

    ![Collegamento Salesforce Sandbox nell'elenco delle applicazioni](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Add User** (Aggiungi utente). Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Salesforce Sandbox nel pannello di accesso, verrà eseguito automaticamente l'accesso all'applicazione Salesforce Sandbox.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
* [Configura provisioning utenti](salesforce-sandbox-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/salesforce-sandbox-tutorial/tutorial_general_01.png
[2]: ./media/salesforce-sandbox-tutorial/tutorial_general_02.png
[3]: ./media/salesforce-sandbox-tutorial/tutorial_general_03.png
[4]: ./media/salesforce-sandbox-tutorial/tutorial_general_04.png

[100]: ./media/salesforce-sandbox-tutorial/tutorial_general_100.png

[200]: ./media/salesforce-sandbox-tutorial/tutorial_general_200.png
[201]: ./media/salesforce-sandbox-tutorial/tutorial_general_201.png
[202]: ./media/salesforce-sandbox-tutorial/tutorial_general_202.png
[203]: ./media/salesforce-sandbox-tutorial/tutorial_general_203.png
