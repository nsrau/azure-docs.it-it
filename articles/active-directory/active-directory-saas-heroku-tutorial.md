---
title: 'Esercitazione: Integrazione di Azure Active Directory con Heroku | Documentazione Microsoft'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Heroku.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: d7d72ec6-4a60-4524-8634-26d8fbbcc833
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: f4ea5c300699a01fb098e2949f1197fdfebc3701


---
# <a name="tutorial-azure-active-directory-integration-with-heroku"></a>Esercitazione: Integrazione di Azure Active Directory con Heroku
Questa esercitazione descrive come integrare Heroku con Azure Active Directory (Azure AD).

L'integrazione di Heroku con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Heroku
* È possibile abilitare gli utenti per l'accesso automatico a Heroku (Single Sign-On) con l'account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con Heroku, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure.
* Sottoscrizione di Heroku abilitata per l'accesso Single Sign-On

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

1. Aggiunta di Heroku dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-heroku-from-the-gallery"></a>Aggiunta di Heroku dalla raccolta
Per configurare l'integrazione di Heroku in Azure AD, è necessario aggiungere Heroku dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Heroku dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **Heroku**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_01.png)
7. Nel riquadro dei risultati selezionare **Heroku** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Heroku con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Heroku che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Heroku.  
La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Username** in Heroku.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Heroku, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Heroku](#creating-an-heroku-test-user)** : per avere una controparte di Britta Simon in Heroku collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD
In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure classico e viene configurato l'accesso Single Sign-On nell'applicazione Heroku.

**Per configurare l'accesso Single Sign-On di Azure AD con Heroku, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Heroku** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6] 
2. Nella pagina **How would you like users to sign on to Heroku** (Stabilire come si desidera che gli utenti accedano a Heroku) selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_03.png) 
3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_04.png) 
   
   > [!NOTE]
   > Se non si conoscono i valori corretti dell'URL di accesso e dell'URL dell'identificatore, vedere "[Per abilitare l'accesso Single Sign-On in Heroku, eseguire la procedura seguente](#x123)" per istruzioni su come ottenerli.   
   > 
   > 

    a. Nella casella di testo **URL di accesso** digitare l'URL utilizzato dagli utenti per accedere all'applicazione Heroku adottando il modello seguente: **"https://sso.heroku.com/saml/\<company name\>/init"**. 

    b. Nella casella di testo **Identificatore** digitare un URL con il modello seguente: "**https://sso.heroku.com/saml/\<nome società\>**".  

    c. Fare clic su **Avanti**.


1. Nella pagina **Configure single sign-on at Heroku** (Configura accesso Single Sign-On in Heroku) seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_05.png) 
   
    a. Fare clic su **Scarica metadati**e quindi salvare il file nel computer.
   
    b. Fare clic su **Avanti**.
2. Per abilitare l'accesso Single Sign-On in Heroku, eseguire la procedura seguente:
   
    a. Accedere all'account Heroku come amministratore.
   
    b. Fare clic sulla scheda **Impostazioni** .
   
    c. Nella pagina **Single Sign On** fare clic su **Upload Metadata** (Carica metadati).
   
    d. Caricare il file di metadati scaricato dal portale di Azure classico.
   
    e. Al termine dell'installazione agli amministratori verrà visualizzata una finestra di dialogo di conferma e l'URL di accesso Single Sign-On per gli utenti finali.
   
    f. <a name="x123"></a>Copiare i valori **Heroku Login URL** (URL accesso Heroku) e **Heroku Entity ID** (ID entità Heroku), quindi nel portale di Azure classico tornare alla pagina **Configurare le impostazioni dell'app** e incollare i valori nelle caselle di testo corrispondenti.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_52.png) 

    g. Fare clic su **Avanti**.

1. Selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**.
   
    ![Single Sign-On di Microsoft Azure AD][10]
2. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
   
    ![Single Sign-On di Microsoft Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
In questa sezione viene creato un utente test chiamato Britta Simon nel portale classico.  

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-heroku-tutorial/create_aaduser_09.png) 
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-heroku-tutorial/create_aaduser_03.png) 
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-heroku-tutorial/create_aaduser_04.png) 
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-heroku-tutorial/create_aaduser_05.png) 
   
    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
   
    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
   
    c. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura:
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-heroku-tutorial/create_aaduser_06.png) 
   
   a. Nella casella di testo **Nome** digitare **Britta**.  
   
   b. Nella casella di testo **Cognome** digitare **Simon**.
   
   c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
   
   d. Nell'elenco **Ruolo** selezionare **Utente**.
   
   e. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-heroku-tutorial/create_aaduser_07.png) 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-heroku-tutorial/create_aaduser_08.png) 
   
    a. Prendere nota del valore visualizzato in **Nuova password**.
   
    b. Fare clic su **Complete**.   

### <a name="creating-an-heroku-test-user"></a>Creazione di un utente test di Heroku
In questa sezione viene creato un utente chiamato Britta Simon in Heroku. Heroku supporta il provisioning JIT (Just-In-Time) che è abilitato per impostazione predefinita.

Non è necessario alcun intervento dell'utente in questa sezione. Quando si accede a Heroku se l'utente non esiste ancora, viene creato un nuovo utente. Dopo il provisioning dell'account, l'utente finale riceve un messaggio di verifica e deve fare clic sul collegamento di conferma.

> [!NOTE]
> Per creare un utente manualmente, è necessario contattare il team di supporto di Heroku.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD
In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Heroku.

![Assegna utente][200] 

**Per assegnare Britta Simon a Heroku, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201] 
2. Nell'elenco di applicazioni selezionare **Heroku**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_50.png) 
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203] 
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On
In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.  
Quando si fa clic sul riquadro Heroku nel pannello di accesso, verrà eseguito automaticamente l'accesso all'applicazione Heroku.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO2-->


