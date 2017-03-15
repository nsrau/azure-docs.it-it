---
title: 'Esercitazione: Integrazione di Azure Active Directory con BGS Online | Documentazione Microsoft'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e BGS Online.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 4fd6b29b-1b46-4fd1-9f5e-16b1c9d892cd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4a07f946d396a9263d5b00202cd5229ddc86d1be
ms.openlocfilehash: 6af7b7777a0ea0b0eba3f499b2e34f30d5bc1ea0
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-bgs-online"></a>Esercitazione: Integrazione di Azure Active Directory con BGS Online
Questa esercitazione descrive come integrare BGS Online con Azure Active Directory (Azure AD).

L'integrazione di BGS Online con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a BGS Online
* È possibile abilitare gli utenti per l'accesso automatico Single Sign-On (SSO) a BGS Online con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con BGS Online, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di BGS Online abilitata per l'accesso Single Sign-On (SSO)

>[!NOTE]
>Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione. 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di BGS Online dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On (SSO) di Microsoft Azure AD

## <a name="adding-bgs-online-from-the-gallery"></a>Aggiunta di BGS Online dalla raccolta
Per configurare l'integrazione di BGS Online in Azure AD, è necessario aggiungere BGS Online dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere BGS Online dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico**fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **BGS Online**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-bgsonline-tutorial/tutorial_bgsonline_01.png)
7. Nel riquadro dei risultati selezionare **BGS Online** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-bgsonline-tutorial/tutorial_bgsonline_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurare e testare l'accesso Single Sign-On (SSO) di Azure AD
In questa sezione viene configurato e testato l'accesso SSO di Azure AD con BGS Online con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso SSO, Azure AD deve conoscere qual è l'utente di BGS Online che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in BGS Online.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in BGS Online.

Per configurare e testare l'accesso SSO di Azure AD con BGS Online, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di BGS Online](#creating-a-bgsonline-test-user)**: per avere una controparte di Britta Simon in BGS Online collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD
In questa sezione viene abilitato l'accesso SSO di Azure AD nel portale di Azure classico e viene configurato l'accesso Single Sign-On nell'applicazione BGS Online.

**Per configurare l'accesso SSO di Azure AD con BGS Online, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **BGS Online** del portale classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6] 
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a BGS Online** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-bgsonline-tutorial/tutorial_bgsonline_03.png) 
3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-bgsonline-tutorial/tutorial_bgsonline_04.png) 
  1. Nella casella di testo **Identificatore** digitare l'URL usato dagli utenti per accedere all'applicazione BGS Online. Per un ambiente di produzione usare il modello `https://<company name>.millwardbrown.report` e per l'ambiente di test usare il modello `https://millwardbrown.marketingtracker.nl/mt5/`
  2. Nella casella di testo **URL di risposta** usare il modello `https://<company name>.millwardbrown.report/sso/saml/AssertionConsumerService.aspx` per l'ambiente di produzione e il modello `https://millwardbrown.marketingtracker.nl/mt5/sso/saml/AssertionConsumerService.aspx` per l'ambiente di test
  3. fare clic su **Avanti**.
4. Nella pagina **Configura accesso Single Sign-On in BGS Online** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-bgsonline-tutorial/tutorial_bgsonline_05.png)
  * Fare clic su **Scarica certificato**e quindi salvare il file nel computer.
5. Per ottenere l'accesso Single Sign-On configurato per l'applicazione, contattare il [team di supporto](mailTo:bgsdashboardteam@millwardbrown.com) di BGS Online e fornire i valori seguenti:
  * I **metadati** scaricati
  * **URL SAML SSO**
6. Nel portale di Azure classico selezionare la conferma della configurazione e quindi fare clic su **Avanti**.
   
    ![Single Sign-On di Microsoft Azure AD][10]
7. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
   
    ![Single Sign-On di Microsoft Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
In questa sezione viene creato un utente test chiamato Britta Simon nel portale classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-bgsonline-tutorial/create_aaduser_09.png) 
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-bgsonline-tutorial/create_aaduser_03.png) 
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-bgsonline-tutorial/create_aaduser_04.png) 
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-bgsonline-tutorial/create_aaduser_05.png) 
  1. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
  2. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
  3. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura:

   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-bgsonline-tutorial/create_aaduser_06.png)   
  1. Nella casella di testo **Nome** digitare **Britta**.  
  2. Nella casella di testo **Cognome** digitare **Simon**.
  3. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
  4. Nell'elenco **Ruolo** selezionare **Utente**.
  5. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-bgsonline-tutorial/create_aaduser_07.png) 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-bgsonline-tutorial/create_aaduser_08.png) 
  1. Prendere nota del valore visualizzato in **Nuova password**.
  2. Fare clic su **Completa**.   

### <a name="create-an-bgs-online-test-user"></a>Creare un utente test per BGS Online
In questa sezione viene creato un utente di nome Britta Simon in BGS Online. Collaborare con il [team di supporto](mailTo:bgsdashboardteam@millwardbrown.com) di BGS Online per aggiungere gli utenti alla piattaforma BGS Online.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD
In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a BGS Online.

![Assegna utente][200] 

**Per assegnare Britta Simon a BGS Online, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201] 
2. Nell'elenco delle applicazioni selezionare **BGS Online**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-bgsonline-tutorial/tutorial_bgsonline_50.png) 
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203]
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On
In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro BGS Online nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione BGS Online.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_205.png

