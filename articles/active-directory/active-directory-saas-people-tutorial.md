---
title: 'Esercitazione: Integrazione di Azure Active Directory con People | Microsoft Docs'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e People.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 7c9b6202-11dd-4bb6-a679-8fb0a7a0ef4e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 04a045f41965b093aab71e59cd9b5f328b44de84
ms.openlocfilehash: eb24c4fe5f382a30acc524065f2ee9e3a27ce94f
ms.lasthandoff: 02/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-people"></a>Esercitazione: Integrazione di Azure Active Directory con People
Questa esercitazione descrive l'integrazione di People con Azure Active Directory (Azure AD).

L'integrazione di People con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a People
* È possibile abilitare gli utenti per l'accesso automatico a People (Single Sign-On) con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale con il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con People, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure.
* Sottoscrizione di People abilitata per l'accesso Single Sign-On (SSO)

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.
> 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di People dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-people-from-the-gallery"></a>Aggiunta di People dalla raccolta
Per configurare l'integrazione di People in Azure AD, è necessario aggiungere People dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere People dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **People**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-people-tutorial/tutorial_people_01.png)
7. Nel riquadro dei risultati selezionare **People** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-people-tutorial/tutorial_people_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con People in base a un utente test di nome "Britta Simon".

Per configurare e testare l'accesso Single Sign-On di Azure AD con People, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di People](#creating-a-people-test-user)** : per avere una controparte di Britta Simon in People collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure classico e configurare l'accesso Single Sign-On nell'applicazione People.

**Per configurare l'accesso Single Sign-On di Azure AD con People, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **People** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    [Configura accesso Single Sign-On][6] 
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a People** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-people-tutorial/tutorial_people_03.png) 
3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura e quindi fare clic su **Avanti**:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-people-tutorial/tutorial_people_04.png) 
   
   1. Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere all'applicazione People usando il modello seguente: **"https://\<nome società\>.peoplehr.com/"**. 
   2. Se non si conosce l'URL del tenant, contattare il team di supporto di People all'indirizzo [customerservices@peoplehr.com](mailto:customerservices@peoplehr.com) per richiederlo.    3. Nella casella di testo **Identificatore** digitare l'URL del tenant. 
   4. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: "**https://itgs.peoplehr.net/Pages/Saml/ConsumeAzureAD.aspx**".
   5. Fare clic su **Avanti**.
4. Nella pagina **Configura accesso Single Sign-On in People** seguire questa procedura e quindi fare clic su **Avanti**:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-people-tutorial/tutorial_people_05.png) 
   
   1. Fare clic su **Scarica metadati**e quindi salvare il file nel computer.
   2. Fare clic su **Avanti**.
5. Per configurare l'accesso SSO sull'applicazione, è necessario accedere al tenant di People come amministratore.
   
   1. Nella barra di spostamento sul lato sinistro fare clic su **Settings (Impostazioni)**.
    ![Configura accesso Single Sign-On](./media/active-directory-saas-people-tutorial/tutorial_people_001.png)    
   2. Fare clic su **"Company" (Azienda)**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-people-tutorial/tutorial_people_002.png) 
    3. In **"Upload 'Single Sign On' SAML meta-data file"** (Carica il file dei metadati SAML "Single Sign-On") fare clic su **Browse** (Sfoglia) per caricare il file di metadati scaricato.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-people-tutorial/tutorial_people_003.png)
6. Nel portale di Azure classico, selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**.
   
    ![Single Sign-On di Microsoft Azure AD][10]
7. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
   
    ![Single Sign-On di Microsoft Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure classico.
Nell'elenco di utenti selezionare **Britta Simon**.

![Creare un utente di Azure AD][20]

**Per creare un utente test People in Azure AD, seguire questa procedura:**

1. Nel **portale di Azure classico**fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-people-tutorial/create_aaduser_09.png)
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-people-tutorial/create_aaduser_03.png) 
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-people-tutorial/create_aaduser_04.png) 
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-people-tutorial/create_aaduser_05.png) 
   
    1. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
    2. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
    3. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura:
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-people-tutorial/create_aaduser_06.png) 
   
   1. Nella casella di testo **Nome** digitare **Britta**.  
   2. Nella casella di testo **Cognome** digitare **Simon**.
   3. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
   4. Nell'elenco **Ruolo** selezionare **Utente**.
   5. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-people-tutorial/create_aaduser_07.png) 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-people-tutorial/create_aaduser_08.png) 
   
   1. Prendere nota del valore visualizzato in **Nuova password**.
   2. Fare clic su **Completa**.   

### <a name="creating-a-people-test-user"></a>Creazione di un utente test di People
Questa sezione descrive come creare un utente chiamato Britta Simon in People. People non supporta il provisioning just-in-time quindi è necessario contattare il team di supporto di People per creare manualmente un utente.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD
L'obiettivo di questa sezione consiste nell'abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a People.

![Assegna utente][200] 

**Per assegnare Britta Simon a People, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201] 
2. Nell'elenco delle applicazioni, selezionare **People**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-people-tutorial/tutorial_people_50.png) 
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203] 
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.
Quando si fa clic sul riquadro People nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione People.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-people-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-people-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-people-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-people-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-people-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-people-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-people-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-people-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-people-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-people-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-people-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-people-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-people-tutorial/tutorial_general_205.png

