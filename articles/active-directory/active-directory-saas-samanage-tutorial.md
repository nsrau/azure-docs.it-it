---
title: 'Esercitazione: Integrazione di Azure Active Directory con Samanage | Documentazione Microsoft'
description: Informazioni su come usare Samanage con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f0db4fb0-7eec-48c2-9c7a-beab1ab49bc2
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/31/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: da49f1d9f8aa5fbbaf6e16f1db03a16f80a2bc92


---
# <a name="tutorial-azure-active-directory-integration-with-samanage"></a>Esercitazione: Integrazione di Azure Active Directory con Samanage
Questa esercitazione descrive l'integrazione di Samanage con Azure Active Directory (Azure AD).

L'integrazione di Samanage con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Samanage
* È possibile abilitare gli utenti per l'accesso automatico a Samanage (Single Sign-On) con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con Samanage, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure valida
* Un tenant di Samanage

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.
> 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test.

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Samanage dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-samanage-from-the-gallery"></a>Aggiunta di Samanage dalla raccolta
Per configurare l'integrazione di Samanage in Azure AD, è necessario aggiungerla dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Samanage dalla raccolta, seguire questa procedura:**

1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-samanage-tutorial/tutorial_general_01.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applications](./media/active-directory-saas-samanage-tutorial/tutorial_general_02.png "Applications")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungi applicazione](./media/active-directory-saas-samanage-tutorial/tutorial_general_03.png "Add application")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-samanage-tutorial/tutorial_general_04.png "Add an application from gallerry")
6. Nella **casella di ricerca** digitare **Samanage**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_01.png "Application gallery")
7. Nel riquadro dei risultati selezionare **Samanage**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Samanage](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_02.png "Samanage")

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con Samanage in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Samanage che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Samanage.

La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Username** in Samanage.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Samanage, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test per Samanage](#creating-a-Samanage-test-user)** : per avere una controparte di Britta Simon in Samanage collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD
In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure classico e viene configurato l'accesso Single Sign-On nell'applicazione Samanage.

**Per configurare Single Sign-On di Azure AD con Samanage, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Samanage** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-samanage-tutorial/tutorial_general_05.png "Configure single sign-on")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Samanage** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
   ![Single Sign-On di Microsoft Azure AD](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_03.png "Microsoft Azure AD Single Sign-On")
3. Nella pagina Configurare le impostazioni dell'app seguire questa procedura:
   
   ![Configura URL app](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_04.png "Configure App URL")
   
   a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<Company Name>.samanage.com/saml_login/<Company Name>`.
   
   b. Fare clic su **Avanti**
   
   > [!NOTE]
   > Si noti che questi non sono i valori reali. È necessario aggiornare questi valori con l'URL di accesso effettivo. Per ottenere questi valori, vedere il passaggio 8.c per altri dettagli o contattare Samanage.
   > 
   > 
4. Nella pagina **Configura accesso Single Sign-On in Samanage** fare clic su **Scarica certificato**, quindi salvare il file di certificato nel computer.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_05.png "Configure Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di Samanage come amministratore.
6. Fare clic su **Dashboard** e selezionare **Setup** (Configurazione) nel riquadro di spostamento a sinistra.
   
   ![Dashboard](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_001.png "Dashboard")
7. Fare clic su **Single Sign-On**.
   
   ![Single Sign-On](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_002.png "Single Sign-On")
8. Nella sezione **Login using SAML** (Accesso tramite SAML) seguire questa procedura:
   
   ![Login using SAML](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_003.png "Login using SAML")
   
   a.  Fare clic su **Enable Single Sign-On with SAML**(Abilita Single Sign-On con SAML).
   
   b.  Nella casella di testo **Identity Provider URL** (URL del provider di identità) inserire il valore di **ID provider di identità** dalla configurazione guidata dell'applicazione Azure AD.    
   
   c.  Verificare che **Login URL** (URL di accesso) corrisponda all'**URL di accesso** indicato nel passaggio 3.
   
   d.  Nella casella di testo **Logout URL** (URL di disconnessione) inserire il valore di **URL disconnessione remota** dalla configurazione guidata dell'applicazione Azure AD.
   
   e. Nella casella di testo **SAML Issuer** (Autorità di certificazione SAML) digitare l'URI ID app impostato nel provider di identità.
   
   f.  Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **Paste your Identity Provider x.509 Certificate below** (Incollare il certificato X.509 del provider di identità di seguito).
   
   g.  Fare clic su **Create users if they do not exist in Samanage**(Crea utenti se non presenti in Samanage).
   
   h.  Fare clic su **Aggiorna**.
9. Nel portale di Azure classico selezionare la conferma della configurazione e quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_06.png "Configure Single Sign-On")
10. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_07.png "Configure Single Sign-On")

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente di test chiamato Britta Simon nel portale classico.

![Creazione di un utente test di Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_00.png)

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_01.png)
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_02.png)
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_03.png)
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_04.png)
   
    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
   
    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
   
    c. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura:
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_05.png)
   
   a. Nella casella di testo **Nome** digitare **Britta**.  
   
   b. Nella casella di testo **Cognome** digitare **Simon**.
   
   c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
   
   d. Nell'elenco **Ruolo** selezionare **Utente**.
   
   e. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_06.png)
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_07.png)
   
    a. Prendere nota del valore visualizzato in **Nuova password**.
   
    b. Fare clic su **Completa**.   

### <a name="creating-a-samanage-test-user"></a>Creazione di un utente test per Samanage
Per consentire agli utenti di Azure AD di accedere a Samanage, è necessario eseguirne il provisioning in Samanage. Nel caso di Samanage, il provisioning è un'attività manuale.

#### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, seguire questa procedura:
1. Accedere al sito aziendale di Samanage come amministratore.
2. Fare clic su **Dashboard** e selezionare **Setup** (Configurazione) nel riquadro di spostamento a sinistra.
   
   ![Configurazione](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_001.png "Setup")
3. Fare clic sulla scheda **Users** .
   
   ![Utenti](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_006.png "Users")
4. Fare clic su **Nuovo utente**.
   
   ![Nuovo utente](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_007.png "New User")
5. In **Name** (Nome) e **Email Address** (Indirizzo di posta elettronica) digitare nome e indirizzo di posta elettronica di un account Azure AD di cui si vuole effettuare il provisioning e fare clic su **Create user** (Crea utente).
   
   ![Crea utente](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_008.png "Creat User")
   
   > [!NOTE]
   > Il titolare dell'account Azure AD riceverà un messaggio di posta elettronica contenente un collegamento da selezionare per confermare e attivare l'account. È possibile usare qualsiasi altro strumento o API di creazione di account utente offerti da Samanage per eseguire il provisioning degli account utente di Azure AD.
   > 
   > 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD
Questa sezione descrive come abilitare Britta Simon per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Samanage.

![Assegna utenti](./media/active-directory-saas-samanage-tutorial/assign_aaduser_00.png "Assign users")

**Per assegnare Britta Simon a Samanage, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utenti](./media/active-directory-saas-samanage-tutorial/assign_aaduser_01.png "Assign users")
2. Nell'elenco di applicazioni selezionare **Samanage**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_08.png)
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utenti](./media/active-directory-saas-samanage-tutorial/assign_aaduser_02.png "Assign users")
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utenti](./media/active-directory-saas-samanage-tutorial/assign_aaduser_03.png "Assign users")

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Samanage nel pannello di accesso, verrà eseguito automaticamente l'accesso all'applicazione Samanage.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)




<!--HONumber=Nov16_HO3-->


