---
title: 'Esercitazione: Integrazione di Azure Active Directory con Novatus | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SECURE DELIVER.
services: active-directory
documentationcenter: ''
author: jeevansd
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: jeedes

---
# <a name="tutorial:-azure-active-directory-integration-with-secure-deliver"></a>Esercitazione: Integrazione di Azure Active Directory con SECURE DELIVER
Questa esercitazione descrive l'integrazione di SECURE DELIVER con Azure Active Directory (Azure AD).  
L'integrazione di SECURE DELIVER con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a SECURE DELIVER
* È possibile abilitare gli utenti per l'accesso automatico a SECURE DELIVER (Single Sign-On) con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con SECURE DELIVER, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure.
* Sottoscrizione di SECURE DELIVER abilitata per l'accesso Single Sign-On

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

1. Aggiunta di SECURE DELIVER dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-secure-deliver-from-the-gallery"></a>Aggiunta di SECURE DELIVER dalla raccolta
Per configurare l'integrazione di SECURE DELIVER in Azure AD, è necessario aggiungere SECURE DELIVER dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere SECURE DELIVER dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **SECURE DELIVER**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-securedeliver-tutorial/tutorial_securedeliver_01.png)
7. Nel riquadro dei risultati selezionare **SECURE DELIVER** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Logo e nome dell'app nella raccolta](./media/active-directory-saas-securedeliver-tutorial/tutorial_securedeliver_06.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con SECURE DELIVER con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di SECURE DELIVER che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SECURE DELIVER.  
La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Username** in SECURE DELIVER.

Per configurare e testare l'accesso Single Sign-On di Azure AD con SECURE DELIVER, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di SECURE DELIVER](#creating-a-secure-deliver-test-user)** : per avere una controparte di Britta Simon in SECURE DELIVER collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure classico e configurare l'accesso Single Sign-On nell'applicazione SECURE DELIVER.

**Per configurare l'accesso Single Sign-On di Azure AD con SECURE DELIVER, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **SECURE DELIVER** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6] 
2. Nella pagina **How would you like users to sign on to SECURE DELIVER** (Stabilire come si desidera che gli utenti accedano a SECURE DELIVER) selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-securedeliver-tutorial/tutorial_securedeliver_03.png) 
3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura e quindi fare clic su **Avanti**:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-securedeliver-tutorial/tutorial_securedeliver_04.png) 
   
    a. Nella casella di testo **URL di accesso** digitare l'URL utilizzato dagli utenti per accedere all'applicazione SECURE DELIVER adottando il modello seguente: **"https://i-securedeliver.jp/sd/\<nome società\>/jsf/login/sso"**.
   
    b. Per ottenere l'URL del tenant, se non se ne conosce il valore, contattare il team di supporto di SECURE DELIVER all'indirizzo [iw-sd-support@fujifilm.com](mailto:iw-sd-support@fujifilm.com) .
   
    c. Nella casella di testo **Identificatore** digitare l'URL del tenant. 
   
    d. Fare clic su **Avanti**
4. Nella pagina **Configure single sign-on at SECURE DELIVER** (Configura accesso Single Sign-On in SECURE DELIVER) seguire questa procedura e quindi fare clic su **Avanti**:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-securedeliver-tutorial/tutorial_securedeliver_05.png) 
   
    a. Fare clic su **Scarica certificato**e quindi salvare il file nel computer.
   
    b. Fare clic su **Next**.
5. Per ottenere la configurazione dell'accesso Single Sign-On per l'applicazione, contattare il team di supporto di SECURE DELIVER all'indirizzo [iw-sd-support@fujifilm.com](mailto:iw-sd-support@fujifilm.com) e fornire i dati seguenti:
   
    •  File del certificato scaricato
   
    •  **ID entità**
   
    •  **URL servizio Single Sign-On**
   
    •  **URL del servizio Single Sign-Out**
6. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**.
   
    ![Single Sign-On di Microsoft Azure AD][10]
7. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
   
    ![Single Sign-On di Microsoft Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test di SECURE DELIVER in Azure AD, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-securedeliver-tutorial/create_aaduser_09.png) 
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-securedeliver-tutorial/create_aaduser_03.png) 
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-securedeliver-tutorial/create_aaduser_04.png) 
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-securedeliver-tutorial/create_aaduser_05.png) 
   
    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
   
    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
   
    c. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura:
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-securedeliver-tutorial/create_aaduser_06.png) 
   
   a. Nella casella di testo **Nome** digitare **Britta**.  
   
   b. Nella casella di testo **Cognome** digitare **Simon**.
   
   c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
   
   d. Nell'elenco **Ruolo** selezionare **Utente**.
   
   e. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-securedeliver-tutorial/create_aaduser_07.png) 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-securedeliver-tutorial/create_aaduser_08.png) 
   
    a. Prendere nota del valore visualizzato in **Nuova password**.
   
    b. Fare clic su **Complete**.   

### <a name="creating-a-secure-deliver-test-user"></a>Creazione di un utente test di SECURE DELIVER
Questa sezione descrive come creare un utente chiamato Britta Simon in SECURE DELIVER. Collaborare con il team di supporto di SECURE DELIVER per aggiungere gli utenti nell'account SECURE DELIVER.

> [!NOTE]
> Per creare un utente manualmente, è necessario contattare il team di supporto di SECURE DELIVER.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD
Questa sezione descrive come abilitare Britta Simon per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SECURE DELIVER.

![Assegna utente][200] 

**Per assegnare Britta Simon a SECURE DELIVER, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201] 
2. Nell'elenco di applicazioni selezionare **SECURE DELIVER**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-securedeliver-tutorial/tutorial_securedeliver_50.png) 
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203] 
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.  
Quando si fa clic sul riquadro SECURE DELIVER nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione SECURE DELIVER.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_205.png



<!--HONumber=Oct16_HO2-->


