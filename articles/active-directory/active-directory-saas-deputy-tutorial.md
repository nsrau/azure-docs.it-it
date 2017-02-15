---
title: 'Esercitazione: Integrazione di Azure Active Directory con Deputy | Microsoft Docs'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Deputy.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 5665c3ac-5689-4201-80fe-fcc677d4430d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/28/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f0db022251b16c2c3fe1ca93c25d8550a2b899d8


---
# <a name="tutorial-azure-active-directory-integration-with-deputy"></a>Esercitazione: Integrazione di Azure Active Directory con Deputy
Questa esercitazione descrive l'integrazione di Deputy con Azure Active Directory (Azure AD).

L'integrazione di Deputy con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Deputy
* È possibile abilitare gli utenti per l'accesso automatico a Deputy (Single Sign-On) con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con Deputy, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di Deputy abilitata per l'accesso Single Sign-On

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

1. Aggiunta di Deputy dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-deputy-from-the-gallery"></a>Aggiunta di Deputy dalla raccolta
Per configurare l'integrazione di Deputy in Azure AD, è necessario aggiungere Deputy dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Deputy dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **Deputy**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_01.png)
7. Nel riquadro dei risultati selezionare **Deputy** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Selezione dell'app nella raccolta](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_0001.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con Deputy in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Deputy che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Deputy.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in Deputy.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Deputy, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Deputy](#creating-a-deputy-test-user)** : per avere una controparte di Britta Simon in Deputy collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD
In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure classico e viene configurato l'accesso Single Sign-On nell'applicazione Deputy.

**Per configurare Single Sign-On di Azure AD con Deputy, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Deputy** del portale di Azure classico, fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6] 
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Deputy** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_03.png)
3. Nella pagina della finestra di dialogo **Configurare le impostazioni dell'app**, se si desidera configurare l'applicazione in **modalità iniziata da IDP**,seguire la procedura seguente e fare clic su **Avanti**:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_04.png)
   
    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<your-subdomain>.<region>.deputy.com`.
   
    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<your-subdomain>.<region>.deputy.com/exec/devapp/samlacs`.
   
    c. Fare clic su **Avanti**.
4. Se si desidera configurare l'applicazione in **SP initiated mode** (Modalità iniziata dal provider di servizi) nella finestra di dialogo **Configurare le impostazioni dell'app** fare clic su **"Mostra opzioni avanzate (facoltativo)"**, quindi digitare l'**URL di accesso** e fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_05.png)
   
    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<your-subdomain>.<region>.deputy.com`.
   
    b. Fare clic su **Avanti**.
   
   > [!NOTE]
   > Il suffissi della dell'area di Deputy è facoltativo oppure usare uno dei seguenti: au | na | eu |as |la |af |an |ent-au |ent-na |ent-eu |ent-as | ent-la | ent-af | ent-an
   > 
   > 
5. Nella pagina **Configura accesso Single Sign-On in Deputy** seguire questa procedura e fare clic su **Avanti**:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_06.png)
   
    a. Fare clic su **Scarica certificato**e quindi salvare il file nel computer.
6. Accedere all'URL seguente: https://(your-subdomain).deputy.com/exec/config/system_config. Passare a **Security Settings**(Impostazioni di sicurezza) e fare clic su **Modifica**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_004.png)
7. Nel portale di Azure classico, nella pagina Configura accesso Single Sign-On in Deputy copiare l'URL SSO SAML. 
8. In questa pagina **Security Settings** (Impostazioni di sicurezza) attenersi alla procedura seguente.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_005.png)
   
    a. Abilitare **Social Login**(Accesso social).
   
    b. Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **OpenSSL Certificate** (Certificato OpenSSL).
   
    c. Nella casella di testo SAM SSO URL (URL SSO SAM), digitare `https://<your subdomain>.deputy.com/exec/devapp/samlacs?dpLoginTo=<saml sso url>`
   
    d. Nella casella di testo SAM SSO URL (URL SSO SAM) sostituire `<your subdomain>` con il sottodominio.
   
    e. Nella casella di testo SAM SSO URL (URL SSO SAM) sostituire `<saml sso url>` con l'URL SSO SAML copiato dal portale di Azure classico.
   
    f. Fare clic su **Salva impostazioni**.
9. Nel portale classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**.
   
    ![Single Sign-On di Microsoft Azure AD][10]
10. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
    
    ![Single Sign-On di Microsoft Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente di test chiamato Britta Simon nel portale classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_09.png)
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_03.png)
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_04.png)
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_05.png)
   
    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
   
    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
   
    c. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura:
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_06.png)
   
   a. Nella casella di testo **Nome** digitare **Britta**.  
   
   b. Nella casella di testo **Cognome** digitare **Simon**.
   
   c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
   
   d. Nell'elenco **Ruolo** selezionare **Utente**.
   
   e. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_07.png)
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_08.png)
   
    a. Prendere nota del valore visualizzato in **Nuova password**.
   
    b. Fare clic su **Complete**.   

### <a name="creating-a-deputy-test-user"></a>Creazione di un utente test di Deputy
Per consentire agli utenti di Azure AD di accedere a Deputy, è necessario eseguirne il provisioning in Deputy. Nel caso di Deputy, il provisioning è un'attività manuale.

#### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, seguire questa procedura:
1. Accedere al sito aziendale di Deputy come amministratore.
2. Nel pannello di navigazione in alto fare clic su **People**(Persone).
   
   ![Persone](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_001.png "People")
3. Fare clic su **Add People** (Aggiungi persone) e quindi su **Add a single person** (Aggiungi una singola persona).
   
   ![Add People (Aggiungi persone)](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_002.png "Add People")
4. Eseguire i passaggi seguenti e fare clic su **Save & Invite** (Salva e invita).
   
   ![Nuovo utente](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_003.png "New User")
   
   a. Nella casella di testo **Name** (Nome) digitare **Britta** e **Simon**.  
   
   b. Nella casella di testo **Email** (Indirizzo di posta elettronica) digitare l'indirizzo di posta elettronica dell'account Azure AD di cui si vuole eseguire il provisioning.
   
   c. Nella casella di testo **Works at** (Lavora presso) digitare il nome dell'azienda.
   
   d. Fare clic sul pulsante **Save & Invite** (Salva e invita).
   
   > [!NOTE]
   > Il titolare dell'account Azure AD riceverà un messaggio di posta elettronica contenente un collegamento da selezionare per confermare e attivare l'account. È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Deputy per eseguire il provisioning degli account utente di AAD.
   > 
   > 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD
L'obiettivo di questa sezione consiste nell'abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Deputy.

![Assegna utente][200]

**Per assegnare Britta Simon a Deputy, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione Directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201]
2. Nell'elenco di applicazioni selezionare **Deputy**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_50.png)
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203]
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Deputy nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Deputy.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


