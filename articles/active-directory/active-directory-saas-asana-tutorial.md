---
title: 'Esercitazione: Integrazione di Azure Active Directory con Asana | Documentazione Microsoft'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Asana.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 837e38fe-8f55-475c-87f4-6394dc1fee2b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4a07f946d396a9263d5b00202cd5229ddc86d1be
ms.openlocfilehash: 257c1032ed8f1347c17be7e51021b9284b73f4a2
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Esercitazione: Integrazione di Azure Active Directory con Asana
Questa esercitazione descrive come integrare Asana con Azure Active Directory (Azure AD).

L'integrazione di Asana con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere ad Asana
* È possibile abilitare gli utenti per l'accesso automatico ad Asana (SSO) con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con Asana, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di **Asana** abilitata per l'accesso Single Sign-On (SSO)

>[!NOTE]
>Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione. 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Asana dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On (SSO) di Microsoft Azure AD

## <a name="adding-asana-from-the-gallery"></a>Aggiunta di Asana dalla raccolta
Per configurare l'integrazione di Asana in Azure AD, è necessario aggiungerla dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Asana dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico**fare clic su **Active Directory**nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **Asana**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-asana-tutorial/tutorial_asana_01.png)
7. Nel riquadro dei risultati selezionare **Asana** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-asana-tutorial/tutorial_asana_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurare e testare l'accesso Single Sign-On (SSO) di Azure AD
In questa sezione viene configurato e testato l'accesso SSO di Azure AD con Asana con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso SSO, Azure AD deve conoscere qual è l'utente di Asana che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Asana.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in Asana.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Asana, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Asana](#creating-an-Asana-test-user)** : per avere una controparte di Britta Simon in Asana collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD
Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure classico e configurare l'accesso SSO nell'applicazione Asana.

**Per configurare Single Sign-On di Azure AD con Asana, seguire questa procedura:**

1. Nel menu in alto fare clic su **Avvio rapido**.
   
    ![Configura accesso Single Sign-On][6]
2. Nella pagina di integrazione dell'applicazione **Asana** del portale classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][7] 
3. Nella pagina **Stabilire come si desidera che gli utenti accedano ad Asana** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-asana-tutorial/tutorial_asana_06.png)
4. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura: 
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-asana-tutorial/tutorial_asana_07.png)
  1. Nella casella di testo URL di accesso digitare l'URL usando il modello seguente: `https://app.asana.com`
  2. Fare clic su **Avanti**.
5. Nella pagina **Configura accesso Single Sign-On in Asana** fare clic su **Download certificato** e quindi salvare il file nel computer. Copiare anche il valore per URL SSO SAML.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-asana-tutorial/tutorial_asana_08.png)
6. Fare clic con il pulsante destro sul certificato, quindi aprire il file di certificato nel Blocco note o nell'editor di testo preferito. Copiare il contenuto tra l'inizio e la fine del titolo del certificato. Questo è il certificato X.509 che si utilizzerà in Asana per configurare SSO.
7. In una finestra del browser diversa accedere all'applicazione Asana. Per configurare SSO in Asana, accedere alle impostazioni dell'area di lavoro facendo clic sul nome dell'area di lavoro in alto a destra della schermata. Fare quindi clic su **\<your workspace name\> Settings** (Impostazioni <nome area di lavoro>). 
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-asana-tutorial/tutorial_asana_09.png)
8. Nella finestra **Organization settings** (Impostazioni organizzazione) fare clic su **Administation** (Amministrazione). Fare clic su **Members must log in via SAML** (I membri devono accedere tramite SAML) per abilitare la configurazione di SSO. Eseguire quindi questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-asana-tutorial/tutorial_asana_10.png)  
  1. Nella casella di testo **Sign-in page URL** (URL pagina di accesso) incollare l'URL di accesso SAML da Azure AD.
  2. Nella casella di testo **X.509 Certificate** (Certificato X.509) incollare il certificato X.509 copiato da Azure AD.
9. Fare clic su **Save**. Per altre informazioni e assistenza, vedere la [guida di Asana per la configurazione di SSO](https://asana.com/guide/help/premium/authentication#gl-saml) .
10. Passare alla pagina **Configura accesso Single Sign-On in Asana** in Azure AD, selezionare la conferma della configurazione dell'accesso SSO e quindi fare clic su **Avanti**.
   
    ![Single Sign-On di Microsoft Azure AD][10]
11. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
   
    ![Single Sign-On di Microsoft Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
In questa sezione viene creato un utente test chiamato Britta Simon nel portale classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-asana-tutorial/create_aaduser_09.png) 
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-asana-tutorial/create_aaduser_03.png) 
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-asana-tutorial/create_aaduser_04.png) 
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-asana-tutorial/create_aaduser_05.png) 
  1. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
  2. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
  3. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura:
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-asana-tutorial/create_aaduser_06.png) 
  1. Nella casella di testo **Nome** digitare **Britta**.   
  2. Nella casella di testo **Cognome** digitare **Simon**.
  3. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
  4. Nell'elenco **Ruolo** selezionare **Utente**.
  5. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-asana-tutorial/create_aaduser_07.png) 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-asana-tutorial/create_aaduser_08.png) 
  1. Prendere nota del valore visualizzato in **Nuova password**. 
  2. Fare clic su **Completa**.   

### <a name="create-an-asana-test-user"></a>Creare un utente test per Asana
In questa sezione viene creato un utente di nome Britta Simon in Asana.

1. In **Asana**, passare alla sezione **Teams** (Team) nel pannello sinistro. Fare clic sul pulsante con il segno più. 
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-asana-tutorial/tutorial_asana_12.png) 
2. Digitare l'indirizzo di posta elettronica britta.simon@contoso.com nella casella di testo e quindi selezionare **Invite**(Invita).
3. Fare clic su **Send Invite** (Invia invito). Il nuovo utente riceverà un messaggio di posta elettronica nel proprio account di posta elettronica e dovrà creare e convalidare l'account.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD
In questa sezione Britta Simon viene abilitata per l'uso dell'accesso SSO di Azure, concedendole così accesso ad Asana.

![Assegna utente][200] 

**Per assegnare Britta Simon ad Asana, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201] 
2. Nell'elenco delle applicazioni selezionare **Asana**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-asana-tutorial/tutorial_asana_50.png) 
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203] 
4. Nell'elenco Tutti gli utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD.

Passare alla pagina di accesso di Asana. Nella casella di testo dell'indirizzo di posta elettronica inserire l'indirizzo di posta elettronica britta.simon@contoso.com. Lasciare vuota la casella di testo della password e quindi fare clic su **Log In** (Accedi). Si verrà reindirizzati alla pagina di accesso di Azure AA. Completare le credenziali di Azure AD. A questo punto si è connessi ad Asana.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-asana-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-asana-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-asana-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-asana-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-asana-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-asana-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-asana-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-asana-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-asana-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-asana-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-asana-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-asana-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-asana-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-asana-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-asana-tutorial/tutorial_general_205.png

