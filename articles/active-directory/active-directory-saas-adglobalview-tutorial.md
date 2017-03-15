---
title: 'Esercitazione: Integrazione di Azure Active Directory con ADP GlobalView | Microsoft Docs'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e ADP GlobalView.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: ffb6464f-714d-41a9-869a-2b7e5ae9f125
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 0f767b96fa8f8f9d1a22709633bee64f0477ffb3
ms.openlocfilehash: 46fbe3233af22605d4cd89227b91313128e523f9
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-adp-globalview"></a>Esercitazione: Integrazione di Azure Active Directory con ADP GlobalView
Questa esercitazione descrive l'integrazione di ADP GlobalView con Azure Active Directory (Azure AD).  

L'integrazione di ADP GlobalView con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere ad ADP GlobalView
* È possibile abilitare gli utenti per l'accesso automatico Single Sign-On (SSO) ad ADP GlobalView con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con ADP GlobalView, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di ADP GlobalView abilitata per l'accesso Single Sign-On (SSO)

>[!NOTE]
>Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione. 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è quello di testare l'accesso Single Sign-On (SSO) di Azure AD in un ambiente di test.  

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di ADP GlobalView dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On (SSO) di Microsoft Azure AD

## <a name="add-adp-globalview-from-the-gallery"></a>Aggiungere ADP GlobalView dalla raccolta
Per configurare l'integrazione di ADP GlobalView in Azure AD, è necessario aggiungere ADP GlobalView dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere ADP GlobalView dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico**fare clic su **Active Directory**nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **ADP GlobalView**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_01.png)
7. Nel riquadro dei risultati selezionare **ADP GlobalView** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_06.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurare e testare l'accesso Single Sign-On (SSO) di Azure AD
Questa sezione descrive come configurare e testare l'accesso SSO di Azure AD con ADP GlobalView in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso SSO, Azure AD deve conoscere qual è l'utente di ADP GlobalView che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e un utente correlato in ADP GlobalView.  

La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Username** (Nome utente) in ADP GlobalView.

Per configurare e testare l'accesso SSO di Azure AD con ADP GlobalView, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test ADP GlobalView](#creating-a-adp-globalview-test-user)** : per avere una controparte di Britta Simon in ADP GlobalView collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD
Questa sezione descrive come abilitare l'accesso SSO di Azure AD nel portale di Azure classico e come configurare l'accesso SSO nell'applicazione ADP GlobalView.

L'applicazione ADP GlobalView prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. 

La schermata seguente illustra un esempio relativo a questa operazione. Il nome dell'attestazione sarà sempre **"PersonImmutableID"** e il valore di cui è stato eseguito il mapping a ExtensionAttribute2, che contiene il valore EmployeeID dell'utente. Il mapping degli utenti da Azure AD ad ADP GlobalView verrà eseguito in EmployeeID, ma è possibile eseguire il mapping a un valore diverso, anche in base alle impostazioni dell'applicazione. 

È possibile collaborare prima di tutto con il team di ADP GlobalView per usare l'identificatore corretto di un utente ed eseguire il mapping di tale valore all'attestazione **"PersonImmutableID"**. È anche possibile mappare l'attestazione di posta elettronica e ID utente come illustrato nell'immagine.

![Configura accesso Single Sign-On](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_02.png) 

Prima di poter configurare l'asserzione SAML, è necessario contattare il team di supporto di ADP GlobalView e richiedere il valore dell'attributo dell'identificatore univoco per il tenant. Questo valore è necessario per configurare l'attestazione personalizzata per l'applicazione.

**Per configurare SSO di Azure AD con ADP GlobalView, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **ADP GlobalView** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6] 
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a ADP GlobalView** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_03.png) 
3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_04.png) 
  1. Nella casella di testo **Identificatore** digitare l'URL usato per identificare l'applicazione ADP GlobalView usando uno dei modelli seguenti: `https://<server name>.globalview.adp.com/federate2` o `https://<server name>.globalview.adp.com/federate`
  2. Nella casella di testo **URL di risposta** digitare l'URL usato da Azure AD per registrare la risposta all'applicazione ADP GlobalView usando uno dei modelli seguenti: `https://<server name>.globalview.adp.com/federate2/sp/ACS.saml2` o `https://<server name>.globalview.adp.com/federate/sp/ACS.saml2`
  3. Fare clic su **Avanti**.
