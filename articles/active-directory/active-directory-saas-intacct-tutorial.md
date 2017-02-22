---
title: 'Esercitazione: Integrazione di Azure Active Directory con Intacct | Documentazione Microsoft'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Intacct.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 970bca01-6480-4a9f-ad99-3e4103b5ea88
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 3a5048300def133c6077179955d9d254b383e73b
ms.openlocfilehash: 7f4795e0622425016a7a1635f9e5d8c7a2e8255c


---
# <a name="tutorial-azure-active-directory-integration-with-intacct"></a>Esercitazione: Integrazione di Azure Active Directory con Intacct

Questa esercitazione descrive come integrare Intacct con Azure Active Directory (Azure AD).

L'integrazione di Intacct con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Intacct
- È possibile abilitare gli utenti per l'accesso automatico a Intacct (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di gestione di Azure

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Intacct, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Intacct abilitata per l'accesso Single Sign-On


> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Intacct dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## <a name="adding-intacct-from-the-gallery"></a>Aggiunta di Intacct dalla raccolta
Per configurare l'integrazione di Intacct in Azure AD, è necessario aggiungere Intacct dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Intacct dalla raccolta, seguire questa procedura:**

1. Nel **[portale di gestione di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Aggiungi** nella parte superiore della finestra di dialogo.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **Intacct**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_000.png)

5. Nel pannello dei risultati selezionare **Intacct** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Intacct con un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Intacct che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Intacct.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in Intacct.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Intacct, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di Intacct](#creating-an-intacct-test-user)**: per avere una controparte di Britta Simon in Intacct collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di gestione di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Intacct.

**Per configurare l'accesso Single Sign-On di Azure AD con Intacct, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Intacct** del portale di gestione di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** in **Modalità** selezionare **Accesso basato su SAML** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_01.png)

3. Nella sezione **URL e dominio Intacct** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_02.png)

    a. Nella casella di testo **Identificatore** digitare `https://saml.intacct.com`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<company name>.intacct.com/ia/acct/sso_response.phtml`

    > [!NOTE] 
    > Si noti che questi non sono i valori reali. È necessario aggiornare questi valori con l'identificatore e l'URL di risposta effettivi. In questo caso è consigliabile usare in Identificatore il valore univoco dell'URL. Per ottenere questi valori, contattare il [team di supporto di Intacct](mailto:info@intacct.com). 

4. Nella sezione **Certificato di firma SAML** fare clic su **Crea nuovo certificato**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_03.png)     

5. Nella finestra di dialogo **Crea nuovo certificato** fare clic sull'icona del calendario e selezionare una **data di scadenza**. Fare quindi clic sul pulsante **Salva**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-intacct-tutorial/tutorial_general_300.png)

6. Nella sezione **Certificato di firma SAML** selezionare **Rendi attivo il certificato nuovo** e fare clic sul pulsante **Salva**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_04.png)

7. Nella finestra popup **Certificato di rollover** fare clic su **OK**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-intacct-tutorial/tutorial_general_400.png)

8. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_05.png) 

9. Nella sezione **Configurazione di Intacct** fare clic su **Configura Intacct** per aprire la finestra **Configura accesso**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_06.png) 

    ![Configura accesso Single Sign-On](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_07.png)

10. In un'altra finestra del Web browser accedere al sito aziendale di Intacct come amministratore.

11. Fare clic sulla scheda **Company** (Azienda) e quindi su **Company Info** (Informazioni sull'azienda).

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_001.png)

12. Fare clic sulla scheda **Security** (Sicurezza) e quindi su **Edit** (Modifica).

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_002.png)

13. Nella sezione **Single sign on (SSO)** seguire questa procedura:

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_003.png)

    a. Selezionare **Abilita Single Sign-On**.

    b. In **Identity provider type** (Tipo di provider di identità) selezionare **SAML 2.0**.

    c. Nella casella di testo **Issuer URL** (URL autorità di certificazione) inserire il valore di **SAML Entity ID** (ID entità SAML) dalla finestra di configurazione dell'applicazione di Azure AD.

    d. Nella casella di testo **Login URL** (URL di accesso) inserire il valore di **SAML Single Sign-On Service URL** (URL servizio Single Sign-On SAML) dalla finestra di configurazione dell'applicazione di Azure AD.

    e. Aprire il certificato scaricato nel Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **Certificate** (Certificato).

    f. Fare clic su **Save**.

  

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di gestione di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di gestione di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-intacct-tutorial/create_aaduser_01.png) 

2. Andare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-intacct-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-intacct-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** eseguire la procedura seguente:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-intacct-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**. 



### <a name="creating-an-intacct-test-user"></a>Creazione di un utente di test di Intacct

Per consentire agli utenti di Azure AD di accedere a Intacct, è necessario eseguirne il provisioning in Intacct.  
Nel caso di Intacct, il provisioning è un'attività manuale.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, eseguire la procedura seguente:

1. Accedere al sito aziendale di Intacct come amministratore.

2. Fare clic sulla scheda **Company** (Azienda) e quindi su **Users** (Utenti).

    ![Provisioning utenti](./media/active-directory-saas-intacct-tutorial/create_aaduser_001.png)

3. Fare clic sulla scheda **Add** (Aggiungi).

    ![Provisioning utenti](./media/active-directory-saas-intacct-tutorial/create_aaduser_002.png)

4. Nella sezione **Informazioni utente** seguire questa procedura:

    ![Provisioning utenti](./media/active-directory-saas-intacct-tutorial/create_aaduser_003.png)

    a. Nella casella di testo **User ID** (ID utente) digitare l'ID utente dell'account di Britta Simon.

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome** digitare **Britta**.  

    d. Nella casella di testo **Email address** (Indirizzo di posta elettronica) digitare l'indirizzo di posta elettronica dell'account di Britta Simon.

    e. Selezionare i **privilegi di amministratore** dell'account di Britta Simon di cui si vuole effettuare il provisioning.

    f. Nella casella di testo **Title** (Titolo) digitare il titolo dell'account di Britta Simon.

    g. Nella casella di testo **Phone** (Telefono) digitare il numero di telefono dell'account di Britta Simon.
    
    h. Fare clic su **Save**.

    > [!NOTE]
    > Il titolare dell'account Azure Active Directory riceverà un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo. È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Intacct per eseguire il provisioning degli account utente di AAD.


### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Intacct.

![Assegna utente][200] 

**Per assegnare Britta Simon a Intacct, seguire questa procedura:**

1. Nel portale di gestione di Azure aprire la visualizzazione applicazioni, passare alla visualizzazione directory e andare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Intacct**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_50.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    


### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Intacct nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Intacct.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_203.png


<!--HONumber=Feb17_HO1-->


