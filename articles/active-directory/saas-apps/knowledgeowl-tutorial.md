---
title: 'Esercitazione: Esercitazione: Integrazione di Azure Active Directory con KnowledgeOwl | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e KnowledgeOwl.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2ae30996-864d-4872-90bc-f770e1ea159a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: 80d8d1664100cdfbbf6d537b41823c2db0204040
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2019
ms.locfileid: "54318918"
---
# <a name="tutorial-azure-active-directory-integration-with-knowledgeowl"></a>Esercitazione: Integrazione di Azure Active Directory con KnowledgeOwl

Questa esercitazione descrive come integrare KnowledgeOwl con Azure Active Directory (Azure AD).

L'integrazione di KnowledgeOwl con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a KnowledgeOwl.
- È possibile abilitare gli utenti per l'accesso automatico a KnowledgeOwl (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con KnowledgeOwl, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di KnowledgeOwl abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di KnowledgeOwl dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-knowledgeowl-from-the-gallery"></a>Aggiunta di KnowledgeOwl dalla raccolta
Per configurare l'integrazione di KnowledgeOwl in Azure AD, è necessario aggiungere KnowledgeOwl dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere KnowledgeOwl dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

1. Nella casella di ricerca digitare **KnowledgeOwl**, selezionare **KnowledgeOwl** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![KnowledgeOwl nell'elenco risultati](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con KnowledgeOwl usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di KnowledgeOwl che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in KnowledgeOwl.

Per configurare e testare l'accesso Single Sign-On di Azure AD con KnowledgeOwl, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creare un utente di test di KnowledgeOwl](#create-a-knowledgeowl-test-user)**: per avere una controparte di Britta Simon in KnowledgeOwl collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione KnowledgeOwl.

**Per configurare Single Sign-On di Azure AD con KnowledgeOwl, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **KnowledgeOwl** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_samlbase.png)

1. Nella sezione **URL e dominio KnowledgeOwl** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di KnowledgeOwl](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_url.png)

    a. Nella casella di testo **Identificatore (ID entità)** digitare un URL usando il criterio seguente:
    |||
    |-|-|
    | `https://app.knowledgeowl.com/sp`|
    | `https://app.knowledgeowl.com/sp/id/<unique ID>`|
    |||

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente:
    |||
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|
    |||

1. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di KnowledgeOwl](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_url1.png)

    Nella casella di testo **URL di accesso** digitare un URL usando il criterio seguente:
    |||
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|
    |||
     
    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli in seguito con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. La procedura è descritta più avanti nell'esercitazione.

1. L'applicazione KnowledgeOwl prevede le asserzioni SAML in un formato specifico. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione "**Attributi utente**" nella pagina di integrazione dell'applicazione.

    ![Configure Single Sign-On](./media/knowledgeowl-tutorial/attribute.png)

1. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come indicato nell'immagine precedente ed eseguire i passaggi descritti di seguito:
    
    | Nome attributo | Valore attributo | Spazio dei nomi|
    | ------------------- | -------------------- | -----|
    | ssoid | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    
    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.
    
    ![Configure Single Sign-On](./media/knowledgeowl-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/knowledgeowl-tutorial/tutorial_attribute_05.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.
    
    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.

    d. Nell'elenco **Spazio dei nomi** immettere il valore dello spazio dei nomi indicato per la riga.
    
    e. Fare clic su **OK**.

1. Nella sezione **Certificato di firma SAML** fare clic su **Certificate (Raw)** (Certificato (base)) e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/knowledgeowl-tutorial/tutorial_general_400.png)
    
1. Nella sezione **Configurazione di KnowledgeOwl** fare clic su **Configura KnowledgeOwl** per aprire la finestra **Configura accesso**. Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configurazione di KnowledgeOwl](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_configure.png)

1. In un'altra finestra del Web browser accedere al sito aziendale di KnowledgeOwl come amministratore.

1. Fare clic su **Settings** (Impostazioni) e quindi selezionare **Security** (Sicurezza).

    ![Configurazione di KnowledgeOwl](./media/knowledgeowl-tutorial/configure1.png)

