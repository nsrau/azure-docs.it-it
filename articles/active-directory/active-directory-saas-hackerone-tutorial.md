---
title: 'Esercitazione: Integrazione di Azure Active Directory con HackerOne | Documentazione Microsoft'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e HackerOne.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 229d1efb-b6a5-4df8-9839-5d551487db4e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2fa331e94425673481689aefeb5393618f2471b9
ms.openlocfilehash: ced5f0fc2d9afa22f2d3ef3a81bb07482f69ec0e
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-hackerone"></a>Esercitazione: Integrazione di Azure Active Directory con HackerOne
Questa esercitazione descrive come integrare HackerOne con Azure Active Directory (Azure AD).

L'integrazione di HackerOne con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a HackerOne
* È possibile abilitare gli utenti per l'accesso Single Sign-On (SSO) automatico a HackerOne con l'account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con HackerOne, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure.
* Sottoscrizione di HackerOne abilitata per l'accesso SSO

>[!NOTE]
>Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione. 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.  

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

*  Aggiunta di HackerOne dalla raccolta
*  Configurazione e test dell'accesso Single Sign-On (SSO) di Azure AD

## <a name="add-hackerone-from-the-gallery"></a>Aggiungere HackerOne dalla raccolta
Per integrare HackerOne in Azure AD, è necessario aggiungere HackerOne dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere HackerOne dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

  ![Applicazioni][4]
6. Nella casella di ricerca digitare **HackerOne**.

  ![Creazione di un utente test di Azure AD](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_01.png)

7. Nel riquadro dei risultati selezionare **HackerOne** e quindi fare clic su **Completa** per aggiungere l'applicazione.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD
Successivamente, vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD con HackerOne con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di HackerOne che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in HackerOne.  
La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Username** in HackerOne.

Per configurare e testare l'accesso Single Sign-On di Azure AD con HackerOne, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di HackerOne](#creating-a-hackerone-test-user)** : per avere una controparte di Britta Simon in HackerOne collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD
Successivamente, viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure classico e viene configurato l'accesso Single Sign-On nell'applicazione HackerOne.

Come parte di questa procedura, verrà richiesto di creare un file di certificato con codifica Base&64;. Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

**Per configurare l'accesso Single Sign-On di Azure AD con HackerOne, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **HackerOne** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6] 
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a HackerOne** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_03.png) 
3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura e quindi fare clic su **Avanti**:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_04.png) 
    1. Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere all'applicazione HackerOne adottando il modello seguente: **"https://hackerone.com/\<company name\>/authentication"**. 
    2. Contattare il team di supporto di HackerOne all'indirizzo [support@hackerone.com](mailto:support@hackerone.com) per ottenere l'URL del tenant, se non lo si conosce.
    3. Nella casella di testo **Identificatore** digitare l'URL del tenant. 
    4. Fare clic su **Avanti**.

4. Nella pagina **Configura accesso Single Sign-On in HackerOne** seguire questa procedura e quindi fare clic su **Avanti**:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_05.png) 
    1. Fare clic su **Scarica certificato**e quindi salvare il file nel computer.
    2. Fare clic su **Avanti**.
5. Accedere al tenant di HackerOne come amministratore.
6. Scegliere **Settings**(Impostazioni) dal menu in alto.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_001.png) 
7. Passare all'opzione "**Authentication**" (Autenticazione) e quindi fare clic su "**Add SAML settings**" (Aggiungi impostazioni SAML).
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_003.png) 
8. Nella finestra di dialogo **SAML Settings** (Impostazioni SAML) seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_004.png) 
    1. Nella casella di testo **Email Domain** (Dominio di posta elettronica) digitare un dominio registrato.
    2. Nel portale di Azure classico copiare il valore di **URL servizio Single Sign-On**e quindi incollarlo nella casella di testo relativa all'URL dell'accesso Single Sign-On.
    3. Creare un file con **codifica Base&64;** dal certificato scaricato.  
    
       >[!TIP] 
       >Per informazioni dettagliate, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).
       >
    4. Aprire il certificato con codifica Base&64; nel Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **Certificato X509** .
    5. Fare clic su **Save**.
9. Nella finestra di dialogo relativa alle impostazioni di autenticazione seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_005.png) 
    1. Fare clic su **Run test**(Esegui test).
    2. Se il valore del campo **Status** (Stato) corrisponde a **Last test status: created** (Stato ultimo test: creato), contattare il team di supporto di HackerOne all'indirizzo [support@hackerone.com](mailto:support@hackerone.com) per richiedere una verifica della configurazione.
10. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e fare clic su **Avanti**.
   
    ![Single Sign-On di Microsoft Azure AD][10]
11. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
   
    ![Single Sign-On di Microsoft Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
Successivamente, viene creato un utente test chiamato Britta Simon nel portale classico.  

![Creare un utente di Azure AD][20]

**Per creare un utente test di SECURE DELIVER in Azure AD, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_09.png) 
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_03.png) 
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_04.png) 
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_05.png) 
    1. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
    2. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
    3. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura:
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_06.png) 
   1. Nella casella di testo **Nome** digitare **Britta**.  
   2. Nella casella di testo **Cognome** digitare **Simon**.
   3. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
   4. Nell'elenco **Ruolo** selezionare **Utente**.
   5. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_07.png) 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_08.png) 
    1. Prendere nota del valore visualizzato in **Nuova password**.
    2. Fare clic su **Complete**.   

### <a name="create-a-hackerone-test-user"></a>Creare un utente test di HackerOne
Successivamente, viene creato un utente chiamato Britta Simon in HackerOne. HackerOne supporta il provisioning JIT (Just-In-Time) che è abilitato per impostazione predefinita.

Non è necessario alcun intervento dell'utente in questa sezione. Quando si accede a HackerOne, viene creato un nuovo utente se non esiste ancora. [Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on).

>[!NOTE]
>Per creare un utente manualmente, è necessario contattare il team di supporto di HackerOne. 
> 

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD
Britta Simon viene quindi abilitata per l'uso dell'accesso Single Sign-On di Azure, mediante la concessione dell'accesso a HackerOne.

![Assegna utente][200] 

**Per assegnare Britta Simon a HackerOne, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201] 
2. Nell'elenco di applicazioni selezionare **HackerOne**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_50.png) 
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203] 
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On
Viene infine eseguito il test della configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.  

Quando si fa clic sul riquadro HackerOne nel pannello di accesso, verrà eseguito automaticamente l'accesso all'applicazione HackerOne.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_205.png

