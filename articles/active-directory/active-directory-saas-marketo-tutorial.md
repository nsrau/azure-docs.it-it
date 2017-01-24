---
title: 'Esercitazione: Integrazione di Azure Active Directory con Marketo | Microsoft Docs'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Marketo.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: b88c45f5-d288-4717-835c-ca965add8735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1a2ff324cfa65977685f6a0eee9cd398b0717519
ms.openlocfilehash: 20f3a8b006e45e3a94e95b516bca292a82c5fd03


---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Esercitazione: Integrazione di Azure Active Directory con Marketo
Questa esercitazione descrive come integrare Marketo con Azure Active Directory (Azure AD).

L'integrazione di Marketo con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Marketo
* È possibile abilitare gli utenti per l'accesso automatico a Marketo (Single Sign-On) con i propri account Azure AD.
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con Marketo, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di Marketo abilitata per l'accesso Single Sign-On

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

1. Aggiunta di Marketo dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-marketo-from-the-gallery"></a>Aggiunta di Marketo dalla raccolta
Per configurare l'integrazione di Marketo in Azure AD, è necessario aggiungere Marketo dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Marketo dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico**fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **Marketo**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_01.png)
7. Nel riquadro dei risultati selezionare **Marketo** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Marketo con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Marketo che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Marketo.

La relazione di collegamento viene stabilita assegnando al valore del **nome utente** in Azure AD lo stesso valore di **Nome utente** in Marketo.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Marketo, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Marketo](#creating-a-predictix-price-reporting-test-user)**: per avere una controparte di Britta Simon in Marketo collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD
In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure classico e viene configurato l'accesso Single Sign-On nell'applicazione Marketo.

**Per configurare l'accesso Single Sign-On di Azure AD con Marketo, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Marketo** del portale di Azure classico, fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6] 
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Marketo** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_03.png) 
3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_04.png) 
   
    a. Nella casella di testo **Identificatore** digitare l'URL usando il modello seguente: `https://saml.marketo.com/sp`
   
    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://login.marketo.com/saml/assertion/\<munchkinid\>`
   
    c. Fare clic su **Avanti**
4. Nella pagina **Configura accesso Single Sign-On in Marketo** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_05.png)
   
    a. Fare clic su **Scarica certificato**e quindi salvare il file nel computer.
   
    b. Fare clic su **Avanti**.
5. Per ottenere l'ID Munchkin dell'applicazione, accedere a Marketo usando le credenziali di amministratore ed eseguire le azioni seguenti:
   
    a. Accedere all'app Marketo usando le credenziali di amministratore.
   
    b. Fare clic sul pulsante Admin (Amministratore) nel riquadro di spostamento in alto.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Passare al menu Integration (Integrazione) e fare clic sul collegamento Munchkin.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_11.png)
   
    d. Copiare l'ID Munchkin visualizzato sullo schermo e completare il valore per l'URL di risposta nella configurazione guidata di Azure AD.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_12.png)
6. Per configurare l'accesso Single Sign-On nell'applicazione, seguire questa procedura:
   
    a. Accedere all'app Marketo usando le credenziali di amministratore.
   
    b. Fare clic sul pulsante Admin (Amministratore) nel riquadro di spostamento in alto.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Passare al menu Integration (Integrazione) e fare clic su Single Sign-On.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Per abilitare le impostazioni SAML, fare clic sul pulsante Edit (Modifica).
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **Abilitare** le impostazioni dell'accesso Single Sign-On.
   
    f. Immettere l'ID dell'autorità emittente, copiato dalla configurazione guidata di Azure AD.
   
    g. Nella casella di testo relativa all'ID dell'entità immettere l'URL nel formato seguente: **http://saml.marketo.com/sp**
   
    h. Selezionare l'opzione **Name Identifier element**
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Se l'identificatore utente non è un valore UPN, modificarlo nella scheda degli attributi.
    > 
    > 
   
    i. Caricare il certificato scaricato dalla configurazione guidata di Azure AD. Salvare le impostazioni.
   
    j. Modificare le impostazioni delle pagine di reindirizzamento.
   
    k. Copiare l'URL di accesso dalla configurazione guidata di Azure AD alla casella di testo **Login URL** (URL di accesso).
   
    l. Copiare l'URL di disconnessione dalla configurazione guidata di Azure AD alla casella di testo **Logout URL** (URL di disconnessione).
   
    m. In Error URL (URL errore) copiare l'URL dell'istanza di Marketo e quindi fare clic sul pulsante Save (Salva) per salvare le impostazioni.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_10.png)

