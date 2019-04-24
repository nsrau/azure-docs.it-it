---
title: 'Esercitazione: integrazione di Azure Active Directory con Adaptive Insights | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Adaptive Insights.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d42c86ec262cd9d3d3db3035d252429e44c1208f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60285640"
---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-insights"></a>Esercitazione: integrazione di Azure Active Directory con Adaptive Insights

Questa esercitazione descrive come integrare Adaptive Insights con Azure Active Directory (Azure AD).

L'integrazione di Adaptive Insights con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere ad Adaptive Insights.
- È possibile abilitare gli utenti per l'accesso automatico ad Adaptive Insights (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Adaptive Insights, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Adaptive Insights abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Adaptive Insights dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-adaptive-insights-from-the-gallery"></a>Aggiunta di Adaptive Insights dalla raccolta
Per configurare l'integrazione di Adaptive Insights in Azure AD, è necessario aggiungere Adaptive Insights dalla raccolta all'elenco delle applicazioni SaaS gestite.

**Per aggiungere Adaptive Insights dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![image](./media/adaptivesuite-tutorial/selectazuread.png)

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![image](./media/adaptivesuite-tutorial/a_select_app.png)
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![image](./media/adaptivesuite-tutorial/a_new_app.png)

4. Nella casella di ricerca, digitare **Adaptive Insights**, selezionare **Adaptive Insights** dal pannello risultati, quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Adaptive Insights usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Adaptive Insights che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Adaptive Insights.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Adaptive Insights, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Adaptive Insights](#create-an-adaptive-insights-test-user)**: per avere una controparte di Britta Simon in Adaptive Insights collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Adaptive Insights.

**Per configurare l'accesso Single Sign-On di Azure AD con Adaptive Insights, seguire questa procedura:**

1. Nel [portale di Azure](https://portal.azure.com/), nella pagina di integrazione dell'applicazione **Adaptive Insights** selezionare **Single Sign-On**.

    ![image](./media/adaptivesuite-tutorial/B1_B2_Select_SSO.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML** per abilitare l'accesso Single Sign-On.

    ![image](./media/adaptivesuite-tutorial/b1_b2_saml_sso.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![image](./media/adaptivesuite-tutorial/b1-domains_and_urlsedit.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_url.png)

    a. Nella casella di testo **Identificatore (ID entità)** digitare un URL usando il criterio seguente: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    >[!NOTE]
    > È possibile ottenere valori dell'identificatore (ID entità) e l'URL di risposta dalla pagina **Impostazioni SSO SAML** di Adaptive Insights.
 
5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML** fare clic su **Scarica** per scaricare il file **Certificato (Base64)** e salvarlo nel computer in uso.

    ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_certficate.png) 

6. Nella sezione **Configura Adaptive Insights** copiare l'URL appropriato in base alle proprie esigenze.

    La descrizione dell'URL potrebbe essere:

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

    ![image](./media/adaptivesuite-tutorial/d1_samlsonfigure.png) 

7. In un'altra finestra del Web browser accedere al sito aziendale di Adaptive Insights come amministratore.

8. Passare alla pagina **Admin**.

    ![Amministratore](./media/adaptivesuite-tutorial/IC805644.png "Amministratore")

9. Nella sezione **Users and Roles** (Utenti e ruoli) fare clic su **Manage SAML SSO Settings** (Gestisci impostazioni SSO SAML).

    ![Gestire le impostazioni SSO SAML](./media/adaptivesuite-tutorial/IC805645.png "Gestire le impostazioni SSO SAML")

10. Nella pagina **SAML SSO Settings** eseguire queste operazioni:

    ![Impostazioni SSO SAML](./media/adaptivesuite-tutorial/IC805646.png "Impostazioni SSO SAML")

    a. Nella casella di testo **Identity provider name** digitare un nome per la configurazione.

    b. Incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure nella casella di testo **Identity provider Entity ID** (ID entità del provider di identità).

    c. Incollare il valore di **URL di accesso** copiato dal portale di Azure nella casella di testo **Identity provider SSO URL** (URL SSO del provider di identità).

    d. Incollare il valore di **URL disconnessione** copiato dal portale di Azure nella casella di testo **Custom logout URL** (Personalizza URL di disconnessione).

    e. Per caricare il certificato scaricato, fare clic su **Scegli file**.

    f. Selezionare le opzioni seguenti per:

    * **SAML user id** (ID utente SAML), selezionare **User's Adaptive Insights user name** (Nome utente dell'utente di Adaptive Insights).

    * **SAML user id location** (Posizione ID utente SAML), selezionare **User id in NameID of Subject** (ID utente in NameID of Subject).

    * **SAML NameID format** (Formato NameID SAML), selezionare **Email address** (Indirizzo di posta elettronica).

    * **Abilita SAML** selezionare **Allow SAML SSO and direct Adaptive Insights login** (Consenti SSO SAML e accesso diretto ad Adaptive Insights).

    g. Copiare l'**UR SSO di Adaptive Insights** e incollarlo nelle caselle di testo **Identificatore (ID entità)** e **URL di risposta** nella sezione **URL e dominio di Adaptive Insights** nel portale di Azure.

    h. Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![image](./media/adaptivesuite-tutorial/d_users_and_groups.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![image](./media/adaptivesuite-tutorial/d_adduser.png)

3. In Proprietà utente seguire questa procedura.

    ![image](./media/adaptivesuite-tutorial/d_userproperties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon\@dominioaziendale.estensione**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare **Proprietà**, selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Selezionare **Create**.
 
### <a name="create-an-adaptive-insights-test-user"></a>Creare un utente di test di Adaptive Insights

Per consentire agli utenti di Azure AD di accedere ad Adaptive Insights, è necessario eseguirne il provisioning in Adaptive Insights. Nel caso di Adaptive Insights, il provisioning è un'attività manuale.

**Per configurare il provisioning utenti, seguire questa procedura:** 

1. Accedere al sito aziendale di **Adaptive Insights** come amministratore.
2. Passare alla pagina **Admin**.

   ![Amministratore](./media/adaptivesuite-tutorial/IC805644.png "Amministratore")

3. Nella sezione **Users and Roles** (Utenti e ruoli) fare clic su **Add User** (Aggiungi utente).

   ![Aggiungere un utente](./media/adaptivesuite-tutorial/IC805648.png "Aggiungere un utente")
   
4. Nella sezione **Nuovo utente** seguire questa procedura:

   ![Invio](./media/adaptivesuite-tutorial/IC805649.png "Invio")

   a. Nelle caselle di testo corrispondenti **Name**, **Login**, **Email**, **Password** digitare il nome, l'account di accesso, l'indirizzo di posta elettronica e la password di un account utente Azure Active Directory valido di cui si vuole eseguire il provisioning.

   b. Selezionare un valore in **Role**.

   c. Fare clic su **Submit**.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Adaptive Insights per eseguire il provisioning degli account utente di Azure AD.
>

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Adaptive Insights.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![image](./media/adaptivesuite-tutorial/d_all_applications.png)

2. Nell'elenco delle applicazioni selezionare **Adaptive Insights**.

    ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_app.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![image](./media/adaptivesuite-tutorial/d_leftpaneusers.png)

4. Selezionare il pulsante **Aggiungi** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![image](./media/adaptivesuite-tutorial/d_assign_user.png)

4. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

5. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Adaptive Insights nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Adaptive Insights.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
