---
title: 'Esercitazione: Integrazione di Azure Active Directory con Asset Bank | Documentazione Microsoft'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Asset Bank.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 3006ad6e-8831-41cd-94aa-7e7ae770ce7b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: cb9e051dcb5cb3c59f80a4e0c022654166ca747d


---
# <a name="tutorial-azure-active-directory-integration-with-asset-bank"></a>Esercitazione: Integrazione di Azure Active Directory con Asset Bank
Questa esercitazione descrive l’integrazione di Asset Bank con Azure Active Directory (Azure AD).

L'integrazione di Asset Bank con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere ad Asset Bank
* È possibile abilitare gli utenti per l'accesso automatico ad Asset Bank (Single Sign-On) con i propri account Azure AD.
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con Asset Bank, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di Asset Bank abilitata per l'accesso Single Sign-On.

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.
> 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test. 

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Asset Bank dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-asset-bank-from-the-gallery"></a>Aggiunta di Asset Bank dalla raccolta
Per configurare l'integrazione di Asset Bank in Azure AD, è necessario aggiungere Asset Bank dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Asset Bank dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **Asset Bank**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-assetbank-tutorial/tutorial_assetbank_01.png)
7. Nel riquadro dei risultati selezionare **Asset Bank** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-assetbank-tutorial/tutorial_assetbank_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con Asset Bank in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Asset Bank che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Asset Bank.

La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Username** in Asset Bank.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Asset Bank, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Asset Bank](#creating-a-asset-bank-test-user)** : per avere una controparte di Britta Simon in Asset Bank collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come abilitare Single Sign-On di Azure AD nel portale di Azure classico e come configurare l'accesso Single Sign-On nell'applicazione Asset Bank.

**Per configurare Single Sign-On di Azure AD con Asset Bank, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Asset Bank** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

![Configura accesso Single Sign-On][6] 

1. Nella pagina **Stabilire come si desidera che gli utenti accedano ad Asset Bank** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-assetbank-tutorial/tutorial_assetbank_03.png) 
2. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-assetbank-tutorial/tutorial_assetbank_04.png) 

    a. Nella casella di testo URL di accesso digitare l'URL utilizzato dagli utenti per accedere all'applicazione Asset Bank adottando il modello seguente: **"https://\<nome società\>.assetbank-server.com"**.

    b. Fare clic su **Avanti**.

1. Nella pagina **Configura accesso Single Sign-On in Asset Bank** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-assetbank-tutorial/tutorial_assetbank_05.png) 
   
    a. Fare clic su **Scarica metadati**e quindi salvare il file nel computer.
   
    b. Fare clic su **Avanti**.
2. Per ottenere la configurazione dell'accesso Single Sign-On per l'applicazione, contattare il team di supporto di Asset Bank all'indirizzo [support@assetbank.co.uk](mailto:support@assetbank.co.uk) e allegare il file di metadati al messaggio di posta elettronica.
3. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**.
   
    ![Single Sign-On di Microsoft Azure AD][10]
4. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
   
    ![Single Sign-On di Microsoft Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure classico.

Nell'elenco di utenti selezionare **Britta Simon**.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-assetbank-tutorial/create_aaduser_09.png) 
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-assetbank-tutorial/create_aaduser_03.png) 
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-assetbank-tutorial/create_aaduser_04.png) 
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-assetbank-tutorial/create_aaduser_05.png) 
   
    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
   
    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
   
    c. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura:
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-assetbank-tutorial/create_aaduser_06.png) 
   
   a. Nella casella di testo **Nome** digitare **Britta**.  
   
   b. Nella casella di testo **Cognome** digitare **Simon**.
   
   c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
   
   d. Nell'elenco **Ruolo** selezionare **Utente**.
   
   e. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-assetbank-tutorial/create_aaduser_07.png) 
8. Nella pagina della finestra di dialogo **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-assetbank-tutorial/create_aaduser_08.png) 
   
    a. Prendere nota del valore visualizzato in **Nuova password**.
   
    b. Fare clic su **Complete**.   

### <a name="creating-a-asset-bank-test-user"></a>Creazione di un utente test di Asset Bank
L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in Asset Bank. Asset Bank supporta il provisioning just-in-time, che è abilitato per impostazione predefinita.

Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso ad Asset Bank verrà creato un nuovo utente, se questo non esiste già. 

> [!NOTE]
> Per creare un utente manualmente, è necessario contattare il team di supporto di Asset Bank.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD
Questa sezione descrive come abilitare Britta Simon per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Asset Bank.

![Assegna utente][200] 

**Per assegnare Britta Simon ad Asset Bank, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201] 
2. Nell'elenco di applicazioni selezionare **Asset Bank**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-assetbank-tutorial/tutorial_assetbank_50.png) 
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203] 
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Asset Bank nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Asset Bank.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-assetbank-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-assetbank-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-assetbank-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-assetbank-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-assetbank-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-assetbank-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-assetbank-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-assetbank-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-assetbank-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-assetbank-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-assetbank-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-assetbank-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-assetbank-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO4-->


