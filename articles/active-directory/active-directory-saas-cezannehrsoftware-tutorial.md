---
title: 'Esercitazione: Integrazione di Azure Active Directory con Cezanne HR Software | Documentazione Microsoft'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Cezanne HR Software.
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
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 9585494ebff68891d374a29e8e3e4b7756914bcc
ms.openlocfilehash: d8a654340df56002e503f2f61e910facb51696c5


---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Esercitazione: Integrazione di Azure Active Directory con Cezanne HR Software

Questa esercitazione descrive come integrare Cezanne HR Software con Azure Active Directory (Azure AD).

L'integrazione di Cezanne HR Software con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Cezanne HR Software.
- È possibile abilitare gli utenti per l'accesso automatico a Cezanne HR Software (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account da una posizione centrale: il portale di gestione di Azure

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Cezanne HR Software, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Cezanne HR Software abilitata per l'accesso Single Sign-On.


> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Cezanne HR Software dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Aggiunta di Cezanne HR Software dalla raccolta
Per configurare l'integrazione di Cezanne HR Software in Azure AD, è necessario aggiungere Cezanne HR Software dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Cezanne HR Software dalla raccolta, seguire questa procedura:**

1. Nel **[portale di gestione di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Aggiungi** nella parte superiore della finestra di dialogo.

    ![Applicazioni][3]

4. Nella casella di ricerca, digitare **Cezanne HR Software**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_00001.png)

5. Nel pannello dei risultati selezionare **Cezanne HR Software** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Cezanne HR Software con un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Cezanne HR Software che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Cezanne HR Software.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in Cezanne HR Software.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Cezanne HR Software, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Cezanne HR Software](#creating-a-cezanne-hr-software-test-user)** : per avere una controparte di Britta Simon in Cezanne HR Software collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di gestione di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Cezanne HR Software.

**Per configurare Single Sign-On di Azure AD con Cezanne HR Software, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Cezanne HR Software** del portale di gestione di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** in **Modalità** selezionare **Accesso basato su SAML** per abilitare Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_01.png)

3. Nella sezione **URL e dominio Cezanne HR Software** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_02.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://w3.cezanneondemand.com/cezannehr/-/<tenant id>`
    
    b. Nella casella di testo **Identificatore** digitare `https://w3.cezanneondemand.com/CezanneOnDemand/`

    > [!NOTE] 
    > Si noti che questi non sono i valori reali. È necessario aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. In questo caso è consigliabile usare in Identificatore il valore univoco dell'URL. Per ottenere questi valori, contattare il [team di supporto di Cezanne HR Software](mailto:info@cezannehr.com).

4. Nella sezione **Certificato di firma SAML** fare clic su **Crea nuovo certificato**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_03.png)     

5. Nella finestra di dialogo **Crea nuovo certificato** fare clic sull'icona del calendario e selezionare una **data di scadenza**. Fare quindi clic sul pulsante **Salva**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_300.png)

6. Nella sezione **Certificato di firma SAML** selezionare **Rendi attivo il certificato nuovo** e fare clic sul pulsante **Salva**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_04.png)

7. Nella finestra popup **Certificato di rollover** fare clic su **OK**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_400.png)

8. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_05.png) 

9. Nella sezione **Configurazione di Cezanne HR Software** fare clic su **Configura Cezanne HR Software** per aprire la finestra **Configura accesso**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_06.png) 

    ![Configura accesso Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_07.png)

10. In un'altra finestra del browser Web accedere al tenant Cezanne HR Software come amministratore.

11. Nella barra di spostamento a sinistra fare clic su **System Setup**(Configurazione di sistema). Passare a **Security Settings**(Impostazioni di sicurezza). Passare quindi a **Single Sign-On Configuration**(Configurazione Single Sign-On).

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

12. Nel pannello **Allow users to log in using the following Single Sign-On (SSO) Service** (Consenti agli utenti di accedere usando il servizio Single Sign-On (SSO) seguente) selezionare la casella **SAML 2.0** e quindi l'opzione **Advanced Configuration** (Configurazione avanzata).

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

