---
title: 'Esercitazione: Integrazione di Azure Active Directory con Citrix ShareFile | Microsoft Docs'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Citrix ShareFile.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ddf6c69b-4a76-4b42-8619-6f2a22800a23
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 9dfbf4bcdcd580773a368b90c869bf9c4fefbd77
ms.lasthandoff: 03/25/2017


---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Esercitazione: Integrazione di Azure Active Directory con Citrix ShareFile

Questa esercitazione descrive come integrare Citrix ShareFile con Azure Active Directory (Azure AD).

L'integrazione di Citrix ShareFile con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Citrix ShareFile
- È possibile abilitare gli utenti per l'accesso automatico a Citrix ShareFile (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di gestione di Azure

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Citrix ShareFile, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Citrix ShareFile abilitata per l'accesso Single Sign-On


> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Citrix ShareFile dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## <a name="adding-citrix-sharefile-from-the-gallery"></a>Aggiunta di Citrix ShareFile dalla raccolta
Per configurare l'integrazione di Citrix ShareFile in Azure AD, è necessario aggiungere Citrix ShareFile dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Citrix ShareFile dalla raccolta, seguire questa procedura:**

1. Nel **[portale di gestione di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Aggiungi** nella parte superiore della finestra di dialogo.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **Citrix ShareFile**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_001.png)

5. Nel pannello dei risultati selezionare **Citrix ShareFile** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Citrix ShareFile in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Citrix ShareFile che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Citrix ShareFile.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **NomeUtente** in Citrix ShareFile.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Citrix ShareFile, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Citrix ShareFile](#creating-a-citrix-sharefile-test-user)**: per avere una controparte di Britta Simon in Citrix ShareFile collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di gestione di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Citrix ShareFile.

**Per configurare Single Sign-On di Azure AD con Citrix ShareFile, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Citrix ShareFile** del portale di gestione di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** in **Modalità** selezionare **Accesso basato su SAML** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_01.png)

3. Nella sezione **Citrix ShareFile Domain and URLs** (URL e dominio Citrix ShareFile) seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_02.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<tenant-name>.sharefile.com/saml/login`    

    > [!NOTE] 
    > Si noti che questi non sono i valori reali. È necessario aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Se si non conoscono questi URL, digitare gli URL di esempio con il modello di esempio e quindi si otterrà l'URL effettivo dopo il completamento del passaggio 13.

4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_05.png) 

5. Nel **Citrix ShareFile Configuration** (Configurazione di Citrix ShareFile) fare clic su **Configure Citrix ShareFile** (Configura Citrix ShareFile) per aprire la finestra **Configura accesso**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_06.png) 

    ![Configura accesso Single Sign-On](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_07.png)

6. In un'altra finestra del Web browser accedere al sito aziendale di Citrix ShareFile come amministratore.

7. Nel barra degli strumenti in alto fare clic su **Admin**.

8. Nel riquadro di spostamento sinistro selezionare **Configure Single Sign-On**.

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_002.png)

9. In **Basic Settings** della finestra di dialogo **Single Sign-On/ SAML 2.0 Configuration** seguire questa procedura:

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-sharefile-tutorial/sso_configuration_2.png)

    a. Fare clic su **Enable SAML**.

    b. Nella casella di testo **Your IDP Issuer/Entity ID** (Autorità emittente IDP/ ID entità) inserire il valore di **SAML Entity ID** (ID entità SAML) dalla finestra di configurazione dell'applicazione di Azure AD.

    c. Fare clic su **Cambia** accanto al campo **Certificato X.509** e quindi caricare il file del certificato scaricato. 

    d. Nella casella di testo **Login URL** (URL di accesso) inserire il valore di **SAML Single Sign-On Service URL** (URL servizio Single Sign-On SAML) dalla finestra di configurazione dell'applicazione di Azure AD.

    e. Nella casella di testo **URL disconnessione** inserire il valore di **Sign-Out URL** (URL di disconnessione) dalla finestra di configurazione dell'applicazione di Azure AD.

10. Fare clic su **Save** nel portale di gestione di Citrix ShareFile.
    
 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di gestione di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di gestione di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sharefile-tutorial/create_aaduser_01.png) 

2. Andare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sharefile-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sharefile-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sharefile-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**. 



### <a name="creating-a-citrix-sharefile-test-user"></a>Creazione di un utente test di Citrix ShareFile

Per consentire agli utenti di Azure AD di accedere a Citrix ShareFile, è necessario eseguirne il provisioning in Citrix ShareFile. Nel caso di Citrix ShareFile, il provisioning è un'attività manuale.

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, seguire questa procedura:

1. Accedere al sito aziendale di Citrix ShareFile come amministratore.

2. Nel barra degli strumenti in alto fare clic su **Gestione utenti**. Passare quindi a **Manage Users Home** (Gestisci pagina iniziale utenti) e fare clic su **Create Employee** (Crea dipendente).

    ![Nuovo utente](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_010.png "Nuovo utente")

3. Nella sezione **Informazioni di base** seguire questa procedura:

    ![Nuovo utente](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_011.png "Nuovo utente")

    a. Nella casella di testo **Indirizzo di posta elettronica** digitare l'indirizzo di posta elettronica dell'account di Britta Simon.  

    b. Nella casella di testo **Nome** digitare **Britta**.  

    c. Nella casella di testo **Cognome** digitare **Simon**.

4. Fare clic su **Aggiungi utente**.

    > [!NOTE]
    > Il titolare dell'account AAD riceverà un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo. È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Citrix ShareFile per eseguire il provisioning degli account utente di Azure AD.



### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure, concedendole così accesso a Citrix ShareFile.

![Assegna utente][200] 

**Per assegnare Britta Simon a Citrix ShareFile, seguire questa procedura:**

1. Nel portale di gestione di Azure aprire la visualizzazione applicazioni, passare alla visualizzazione directory e andare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni, selezionare **Citrix ShareFile**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_50.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    


### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Citrix ShareFile nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Citrix ShareFile.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_203.png
