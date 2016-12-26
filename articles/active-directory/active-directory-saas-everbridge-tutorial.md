---
title: 'Esercitazione: Integrazione di Azure Active Directory con Everbridge | Microsoft Docs'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Everbridge.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 58d7cd22-98c0-4606-9ce5-8bdb22ee8b3e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 5608f38de6ae78772499a40c4e2d449cd5efc79c
ms.openlocfilehash: 511a3883cadd9bf2fe1cc39a8626c49fa19a7d8e


---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Esercitazione: Integrazione di Azure Active Directory con EverBridge
Questa esercitazione descrive l'integrazione di Everbridge con Azure Active Directory (Azure AD).

L'integrazione di Everbridge con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Everbridge
* È possibile abilitare gli utenti per l'accesso automatico a Everbridge (Single Sign-On) con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con Everbridge, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di Everbridge abilitata per l'accesso Single Sign-On

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

1. Aggiunta di Everbridge dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-everbridge-from-the-gallery"></a>Aggiunta di Everbridge dalla raccolta
Per configurare l'integrazione di Everbridge in Azure AD, è necessario aggiungere Everbridge dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Everbridge dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **Everbridge**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_01.png)
7. Nel riquadro dei risultati selezionare **Everbridge** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Selezione dell'app nella raccolta](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_001.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con Everbridge in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Everbridge che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Everbridge.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in Everbridge.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Everbridge, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Everbridge](#creating-a-everbridge-test-user)** : per avere una controparte di Britta Simon in Everbridge collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD
In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale classico e viene configurato l'accesso Single Sign-On nell'applicazione Everbridge.

**Per configurare l'accesso Single Sign-On di Azure AD con Everbridge, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Everbridge** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6] 
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Everbridge** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_03.png) 
3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura e fare clic su **Avanti**:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_04.png)
   
    a. Nella casella di testo **Identificatore** digitare l'URL usando il modello seguente: `https://sso.everbridge.net/{<company name>}`
   
    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://manager.everbridge.net/saml/SSO/{<company name>}/alias/defaultAlias`
   
    c. Fare clic su **Avanti**
4. Nella pagina **Configura accesso Single Sign-On in Everbridge** seguire questa procedura e fare clic su **Avanti**:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_05.png)
   
    a. Fare clic su **Scarica metadati**e quindi salvare il file nel computer.
   
    b. Fare clic su **Avanti**.
5. Per ottenere SSO configurato per l'applicazione, è necessario accedere al tenant di Everbridge come amministratore.
6. Nel menu in alto fare clic sulla scheda **Settings** (Impostazioni) e selezionare **Single Sign-On** sotto **Security** (Sicurezza).
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_002.png)
   
    a. Nella casella di testo **Name** digitare il nome del provider di identità (ad esempio, il nome della propria società).
   
    b. Nella casella di testo **API Name** (Nome API) digitare il nome dell'API.
   
    C. Fare clic sul pulsante **Choose File** (Scegli file) per caricare il file di metadati scaricato al **passaggio 4**.
   
    d. In **SAML Identity Location**(Percorso identità SAML) selezionare "Identity is in the NameIdentifier element of the Subject statement" (L'identità è nell'elemento NameIdentifier dell'istruzione Subject).
   
    e. Copiare l'URL SSO SAML di Azure AD nella casella **Identity Provider Login URL** (URL di accesso provider di identità) in Everbridge.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_003.png)
   
    f. Per **Service Provider Initiated Request Binding**(Binding richiesta avviato dal provider di servizi), selezionare HTTP Redirect (Reindirizzamento HTTP).
7. Nel portale di Azure classico selezionare la conferma della configurazione e fare clic su **Avanti**.
   
    ![Single Sign-On di Microsoft Azure AD][10]
8. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
   
    ![Single Sign-On di Microsoft Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente di test chiamato Britta Simon nel portale classico.

Nell'elenco di utenti selezionare **Britta Simon**.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-everbridge-tutorial/create_aaduser_09.png)
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-everbridge-tutorial/create_aaduser_03.png)
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-everbridge-tutorial/create_aaduser_04.png)
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-everbridge-tutorial/create_aaduser_05.png)
   
    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
   
    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
   
    c. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-everbridge-tutorial/create_aaduser_06.png)
   
    a. Nella casella di testo **Nome** digitare **Britta**.  
   
    b. Nella casella di testo **Cognome** digitare **Simon**.
   
    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
   
    d. Nell'elenco **Ruolo** selezionare **Utente**.
   
    e. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-everbridge-tutorial/create_aaduser_07.png)
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-everbridge-tutorial/create_aaduser_08.png)
   
    a. Prendere nota del valore visualizzato in **Nuova password**.
   
    b. Fare clic su **Completa**.   

### <a name="creating-a-everbridge-test-user"></a>Creazione di un utente test di Everbridge
In questa sezione viene creato un utente chiamato Britta Simon in Everbridge. Collaborare con il team di supporto di Everbridge, all'indirizzo <mailto:support@everbridge.com>, per aggiungere gli utenti alla piattaforma Everbridge.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD
Questa sezione descrive come abilitare Britta Simon per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Everbridge.

![Assegna utente][200]

**Per assegnare Britta Simon a Everbridge, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201]
2. Nell'elenco delle applicazioni selezionare **Everbridge**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_50.png)
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203]
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Everbridge nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Everbridge.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO4-->


