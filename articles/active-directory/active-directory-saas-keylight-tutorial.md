---
title: 'Esercitazione: Integrazione di Azure Active Directory con Keylight | Documentazione Microsoft'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Keylight.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 234a32f1-9f56-4650-9e31-7b38ad734b1a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: ed2fc2b34ff10acc806daec84986f8db58e713c3
ms.openlocfilehash: f58c0967890ee99c574957f0cdfe1bb412f7f9e8
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-keylight"></a>Esercitazione: Integrazione di Azure Active Directory con Keylight
Questa esercitazione descrive come integrare Keylight con Azure Active Directory (Azure AD).

L'integrazione di Keylight con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Keylight.
* È possibile abilitare gli utenti per l'accesso Single Sign-On automatico (SSO) a Keylight con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con Keylight, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure.
* Sottoscrizione di Keylight abilitata per l'accesso Single Sign-On

>[!NOTE]
>Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione. 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. 

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Keylight dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="add-keylight-from-the-gallery"></a>Aggiungere Keylight dalla raccolta
Per configurare l'integrazione di Keylight in Azure AD, è necessario aggiungere Keylight dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Keylight dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico**fare clic su **Active Directory**nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **Keylight**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_01.png)
7. Nel riquadro dei risultati selezionare **Keylight** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_02.png)

## <a name="configure-and-testing-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Keylight con un utente test di nome "Britta Simon".

Per configurare e testare l'accesso Single Sign-On di Azure AD con Keylight, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente test di Keylight](#creating-a-keylight-test-user)**: per avere una controparte di Britta Simon in Keylight collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD
In questa sezione viene abilitato l'accesso Single Sign-On di Azure nel portale di Azure classico e viene configurato l'accesso Single Sign-On nell'applicazione Keylight.

**Per configurare l'accesso Single Sign-On di Azure AD con Keylight, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Keylight** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6] 
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Keylight**, selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_03.png) 
3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_04.png) 

    * Nella casella di testo URL di accesso digitare l'URL utilizzato dagli utenti per accedere all'applicazione Keylight adottando il modello seguente: **"https://\<nome società\>.keylightgrc.com/Login.aspx?saml=1"**.

4. Nella pagina **Configura accesso Single Sign-On in Keylight** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_05.png) 
   
    1. Fare clic su **Scarica certificato**e quindi salvare il file nel computer.
    2. Fare clic su **Avanti**.
5. Per abilitare l'accesso Single Sign-On in Keylight, seguire questa procedura:
   
    1. Accedere al proprio account Keylight come amministratore.
    2. Nel menu in alto fare clic su **Person** (Persona) e selezionare **Keylight Setup** (Configurazione Keylight).
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-keylight-tutorial/401.png) 
    3. Nella visualizzazione albero a sinistra fare clic su **SAML**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-keylight-tutorial/402.png) 
    4. Nella finestra di dialogo **SAML Settings** (Impostazioni SAML) fare clic su **Edit** (Modifica).
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-keylight-tutorial/404.png) 
6. Nella pagina della finestra di dialogo **Edit SAML Settings (Modifica impostazioni SAML)** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-keylight-tutorial/405.png) 
   
    1. Impostare **SAML authentication** (Autenticazione SAML) su **Active** (Attiva).
    2. Nel portale di Azure AD classico copiare il valore di **URL SSO SAML** e incollarlo nella casella di testo **Identity Provider Login URL** (URL di accesso al provider di identità).
    3. Nel portale di Azure AD classico copiare il valore di **URL servizio Single Sign-Out** e incollarlo nella casella di testo **Identity Provider Logout URL** (URL di disconnessione dal provider di identità).
    4. Fare clic su **Choose File** (Scegli file) per selezionare il certificato Keylight scaricato e quindi fare clic su **Open** (Apri) per caricare il certificato.
    5. Impostare **SAML User Id location** (Posizione ID utente SAML) su **NameIdentifier element of the subject statement** (Elemento NameIdentifier dell'istruzione Subject).
    6. Immettere il **Keylight Service Provider (Provider di servizi Keylight) adottando il modello seguente:**https://&lt;Nome società&gt;.keylightgrc.com**.
    7. Impostare i seguenti elementi:
     * **Auto-provision users** (Utenti che eseguono il provisioning automatico) su **Active** (Attivo).
     * **Auto-provision account type** (Tipo di account di provisioning automatico) su **Full User** (Utente completo).
     * **Auto-provision security role** (Ruolo di sicurezza del provisioning automatico) selezionare **Standard User with SAML** (Utente standard con SAML).
     * **Auto-provision security config** (Configurazione di sicurezza del provisioning automatico) selezionare **Standard User Configuration** (Configurazione utente standard).
    8. Immettere le informazioni seguenti:    
     * Nella casella di testo Email attribute digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
     * Nella casella di testo **First name attribute** digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
     * Nella casella di testo **Last name attribute** digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
    9. Fare clic su **Save**.

7. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e fare clic su **Avanti**.
   
    ![Single Sign-On di Microsoft Azure AD][10]
8. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
   
    ![Single Sign-On di Microsoft Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
In questa sezione viene creato un utente test chiamato Britta Simon nel portale di Azure classico.

* Nell'elenco di utenti selezionare **Britta Simon**.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_09.png) 
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_03.png) 
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_04.png) 
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_05.png) 
   
   1. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
   2. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
   3. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura:
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_06.png) 
   
   1. Nella casella di testo **Nome** digitare **Britta**.    
   2. Nella casella di testo **Cognome** digitare **Simon**.
   3. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
   4. Nell'elenco **Ruolo** selezionare **Utente**.
   5. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_07.png) 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_08.png) 
   
    1. Prendere nota del valore visualizzato in **Nuova password**.
    2. Fare clic su **Complete**.   

### <a name="create-a-keylight-test-user"></a>Creare un utente test di Keylight
In questa sezione viene creato un utente chiamato Britta Simon in Keylight. Keylight supporta il provisioning just-in-time, abilitato per impostazione predefinita.

Non è necessario alcun intervento dell'utente in questa sezione. Quando si accede a Keylight se l'utente non esiste ancora, viene creato un nuovo utente. 

>[!NOTE]
>Per creare un utente manualmente, è necessario contattare il team di supporto di Keylight. 
> 

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD
In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Keylight.

![Assegna utente][200] 

**Per assegnare Britta Simon a Keylight, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201] 
2. Nell'elenco di applicazioni selezionare **Keylight**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_50.png) 
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203] 
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On
In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Keylight nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Keylight.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_205.png

