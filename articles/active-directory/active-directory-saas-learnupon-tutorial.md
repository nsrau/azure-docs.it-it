---
title: 'Esercitazione: Integrazione di Azure Active Directory con Novatus | Documentazione Microsoft'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e LearnUpon.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: b11c6315-c79d-4f34-9610-bd17070ab7c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4a07f946d396a9263d5b00202cd5229ddc86d1be
ms.openlocfilehash: 02619192a5106a064a9f97cddac7c20bd73559e7
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Esercitazione: Integrazione di Azure Active Directory con LearnUpon
Questa esercitazione descrive l'integrazione di LearnUpon con Azure Active Directory (Azure AD).

L'integrazione di LearnUpon con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a LearnUpon
* È possibile abilitare gli utenti per l'accesso Single Sign-On automatico a LearnUpon (SSO) con i propri account Azure AD
* È possibile gestire gli account in una posizione centrale, il portale di Azure Active Directory classico. 

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con LearnUpon, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di LearnUpon abilitata per l'accesso Single Sign-On (SSO)

>[!NOTE]
>Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione. 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è quello di testare l'accesso Single Sign-On (SSO) di Azure AD in un ambiente di test. 

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di LearnUpon dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On (SSO) di Microsoft Azure AD

## <a name="adding-learnupon-from-the-gallery"></a>Aggiunta di LearnUpon dalla raccolta
Per configurare l'integrazione di LearnUpon in Azure AD, è necessario aggiungere LearnUpon dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere LearnUpon dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico**fare clic su **Active Directory**nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **LearnUpon**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_01.png)
7. Nel riquadro dei risultati selezionare **LearnUpon** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurare e testare l'accesso Single Sign-On (SSO) di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con LearnUpon in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di LearnUpon che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in LearnUpon.  

La relazione di collegamento viene stabilita assegnando al valore del **nome utente** in Azure AD lo stesso valore di **Username** in LearnUpon.

Per configurare e testare l'accesso Single Sign-On di Azure AD con LearnUpon, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test LearnUpon](#creating-a-learnupon-test-user)** : per avere una controparte di Britta Simon in LearnUpon collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD
Questa sezione descrive come abilitare l'accesso SSO di Azure AD nel portale di Azure classico e configurare l'accesso Single Sign-On nell'applicazione LearnUpon.

**Per configurare Single Sign-On di Azure AD con LearnUpon, seguire questa procedura:**

1. Nel pagina di integrazione dell'applicazione **LearnUpon** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6] 
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a LearnUpon** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_03.png) 
3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_04.png) 
  1. Nella casella di testo **URL di risposta** digitare l'URL del servizio consumer di asserzione usando il modello seguente: `https://\<companyname\>.learnupon.com/saml/consumer`
  2. Fare clic su **Avanti**. 
4. Nella pagina **Configura accesso Single Sign-On in LearnUpon** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_05.png)   
  1. Fare clic su **Scarica certificato**e quindi salvare il file nel computer. Questo certificato e gli URL dei metadati (ID entità, URL di accesso SSO e URL di disconnessione SSO) saranno necessari per configurare l'accesso Single Sign-On nel sito LearnUpon.
  2. Fare clic su **Avanti**.
5. Aprire un'altra istanza del browser e accedere a LearnUpon con un account amministratore. 
6. Fare clic sulla scheda **Impostazioni** .
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_06.png) 
7. Fare clic su **Single Sign On - SAML** e quindi fare clic su **General Settings** (Impostazioni generali) per configurare le impostazioni SAML.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_07.png) 
8. Nella sezione **General Settings** (Impostazioni generali), seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_08.png)  
  1. Selezionare **Enabled**.
  2. Per **Version** (Versione) selezionare **2.0**.
  3. In **Skip conditions** (Ignorare le condizioni) selezionare **No**.
  4. Nella casella di testo **SAML Token Post param name** (Nome parametro POST di richiesta token SAML) digitare il nome del parametro POST di richiesta all'URL del consumer SAML indicato in precedenza che contiene l'asserzione SAML da verificare e autenticare, ad esempio **SAMLResponse**.
  5. Nella casella di testo **Name Identifier Format** (Formato identificatore nome) digitare il valore che indica la posizione dell'identificatore degli utenti (indirizzo di posta elettronica) nell'asserzione SAML, ad esempio **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.
  6. Nella casella di testo **Identify Provider Location** (Identificare il percorso del provider) digitare il valore che indica il percorso a cui verranno indirizzati gli utenti quando fanno clic sull'icona caricata dalla schermata di accesso del portale di Azure classico.
  7. Nel portale di Azure classico copiare il valore di **URL servizio Single Sign-Out** e quindi incollarlo nella casella di testo **Sign out URL** (URL di disconnessione).
  8. Fare clic su **Manage finger prints**(Gestione impronte digitali) e quindi caricare l'impronta digitale del certificato scaricato. 
9. Fare clic su **User Settings**(Impostazioni utente), e seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_11.png)  
  1. Nella casella di testo **First Name Identifier Format** (Formato identificatore nome) digitare il valore che indica la posizione del nome degli utenti dell'asserzione SAML, ad esempio http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname. 
  2. Nella casella di testo **Last Name Identifier Format** (Formato identificatore cognome) digitare il valore che indica la posizione del nome degli utenti dell'asserzione SAML, ad esempio http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname.
7. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**.
   
    ![Single Sign-On di Microsoft Azure AD][10]
8. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
   
    ![Single Sign-On di Microsoft Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_09.png) 
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_03.png) 
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_04.png) 
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_05.png) 
  1. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
  2. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
  3. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura:
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_06.png) 
  1. Nella casella di testo **Nome** digitare **Britta**.   
  2. Nella casella di testo **Cognome** digitare **Simon**.
  3. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
  4. Nell'elenco **Ruolo** selezionare **Utente**.
  5. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_07.png) 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_08.png) 
  1. Prendere nota del valore visualizzato in **Nuova password**. 
  2. Fare clic su **Complete**.   

### <a name="create-a-learnupon-test-user"></a>Creare un utente test LearnUpon
Questa sezione descrive come creare un utente chiamato Britta Simon in LearnUpon. LearnUpon supporta il provisioning just-in-time, che è abilitato per impostazione predefinita.

Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso a LearnUpon verrà creato un nuovo utente, se questo non esiste già. [Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on).

>[!NOTE]
>Per creare un utente manualmente, è necessario contattare il team di supporto di LearnUpon. 
> 

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD
Questa sezione descrive come abilitare Britta Simon a usare l'accesso Single Sign-On di Azure concedendole l'accesso a LearnUpon.

![Assegna utente][200] 

**Per assegnare Britta Simon a LearnUpon, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201] 
2. Nell'elenco di applicazioni selezionare **LearnUpon**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_50.png) 
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203] 
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.  

Quando si fa clic sul riquadro LearnUpon nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione LearnUpon.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_205.png

