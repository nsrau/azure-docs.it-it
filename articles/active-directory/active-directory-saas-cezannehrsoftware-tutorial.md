---
title: 'Esercitazione: Integrazione di Azure Active Directory con Cezanne HR Software | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Cezanne HR Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: fb8f95bf-c3c1-4e1f-b2b3-3b67526be72d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: 623c438edfce5f98c2d32d8bb25a97d86aa77909
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="tutorial-integrate-azure-active-directory-with-cezanne-hr-software"></a>Esercitazione: Eseguire l'integrazione di Azure Active Directory con Cezanne HR Software

Questa esercitazione descrive come integrare Cezanne HR Software con Azure Active Directory (Azure AD).

L'integrazione di Cezanne HR Software con Azure AD offre i vantaggi seguenti. È possibile:

- È possibile controllare in Azure AD chi può accedere a Cezanne HR Software.
- È possibile abilitare gli utenti per l'accesso automatico a Cezanne HR Software (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Cezanne HR Software, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Cezanne HR Software abilitata per l'accesso SSO

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, seguire queste indicazioni:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene testato l'accesso SSO di Azure AD in un ambiente di test. 

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

* Aggiunta di Cezanne HR Software dalla raccolta
* Configurazione e test dell'accesso Single Sign-On (SSO) di Microsoft Azure AD

## <a name="add-cezanne-hr-software-from-the-gallery"></a>Aggiungere Cezanne HR Software dalla raccolta
Per configurare l'integrazione di Cezanne HR Software in Azure AD, è necessario aggiungere Cezanne HR Software dalla raccolta all'elenco di app SaaS gestite.

Per aggiungere Cezanne HR Software dalla raccolta, seguire questa procedura:

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro. 

    ![Pulsante "Azure Active Directory"][1]

2. Selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Collegamento "Tutte le applicazioni"][2]
    
3. Per aggiungere una nuova applicazione, fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo **Tutte le applicazioni**.

    ![Pulsante "Nuova applicazione"][3]

4. Nella casella di ricerca, digitare **Cezanne HR Software**.

    ![Casella di ricerca](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_search.png)

5. Nell'elenco risultati selezionare **Cezanne HR Software** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Elenco risultati](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Cezanne HR Software usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Cezanne HR Software corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Cezanne HR Software.

Per stabilire la relazione di collegamento, in Cezanne HR Software assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso SSO di Azure AD con Cezanne HR Software, è necessario completare le procedure di base seguenti.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

In questa sezione è possibile abilitare l'accesso SSO di Azure AD nel portale di Azure e configurare l'accesso SSO nell'applicazione Cezanne HR Software seguendo questa procedura:

1. Nella pagina di integrazione dell'applicazione **Cezanne HR Software** del portale di Azure fare clic su **Single Sign-On**.

    ![Comando "Single Sign-On"][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso SSO.
 
    ![Casella "Modalità"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_samlbase.png)

3. In **URL e dominio Cezanne HR Software** seguire questa procedura:

    ![Sezione "URL e dominio Cezanne HR Software"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_url.png)

    a. Nella casella **URL di accesso** digitare un URL con la sintassi seguente: `https://w3.cezanneondemand.com/cezannehr/-/<tenant id>`

    b. Nella casella **URL di risposta** digitare un URL con la sintassi seguente: `https://w3.cezanneondemand.com:443/<tenantid>`    
     
    > [!NOTE] 
    > I valori precedenti non sono valori reali. Aggiornarli con l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Cezanne HR Software](mailto:info@cezannehr.com).

4. In **Certificato di firma SAML** selezionare **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Sezione "Certificato di firma SAML"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_certificate.png) 

5. Selezionare **Salva**.

    ![Pulsante "Salva"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_400.png)
    
6. In **Configurazione di Cezanne HR Software** selezionare **Configura Cezanne HR Software** per aprire la finestra **Configura accesso**. Copiare l'**ID di entità SAML** e l'**URL del servizio Single Sign-On SAML** dalla **sezione di riferimento rapido**.

    ![Sezione "Configurazione di Cezanne HR Software"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure.png) 

7. In un'altra finestra del Web browser accedere al tenant Cezanne HR Software come amministratore.