7. Per abilitare l'accesso Single Sign-On per gli utenti, completare le azioni seguenti:
   
    a. Accedere all'app Marketo usando le credenziali di amministratore.
   
    b. Fare clic sul pulsante **Admin** (Amministratore) nel riquadro di spostamento in alto.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Passare al menu **Security** (Sicurezza) e fare clic su **Login Settings** (Impostazioni di accesso). 
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_13.png)
   
    d. Selezionare l'opzione **Require SSO** (Richiedi SSO) e salvare le impostazioni.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_14.png)
8. Nel portale classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**.
   
    ![Single Sign-On di Microsoft Azure AD][10]
9. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
   
    ![Single Sign-On di Microsoft Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
In questa sezione viene creato un utente test chiamato Britta Simon nel portale classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_09.png) 
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_03.png) 
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_04.png) 
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:  ![Creazione di un utente test di Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_05.png) 
   
    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
   
    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
   
    c. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura:  ![Creazione di un utente test di Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_06.png) 
   
    a. Nella casella di testo **Nome** digitare **Britta**.  
   
    b. Nella casella di testo **Cognome** digitare **Simon**.
   
    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
   
    d. Nell'elenco **Ruolo** selezionare **Utente**.
   
    e. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_07.png) 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_08.png) 
   
    a. Prendere nota del valore visualizzato in **Nuova password**.
   
    b. Fare clic su **Complete**.   

### <a name="creating-an-marketo-test-user"></a>Creazione di un utente di test Marketo
In questa sezione viene creato un utente chiamato Britta Simon in Marketo. Seguire questa procedura per creare un utente nella piattaforma Marketo.

1. Accedere all'app Marketo usando le credenziali di amministratore.
2. Fare clic sul pulsante **Admin** (Amministratore) nel riquadro di spostamento in alto.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
3. Passare al menu **Security** (Sicurezza) e fare clic su **Users & Roles** (Utenti e ruoli)
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_19.png)  
4. Fare clic sul collegamento **Invite New User** (Invita nuovo utente) nella scheda Users (Utenti).
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_15.png) 
5. La procedura guidata (Invita nuovo utente) immetterà i valori per le informazioni seguenti.
   
    a. Immettere l'indirizzo di **Email** (Posta elettronica) dell'utente nella casella di testo.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_16.png)
   
    b. Immettere il valore per **First Name** (Nome) nella casella di testo.
   
    c. Immettere il valore per **Last Name** (Cognome) nella casella di testo.
   
    d. Fare clic su Next (Avanti).
6. Nella scheda **Permissions** (Autorizzazioni) selezionare i ruoli utente e fare clic su Next (Avanti).
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_17.png)
7. Fare clic sul pulsante Send (Invia) per inviare l'invito all'utente.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_18.png)
8. L'utente riceverà la notifica tramite posta elettronica e dovrà fare clic sul collegamento e modificare la password per attivare l'account. 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD
In questa sezione, Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Marketo.

![Assegna utente][200] 

**Per assegnare Britta Simon a Marketo, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201] 
2. Nell'elenco di applicazioni, selezionare **Marketo**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_50.png) 
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203]
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On
In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Marketo nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Marketo.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO1-->


