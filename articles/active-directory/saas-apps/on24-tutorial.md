---
title: 'Esercitazione: Integrazione di Azure Active Directory con ON24 Virtual Environment SAML Connection | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e ON24 Virtual Environment SAML Connection.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d4028fb5-b2ad-4c5d-b123-7b675c509d64
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: jeedes
ms.openlocfilehash: 1ec18f0013a7fa640395a8b8bedd9df8b0924c3a
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "49071062"
---
# <a name="tutorial-azure-active-directory-integration-with-on24-virtual-environment-saml-connection"></a>Esercitazione: Integrazione di Azure Active Directory con ON24 Virtual Environment SAML Connection

Questa esercitazione descrive come integrare ON24 Virtual Environment SAML Connection con Azure Active Directory (Azure AD).

L'integrazione di ON24 Virtual Environment SAML Connection con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a ON24 Virtual Environment SAML Connection.
- È possibile abilitare gli utenti per l'accesso automatico a ON24 Virtual Environment SAML Connection (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con ON24 Virtual Environment SAML Connection, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione abilitata per l'accesso Single Sign-On a ON24 Virtual Environment SAML Connection

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di ON24 Virtual Environment SAML Connection dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-on24-virtual-environment-saml-connection-from-the-gallery"></a>Aggiunta di ON24 Virtual Environment SAML Connection dalla raccolta
Per configurare l'integrazione di ON24 Virtual Environment SAML Connection in Azure AD, è necessario aggiungere ON24 Virtual Environment SAML Connection dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere ON24 Virtual Environment SAML Connection dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![image](./media/on24-tutorial/selectazuread.png)

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![image](./media/on24-tutorial/a_select_app.png)
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![image](./media/on24-tutorial/a_new_app.png)

4. Nella casella di ricerca, digitare **ON24 Virtual Environment SAML Connection**, selezionare **ON24 Virtual Environment SAML Connection** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![image](./media/on24-tutorial/tutorial_on24_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con ON24 Virtual Environment SAML Connection mediante un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere quale utente di ON24 Virtual Environment SAML Connection corrisponde a un determinato utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in ON24 Virtual Environment SAML Connection.

Per configurare e testare l'accesso Single Sign-On di Azure AD con ON24 Virtual Environment SAML Connection è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di ON24 Virtual Environment SAML Connection](#create-an-on24-virtual-environment-saml-connection-test-user)**: per avere una controparte di Britta Simon in ON24 Virtual Environment SAML Connection collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione ON24 Virtual Environment SAML Connection.

**Per configurare l'accesso Single Sign-On di Azure AD con ON24 Virtual Environment SAML Connection, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **ON24 Virtual Environment SAML Connection** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![image](./media/on24-tutorial/B1_B2_Select_SSO.png)

2. Fare clic su **Modifica modalità Single Sign-On** nella parte superiore della schermata per selezionare la modalità **SAML**.

      ![image](./media/on24-tutorial/b1_b2_saml_ssso.png)

3. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML** per abilitare il Single Sign-On.

    ![image](./media/on24-tutorial/b1_b2_saml_sso.png)

4. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![image](./media/on24-tutorial/b1-domains_and_urlsedit.png)

5. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![image](./media/on24-tutorial/tutorial_on24_url.png)

    a. Nella casella di testo **Identificatore** digitare un URL:

     **URL ambiente di produzione**
    
    `SAML-VSHOW.on24.com`

    `SAML-Gateway.on24.com` 

    `SAP PROD SAML-EliteAudience.on24.com` 
                
     **URL ambiente di controllo di qualità**
    
    `SAMLQA-VSHOW.on24.com` 

    `SAMLQA-Gateway.on24.com` 

    `SAMLQA-EliteAudience.on24.com`
 
    b. Nella casella di testo **URL di risposta** digitare un URL:
    
     **URL ambiente di produzione**
    
    `https://federation.on24.com/sp/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1WU2hvdy5vbjI0LmNvbSJ9/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1HYXRld2F5Lm9uMjQuY29tIn0/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1FbGl0ZUF1ZGllbmNlLm9uMjQuY29tIn0/ACS.saml2`

     **URL ambiente di controllo di qualità**
    
    `https://qafederation.on24.com/sp/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLVZzaG93Lm9uMjQuY29tIn0/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUdhdGV3YXkub24yNC5jb20ifQ/ACS.saml2`
     
    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUVsaXRlQXVkaWVuY2Uub24yNC5jb20ifQ/ACS.saml2` 

    c. Fare clic su **Impostare URL aggiuntivi**. 

    d. Nella casella di testo **Stato dell'inoltro** digitare un URL: `https://vshow.on24.com/vshow/ms_azure_saml_test?r=<ID>`

    e. Se si vuole configurare l'applicazione in modalità avviata da **SP**,nella sezione **URL di accesso** digitare un URL: `https://vshow.on24.com/vshow/<INSTANCENAME>`

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il certificato appropriato in base ai propri requisiti e salvarlo nel computer in uso.

    ![image](./media/on24-tutorial/tutorial_on24_certificate.png) 

7. Per configurare l'accesso Single Sign-On sul lato **ON24 Virtual Environment SAML Connection**, è necessario inviare il certificato/i metadati scaricati dal portale di Azure al [team di supporto di ON24 Virtual Environment SAML Connection](https://www.on24.com/about-us/support/). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel portale di Azure, nel riquadro sinistro, selezionare **Azure Active Directory**, selezionare **Utenti** e quindi selezionare **Tutti gli utenti**.

    ![image](./media/on24-tutorial/d_users_and_groups.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![image](./media/on24-tutorial/d_adduser.png)

3. In Proprietà utente seguire questa procedura.

    ![image](./media/on24-tutorial/d_userproperties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare **Proprietà**, selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Selezionare **Create**.
 
### <a name="create-an-on24-virtual-environment-saml-connection-test-user"></a>Creare un utente di test di ON24 Virtual Environment SAML Connection

In questa sezione viene creato un utente chiamato Britta Simon in ON24 Virtual Environment SAML Connection. Collaborare con il [team di supporto clienti di ON24 Virtual Environment SAML Connection](https://www.on24.com/about-us/support/) per aggiungere gli utenti alla piattaforma ON24 Virtual Environment SAML Connection. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a ON24 Virtual Environment SAML Connection.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![image](./media/on24-tutorial/d_all_applications.png)

2. Nell'elenco delle applicazioni selezionare **ON24 Virtual Environment SAML Connection**.

    ![image](./media/on24-tutorial/tutorial_on24_app.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![image](./media/on24-tutorial/d_leftpaneusers.png)

4. Fare clic sul pulsante **Aggiungi** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![image](./media/on24-tutorial/d_assign_user.png)

4. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

5. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro ON24 Virtual Environment SAML Connection nel Pannello di accesso, si dovrebbe accedere automaticamente all'applicazione ON24 Virtual Environment SAML Connection.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

