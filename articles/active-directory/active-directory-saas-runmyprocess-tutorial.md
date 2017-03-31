---
title: 'Esercitazione: Integrazione di Azure Active Directory con RunMyProcess | Microsoft Docs'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e RunMyProcess.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: d31f7395-048b-4a61-9505-5acf9fc68d9b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 8aeac420b3848a270a3a9a325c9db0f1569a33e6
ms.openlocfilehash: 2f6bcdd9ea97494d3d12ea4ffd541b4b95a91c4f
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-runmyprocess"></a>Esercitazione: Integrazione di Azure Active Directory con RunMyProcess
L'obiettivo di questa esercitazione è descrivere l'integrazione di RunMyProcess con Azure Active Directory (Azure AD).

L'integrazione di RunMyProcess con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a RunMyProcess
* È possibile abilitare gli utenti per l'accesso Single Sign-On automatico a RunMyProcess (SSO) con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con RunMyProcess, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di RunMyProcess abilitata per l'accesso Single Sign-On (SSO)

>[!NOTE]
>Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione. 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è quello di testare l'accesso Single Sign-On (SSO) di Azure AD in un ambiente di test.

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di RunMyProcess dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On (SSO) di Microsoft Azure AD

## <a name="add-runmyprocess-from-the-gallery"></a>Aggiungere RunMyProcess dalla raccolta
Per configurare l'integrazione di RunMyProcess in Azure AD, è necessario aggiungere RunMyProcess dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere RunMyProcess dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico**fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **RunMyProcess**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_01.png)
7. Nel riquadro dei risultati selezionare **RunMyProcess**, quindi fare clic su **Complete** per aggiungere l'applicazione.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_0001.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurare e testare l'accesso Single Sign-On (SSO) di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con RunMyProcess in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di RunMyProcess che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in RunMyProcess.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in RunMyProcess.

Per configurare e testare l'accesso SSO di Azure AD con RunMyProcess, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di RunMyProcess](#creating-a-runmyprocess-test-user)**: per avere una controparte di Britta Simon in RunMyProcess collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD
In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure classico e viene configurato l'accesso SSO nell'applicazione RunMyProcess.

**Per configurare l'accesso SSO di Azure AD con RunMyProcess, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **RunMyProcess** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6] 
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a RunMyProcess** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_03.png) 
3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_04.png) 
  1. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://live.runmyprocess.com/live/<tenant id>`. 
  2. Fare clic su **Avanti**.
    >[!NOTE]
    >È necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere tale valore, contattare il team di supporto di RunMyProcess tramite <mailto:support@runmyprocess.com>.
    >  
4. Nella pagina **Configura accesso Single Sign-On in RunMyProcess** fare clic su **Scarica certificato** e quindi salvare il file nel computer.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_05.png)
5. In un'altra finestra del Web browser accedere al tenant di RunMyProcess come amministratore.
6. Nel pannello di spostamento a sinistra fare clic su **Account** e selezionare **Configuration** (Configurazione).
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_001.png)
7. Passare alla sezione **Authentication method** (Metodo di autenticazione) e seguire questa procedura:
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_002.png)
  1. In **Metodo** selezionare **SSO con Samlv2**. 
  2. Nella casella di testo **SSO redirect** (Reindirizzamento SSO) inserire il valore di **URL SSO SAML** dalla configurazione guidata dell'applicazione di Azure AD.
  3. Nella casella di testo **Logout redirect** (Reindirizzamento disconnessione) inserire il valore di **URL servizio Single Sign-Out** dalla configurazione guidata dell'applicazione di Azure AD.
  4. Nella casella di testo **Name Id Format** (Formato ID nome) inserire il valore di **Formato identificatore nome** dalla configurazione guidata dell'applicazione di Azure AD.
  5. Copiare il contenuto del file del certificato scaricato e incollarlo nella casella di testo **Certificate** (Certificato). 
  6. Fare clic sull'icona di **salvataggio**.
8. Nel portale di Azure classico selezionare la conferma della configurazione e fare clic su **Avanti**.
   
    ![Single Sign-On di Microsoft Azure AD][10]
9. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
   
    ![Single Sign-On di Microsoft Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
Questa sezione descrive come creare un utente di test chiamato Britta Simon nel portale classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_01.png) 
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_02.png) 
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_03.png) 
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_04.png) 
 1. In Tipo di utente selezionare Nuovo utente nell'organizzazione.  
 2. Nella casella di testo **Nome utente** digitare **BrittaSimon**. 
 3. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura:

   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_05.png)  
 1. Nella casella di testo **Nome** digitare **Britta**.   
 2. Nella casella di testo **Cognome** digitare **Simon**. 
 3. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**. 
 4. Nell'elenco **Ruolo** selezionare **Utente**. 
 5. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_06.png) 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_07.png)  
 1. Prendere nota del valore visualizzato in **Nuova password**.
 2. Fare clic su **Completa**.   

### <a name="create-a-runmyprocess-test-user"></a>Creare un utente test di RunMyProcess
Per consentire agli utenti di Azure AD di accedere a RunMyProcess, è necessario eseguirne il provisioning in RunMyProcess. Nel caso di RunMyProcess, il provisioning è un'attività manuale.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di RunMyProcess come amministratore.
2. Nel pannello di spostamento a sinistra fare clic su **Account** e selezionare **Users** (Utenti), quindi fare clic su **New User** (Nuovo utente).
   
   ![Nuovo utente](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_003.png "Nuovo utente")
3. Nella sezione **Impostazioni utente** , eseguire la procedura seguente:
   
   ![Profilo](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_004.png "Profilo")   
 1. Digitare il **nome** e l'**indirizzo di posta elettronica** di un account di Azure Active Directory valido di cui si desidera eseguire il provisioning nelle relative caselle di testo. 
 2. Selezionare una **lingua per l'IDE**, una **lingua** e un **profilo**. 
 3. Selezionare **Inviami un messaggio di posta elettronica di creazione account**. 
 4. Fare clic su **Save**.
   
   >[!NOTE]
   >È possibile usare qualsiasi altro strumento o API di creazione di account utente offerti da RunMyProcess per eseguire il provisioning degli account utente di Azure AD. 
   > 

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD
Questa sezione descrive come abilitare Britta Simon a usare l'accesso Single Sign-On di Azure concedendole l'accesso a RunMyProcess.

![Assegna utente][200] 

**Per assegnare Britta Simon a RunMyProcess, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201] 
2. Nell'elenco di applicazioni selezionare **RunMyProcess**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_50.png) 
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203]
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro RunMyProcess nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione RunMyProcess.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_205.png