1. Scorrere verso il basso fino a **SAML SSO Integration** (Integrazione SSO SAML) e seguire questa procedura:
    
    ![Configurazione di KnowledgeOwl](./media/knowledgeowl-tutorial/configure2.png)

    a. Selezionare **Enable SAML SSO** (Abilita SSO SAML).

    b. Copiare il valore di **SP Entity ID** (ID entità del provider di servizi) e incollarlo in **Identificatore (ID entità)** nella sezione **URL e dominio KnowledgeOwl** del portale di Azure.

    c. Copiare il valore di **SP Login URL** (URL di accesso del provider di servizi) e incollarlo nelle caselle di testo **URL di accesso e URL di risposta** nella sezione **URL e dominio KnowledgeOwl** del portale di Azure.

    d. Nella casella di testo **IdP entityID** (ID entità IdP) incollare il valore di **ID entità SAML** copiato dal portale di Azure.

    e. Nella casella di testo **IDP Login URL** (URL di accesso IdP) incollare il valore di **SAML Single Sign-On Service URL** (URL servizio Single Sign-On SAML) copiato dal portale di Azure.

    f. Nella casella di testo **IdP Logout URL** (URL di disconnessione IdP) incollare il valore di **Sign-Out URL** (URL di disconnessione) copiato dal portale di Azure

    g. Caricare il certificato scaricato dal portale di Azure facendo clic su **Upload IdP Certificate** (Carica certificato IdP).

    h. Fare clic su **Map SAML Attributes** (Esegui il mapping degli attributi SAML) per eseguire il mapping degli attributi e seguire questa procedura:
    
    ![Configurazione di KnowledgeOwl](./media/knowledgeowl-tutorial/configure3.png)

    * Immettere `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid` nella casella di testo **SSO ID** (ID SSO)
    * Immettere `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` nella casella di testo **Username/Email** (Nome utente/indirizzo di posta elettronica).
    * Immettere `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` nella casella di testo **First Name** (Nome).
    * Immettere `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` nella casella di testo **Last Name** (Cognome).
    * Fare clic su **Save**

    i. Fare clic su **Save** nella parte inferiore della pagina.

    ![Configurazione di KnowledgeOwl](./media/knowledgeowl-tutorial/configure4.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/knowledgeowl-tutorial/create_aaduser_01.png)

1. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/knowledgeowl-tutorial/create_aaduser_02.png)

1. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/knowledgeowl-tutorial/create_aaduser_03.png)

1. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/knowledgeowl-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="create-a-knowledgeowl-test-user"></a>Creare un utente di test di KnowledgeOwl

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in KnowledgeOwl. KnowledgeOwl supporta il provisioning JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se ancora non esiste un nuovo utente, viene creato durante un tentativo di accesso a KnowledgeOwl.
>[!Note]
>Per creare un utente manualmente, è necessario contattare il  [team di supporto di KnowledgeOwl](mailto:support@knowledgeowl.com).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a KnowledgeOwl.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a KnowledgeOwl, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco di applicazioni selezionare **KnowledgeOwl**.

    ![Collegamento di KnowledgeOwl nell'elenco delle applicazioni](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_app.png)  

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro KnowledgeOwl nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione KnowledgeOwl.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/knowledgeowl-tutorial/tutorial_general_01.png
[2]: ./media/knowledgeowl-tutorial/tutorial_general_02.png
[3]: ./media/knowledgeowl-tutorial/tutorial_general_03.png
[4]: ./media/knowledgeowl-tutorial/tutorial_general_04.png

[100]: ./media/knowledgeowl-tutorial/tutorial_general_100.png

[200]: ./media/knowledgeowl-tutorial/tutorial_general_200.png
[201]: ./media/knowledgeowl-tutorial/tutorial_general_201.png
[202]: ./media/knowledgeowl-tutorial/tutorial_general_202.png
[203]: ./media/knowledgeowl-tutorial/tutorial_general_203.png

