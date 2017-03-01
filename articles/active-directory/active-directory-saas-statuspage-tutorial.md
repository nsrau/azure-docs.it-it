---
title: 'Esercitazione: Integrazione di Azure Active Directory con StatusPage | Documentazione Microsoft'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e StatusPage.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: f6ee8bb3-df43-4c0d-bf84-89f18deac4b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7cc133d6289bffbc3b7fc591104bc51ebfc67ddd
ms.openlocfilehash: 3b901bcb08a194f72c3dc75e33f2b94fffea370e
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>Esercitazione Integrazione di Azure Active Directory con StatusPage
Questa esercitazione descrive l'integrazione di StatusPage con Azure Active Directory (Azure AD).

L'integrazione di StatusPage con Azure AD offre i vantaggi seguenti: 

* È possibile controllare in Azure AD chi può accedere a StatusPage 
* È possibile abilitare gli utenti per l'accesso Single Sign-on automatico a StatusPage con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con StatusPage, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di StatusPage abilitata per l'accesso Single Sign-On (SSO)

>[!NOTE]
>Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione. 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On (SSO) di Azure AD in un ambiente di test. 

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

* Aggiunta di StatusPage dalla raccolta 
* Configurazione e test dell'accesso Single Sign-On (SSO) di Azure AD

## <a name="add-statuspage-from-the-gallery"></a>Aggiungere StatusPage dalla raccolta
Per configurare l'integrazione di StatusPage in Azure AD, è necessario aggiungere StatusPage dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere StatusPage dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **StatusPage**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_01.png)
7. Nel riquadro dei risultati selezionare **StatusPage** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurare e testare l'accesso Single Sign-On (SSO) di Azure AD
Questa sezione descrive come configurare e testare l'accesso SSO di Azure AD con StatusPage con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso SSO, Azure AD deve conoscere qual è l'utente di StatusPage che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in StatusPage.

La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Username** (Nome utente) in StatusPage.

Per configurare e testare l'accesso Single Sign-On di Azure AD con StatusPage, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test per StatusPage](#creating-a-statuspage-test-user)** : per avere una controparte di Britta Simon in StatusPage collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD
Questa sezione descrive come abilitare l'accesso SSO di Azure AD nel portale di Azure classico e configurare l'accesso Single Sign-On nell'applicazione StatusPage. 

**Per configurare l'accesso SSO di Azure AD con StatusPage, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **StatusPage** nel portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6] 
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a StatusPage** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_03.png) 
3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_04.png) 
   
   >[!NOTE]
   >Per richiedere i metadati necessari per configurare l'accesso Single Sign-On, contattare il team di supporto di StatusPage all'indirizzo [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io). 
   > 
  1. Dal file di metadati, copiare il valore relativo all'autorità di certificazione e quindi incollarlo nella casella di testo **Identificatore** .
  2. Dal file di metadati copiare il valore relativo all'URL di risposta e quindi incollarlo nella casella di testo **Reply URL** .
  3. Fare clic su **Avanti**.

4. Nella pagina **Configura accesso Single Sign-On in StatusPage** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_05.png)   
  1. Fare clic su **Scarica certificato**e quindi salvare il file nel computer.
  2. Fare clic su **Avanti**.
5. In un'altra finestra del browser accedere al sito aziendale di StatusPage come amministratore.
6. Nella barra degli strumenti principale scegliere **Manage Account**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_06.png) 
7. Fare clic sulla scheda **Single Sign-on** . 
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_07.png) 
8. Nella pagina di configurazione dell’accesso Single Sign-On, seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_08.png)  
  1. Nella pagina **Configura accesso Single Sign-On in StatusPage** del portale di Azure classico copiare il valore di **URL servizio Single Sign-On** e quindi incollarlo nella casella di testo **SSO Target URL**. 
  2. Aprire il certificato scaricato nel Blocco note, copiarne il contenuto e incollarlo nella casella di testo **Certificato** . 
  3. Fare clic su **Save**.
9. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e fare clic su **Avanti**. 
   
    ![Single Sign-On di Microsoft Azure AD][10]
10. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
   
    ![Single Sign-On di Microsoft Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_09.png)  
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_03.png) 
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso. 
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_04.png) 
5. Nella pagina **Informazioni sull'utente** seguire questa procedura: 
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_05.png) 
  1. In Tipo di utente selezionare Nuovo utente nell'organizzazione.  
  2. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
  3. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura: 
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_06.png)  
  1. Nella casella di testo **Nome** digitare **Britta**.  
  2. Nella casella di testo **Cognome** digitare **Simon**.
  3. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
  4. Nell'elenco **Ruolo** selezionare **Utente**.
  5. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_07.png) 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_08.png) 
  1. Prendere nota del valore visualizzato in **Nuova password**  
  2. Fare clic su **Completa**.   

### <a name="create-a-statuspage-test-user"></a>Creare un utente test per StatusPage
Questa sezione descrive come creare un utente chiamato Britta Simon in StatusPage.

StatusPage supporta il provisioning JIT (just-in-time), È già stato abilitato in [Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on).

**Per creare un utente denominato Britta Simon in StatusPage, seguire questa procedura:**

1. Accedere al sito aziendale di StatusPage come amministratore.
2. Nel menu in alto fare clic su **Gestisci Account**.
3. Fare clic sulla scheda Team Members. 
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_10.png) 
4. Fare clic su **Add Team Member**. 
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_11.png) 
5. Digitare i valori **Indirizzo di posta elettronica**, **Nome** e **Cognome** di un utente valido di cui si vuole eseguire il provisioning nelle caselle di testo corrispondenti. 
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_12.png) 
6. Per **Ruolo** scegliere **Client Administrator** (Amministratore client).
7. Fare clic su **Create Account**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD
Questa sezione descrive come abilitare Britta Simon per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a StatusPage.

![Assegna utente][200] 

**Per assegnare Britta Simon a StatusPage, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201] 
2. Nell'elenco di applicazioni selezionare **StatusPage**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_50.png) 
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203] 
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro StatusPage nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione StatusPage.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_205.png







