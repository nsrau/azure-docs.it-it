---
title: 'Esercitazione: Integrazione di Azure Active Directory con Origami | Microsoft Docs'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Origami
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: a28bb0ba-b564-46ba-accc-e587699295d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d5499636ff1c560dd5a50924f0fd67d8dac50203


---
# <a name="tutorial-azure-active-directory-integration-with-origami"></a>Esercitazione: Integrazione di Azure Active Directory con Origami
Questa esercitazione descrive come integrare Origami con Azure Active Directory (Azure AD).

L'integrazione di Origami con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Origami
* È possibile abilitare gli utenti perché possano accedere automaticamente a Origami (Single Sign-On) con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con Origami, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di Origami abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.
> 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Origami dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-origami-from-the-gallery"></a>Aggiunta di Origami dalla raccolta
Per configurare l'integrazione di Origami in Azure AD, è necessario aggiungere Origami dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Origami dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **Origami**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-origami-tutorial/tutorial_origami_01.png)
7. Nel riquadro dei risultati selezionare **Origami**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-origami-tutorial/tutorial_origami_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Origami con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente controparte di Origami che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Origami.

La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Username** in Origami.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Origami, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Origami](#creating-a-origami-test-user)**: per avere una controparte di Britta Simon in Origami collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD
In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure classico e viene configurato l'accesso Single Sign-On nell'applicazione Origami.

**Per configurare l'accesso Single Sign-On di Azure AD con Origami, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Origami** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6] 
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Origami** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-origami-tutorial/tutorial_origami_03.png) 
3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-origami-tutorial/tutorial_origami_04.png) 
   
    a. Nella casella di testo **URL di accesso** digitare l'URL utilizzato dagli utenti per accedere all'applicazione Origami usando il modello seguente: **https://live.origamirisk.com/origami/account/login?account=\<nome società\>**
   
    b. click **Avanti**
4. Nella pagina **Configura accesso Single Sign-On in Origami** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-origami-tutorial/tutorial_origami_05.png)
   
    a. Fare clic su **Scarica certificato**e quindi salvare il file nel computer.
   
    b. Fare clic su **Avanti**.
5. Accedere all'account Origami con diritti di amministratore.
6. Nel menu in alto fare clic su **Admin**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-origami-tutorial/tutorial_origami_51.png)
7. Nella pagina della finestra di dialogo Single Sign On Setup (Imposta accesso Single Sign-On) eseguire la procedura seguente:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-origami-tutorial/123.png)
   
    a. Selezionare **Abilita Single Sign-On**.
   
    b. Nel portale di Azure classico copiare il valore di **URL SSO SAML** e incollarlo nella casella di testo **Identity Provider Login URL** (URL di accesso al provider di identità).
   
    c. Nel portale di Azure classico copiare l'**URL SERVIZIO SINGLE SIGN-OUT** e incollarlo nella casella di testo **Identity Provider's Sign-out Page URL (URL di disconnessione dal provider di identità)**.
   
    d. Fare clic su **Sfoglia** per caricare il certificato scaricato dal portale di Azure classico.
   
    e. Fare clic su **Salva modifiche**.
8. Nel portale classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**.
   
    ![Single Sign-On di Microsoft Azure AD][10]
9. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
   
    ![Single Sign-On di Microsoft Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
In questa sezione viene creato un utente test chiamato Britta Simon nel portale classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_09.png) 
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_03.png) 
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_04.png) 
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:  ![Creazione di un utente test di Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_05.png) 
   
    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
   
    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
   
    c. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura: ![Creazione di un utente test di Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_06.png) 
   
   a. Nella casella di testo **Nome** digitare **Britta**.  
   
   b. Nella casella di testo **Cognome** digitare **Simon**.
   
   c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
   
   d. Nell'elenco **Ruolo** selezionare **Utente**.
   
   e. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_07.png) 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_08.png) 
   
    a. Prendere nota del valore visualizzato in **Nuova password**.
   
    b. Fare clic su **Complete**.   

### <a name="creating-an-origami-test-user"></a>Creazione di un utente test in Origami
In questa sezione viene creato un utente di nome Britta Simon in Origami. 

1. Accedere all'account Origami con diritti di amministratore.
2. Nel menu in alto fare clic su **Admin**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-origami-tutorial/tutorial_origami_51.png)
3. Nella finestra di dialogo **Users and Security** (Utenti e sicurezza) fare clic su **Utenti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-origami-tutorial/tutorial_origami_54.png)
4. Fare clic su **Add new user**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-origami-tutorial/tutorial_origami_55.png)
5. Nella finestra di dialogo Add New User seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-origami-tutorial/tutorial_origami_56.png)
   
    a. Nella casella di testo **Nome utente** del portale di Azure classico digitare il nome utente "Britta Simon".
   
    b. Nella casella di testo **Password** digitare una password.
   
    c. Nella casella di testo **Conferma password** digitare di nuovo la password.
   
    d. Nella casella di testo **Nome** digitare **Britta**.
   
    e. Nella casella di testo **Cognome** digitare **Simon**.
   
    f. Fare clic su **Save**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-origami-tutorial/tutorial_origami_57.png)
6. Assegnare i **ruoli utente** e l'**accesso client** all'utente. 
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-origami-tutorial/tutorial_origami_58.png)

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD
In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Origami.

![Assegna utente][200] 

**Per assegnare Britta Simon a Origami, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201] 
2. Nell'elenco di applicazioni, selezionare **Origami**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-origami-tutorial/tutorial_origami_50.png) 
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203]
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On
In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Origami nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Origami.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-origami-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-origami-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-origami-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-origami-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-origami-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-origami-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-origami-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-origami-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-origami-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-origami-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-origami-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-origami-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-origami-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


