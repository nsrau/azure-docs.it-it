---
title: 'Esercitazione: Integrazione di Azure Active Directory con Image Relay | Microsoft Docs'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Image Relay.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 65bb5990-07ef-4244-9f41-cd28fc2cb5a2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4f7a99e82c73d446c928a821f80fc640a1994d67
ms.openlocfilehash: cfcb97cbf5b1d2beff5c868ce8371918dbba4e3e


---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>Esercitazione: Integrazione di Azure Active Directory con Image Relay
Questa esercitazione descrive l'integrazione di Image Relay con Azure Active Directory (Azure AD).  
L'integrazione di Image Relay con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Image Relay
* È possibile abilitare gli utenti per l'accesso automatico a Image Relay (Single Sign-On) con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con Image Relay, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di Image Relay abilitata per l'accesso Single Sign-On

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

1. Aggiunta di Image Relay dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-image-relay-from-the-gallery"></a>Aggiunta di Image Relay dalla raccolta
Per configurare l'integrazione di Image Relay in Azure AD, è necessario aggiungere Image Relay dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Image Relay dalla raccolta, seguire questa procedura:**

1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella **casella di ricerca** digitare Image Relay.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_01.png)
7. Nel riquadro dei risultati selezionare **Image Relay** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con ImageRelay con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD richiede un account utente che rappresenta l'utente correlato in Image Relay.  In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Image Relay.  
La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Nomeutente** in Image Relay.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Image Relay, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Image Relay](#creating-a-userecho-test-user)**: per avere una controparte di Britta Simon in Image Relay collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel Portale di Azure classico e configurare l'accesso Single Sign-On nell'applicazione Image Relay.

**Per configurare l'accesso Single Sign-On di Azure AD con Image Relay, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Image Relay** del Portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
     ![Configura accesso Single Sign-On][6] 
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Image Relay** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_03.png) 
3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:
   
     ![Configura accesso Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_04.png) 
   
    a. Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere all'applicazione Image Relay, ad esempio *https://fabrikam.ImageRelay.com/*.
   
    b. Fare clic su **Avanti**.
4. Nella pagina **Configura accesso Single Sign-On in Image Relay** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_05.png) 
   
    a. Fare clic su **Scarica certificato**e quindi salvare il file nel computer.
   
    b. Fare clic su **Next**.
5. In un'altra finestra del browser accedere al sito aziendale di Image Relay come amministratore.
6. Nel barra degli strumenti in alto fare clic sul carico di lavoro **Users & Permissions** (Utenti e autorizzazioni).
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_06.png) 
7. Fare clic su **Create New Permission**(Crea nuova autorizzazione).
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_08.png) 
8. Nel carico di lavoro **Impostazioni Single Sing-On** selezionare la casella di controllo **This Group can only sign-in via Single Sign On** (Questo gruppo può accedere solo con Single Sign-On) e quindi fare clic su **Salva**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_09.png) 
9. Passare ad **Account Settings**(Impostazioni account).
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_10.png) 
10. Selezionare il carico di lavoro **Single Sign On Settings** (Impostazioni Single Sing-On).
    
     ![Configura accesso Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_11.png)
11. Nella finestra di dialogo **SAML Settings** (Impostazioni SAML) seguire questa procedura:
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_12.png)
    
    a. Nel portale di Azure classico copiare il valore di **URL servizio Single Sign-On** e quindi incollarlo nella casella di testo **URL di accesso**.

    b. Nel Portale di Azure classico copiare il valore di **URL servizio Single Sign-On** e quindi incollarlo nella casella di testo **URL disconnessione**.

    c. Come **Name Id Format** (Formato ID nome) selezionare**urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.

    d. Come **Binding Options for Requests from the Service Provider (Image Relay)** (Opzioni di associazione per le richieste dal provider di servizi - Inoltro immagini) selezionare **POST Binding** (Associazione POST).

    e. In **Certificato X.509** fare clic su **Aggiorna certificato**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. Aprire il certificato scaricato nel Blocco note, copiarne il contenuto e incollarlo nella casella di testo X.509 Certificate (Certificato X.509).

    ![Configura accesso Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. Nella sezione **Just-In-Time User Provisioning** (Provisioning utenti Just-In-Time) selezionare la casella di controllo **Enable Just-In-Time User Provisioning** (Abilita provisioning utenti Just-In-Time).

    ![Configura accesso Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Selezionare il gruppo di autorizzazioni, ad esempio, **SSO Basic**(SSO di base) a cui sarà consentito l'accesso solo tramite Single Sign-On.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. Fare clic su **Save**.

1. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e fare clic su **Avanti**.
   
    ![Single Sign-On di Microsoft Azure AD][10]
2. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.
   
    ![Single Sign-On di Microsoft Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_09.png) 
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_03.png) 
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_04.png) 
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_05.png) 
   
    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
   
    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
   
    c. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_06.png) 
   
    a. Nella casella di testo **Nome** digitare **Britta**.  
   
    b. Nella casella di testo **Cognome** digitare **Simon**.
   
    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
   
    d. Nell'elenco **Ruolo** selezionare **Utente**.
   
    e. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_07.png) 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_08.png) 
   
    a. Prendere nota del valore visualizzato in **Nuova password**.
   
    b. Fare clic su **Complete**.   

### <a name="creating-an-image-relay-test-user"></a>Creazione di un utente test di Image Relay
Questa sezione descrive come creare un utente chiamato Britta Simon in Image Relay.

**Per creare un utente chiamato Britta Simon in Image Relay, seguire questa procedura:**

1. Accedere al sito aziendale di Image Relay come amministratore.
2. Andare in **Users & Permissions** (Utenti e autorizzazioni) e selezionare **Create SSO User** (Crea utente SSO).
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_21.png) 
3. Nei campi **Email**, **Nome**, **Cognome** e **Società** specificare l'indirizzo di posta elettronica, il nome, il cognome e la società dell'utente di cui si vuole effettuare il provisioning e selezionare il gruppo di autorizzazioni (ad esempio, SSO Basic) che potrà eseguire l'accesso solo tramite Single Sign-On.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_22.png) 
4. Fare clic su **Crea**.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD
Questa sezione descrive come abilitare Britta Simon per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Image Relay.

![Assegna utente][200] 

**Per assegnare Britta Simon a Image Relay, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure classico, nella visualizzazione directory scegliere **Applicazioni** dal menu in alto.
   
    ![Assegna utente][201] 
2. Nell'elenco di applicazioni selezionare **Image Relay**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_23.png) 
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203] 
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.  
Quando si fa clic sul riquadro Image Relay nel riquadro di accesso, si dovrebbe accedere automaticamente all'applicazione Image Relay.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO4-->


