---
title: 'Esercitazione: Integrazione di Azure Active Directory con Cezanne HR Software | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Cezanne HR Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 62b42e15-c282-492d-823a-a7c1c539f2cc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2017
ms.author: jeedes
ms.openlocfilehash: cf44d749ecbfcffb3d5a6e5e12aa49e66f7cde2e
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2017
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Esercitazione: Integrazione di Azure Active Directory con Cezanne HR Software

Questa esercitazione descrive come integrare Cezanne HR Software con Azure Active Directory (Azure AD).

L'integrazione di Cezanne HR Software con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Cezanne HR Software.
- È possibile abilitare gli utenti per l'accesso automatico a Cezanne HR Software (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Cezanne HR Software, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Cezanne HR Software abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Cezanne HR Software dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Aggiunta di Cezanne HR Software dalla raccolta
Per configurare l'integrazione di Cezanne HR Software in Azure AD, è necessario aggiungere Cezanne HR Software dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Cezanne HR Software dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Cezanne HR Software**, selezionare **Cezanne HR Software** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Cezanne HR Software nell'elenco risultati](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Cezanne HR Software con un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Cezanne HR Software che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Cezanne HR Software.

Per stabilire la relazione di collegamento, in Cezanne HR Software assegnare il valore di **nome utente** di Azure AD come valore dell'attributo **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Cezanne HR Software, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente test di Cezanne HR Software](#create-a-cezannehrsoftware-test-user)**: per avere una controparte di Britta Simon in Cezanne HR Software collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione nel portale di Azure viene abilitato l'accesso Single Sign-On di Azure AD, che viene configurato nell'applicazione Cezanne HR Software.

**Per configurare Single Sign-On di Azure AD con Cezanne HR Software, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Cezanne HR Software** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento per la configurazione dell'accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_samlbase.png)

3. Nella sezione **URL e dominio Cezanne HR Software** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Cezanne HR Software](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_url.png)

    a. Nella casella di testo **URL accesso** digitare l'URL: `https://w3.cezanneondemand.com/CezanneOnDemand/-/optyma`

    b. Nella casella di testo **Identificatore** digitare l'URL: `https://w3.cezanneondemand.com/CezanneOnDemand/`

    c. Nella casella di testo **URL di risposta** digitare l'URL: `https://w3.cezanneondemand.com:443/cezanneondemand/-/optyma/Saml/samlp`

4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva di Configura accesso Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di Cezanne HR Software** fare clic su **Configura Cezanne HR Software** per aprire la finestra **Configura accesso**. Copiare l'**ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configurazione di Cezanne HR Software](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure.png) 

7. In un'altra finestra del browser Web accedere al tenant Cezanne HR Software come amministratore.

8. Nella barra di spostamento a sinistra fare clic su **System Setup**(Configurazione di sistema). Passare a **Security Settings**(Impostazioni di sicurezza). Passare quindi a **Single Sign-On Configuration**(Configurazione Single Sign-On).

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

9. Nel pannello **Allow users to log in using the following Single Sign-On (SSO) Service** (Consenti agli utenti di accedere usando il servizio Single Sign-On (SSO) seguente) selezionare la casella **SAML 2.0** e quindi l'opzione **Advanced Configuration** (Configurazione avanzata).

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

10. Fare clic sul pulsante **Add New** (Aggiungi nuovo).

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

11. Seguire questa procedura nella sezione **SAML 2.0 IDENTITY PROVIDERS** (PROVIDER DI IDENTITÀ SAML 2.0).

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)
    
    a. Immettere il nome del provider di identità in **Display Name**(Nome visualizzato).

    b. Nella casella di testo **Entity Identifier** (Identificatore entità) incollare il valore **SAML Entity ID** (ID entità SAML) copiato dal portale di Azure. 

    c. Modificare il valore **SAML Binding** (Associazione SAML) in 'POST'.

    d. Nella casella di testo **Security Token Service Endpoint** (Endpoint servizio token di sicurezza) incollare il valore del campo **SAML Single Sign-on Service URL** (URL servizio Single Sign-On SAML) copiato dal portale di Azure.

    e. Nella casella di testo User ID Attribute Name (Nome attributo ID utente) inserire `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    f. Fare clic sull'icona **Upload** (Carica) per caricare il certificato scaricato dal portale di Azure.
    
    g. Fare clic sul pulsante **OK** . 

12. Fare clic sul pulsante **Salva** .

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-cezanne-hr-software-test-user"></a>Creare un utente test di Cezanne HR Software

Per consentire agli utenti di Azure AD di accedere a Cezanne HR Software, è necessario eseguirne il provisioning in Cezanne HR Software. Nel caso di Cezanne HR Software, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1.  Accedere al sito aziendale di Cezanne HR Software come amministratore.

2.  Nella barra di spostamento a sinistra fare clic su **System Setup**(Configurazione di sistema). Passare a **Gestisci utenti**. Passare quindi ad **Add New User**(Aggiungi nuovo utente).

    ![Nuovo utente](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Nuovo utente")

3.  Nella sezione **Person Details** (Dettagli persona) seguire questa procedura:

    ![Nuovo utente](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Nuovo utente")
    
    a. Impostare **Internal User** (Utente interno) su OFF.
    
    b. Digitare il nome dell'utente, ad esempio **Britta**, nella casella di testo **First Name** (Nome).  
 
    c. Digitare il nome dell'utente, ad esempio **Simon**, nella casella di testo **Last Name** (Cognome).
    
    d. Nella casella di testo **Email** digitare l'indirizzo e-mail dell'utente, ad esempio Brittasimon@contoso.com.

4.  Nella sezione **Account Information** (Informazioni account) seguire questa procedura:

    ![Nuovo utente](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Nuovo utente")
    
    a. Nella casella di testo **Username** (Nome utente) digitare l'indirizzo di posta elettronica di un utente, ad esempio Brittasimon@contoso.com.
    
    b. Nella casella di testo **Password** digitare la password dell'utente.
    
    c. Selezionare **HR Professional** (Professionista HR) come **Security Role** (Ruolo di sicurezza).
    
    d. Fare clic su **OK**.

5. Passare alla scheda **Single Sign-On** e selezionare **Add New** (Aggiungi nuovo) nell'area **SAML 2.0 Identifiers** (Identificatori SAML 2.0).

    ![Utente](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Utente")

6. Scegliere il provider di identità per **Identity Provider** (Provider di identità) e nella casella di testo **User Identifier** (Identificatore utente) immettere l'indirizzo di posta elettronica dell'account Britta Simon.

    ![Utente](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Utente")
    
7. Fare clic sul pulsante **Salva** .

    ![Utente](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Utente")

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a Cezanne HR Software.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Cezanne HR Software, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Cezanne HR Software**.

    ![Nell'elenco di applicazioni selezionare Cezanne HR Software](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Cezanne HR Software nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Cezanne HR Software.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_203.png

