---
title: 'Esercitazione: Integrazione di Azure Active Directory con Jostle | Documentazione Microsoft'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Jostle.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 9ca4ca1f-8f68-4225-81a6-1666b486d6a8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: f98e199e06d5d272a23efcd4eaaa5e5f1629f1bc
ms.openlocfilehash: d1276b4adbbf40a586c6f2f2924d108cb96f8be4
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-jostle"></a>Esercitazione: Integrazione di Azure Active Directory con Jostle
Questa esercitazione descrive come integrare Jostle con Azure Active Directory (Azure AD).

L'integrazione di Jostle con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Jostle
* È possibile abilitare gli utenti per l'accesso Single Sign-On automatico a Jostle (SSO) con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con Jostle, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di **Jostle** abilitata per l'accesso Single Sign-On (SSO)

>[!NOTE]
>Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione. 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene testato l'accesso SSO di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Jostle dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On (SSO) di Microsoft Azure AD

## <a name="adding-jostle-from-the-gallery"></a>Aggiunta di Jostle dalla raccolta
Per configurare l'integrazione di Jostle in Azure AD, è necessario aggiungere Jostle dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Jostle dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico**fare clic su **Active Directory**nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **Jostle**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-jostle-tutorial/tutorial_jostle_01.png)
7. Nel riquadro dei risultati selezionare **Jostle**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-jostle-tutorial/tutorial_jostle_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurare e testare l'accesso Single Sign-On (SSO) di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Jostle in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso SSO, Azure AD deve conoscere qual è l'utente di Jostle che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Jostle.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Nome utente** in Jostle.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Jostle, è necessario completare i blocchi predefiniti seguenti:
1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Jostle](#creating-a-jostle-test-user)** : per avere una controparte di Britta Simon in Jostle collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-sso"></a>Configurazione dell'accesso Single Sign-On (SSO) di Microsoft Azure AD
Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure classico e configurare l'accesso SSO nell'applicazione Jostle.

**Per configurare l'accesso SSO di Azure AD con Jostle, seguire questa procedura:**

1. Nel menu in alto fare clic su **Avvio rapido**.
   
    ![Configura accesso Single Sign-On][6]
2. Nella pagina di integrazione dell'applicazione **Jostle** del portale di Azure classico, fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][7] 
3. Nella pagina **Stabilire come si desidera che gli utenti accedano a Jostle** selezionare **Single Sign-On di Azure AD** e fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-jostle-tutorial/tutorial_jostle_06.png)
4. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura: 
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-jostle-tutorial/tutorial_jostle_07.png)
  1. Nella casella di testo URL di accesso digitare l'URL usando il modello seguente: `https://<subdomain>.jostle.us/jostle-prod/`
  2. Fare clic su **Avanti**.
5. Nella pagina **Configurare l'accesso Single Sign-On in Jostle** fare clic su **Scarica metadati** e salvare il file sul computer.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-jostle-tutorial/tutorial_jostle_08.png)
6. Per ottenere l'accesso Single Sign-On configurato per l'applicazione, contattare l'account manager o il team di supporto tecnico di Jostle. Il canale appropriato fornirà l'aiuto richiesto per configurare l'accesso SSO. Si noti che è necessario inviare un messaggio di posta elettronica all'indirizzo <mailto:support@jostle.me>e allegare il file dei metadati scaricato.
7. Nel portale di Azure classico selezionare la conferma della configurazione e quindi fare clic su **Avanti**.
   
    ![Single Sign-On di Microsoft Azure AD][10]
8. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
   
    ![Single Sign-On di Microsoft Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
In questa sezione viene creato un utente test chiamato Britta Simon nel portale classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-jostle-tutorial/create_aaduser_09.png) 
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-jostle-tutorial/create_aaduser_03.png) 
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-jostle-tutorial/create_aaduser_04.png) 
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-jostle-tutorial/create_aaduser_05.png) 
  1. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
  2. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
  3. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura:
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-jostle-tutorial/create_aaduser_06.png) 
  1. Nella casella di testo **Nome** digitare **Britta**.  
  2. Nella casella di testo **Cognome** digitare **Simon**.
  3. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
  4. Nell'elenco **Ruolo** selezionare **Utente**.
  5. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-jostle-tutorial/create_aaduser_07.png) 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-jostle-tutorial/create_aaduser_08.png) 
  1. Prendere nota del valore visualizzato in **Nuova password**.
  2. Fare clic su **Completa**.   

### <a name="create-a-jostle-test-user"></a>Creare un utente test di Jostle
In questa sezione viene creato un utente chiamato Britta Simon in Jostle. Se non si sa come aggiungere Britta Simon in Jostle, rivolgersi al team di supporto di Jostle per aggiungere l'utente test e abilitare SSO. A tale scopo, scrivere a <mailto:support@jostle.me>.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD
In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Jostle.

![Assegna utente][200] 

**Per assegnare Britta Simon a Jostle, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201] 
2. Nell'elenco delle applicazioni, selezionare **Jostle**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-jostle-tutorial/tutorial_jostle_50.png) 
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203] 
4. Nell'elenco Tutti gli utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Jostle nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Jostle.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-jostle-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jostle-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jostle-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jostle-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-jostle-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-jostle-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-jostle-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-jostle-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-jostle-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-jostle-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jostle-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jostle-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-jostle-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-jostle-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-jostle-tutorial/tutorial_general_205.png