4. Nella pagina **Configura accesso Single Sign-On in ADP GlobalView** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_05.png)   
  1. Fare clic su **Scarica certificato**e quindi salvare il file nel computer.
  2. Fare clic su **Avanti**.
5. Per ottenere l'accesso Single Sign-On configurato per l'applicazione, contattare il team di supporto ADP GlobalView e fornire i seguenti elementi: 
   
   * Il **certificato** scaricato
   * L'**ID entità**
   * L'**URL SSO SAML**
   * L'**URL servizio Single Sign-Out**

    >[!NOTE]
    >Non appena il team di **ADP GlobalView** avrà configurato l'istanza, ottenere il valore **RelayState**. Per configurarlo, seguire i passaggi indicati di seguito. Al termine della configurazione, è possibile testare l'integrazione. Si noti quindi che questa è un'operazione di configurazione importante per il funzionamento dell'integrazione con questa applicazione.
    >

6. Per configurare il valore RelayState in Azure AD, seguire questa procedura:   
 1. Accedere al [portale di gestione di Azure](https://portal.azure.com) come amministratore.
 2. Nel riquadro di spostamento a sinistra fare clic su **Altri servizi**. 
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_07.png)  
 3. Nella casella di testo **Cerca** digitare **Azure Active Directory** e quindi fare clic sul collegamento correlato.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_08.png)  
 4. Fare clic su **Applicazioni aziendali**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_09.png) 
 5. Nella sezione **Gestisci** fare clic su **Tutte le applicazioni**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_10.png) 
 6. Nella casella di testo **Cerca** digitare **ADP eTime** e quindi fare clic sul collegamento correlato. 
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_11.png) 
 7. Nella sezione **Gestisci** fare clic su **Single Sign-On**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_12.png)  
 8. Selezionare **Mostra impostazioni URL avanzate**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_13.png)
 9. Nella casella di testo **Stato dell'inoltro** digitare un valore usando i modelli seguenti:
   
    `https://<server name>.globalview.adp.com/gvolution/session/<instance name>/sso` 
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_14.png)  
 10. Salvare le impostazioni.
7. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**.
   
    ![Single Sign-On di Microsoft Azure AD][10]
8. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
   
    ![Single Sign-On di Microsoft Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure classico.  

* Nell'elenco di utenti selezionare **Britta Simon**.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_09.png) 
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_03.png) 
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_04.png) 
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_05.png)  
 1. In Tipo di utente selezionare Nuovo utente nell'organizzazione.  
 2. Nella casella di testo **Nome utente** digitare **BrittaSimon**. 
 3. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura:
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_06.png)  
 1. Nella casella di testo **Nome** digitare **Britta**.    
 2. Nella casella di testo **Cognome** digitare **Simon**. 
 3. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**. 
 4. Nell'elenco **Ruolo** selezionare **Utente**. 
 5. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_07.png) 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_08.png)  
 1. Prendere nota del valore visualizzato in **Nuova password**.  
 2. Fare clic su **Completa**.   

### <a name="create-a-adp-globalview-test-user"></a>Creare un utente test per ADP GlobalView
L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in ADP GlobalView. Collaborare con il team di supporto di ADP GlobalView per aggiungere gli utenti nell'account ADP GlobalView. 

>[!NOTE]
>Per creare un utente manualmente, è necessario contattare il team di supporto di ADP GlobalView.
> 

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD
Questa sezione descrive come abilitare Britta Simon all'uso dell'accesso SSO di Azure concedendole l'accesso ad ADP GlobalView.

![Assegna utente][200] 

**Per assegnare Britta Simon ad ADP GlobalView, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201] 
2. Nell'elenco di applicazioni selezionare **ADP GlobalView**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_50.png) 
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203] 
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.  

Quando si fa clic sul riquadro ADP GlobalView nel pannello di accesso, si accede automaticamente all'applicazione ADP GlobalView.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_205.png

