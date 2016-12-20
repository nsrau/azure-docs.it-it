---
title: 'Esercitazione: Integrazione di Azure Active Directory con Proofpoint on Demand | Microsoft Docs'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Proofpoint on Demand.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 773e7f7d-ec31-411b-860d-6a6633335d43
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 5c6129050dacf098fa60439175b91b3a2d219182


---
# <a name="tutorial-azure-active-directory-integration-with-proofpoint-on-demand"></a>Esercitazione: Integrazione di Azure Active Directory con Proofpoint on Demand
Questa esercitazione descrive come integrare Proofpoint on Demand con Azure Active Directory (Azure AD).

L'integrazione di Proofpoint on Demand con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Proofpoint on Demand.
* È possibile abilitare gli utenti per l'accesso automatico a Proofpoint on Demand (Single Sign-On o SSO) con i propri account Azure AD.
* È possibile gestire gli account da una posizione centrale, ovvero il portale di Azure classico.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con Proofpoint on Demand, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione con accesso Single Sign-On di Proofpoint on Demand.

A questo scopo, seguire queste indicazioni:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiungere Proofpoint on Demand dalla raccolta.
2. Configurare e testare l'accesso Single Sign-On di Azure AD.

## <a name="add-proofpoint-on-demand-from-the-gallery"></a>Aggiungere Proofpoint on Demand dalla raccolta
Per configurare l'integrazione di Proofpoint on Demand in Azure AD, è necessario aggiungere Proofpoint on Demand dalla raccolta al proprio elenco di app SaaS gestite.

