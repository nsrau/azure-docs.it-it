---
title: 'Esercitazione: Integrazione di Azure Active Directory con Pluralsight | Documentazione Microsoft'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Pluralsight.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 4c3f07d2-4e1f-4ea3-9025-c663f1f2b7b4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: f77f459219a5c9a0e218de924c0c7578647a3594
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-pluralsight"></a>Esercitazione: Integrazione di Azure Active Directory con Pluralsight
Questa esercitazione descrive l'integrazione di Pluralsight con Azure Active Directory (Azure AD).

L'integrazione di Pluralsight con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Pluralsight
* È possibile abilitare gli utenti per l'accesso Single Sign-On automatico a Pluralsight (SSO) con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con Pluralsight, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure.
* Sottoscrizione di Pluralsight abilitata per l'accesso SSO

>[!NOTE]
>Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione. 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test. 

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Pluralsight dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On (SSO) di Microsoft Azure AD

## <a name="add-pluralsight-from-the-gallery"></a>Aggiungere Pluralsight dalla raccolta
Per configurare l'integrazione di Pluralsight in Azure AD, è necessario aggiungere Pluralsight dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Pluralsight dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **Pluralsight**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_01.png)
7. Nel riquadro dei risultati selezionare **Pluralsight** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_06.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso SSO di Azure AD con Pluralsight con un utente test di nome "Britta Simon".

Per configurare e testare l'accesso Single Sign-On di Azure AD con Pluralsight, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test Pluralsight](#creating-a-pluralsight-test-user)** : per avere una controparte di Britta Simon in Pluralsight collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD
Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure classico e configurare l'accesso SSO nell'applicazione Pluralsight.

L'applicazione Pluralsight prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. La schermata seguente illustra un esempio relativo a questa operazione. 

![Configura accesso Single Sign-On](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_02.png) 

È anche possibile aggiungere l'attributo **ID univoco** con il valore appropriato come EmployeeID o un altro elemento adatto per l'organizzazione. Si noti anche che questo attributo non è obbligatorio, ma può essere aggiunto per identificare l'utente univoco. 

**Per configurare l'accesso Single Sign-On di Azure AD con Pluralsight, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Pluralsight** del portale di Azure classico fare clic su **Attributi** nel menu in alto.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-pluralsight-tutorial/tutorial_general_81.png) 
2. Per rimuovere gli **attributi token SAML**ridondanti, seguire questa procedura: 
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-pluralsight-tutorial/2829.png) 
  * Per ogni attributo utente nella casella rossa della tabella precedente, selezionare l'attributo passando il mouse e quindi fare clic per eliminare. 
3. Per aggiungere gli **attributi token SAML**necessari, per ogni riga della tabella sotto, seguire questa procedura:
   
   | Nome attributo | Valore attributo |
   | --- | --- |
   | Nome |user.givenname |
   | Cognome |user.surname |
   | Email |user.mail |
4. Fare clic su **aggiungi attributo utente** per aprire la finestra di dialogo **Aggiungi attributo utente**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-pluralsight-tutorial/tutorial_general_82.png)
  1. Nella casella di testo **Nome attributo** digitare il nome dell'attributo indicato per quella riga.
  2. Nell'elenco **Valore attributo** selezionare il valore dell'attributo indicato per quella riga.
  3. Fare clic su **Completa**.    
  4. Fare clic su **Applica modifiche**.
 
   ![Configura accesso Single Sign-On](./media/active-directory-saas-pluralsight-tutorial/3232.png) 
    
5. Nel menu in alto fare clic su **Avvio rapido**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-pluralsight-tutorial/tutorial_general_83.png)  
6. Nella pagina di integrazione dell'applicazione **Pluralsight** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6] 
7. Nella pagina **Stabilire come si desidera che gli utenti accedano a Pluralsight** selezionare **Azure AD Single Sign-On** (Accesso Single Sign-On di Azure AD) e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_03.png) 
8. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_04.png) 
  1. Nella casella di testo URL di accesso digitare l'URL usato dagli utenti per accedere all'applicazione Pluralsight adottando il modello seguente: `https://<instance name>.pluralsight.com/sso/<comapny name>`
  2. Fare clic su **Avanti**.
9. Nella pagina **Configura accesso Single Sign-On in Pluralsight** seguire questa procedura:

  ![Configura accesso Single Sign-On](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_05.png)   
  1. Fare clic su **Scarica metadati**e quindi salvare il file nel computer.
  2. Fare clic su **Avanti**.
10. Per ottenere l'accesso Single Sign-On configurato per l'applicazione, contattare il team dei [servizi professionali](mailTo:professionalservices@pluralsight.com) di Pluralsight e comunicare il file di metadati scaricato.
11. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**.
   
    ![Single Sign-On di Microsoft Azure AD][10]
12. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
   
    ![Single Sign-On di Microsoft Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure classico.

* Nell'elenco di utenti selezionare **Britta Simon**.

  ![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_09.png) 
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_03.png) 
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_04.png) 
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_05.png)  
  1. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
  2. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
  3. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura:
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_06.png) 
  1. Nella casella di testo **Nome** digitare **Britta**.  
  2. Nella casella di testo **Cognome** digitare **Simon**.
  3. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
  4. Nell'elenco **Ruolo** selezionare **Utente**.
  5. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_07.png) 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_08.png) 
  1. Prendere nota del valore visualizzato in **Nuova password**.
  2. Fare clic su **Completa**.   

### <a name="create-a-pluralsight-test-user"></a>Creare un utente test Pluralsight
Questa sezione descrive come creare un utente chiamato Britta Simon in Pluralsight. Collaborare con il team di supporto di Pluralsight per aggiungere utenti all'account Pluralsight. 

>[!NOTE]
>Per creare un utente manualmente, è necessario contattare il team di supporto di Pluralsight. 
> 

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD
Questa sezione descrive come abilitare Britta Simon a usare l'accesso Single Sign-On di Azure concedendole l'accesso a Pluralsight.

![Assegna utente][200] 

**Per assegnare Britta Simon a Pluralsight, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale di Azure classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201] 
2. Nell'elenco delle applicazioni selezionare **Pluralsight**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_50.png) 
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203] 
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Pluralsight nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Pluralsight.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_205.png