8. Nel riquadro sinistro selezionare **System Setup** (Configurazione di sistema). Selezionare **Security Settings** > **Single Sign-On Configuration** (Impostazioni di sicurezza > Configurazione Single Sign-on).

    ![Collegamento per la configurazione Single Sign-on](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

9. Nel riquadro **Allow users to log in using the following Single Sign-On (SSO) Service** (Consenti agli utenti di accedere usando il servizio Single Sign-On (SSO) seguente) selezionare la casella di controllo **SAML 2.0** e quindi l'opzione **Advanced Configuration** (Configurazione avanzata).

    ![Opzioni dei servizi Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

10. Selezionare **Add New** (Aggiungi nuovo).

    ![Pulsante "Add New" (Aggiungi nuovo)](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

11. In **SAML 2.0 Identity Providers** (Provider di identità SAML 2.0) seguire questa procedura:

    ![Sezione relativa ai provider di identità SAML 2.0](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)
    
    a. Nella casella **Display Name**(Nome visualizzato) immettere il nome del provider di identità.

    b. Nella casella **Entity Identifier** (Identificatore entità) incollare l'**ID di entità SAML** copiato dal portale di Azure. 

    c. Nella casella di riepilogo **SAML Binding** (Associazione SAML) selezionare **POST**.

    d. Nella casella **Security Token Service Endpoint** (Endpoint del servizio token di sicurezza) incollare l'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure. 
    
    e. Nella casella **User ID Attribute Name** (Nome attributo ID utente) immettere `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    f. Per caricare il certificato scaricato da Azure AD, fare clic sul pulsante **Upload** (Carica).
    
    g. Selezionare **OK**. 

12. Selezionare **Salva**.

    ![Pulsante "Salva"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

> [!TIP]
> Durante la configurazione dell'app, nel [portale di Azure](https://portal.azure.com) è disponibile un riepilogo delle istruzioni precedenti. Dopo aver aggiunto l'app dalla sezione **Active Directory** > **Applicazioni aziendali**, selezionare la scheda **Single Sign-On**. Accedere quindi alla documentazione incorporata tramite la sezione **Configurazione**. 

Per altre informazioni sulla funzione di documentazione incorporata vedere [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
In questa sezione viene creato un utente di test di nome Britta Simon nel portale di Azure.

![Utente di test Britta Simon][100]

Per creare un utente di test in Azure AD, seguire questa procedura:

1. Nel **portale di Azure** fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante "Azure Active Directory"](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_01.png) 

2. Per visualizzare l'elenco di utenti, selezionare **Utenti e gruppi** > **Tutti gli utenti**.
    
    ![Collegamento "Tutti gli utenti"](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_02.png) 
    
    Verrà visualizzata la finestra di dialogo **Tutti gli utenti**.

3. Per aprire la finestra di dialogo **Utente**, fare clic su **Aggiungi**.
 
    ![Pulsante "Aggiungi"](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png) 

4. Nella finestra di dialogo **Utente** seguire questa procedura:
 
    ![Finestra di dialogo "Utente"](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png) 

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'**indirizzo e-mail** dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore generato nella casella **Password**.

    d. Selezionare **Crea**.
 
### <a name="create-a-cezanne-hr-software-test-user"></a>Creare un utente di test di Cezanne HR Software

Per consentire agli utenti di Azure AD di accedere a Cezanne HR Software, è necessario effettuarne il provisioning in Cezanne HR Software. Nel caso di Cezanne HR Software, il provisioning è un'attività manuale.

Per effettuare il provisioning di un account utente, seguire questa procedura:

1.  Accedere al sito aziendale di Cezanne HR software come amministratore.

2.  Nel riquadro sinistro selezionare **System Setup** > **Manage Users** > **Add New User** (Configurazione di sistema > Gestisci utenti > Aggiungi nuovo utente).

    ![Collegamento per l'aggiunta di un nuovo utente](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Nuovo utente")

3.  In **Person Details** (Dettagli persona) seguire questa procedura:

    ![Sezione relativa ai dettagli persona](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Nuovo utente")
    
    a. Impostare **Internal User** (Utente interno) su **OFF**.
    
    b. Nella casella **First Name** (Nome) digitare il nome dell'utente, ad esempio **Britta**.  
 
    c. Nella casella **Last Name** (Cognome) digitare il cognome dell'utente, ad esempio **Simon**.
    
    d. Nella casella **E-mail** (Posta elettronica) digitare l'indirizzo e-mail dell'utente, ad esempio Brittasimon@contoso.com.

4.  In **Account Information** (Informazioni account) seguire questa procedura:

    ![Sezione relativa alle informazioni account](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Nuovo utente")
    
    a. Nella casella **Username** (Nome utente) digitare l'indirizzo e-mail dell'utente, ad esempio Brittasimon@contoso.com.
    
    b. Nella casella **Password** digitare la password dell'utente.
    
    c. Nella casella **Security Role** (Ruolo di sicurezza) selezionare **HR Professional** (Professionista HR).
    
    d. Selezionare **OK**.

5. Nella scheda **Single Sign-On** selezionare **Add New** (Aggiungi nuovo) nella sezione **SAML 2.0 Identifiers** (Identificatori SAML 2.0).

    ![Pulsante "Add New" (Aggiungi nuovo)](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Utente")

6. Nella casella di riepilogo **Identity Provider** (Provider di identità) selezionare il provider di identità. Nella casella **User Identifier** (Identificatore utente) immettere l'indirizzo e-mail dell'account dell'utente di test Britta Simon.

    ![Caselle relative a provider di identità e ID utente](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Utente")
    
7. Selezionare **Salva**.

    ![Pulsante di salvataggio](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Utente")

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Cezanne HR Software.

![Accesso utente di test][200] 

1. Nel portale di Azure aprire la visualizzazione applicazioni e quindi passare alla visualizzazione directory. Selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Collegamento "Tutte le applicazioni"][201] 

2. Nell'elenco di applicazioni selezionare **Cezanne HR Software**.

    ![Elenco "Applicazioni"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegnare utenti][202] 

4. Selezionare **Aggiungi**. Nella finestra di dialogo **Aggiungi assegnazione** selezionare quindi **Utenti e gruppi**.

    ![Collegamento "Utenti e gruppi"][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco **Utenti**.

6. Nella finestra di dialogo **Utenti e gruppi** fare clic su **Seleziona**.

7. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.
    
### <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso SSO di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Cezanne HR Software nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Cezanne HR Software.

## <a name="next-steps"></a>Passaggi successivi

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

