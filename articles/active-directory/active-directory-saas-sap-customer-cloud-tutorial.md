---
title: 'Esercitazione: Integrazione di Azure Active Directory con SAP Cloud for Customer | Microsoft Docs'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e SAP Cloud for Customer.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 90154dab-eba2-4563-bcf0-f2acc797ea97
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: f454e7e218764e00cc19ca67b0edade213834b75
ms.openlocfilehash: bec672005b0b1856faeb230f5674013a37add774


---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-for-customer"></a>Esercitazione: Integrazione di Azure Active Directory con SAP Cloud for Customer
Questa esercitazione descrive come integrare SAP Cloud for Customer con Azure Active Directory (Azure AD).

L'integrazione di SAP Cloud for Customer con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a SAP Cloud for Customer
* È possibile abilitare gli utenti per l'accesso automatico a SAP Cloud for Customer (Single Sign-On) con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con SAP Cloud for Customer, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di SAP Cloud for Customer abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.
> 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di SAP Cloud for Customer dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>Aggiunta di SAP Cloud for Customer dalla raccolta
Per configurare l'integrazione di SAP Cloud for Customer in Azure AD, è necessario aggiungere SAP Cloud for Customer dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere SAP Cloud for Customer dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico**fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Active Directory][1]

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]

4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]

6. Nella casella di ricerca, digitare **SAP Cloud for Customer**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_01.png)

7. Nel riquadro dei risultati selezionare **SAP Cloud for Customer** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Active Directory](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SAP Cloud for Customer con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di SAP Cloud for Customer che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SAP Cloud for Customer.

Per configurare e testare l'accesso Single Sign-On di Azure AD con SAP Cloud for Customer, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di SAP Cloud for Customer](#creating-an-sap-business-bydesign-test-user)**: per avere una controparte di Britta Simon in SAP Cloud for Customer collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD
In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure classico e viene configurato l'accesso Single Sign-On nell'applicazione SAP Cloud for Customer. 

**Per configurare l'accesso Single Sign-On di Azure AD con SAP Cloud for Customer, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **SAP Cloud for Customer** del portale di Azure classico fare clic su **Attributi** nel menu in alto.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_80.png) 

2. Nell'elenco degli attributi del token SAML selezionare l'attributo nameidentifier e quindi fare clic su **Modifica**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_84.png) 

3. Nella finestra di dialogo **Modifica attributo utente** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_85.png) 

    a. Dall'elenco **Valori attributo** selezionare la funzione **ExtractMailPrefix()**.

    b. Nell'elenco **Posta** selezionare l'attributo utente da usare per l'implementazione. 
    Ad esempio, se si vuole usare il valore EmployeeID come identificatore utente univoco e il valore dell'attributo è stato archiviato in ExtensionAttribute2, selezionare user.extensionattribute2. 

    c. Fare clic su **Complete**. 


1. Nella pagina di integrazione dell'applicazione **SAP Cloud for Customer** del portale classico, fare clic su **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6] 

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a SAP Cloud for Customer** selezionare**Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_03.png) 

3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_04.png) 
   
    a. Nella casella di testo **URL di accesso`https://<server name>.crm.ondemand.com` digitare l'URL usato dagli utenti per accedere all'applicazione SAP Cloud for Customer usando il modello seguente: **
   
    b. Fare clic su **Avanti**

4. Nella pagina **Configura accesso Single Sign-On in SAP Cloud for Customer** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_05.png)
   
    a. Fare clic su **Scarica metadati**e quindi salvare il file nel computer.
   
    b. Fare clic su **Next**.

