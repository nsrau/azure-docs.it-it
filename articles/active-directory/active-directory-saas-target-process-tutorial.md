---
title: 'Esercitazione: Integrazione di Azure Active Directory con TargetProcess | Microsoft Docs'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e TargetProcess.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 7cb91628-e758-480d-a233-7a3caaaff50d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: 7cf207e3a853359794a5988aff854cf766734f9b
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-targetprocess"></a>Esercitazione: Integrazione di Azure Active Directory con TargetProcess
Questa esercitazione descrive l'integrazione di TargetProcess con Azure Active Directory (Azure AD).

L'integrazione di TargetProcess con Azure AD offre i vantaggi seguenti: 

* È possibile controllare in Azure AD chi può accedere a TargetProcess 
* È possibile abilitare gli utenti per l'accesso Single Sign-On automatico a TargetProcess (SSO) con i propri account Azure AD
* È possibile gestire gli account in una posizione centrale, il portale di Azure Active Directory classico.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con TargetProcess, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di TargetProcess abilitata per l'accesso SSO

>[!NOTE]
>Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione. 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è quello di testare l'accesso Single Sign-On (SSO) di Azure AD in un ambiente di test. 

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di TargetProcess dalla raccolta 
2. Configurazione e test dell'accesso Single Sign-On (SSO) di Microsoft Azure AD

## <a name="adding-targetprocess-from-the-gallery"></a>Aggiunta di TargetProcess dalla raccolta
Per configurare l'integrazione di TargetProcess in Azure AD, è necessario aggiungere TargetProcess dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere TargetProcess dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico**fare clic su **Active Directory**nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **TargetProcess**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_01.png)
7. Nel riquadro dei risultati selezionare **TargetProcess** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_10.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurare e testare l'accesso Single Sign-On (SSO) di Azure AD
Questa sezione descrive come configurare e testare l'accesso SSO di Azure AD con TargetProcess con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso SSO, Azure AD deve conoscere qual è l'utente di TargetProcess che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in TargetProcess.

La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Username** (Nome utente) in TargetProcess.

Per configurare e testare l'accesso SSO di Azure AD con TargetProcess, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test per TargetProcess](#creating-a-targetprocess-test-user)** - per avere una controparte di Britta Simon in TargetProcess collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD
Questa sezione descrive come abilitare l'accesso SSO di Azure AD nel portale di Azure classico e configurare l'accesso Single Sign-On nell'applicazione TargetProcess. 

Come parte di questa procedura, verrà richiesto di creare un file di certificato con codifica Base&64;. Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

Per configurare l'accesso SSO per TargetProcess, è necessario un dominio registrato. Se ancora non si ha un dominio registrato, contattare il team di supporto di TargetProcess all'indirizzo [support@flatterfiles.com](mailto:support@flatterfiles.com).  

**Per configurare l'accesso Single Sign-On di Azure AD con TargetProcess, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **TargetProcess** nel portale di Azure AD classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6]
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a TargetProcess** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_02.png) 
3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_03.png) 
  1. Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere all'applicazione TargetProcess, ad esempio *https://fabrikam.TargetProcess.com/*.
  2. Fare clic su **Avanti**.
4. Nella pagina **Configura accesso Single Sign-On in TargetProcess** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_04.png)   
  1. Fare clic su **Scarica certificato**e quindi salvare il file nel computer.
  2. Fare clic su **Avanti**.
5. Accedere all'applicazione TargetProcess come amministratore.
6. Nel menu in alto fare clic su **Impostazione**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_05.png)
7. Fare clic su **Impostazioni**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_06.png) 
8. Fare clic su **Single Sign-On**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_07.png) 
9. Nella finestra di dialogo delle impostazioni Single Sign-On seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_08.png)   
  1. Fare clic su **Abilita Single Sign-on**.
  2. Nella pagina **Configura accesso Single Sign-On in TargetProcess** nel portale di Azure classico copiare il valore di **URL servizio Single Sign-On** e incollarlo nella casella di testo **URL accesso**.
  3. Aprire il certificato scaricato nel Blocco note, copiarne il contenuto e incollarlo nella casella di testo **Certificato** .
  4. Fare clic su **Abilita Provisioning JIT**.
10. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e fare clic su **Avanti**. 
   
    ![Single Sign-On di Microsoft Azure AD][10]
11. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
   
    ![Single Sign-On di Microsoft Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure classico.

* Nell'elenco di utenti selezionare **Britta Simon**.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_09.png)  
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_03.png) 
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso. 
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_04.png) 
5. Nella pagina **Informazioni sull'utente** seguire questa procedura: 
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_05.png)   
  1. In Tipo di utente selezionare Nuovo utente nell'organizzazione. 
  2. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
  3. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura: 
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_06.png) 
  1. Nella casella di testo **Nome** digitare **Britta**.  
  2. Nella casella di testo **Cognome** digitare **Simon**.
  3. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
  4. Nell'elenco **Ruolo** selezionare **Utente**.
  5. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_07.png) 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_08.png) 
  1. Prendere nota del valore visualizzato in **Nuova password**.
  2. Fare clic su **Completa**.   

### <a name="create-a-targetprocess-test-user"></a>Creare un utente test per TargetProcess
Questa sezione descrive come creare un utente chiamato Britta Simon in TargetProcess.

TargetProcess supporta il provisioning JIT (just-in-time), È già stato abilitato in [Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on).

Non è necessario alcun intervento dell'utente in questa sezione.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD
Questa sezione descrive come abilitare Britta Simon per l'uso dell'accesso SSO di Azure AD concedendole l'accesso a TargetProcess.

![Assegna utente][200] 

**Per assegnare Britta Simon a TargetProcess, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201] 
2. Selezionare **TargetProcess**dall'elenco di applicazioni.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_09.png) 
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203] 
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro TargetProcess nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione TargetProcess.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_205.png







