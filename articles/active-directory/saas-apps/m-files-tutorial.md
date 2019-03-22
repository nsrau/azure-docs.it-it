---
title: 'Esercitazione: Integrazione di Azure Active Directory con M-Files | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e M-Files.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4536fd49-3a65-4cff-9620-860904f726d0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: jeedes
ms.openlocfilehash: 3dab784d706228793c6acc0f930f39ba1962caeb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57848984"
---
# <a name="tutorial-azure-active-directory-integration-with-m-files"></a>Esercitazione: Integrazione di Azure Active Directory con M-Files

Questa esercitazione descrive come integrare M-Files con Azure Active Directory (Azure AD).
L'integrazione di M-Files con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a M-Files.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a M-Files con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con M-Files, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di M-Files abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* M-Files supporta l'accesso SSO avviato da **SP**

## <a name="adding-m-files-from-the-gallery"></a>Aggiunta di M-Files dalla raccolta

Per configurare l'integrazione di M-Files in Azure AD, è necessario aggiungere M-Files dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere M-Files dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **M-Files**, selezionare **M-Files** nel pannello dei risultati e fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![M-Files nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con M-Files usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in M-Files.

Per configurare e testare l'accesso Single Sign-On di Azure AD con M-Files, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per M-Files](#configure-m-files-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di M-Files](#create-m-files-test-user)**: per avere una controparte di Britta Simon in M-Files collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con M-Files, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **M-Files** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di M-Files](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare un URL usando il modello seguente: `https://<tenantname>.cloudvault.m-files.com/authentication/MFiles.AuthenticationProviders.Core/sso`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL usando il modello seguente: `https://<tenantname>.cloudvault.m-files.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere tali valori, contattare il [team di supporto client di M-Files](mailto:support@m-files.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura M-Files** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-m-files-single-sign-on"></a>Configurare l'accesso Single Sign-On per M-Files

1. Per ottenere la configurazione dell'accesso Single Sign-On per l'applicazione, contattare il [team di supporto di M-Files](mailto:support@m-files.com) e fornire i metadati scaricati.
   
    >[!NOTE]
    >Se si desidera configurare l'accesso Single Sign-On per l'applicazione desktop M-Files, attenersi alla procedura seguente. Non sono necessarie operazioni aggiuntive se si desidera configurare l'accesso Single Sign-On per la versione Web di M-Files.  

1. Per configurare l'applicazione desktop M-Files in modo da abilitare l'accesso Single Sign-On con Azure AD attenersi alla procedura seguente. Per scaricare M-Files, passare alla pagina di [download di M-Files](https://www.m-files.com/en/download-latest-version).

1. Aprire la finestra **M-Files Desktop Settings** (Impostazioni M-Files Desktop). Fare quindi clic su **Aggiungi**.
   
    ![Configure Single Sign-On](./media/m-files-tutorial/tutorial_m_files_10.png)

1. Nella finestra **Document Vault Connection Properties** (Proprietà connessione insieme di credenziali del documento) eseguire le seguenti operazioni:
   
    ![Configure Single Sign-On](./media/m-files-tutorial/tutorial_m_files_11.png)  

    Nella sezione Server digitare i valori seguenti:  

    a. Per **Nome** digitare `<tenant-name>.cloudvault.m-files.com`. 
 
    b. Per **Numero porta** digitare **4466**. 

    c. Per **Protocollo** selezionare **HTTPS**. 

    d. Nel campo **Autenticazione** selezionare l'opzione relativa **Specific Windows user** (Utente specifico di Windows). Verrà visualizzata una pagina di accesso. Inserire le credenziali di Azure AD. 

    e. Per **Vault on Server** (Insieme di credenziali nel server) selezionare l'insieme di credenziali corrispondente nel server.
 
    f. Fare clic su **OK**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

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

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a M-Files.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **M-Files**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **M-Files**.

    ![Collegamento di M-Files nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-m-files-test-user"></a>Creare l'utente di test di M-Files

Questa sezione descrive come creare un utente chiamato Britta Simon in M-Files. Collaborare con il [team di supporto M-Files](mailto:support@m-files.com) per aggiungere gli utenti in M-Files.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di M-Files nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione M-Files per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