13. Fare clic sul pulsante **Add New** (Aggiungi nuovo).

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

14. Seguire questa procedura nella sezione **SAML 2.0 IDENTITY PROVIDERS** (PROVIDER DI IDENTITÀ SAML 2.0).

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

    a. Immettere il nome del provider di identità in **Display Name**(Nome visualizzato).

    b. Nella casella di testo **Entity Identifier** (Identificatore entità) inserire il valore di **SAML Entity ID** (ID entità SAML) dalla finestra di configurazione dell'applicazione di Azure AD.

    c. Modificare il valore **SAML Binding** (Associazione SAML) in 'POST'.

    d. Nella casella di testo **Security Token Service Endpoint** (Endpoint del servizio token di sicurezza) inserire il valore di **SAML Single Sign-on Service URL** (URL servizio Single Sign-On SAML) dalla configurazione guidata dell'applicazione di Azure AD.

    e. Nella casella di testo **User ID Attribute Name** (Nome attributo ID utente) immettere "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name".

    f. Fare clic sull'icona **Upload** (Carica) per caricare il certificato scaricato da Azure AD.

    g. Fare clic sul pulsante **OK** . 

15. Fare clic sul pulsante **Salva** .

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)



### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di gestione di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di gestione di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_01.png) 

2. Andare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** eseguire la procedura seguente:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**. 



### <a name="creating-a-cezanne-hr-software-test-user"></a>Creazione di un utente test di Cezanne HR Software

Per consentire agli utenti di Azure AD di accedere a Cezanne HR Software, è necessario eseguirne il provisioning in Cezanne HR Software. Nel caso di Cezanne HR Software, il provisioning è un'attività manuale.

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, seguire questa procedura:

1.  Accedere al sito aziendale di Cezanne HR Software come amministratore.

2.  Nella barra di spostamento a sinistra fare clic su **System Setup**(Configurazione di sistema). Passare a **Gestisci utenti**. Passare quindi ad **Add New User**(Aggiungi nuovo utente).

    ![Nuovo utente](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Nuovo utente")

3.  Nella sezione **Person Details** (Dettagli persona) seguire questa procedura:

    ![Nuovo utente](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Nuovo utente")

    a. Impostare **Internal User** (Utente interno) su OFF.

    b. Nella casella di testo **Nome** digitare **Britta**.  

    c. Nella casella di testo **Cognome** digitare **Simon**.

    d. Nella casella di testo **E-mail** digitare l'indirizzo di posta elettronica dell'account di Britta Simon.

4.  Nella sezione **Account Information** (Informazioni account) seguire questa procedura:

    ![Nuovo utente](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Nuovo utente")

    a. Nella casella di testo **Username** (Nome utente) digitare l'indirizzo di posta elettronica di Britta Simon.

    b. Nella casella di testo **Password** digitare la password dell'account di Britta Simon.

    c. Selezionare **HR Professional** (Professionista HR) come **Security Role** (Ruolo di sicurezza).

    d. Fare clic su **OK**.

5. Passare alla scheda **Single Sign-On** e selezionare **Add New** (Aggiungi nuovo) nell'area **SAML 2.0 Identifiers** (Identificatori SAML 2.0).

    ![Utente](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Utente")

6. Scegliere il provider di identità per **Identity Provider** (Provider di identità) e nella casella di testo **User Identifier** (Identificatore utente) immettere l'indirizzo di posta elettronica dell'account Britta Simon.

    ![Utente](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Utente")
    
7. Fare clic sul pulsante **Salva** .

    ![Utente](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Utente")



### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Cezanne HR Software.

![Assegna utente][200] 

**Per assegnare Britta Simon a Cezanne HR Software, seguire questa procedura:**

1. Nel portale di gestione di Azure aprire la visualizzazione applicazioni, passare alla visualizzazione directory e andare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Cezanne HR Software**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_50.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    


### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Cezanne HR Software nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Cezanne HR Software.


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


<!--HONumber=Feb17_HO1-->