1. Nel portale di Azure classico fare clic su **Active Directory** nel riquadro di spostamento a sinistra.
   
    ![Icona di Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory scegliere **APPLICAZIONI** dal menu superiore.
   
    ![Voce del menu APPLICAZIONI][2]
4. Fare clic su **AGGIUNGI** nella parte inferiore della pagina.
   
    ![Pulsante AGGIUNGI][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Opzione per aggiungere un'applicazione dalla raccolta][4]
6. Nella casella di ricerca digitare **Proofpoint on Demand**.
   
    ![Casella per l'immissione di "Proofpoint on Demand"](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_01.png)
7. Nel riquadro dei risultati selezionare **Proofpoint on Demand** e quindi fare clic su **Completa** per aggiungere l'applicazione.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Proofpoint on Demand con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Proofpoint on Demand che corrisponde a un utente di Azure AD. In altre parole, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Proofpoint on Demand.

La relazione di collegamento viene stabilita assegnando al valore **nome utente** in Azure AD lo stesso valore di **Username** (Nome utente) in Proofpoint on Demand.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Proofpoint on Demand, è necessario completare le procedure seguenti:

1. [Configurare l'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on) per consentire agli utenti di usare questa funzionalità.
2. [Creare un utente test di Azure AD](#creating-an-azure-ad-test-user) per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. [Creare un utente test di Proofpoint on Demand](#creating-a-proofpoint-ondemand-test-user) per avere una controparte di Britta Simon in Proofpoint on Demand collegata alla rappresentazione di tale utente in Azure AD.
4. [Assegnare l'utente test di Azure AD](#assigning-the-azure-ad-test-user) per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. [Testare l'accesso Single Sign-On](#testing-single-sign-on) per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD
In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure classico e viene configurato l'accesso Single Sign-On nell'applicazione Proofpoint on Demand.

1. Nella pagina di integrazione dell'applicazione **Proofpoint on Demand** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Pulsante "Configura accesso Single Sign-On"][6]
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Proofpoint on Demand** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
    ![Pulsante di opzione "Single Sign-On di Microsoft Azure AD"](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_03.png)
3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:
   
    ![Pagina "Configurare le impostazioni dell'app" con caselle compilate](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_04.png)
   
    a. Nella casella **URL DI ACCESSO** digitare l'URL usato dall'utente per accedere all'applicazione Proofpoint on Demand. Usare il modello seguente: **https://\<nomehost\>.pphosted.com/ppssamlsp_nomehost**
   
    b. Nella casella **IDENTIFICATORE** digitare l'URL usando il modello seguente: **https://\<nomehost/>.pphosted.com/ppssamlsp**
   
    c. Nella casella **URL DI RISPOSTA** digitare l'URL usando il modello seguente: **https://\<nomehost/>.pphosted.com:portnumber/v1/samlauth/samlconsumer**
   
    d. Fare clic su **Next**.
4. Nella pagina **Configura accesso Single Sign-On in Proofpoint on Demand** seguire questa procedura:
   
    ![Pagina "Configura accesso Single Sign-On in Proofpoint on Demand" con pulsante "Scarica certificato"](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_05.png)
   
    a. Fare clic su **Scarica certificato**e quindi salvare il file nel computer.
   
    b. Fare clic su **Avanti**.
5. Per ottenere l'accesso Single Sign-On configurato per l'applicazione, contattare il team di supporto Proofpoint on Demand e fornire i seguenti elementi:
   
    • Certificato scaricato
   
    • ID entità
   
    • URL SSO SAML
6. Nel portale di Azure classico selezionare la conferma della configurazione e fare clic su **Avanti**.
   
    ![Casella di controllo che conferma la configurazione dell'accesso Single Sign-On][10]
7. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
   
    ![Pagina di conferma][11]

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
In questa sezione viene creato un utente test chiamato Britta Simon nel portale classico.

![Utente test nell'elenco di utenti][20]

1. Nel portale di Azure classico fare clic su **Active Directory** nel riquadro di spostamento a sinistra.
   
    ![Icona di Active Directory](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_09.png)
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, scegliere **UTENTI** dal menu in alto.
   
    ![Voce del menu UTENTI](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_03.png)
4. Per aprire la finestra di dialogo **Aggiungi utente** , fare clic su **AGGIUNGI UTENTE** sulla barra degli strumenti in basso.
   
    ![Pulsante AGGIUNGI UTENTE](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_04.png)
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:  ![Pagina "Informazioni sull'utente" con caselle compilate](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_05.png)
   
    a. Nella casella **TIPO DI UTENTE** selezionare **Nuovo utente nell'organizzazione**.
   
    b. Nella casella di testo **NOME UTENTE** digitare **BrittaSimon**.
   
    c. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura: ![Pagina "Profilo utente" con caselle compilate](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_06.png)
   
   a. Nella casella di testo **NOME** digitare **Britta**.  
   
   b. Nella casella di testo **COGNOME** digitare **Simon**.
   
   c. Nella casella di testo **NOME VISUALIZZATO** digitare **Britta Simon**.
   
   d. Nell'elenco **RUOLO** selezionare **Utente**.
   
   e. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **Crea**.
   
    ![Pulsante per la creazione di una password temporanea](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_07.png)
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Pagina "Ottieni password temporanea" con informazioni sulla password](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_08.png)
   
    a. Prendere nota del valore visualizzato in **NUOVA PASSWORD**.
   
    b. Fare clic su **Complete**.   

### <a name="create-a-proofpoint-on-demand-test-user"></a>Creare un utente test di Proofpoint on Demand
In questa sezione viene creato un utente di nome Britta Simon in Proofpoint on Demand. Per aggiungere utenti alla piattaforma di Proofpoint on Demand, collaborare con il team di supporto di Proofpoint on Demand.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD
In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure, concedendole così accesso a Proofpoint on Demand.

![Informazioni sull'utente, con accesso abilitato tramite il metodo diretto][200]

1. Nella visualizzazione directory del portale classico scegliere **APPLICAZIONI** dal menu in alto per aprire la visualizzazione applicazioni.
   
    ![Voce del menu APPLICAZIONI][201]
2. Nell'elenco di applicazioni selezionare **Proofpoint on Demand**.
   
    ![Elenco di applicazioni con Proofpoint on Demand selezionato](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_50.png)
3. Scegliere **UTENTI** dal menu in alto.
   
    ![Voce del menu UTENTI][203]
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **ASSEGNA** sulla barra degli strumenti in basso.
   
    ![Pulsante Assegna][205]

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On
In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro **Proofpoint on Demand** nel pannello in accesso, verrà eseguito automaticamente l'accesso all'applicazione Proofpoint on Demand.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


