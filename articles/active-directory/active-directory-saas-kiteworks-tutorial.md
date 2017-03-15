---
title: Esercitazione Integrazione di Azure Active Directory con Kiteworks | Microsoft Docs
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Kiteworks.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: f7984aaf-ab1f-4a85-9646-a9523f5275d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: d3be35d6c7efea5e5a784ee3c0d1965cc11bfcfe
ms.openlocfilehash: f64add6d742de24d0144db44e7c3885feb7a1139
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-kiteworks"></a>Esercitazione Integrazione di Azure Active Directory con Kiteworks
Questa esercitazione descrive l'integrazione di Kiteworks con Azure Active Directory (Azure AD). 

L'integrazione di Kiteworks con Azure AD offre i vantaggi seguenti: 

* È possibile controllare in Azure AD chi può accedere a Kiteworks 
* È possibile abilitare gli utenti per l'accesso Single Sign-On automatico a Kiteworks (SSO) con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: Azure Active Directory 

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con Kiteworks, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di Kiteworks abilitata per l'accesso Single Sign-On (SSO)

>[!NOTE]
>Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione. 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è quello di testare l'accesso Single Sign-On (SSO) di Azure AD in un ambiente di test.  

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Kiteworks dalla raccolta 
2. Configurazione e test dell'accesso Single Sign-On (SSO) di Microsoft Azure AD

## <a name="add-kiteworks-from-the-gallery"></a>Aggiungere Kiteworks dalla raccolta
Per configurare l'integrazione di Kiteworks in Azure AD, è necessario aggiungere Kiteworks dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Kiteworks dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico**fare clic su **Active Directory**nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **Kiteworks**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_01.png)
7. Nel pannello dei risultati selezionare **Kiteworks** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_02.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con Kiteworks in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Kiteworks che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Kiteworks.

La relazione di collegamento viene stabilita assegnando al valore del **nome utente** in Azure AD lo stesso valore di **Username** (Nome utente) in Kiteworks.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Kiteworks, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test per Kiteworks](#creating-a-kiteworks-test-user)** : per avere una controparte di Britta Simon in Kiteworks collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD
Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure classico e come configurare l'accesso Single Sign-On nell'applicazione Kiteworks. 

Come parte di questa procedura, verrà richiesto di creare un file di certificato con codifica Base&64;. Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

Per configurare l'accesso SSO per Kiteworks, è necessario un dominio registrato. Se ancora non si dispone ancora di un dominio registrato, contattare il team di supporto di Kiteworks.  

**Per configurare l'accesso Single Sign-On di Azure AD con Kiteworks, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Kiteworks** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6] 
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Kiteworks** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_03.png) 
3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_04.png) 
  1. Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere all'applicazione Kiteworks, ad esempio *https://fabrikam.kiteworks.com/*.
  2. Fare clic su **Avanti**.
4. Nella pagina **Configura accesso Single Sign-On in Kiteworks** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_05.png)   
  1. Fare clic su **Scarica certificato**e quindi salvare il file nel computer.
  2. Fare clic su **Avanti**.
5. Accedere al sito aziendale di Kiteworks come amministratore.
6. Nel barra degli strumenti in alto fare clic su **Impostazioni**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_06.png) 
7. Nella sezione **Authentication and Authorization** (Autenticazione e autorizzazione) fare clic su **SSO Setup** (Configurazione SSO). 
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_07.png) 
8. Nella pagina di configurazione dell’accesso Single Sign-On, seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_09.png)   
  1. Selezionare **Autentica tramite Single Sign-On**.
  2. Selezionare **Avvia richiesta autenticazione**.
  3. Nella pagina della finestra di dialogo **Configura accesso Single Sign-On in Kiteworks** nel portale di Azure classico copiare il valore di **ID entità** e incollarlo nella casella di testo **IDP Entity ID** (ID entità IDP). 
  4. Nella pagina **Configura accesso Single Sign-On in Kiteworks** del portale di Azure classico copiare il valore di **URL servizio Single Sign-On** e incollarlo nella casella di testo **URL servizio Single Sign-On**.
  5. Nella pagina **Configura accesso Single Sign-On in Kiteworks** del portale di Azure classico copiare il valore di **URL servizio Single Sign-Out** e incollarlo nella casella di testo **Single Logout Service URL** (URL servizio punto di disconnessione singolo).
  6. Aprire il certificato scaricato nel Blocco note, copiare il contenuto e incollarlo nella casella di testo **RSA Public Key Certificate** . 
  7. Fare clic su **Save**.
9. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e fare clic su **Avanti**. 
   
    ![Single Sign-On di Microsoft Azure AD][10]
10. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
   
    ![Single Sign-On di Microsoft Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_09.png)  
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_03.png) 
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso. 
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_04.png) 
5. Nella pagina **Informazioni sull'utente** seguire questa procedura: 
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_05.png)  
  1. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
  2. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
  3. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura: 
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_06.png) 
  1. Nella casella di testo **Nome** digitare **Britta**.  
  2. Nella casella di testo **Cognome** digitare **Simon**.
  3. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
  4. Nell'elenco **Ruolo** selezionare **Utente**.
  5. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_07.png) 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_08.png) 
  1. Prendere nota del valore visualizzato in **Nuova password**.
  2. Fare clic su **Complete**.   

### <a name="create-a-kiteworks-test-user"></a>Creare un utente test per Kiteworks
Questa sezione descrive come creare un utente chiamato Britta Simon in Kiteworks.

Kiteworks supporta il provisioning JIT (just-in-time), che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Durante il tentativo di accesso a Kiteworks verrà creato un nuovo utente, se questo non esiste già.

>[!NOTE]
>Per creare un utente manualmente, è necessario contattare il team di supporto di Kiteworks.
>  

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD
Questa sezione descrive come abilitare Britta Simon a usare l'accesso Single Sign-On di Azure concedendole l'accesso a Kiteworks.

![Assegna utente][200] 

**Per assegnare Britta Simon a Kiteworks, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure classico, nella visualizzazione directory scegliere **Applicazioni** dal menu in alto.
   
    ![Assegna utente][201] 
2. Nell'elenco di applicazioni selezionare **Kiteworks**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_50.png) 
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203] 
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.  

Quando si fa clic sul riquadro Kiteworks nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Kiteworks.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_205.png







