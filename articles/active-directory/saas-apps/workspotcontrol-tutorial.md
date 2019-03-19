---
title: 'Esercitazione: integrazione di Azure Active Directory con Workspot Control | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Workspot Control.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3ea8e4e9-f61f-4f45-b635-b0e306eda3d1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97f375c6f48d3dc497eb59e76f19fc64cf906b56
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57886508"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>Esercitazione: integrazione di Azure Active Directory con Workspot Control

Questa esercitazione descrive come integrare Workspot Control con Azure Active Directory (Azure AD).

L'integrazione di Workspot Control con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Workspot Control.
- È possibile abilitare gli utenti per l'accesso automatico a Workspot Control (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Workspot Control, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Workspot Control abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Workspot Control dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-workspot-control-from-the-gallery"></a>Aggiunta di Workspot Control dalla raccolta
Per configurare l'integrazione di Workspot Control in Azure AD, è necessario aggiungere Workspot Control dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Workspot Control dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![image](./media/workspotcontrol-tutorial/selectazuread.png)

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![image](./media/workspotcontrol-tutorial/a_select_app.png)
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![image](./media/workspotcontrol-tutorial/a_new_app.png)

4. Nella casella di ricerca digitare **Workspot Control**, selezionare **Workspot Control** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Workspot Control usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Workspot Control corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Workspot Control.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Workspot Control, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Workspot Control](#create-a-workspot-control-test-user)**: per avere una controparte di Britta Simon in Workspot Control collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Workspot Control.

**Per configurare Single Sign-On di Azure AD con Workspot Control, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Workspot Control** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![image](./media/workspotcontrol-tutorial/B1_B2_Select_SSO.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML** per abilitare l'accesso Single Sign-On.

    ![image](./media/workspotcontrol-tutorial/b1_b2_saml_sso.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![image](./media/workspotcontrol-tutorial/b1-domains_and_urlsedit.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_url.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<INSTANCENAME>-saml.workspot.com/saml/metadata`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<INSTANCENAME>-saml.workspot.com/saml/assertion`

    c. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

     ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_url1.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<INSTANCENAME>-saml.workspot.com/`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere tali valori, contattare il [team di supporto clienti di Workspot Control](mailto:support@workspot.com). 

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML** fare clic su **Scarica** per scaricare il file **Certificato (Base64)** e salvarlo nel computer in uso.

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_certficate.png) 

6. Nella sezione **Configura Workspot Control** copiare l'URL appropriato in base alle proprie esigenze.

    La descrizione dell'URL potrebbe essere:

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

    ![image](./media/workspotcontrol-tutorial/d1_samlsonfigure.png) 

7. In un'altra finestra del Web browser accedere a Workspot Control come amministratore della sicurezza.

8. Sulla barra degli strumenti nella parte superiore della pagina fare clic su  **Setup** e quindi passare a  **SAML**.

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

9. Nella pagina **Security Assertion Markup Language Configuration** (Configurazione di Security Assertion Markup Language) seguire questa procedura:
 
    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    a. Nella casella di testo **Entity ID** (ID entità) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.   

    b. Nella casella di testo **Signon Service URL** (URL del servizio di accesso) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    c. Nella casella di testo **Logout Service URL** (URL servizio di disconnessione) incollare il valore di **URL disconnessione** copiato dal portale di Azure. 

    d. Fare clic sul pulsante **Update File** (Aggiorna file) per caricare il certificato codificato in base 64 scaricato dal portale di Azure nel certificato X.509.

    e. Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![image](./media/workspotcontrol-tutorial/d_users_and_groups.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![image](./media/workspotcontrol-tutorial/d_adduser.png)

3. In Proprietà utente seguire questa procedura.

    ![image](./media/workspotcontrol-tutorial/d_userproperties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel **nome utente** tipo di campo **brittasimon\@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare **Proprietà**, selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Selezionare **Create**.
 
### <a name="create-a-workspot-control-test-user"></a>Creare un utente di test di Workspot Control

Per consentire agli utenti di Azure AD di accedere a Workspot Control, è necessario effettuarne il provisioning in Workspot Control. Nel caso di Workspot Control il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere a Workspot Control come amministratore della sicurezza.

2. Sulla barra degli strumenti nella parte superiore della pagina fare clic su  **Utenti** e quindi passare a  **Aggiungi utente**.

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. Nella pagina **Add a New User** (Aggiungi un nuovo utente) seguire questa procedura:

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    a. Nella casella di testo **First Name** (Nome) immettere il nome dell'utente, ad esempio **Britta**.

    b. Nella casella di testo **Last name** (Cognome) immettere il cognome dell'utente, ad esempio **Simon**.

    c. Nelle **messaggio di posta elettronica** testo immettere l'indirizzo di posta elettronica dell'utente, ad esempio **Brittasimon\@contoso.com**.

    d. Selezionare il ruolo utente appropriato nell'elenco a discesa **Role** (Ruolo).

    e. Selezionare il gruppo utenti appropriato nell'elenco a discesa **Group** (Gruppo).

    f. Fare clic su **Add User**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Workspot Control.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![image](./media/workspotcontrol-tutorial/d_all_applications.png)

2. Nell'elenco di applicazioni selezionare **Workspot Control**.

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_app.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![image](./media/workspotcontrol-tutorial/d_leftpaneusers.png)

4. Selezionare il pulsante **Aggiungi** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![image](./media/workspotcontrol-tutorial/d_assign_user.png)

4. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

5. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Workspot Control nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Workspot Control.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
