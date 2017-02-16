---
title: 'Esercitazione: Integrazione di Azure Active Directory con Small Improvements | Documentazione Microsoft'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Small Improvements.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 59c8a112-41e1-4337-9ef3-3d7029780d61
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 75a1578578cabd6178b15db87c04854a6cbac6fe


---
# <a name="tutorial-azure-active-directory-integration-with-small-improvements"></a>Esercitazione: Integrazione di Azure Active Directory con Small Improvements
Questa esercitazione descrive l'integrazione di Small Improvements con Azure Active Directory (Azure AD).  
L'integrazione di Small Improvements con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Small Improvements
* È possibile abilitare gli utenti per l'accesso automatico a Small Improvements (Single Sign-On) con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con Small Improvements, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di Small Improvements abilitata per l'accesso Single Sign-On

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

1. Aggiunta di Small Improvements dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-small-improvements-from-the-gallery"></a>Aggiunta di Small Improvements dalla raccolta
Per configurare l'integrazione di Small Improvements in Azure AD, è necessario aggiungere Small Improvements dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Small Improvements dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Digitare **Small Improvements**nella casella di ricerca.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_01.png)
7. Nel riquadro dei risultati selezionare **Small Improvements** e fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con Small Improvements con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Small Improvements che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Small Improvements.  
La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Username** (Nome utente) in Small Improvements.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Small Improvements, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Small Improvements](#creating-a-small-improvements-test-user)** : per avere una controparte di Britta Simon in Small Improvements collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure classico e configurare l'accesso Single Sign-On nell'applicazione Small Improvements.

**Per configurare l'accesso Single Sign-On di Azure AD con Small Improvements, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Small Improvements** del portale di Azure classico, fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6] 
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Small Improvements**, selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_03.png) 
3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_04.png) 
   
   > [!NOTE]
   > Contattare il team di supporto di Small Improvements all'indirizzo [Support@small-improvements.com](mailto:support@small-improvements.com) per configurare un nome di dominio per l'account. Questa operazione è necessaria per il corretto funzionamento dell'accesso Single Sign-On.
   > 
   > 

    a. Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere all'applicazione Small Improvements.  
    b. Fare clic su **Next**.


1. Nella pagina **Configura accesso Single Sign-On in Small Improvements** , seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_05.png) 
   
    a. Fare clic su **Scarica certificato**e quindi salvare il file nel computer.
   
    b. Fare clic su **Next**.
2. In un'altra finestra del browser accedere al sito aziendale di Small Improvements come amministratore.
3. Dalla pagina dashboard principale fare clic sul pulsante **Amministrazione** a sinistra.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_06.png) 
4. Fare clic sul pulsante **SSO SAML** nella sezione **Integrazioni**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_07.png) 
5. Nella pagina di configurazione dell’accesso Single Sign-On, seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_08.png) 
   
    a. Nella pagina **Configura accesso Single Sign-On in Small Improvements** del portale di Azure classico, copiare il valore di **URL SSO SAML** e incollarlo nella casella di testo **HTTP Endpoint** (Endpoint HTTP).
   
    b. Aprire il certificato scaricato nel Blocco note, copiarne il contenuto e incollarlo nella casella di testo **Certificato x509** .
   
    c. Se si vuole che l'opzione di autenticazione al modulo di accesso e accesso Single Sign-On sia disponibile per gli utenti, selezionare l'opzione **Abilitare l'accesso anche tramite account di accesso e password** . 
   
    d. Immettere il valore appropriato per il nome del pulsante di accesso Single Sign-On nella casella di testo **Richiesta SAML** 
   
    e. Fare clic su **Save**.
6. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e fare clic su **Avanti**.
   
    ![Single Sign-On di Microsoft Azure AD][10]
7. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
   
    ![Single Sign-On di Microsoft Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure classico.  

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_09.png) 
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_03.png) 
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_04.png) 
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_05.png) 
   
    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
   
    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
   
    c. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura:
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_06.png) 
   
   a. Nella casella di testo **Nome** digitare **Britta**.  
   
   b. Nella casella di testo **Cognome** digitare **Simon**.
   
   c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
   
   d. Nell'elenco **Ruolo** selezionare **Utente**.
   e. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_07.png) 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_08.png) 
   
    a. Prendere nota del valore visualizzato in **Nuova password**.
   
    b. Fare clic su **Complete**.   

### <a name="creating-a-small-improvements-test-user"></a>Creazione di un utente test di Small Improvements
Questa sezione descrive come creare un utente di nome Britta Simon in Small Improvements.
È già stato abilitato in [Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on).

**Per creare un utente di nome Britta Simon in Small Improvements, seguire questa procedura:**

1. Accedere al sito aziendale di Small Improvements come amministratore.
2. Dalla home page passare al menu sulla sinistra e fare clic su **Amministrazione**.
3. Fare clic sul pulsante **Directory utenti** nella sezione Gestione utenti. 
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_10.png) 
4. Fare clic su **Add Team Member**.
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_11.png) 
5. Digitare il **nome**, il **cognome** e l'**indirizzo di posta elettronica** di un utente valido di cui si vuole effettuare il provisioning nelle caselle di testo corrispondenti. 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_12.png) 
6. È inoltre possibile scegliere di immettere il messaggio personale nella casella di testo **Invia messaggio di notifica** . Se non si vuole inviare la notifica, deselezionare questa casella di controllo.
7. Fare clic su **Crea utenti**.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD
Questa sezione descrive come abilitare Britta Simon per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Small Improvements.

    ![Assign User][200] 

**Per assegnare Britta Simon a Small Improvements, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201] 
2. Selezionare **Small Improvements**dall'elenco di applicazioni.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_50.png) 
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203] 
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.  
Quando si fa clic sul riquadro Small Improvements nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Small Improvements.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


