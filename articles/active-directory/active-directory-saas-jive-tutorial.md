---
title: 'Esercitazione: Integrazione di Azure Active Directory con Jive | Microsoft Docs'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Jive.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 9fc5659a-c116-4a1b-a601-333325a26b46
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 6a01f53ce05aa8084f0a18e56714b1790cfce912
ms.openlocfilehash: 304ee87e82ce5acc4479fd16d3ac1aa340e42815


---
# <a name="tutorial-azure-active-directory-integration-with-jive"></a>Esercitazione: Integrazione di Azure Active Directory con Jive
Questa esercitazione descrive come integrare Jive con Azure Active Directory (Azure AD).

L'integrazione di Jive con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Jive
* È possibile abilitare gli utenti per l'accesso automatico a Jive (Single Sign-On) con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con Jive, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di Jive abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.
> 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Jive dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-jive-from-the-gallery"></a>Aggiunta di Jive dalla raccolta
Per configurare l'integrazione di Jive in Azure AD, è necessario aggiungere Jive dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Jive dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico**fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **Jive**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-jive-tutorial/tutorial_jive_01.png)
7. Nel riquadro dei risultati selezionare **Jive** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-jive-tutorial/tutorial_jive_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Jive in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Jive che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Jive.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **nome utente** in Jive.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Jive, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Jive](#creating-a-jive-test-user)** : per avere una controparte di Britta Simon in Jive collegata alla relativa rappresentazione in Azure AD.
4. **[Configurazione del provisioning utente](#configuring-user-provisioning)** : per stabilire come abilitare il provisioning degli account utente di Active Directory in Jive.
5. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
6. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure classico e configurare l'accesso Single Sign-On nell'applicazione Jive.

**Per configurare l'accesso Single Sign-On di Azure AD con Jive, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Jive** del portale classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6] 
2. Nella pagina **How would you like users to sign on to Jive** (Stabilire come si desidera che gli utenti accedano a Jive) selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-jive-tutorial/tutorial_jive_03.png) 
3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-jive-tutorial/tutorial_jive_04.png) 
   
    a. Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere all'applicazione Jive usando il modello seguente: **https://\<nome cliente\>.jivecustom.com**.
   
    b. click **Avanti**
4. Nella pagina **Configura accesso Single Sign-On in Jive** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-jive-tutorial/tutorial_jive_05.png)
   
    a. Fare clic su **Scarica certificato**e quindi salvare il file nel computer.
   
    b. Fare clic su **Next**.
5. Accedere al tenant di Jive come amministratore.
6. Nel menu in alto fare clic su "**SAML**".
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-jive-tutorial/tutorial_jive_002.png)
   
    a. Selezionare **Abilitato** nella scheda **Generale**.
   
    b. Fare clic sul pulsante**Save all SAML settings**(Salva tutte le impostazioni SAML).
7. Passare alla scheda**IdP Metadata**(Metadati IdP).
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-jive-tutorial/tutorial_jive_003.png)
   
    a. Copiare il contenuto del file XML di metadati scaricato e incollarlo nella casella di testo **Identity Provider (IdP) Metadata** (Metadati del provider di identità - IdP).
   
    b. Fare clic sul pulsante**Save all SAML settings**(Salva tutte le impostazioni SAML). 
8. Passare alla scheda**User Attribute Mapping**(Mapping degli attributi utente).
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-jive-tutorial/tutorial_jive_004.png)
   
    a. Nella casella di testo **Email** copiare e incollare il nome dell'attributo del valore **mail**.
   
    b. Nella casella di testo **Nome** copiare e incollare il nome dell'attributo del valore **givenname**.
   
    c. Nella casella di testo **Cognome** copiare e incollare il nome dell'attributo del valore **surname**.
9. Nel portale di Azure AD selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**.
   ![Accesso Single Sign-On di Azure AD][10]
10. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
    ![Accesso Single Sign-On di Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
In questa sezione viene creato un utente test chiamato Britta Simon nel portale classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_09.png) 
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_03.png) 
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_04.png) 
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:  ![Creazione di un utente test di Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_05.png) 
   
    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
   
    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
   
    c. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_06.png) 
   
    a. Nella casella di testo **Nome** digitare **Britta**.  
   
    b. Nella casella di testo **Cognome** digitare **Simon**.
   
    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
   
    d. Nell'elenco **Ruolo** selezionare **Utente**.
   
    e. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_07.png) 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_08.png) 
   
    a. Prendere nota del valore visualizzato in **Nuova password**.
   
    b. Fare clic su **Completa**.   

### <a name="creating-a-jive-test-user"></a>Creazione di un utente test di Jive
In questa sezione viene creato un utente di nome Britta Simon in Jive. Collaborare con il team di supporto di Jive per aggiungere gli utenti alla piattaforma Jive.

### <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente
Questa sezione descrive come abilitare il provisioning degli account utente di Active Directory in Jive.  
Come parte di questa procedura, specificare un token di sicurezza utente da richiedere a Jive.com.

La schermata seguente mostra un esempio della finestra di dialogo correlata in Azure AD:

![Configurare il provisioning degli utenti](./media/active-directory-saas-jive-tutorial/IC698794.png "Configurare il provisioning degli utenti")

#### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning utente, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Jive** nel portale di gestione di Azure fare clic su **Configura provisioning utenti** per aprire la finestra di dialogo **Configura provisioning utenti**.
2. Nella pagina **Immettere le credenziali Jive per abilitare il provisioning utenti automatico** specificare le impostazioni di configurazione seguenti:
   
    a. Nella casella di testo **Jive Admin User Name** (Nome utente amministratore Jive) digitare un nome di account Jive che abbia il profilo **Amministratore di sistema** assegnato in Jive.com.
   
    b. Nella casella di testo **Password amministratore Jive** digitare la password per questo account.
   
    c. Nella casella di testo **URL tenant di Jive** digitare l'URL del tenant di Jive.
      
      > [!NOTE]
      > L'URL del tenant di Jive è l'URL usato dall'organizzazione per accedere a Jive.  
      > L'URL in genere ha il formato seguente: **www.\<organizzazione\>.jive.com**.
      > 
      > 
   
    d. Fare clic su **Convalida** per verificare la configurazione.

    e. Fare clic sul pulsante **Avanti** per aprire la pagina **Conferma**.

3. Nella pagina **Conferma** fare clic sul segno di spunta per salvare la configurazione.

È ora possibile creare un account di test. Attendere 10 minuti e quindi verificare che l'account sia stato sincronizzato con Jive.com.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD
In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Jive.

![Assegna utente][200] 

**Per assegnare Britta Simon a Jive, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201] 
2. Nell'elenco delle applicazioni selezionare **Jive**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-jive-tutorial/tutorial_jive_50.png) 
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203]
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On
In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Jive nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Jive.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-jive-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jive-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jive-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jive-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-jive-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-jive-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-jive-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-jive-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jive-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jive-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-jive-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-jive-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-jive-tutorial/tutorial_general_205.png



<!--HONumber=Feb17_HO3-->