5. Per ottenere l'accesso SSO configurato, seguire questa procedura:
   
    a. Accedere al portale di SAP Cloud for Customer con diritti di amministratore.
   
    b. Passare a **Attività comuni di gestione utenti e applicazioni** e fare clic sulla scheda **Provider di identità**.
   
    c. Fare clic su **New Identity Provider** (Nuovo provider di identità) e selezionare il file XML dei metadati scaricato dal portale di Azure classico. Quando si importano i metadati, il sistema carica automaticamente il certificato di firma e il certificato di crittografia necessari.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_54.png)
   
    d. Azure AD richiede l'elemento URL del servizio consumer di asserzione nella richiesta SAML, quindi selezionare la casella di controllo **Include Assertion Consumer Service URL** (Includi URL del servizio Consumer di asserzione).
   
    e. Fare clic su **Activate Single Sign-On**(Attiva Single Sign-On).
   
    f. Salvare le modifiche.
   
    g. Fare clic sulla scheda **My System** (Sistema locale).
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_52.png)
   
    h. Copiare il valore di **URL SSO** e incollarlo nella casella di testo **URL di accesso Azure AD**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_53.png)
   
    i. Specificare se il dipendente può scegliere manualmente tra l'accesso con ID utente e password o SSO selezionando **Manual Identity Provider Selection**(Selezione manuale provider di identità).
   
    j. Nella sezione **SSO URL** (URL SSO), specificare l'URL che deve essere usato dai dipendenti per l'accesso al sistema. 
    Nell'elenco **URL Sent to Employee** (URL inviato al dipendente) è possibile scegliere tra le opzioni seguenti:
   
    **Non-SSO URL**
   
    Il sistema invia al dipendente solo il normale URL di sistema. Il dipendente non può accedere con SSO e deve usare invece la password o il certificato.
   
    **SSO URL** 
   
    Il sistema invia al dipendente solo l'URL SSO. Il dipendente può accedere con SSO. La richiesta di autenticazione viene reindirizzata tramite IdP.
   
    **Automatic Selection**
   
    Se SSO non è attivo, il sistema invia al dipendente il normale URL di sistema. Se SSO è attivo, il sistema verifica se il dipendente ha una password. Se è disponibile una password, vengono inviati al dipendente sia l'URL SSO che l'URL non SSO. Tuttavia, se il dipendente non ha una password, riceverà solo l'URL SSO.
   
    k. Salvare le modifiche.

6. Nel portale classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**.
   
    ![Single Sign-On di Microsoft Azure AD][10]

7. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
   
    ![Single Sign-On di Microsoft Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
In questa sezione viene creato un utente test chiamato Britta Simon nel portale di Azure classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sapcloudforcustomer-tutorial/create_aaduser_09.png) 

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sapcloudforcustomer-tutorial/create_aaduser_03.png) 

4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sapcloudforcustomer-tutorial/create_aaduser_04.png) 

5. Nella pagina **Informazioni sull'utente** seguire questa procedura:

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sapcloudforcustomer-tutorial/create_aaduser_05.png) 
   
    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
   
    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
   
    c. Fare clic su **Avanti**.

6. Nella pagina **Profilo utente** seguire questa procedura:  ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sapcloudforcustomer-tutorial/create_aaduser_06.png) 
   
    a. Nella casella di testo **Nome** digitare **Britta**.  
   
    b. Nella casella di testo **Cognome** digitare **Simon**.
   
    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
   
    d. Nell'elenco **Ruolo** selezionare **Utente**.
   
    e. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sapcloudforcustomer-tutorial/create_aaduser_07.png) 

8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sapcloudforcustomer-tutorial/create_aaduser_08.png) 
   
    a. Prendere nota del valore visualizzato in **Nuova password**.
   
    b. Fare clic su **Complete**.   

### <a name="creating-an-sap-cloud-for-customer-test-user"></a>Creazione di un utente test di SAP Cloud for Customer
In questa sezione viene creato un utente di nome Britta Simon in SAP Cloud for Customer. Per aggiungere gli utenti nella piattaforma SAP Cloud for Customer, contattare il team di supporto di SAP Cloud for Customer. 

> [!NOTE]
> Assicurarsi che il valore di NameID corrisponda al campo username nella piattaforma SAP Cloud for Customer.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD
In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SAP Cloud for Customer.

![Assegna utente][200] 

**Per assegnare Britta Simon a SAP Cloud for Customer, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione Directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni, selezionare **SAP Cloud for Customer**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_50.png) 

3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203]

4. Nell'elenco di utenti selezionare **Britta Simon**.

5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On
In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro SAP Cloud for Customer nel pannello di accesso, si accederà automaticamente all'applicazione SAP Cloud for Customer.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_205.png



<!--HONumber=Feb17_HO2-->


