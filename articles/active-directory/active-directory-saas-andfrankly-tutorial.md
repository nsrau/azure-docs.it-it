---
title: 'Esercitazione: Integrazione di Azure Active Directory con &frankly | Documentazione Microsoft'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e &frankly.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 1d702060-1b89-4e9d-9f01-ede4f1171c73
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4a07f946d396a9263d5b00202cd5229ddc86d1be
ms.openlocfilehash: f32a4df30ee8fa34a754a6181600bd8d66ec71c1
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-frankly"></a>Esercitazione: Integrazione di Azure Active Directory con &frankly
Questa esercitazione descrive l'integrazione di &frankly con Azure Active Directory (Azure AD).

L'integrazione di &frankly con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a &frankly
* È possibile abilitare gli utenti per l'accesso automatico Single Sign-On (SSO) a &frankly con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con &frankly, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di &frankly abilitata per l'accesso Single Sign-On (SSO)

>[!NOTE]
>Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione. 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test.

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di &frankly dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On (SSO) di Microsoft Azure AD

## <a name="adding-frankly-from-the-gallery"></a>Aggiunta di &frankly dalla raccolta
Per configurare l'integrazione di &frankly in Azure AD, è necessario aggiungerla dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere &frankly dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **&frankly**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-andfrankly-tutorial/tutorial_andfrankly_01.png)
7. Nel riquadro dei risultati selezionare **&frankly** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Selezione dell'app nella raccolta](./media/active-directory-saas-andfrankly-tutorial/tutorial_andfrankly_0001.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurare e testare l'accesso Single Sign-On (SSO) di Azure AD
Questa sezione descrive come configurare e testare l'accesso SSO di Azure AD con &frankly in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso SSO, Azure AD deve conoscere qual è l'utente di &frankly che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in &frankly.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** in &frankly.

Per configurare e testare l'accesso Single Sign-On di Azure AD con &frankly, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di &frankly](#creating-a-&frankly-test-user)**: per avere una controparte di Britta Simon in &frankly collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD
In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale classico e viene configurato l'accesso SSO nell'applicazione &frankly.

**Per configurare l'accesso SSO di Azure AD con &frankly, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **&frankly** del portale classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6] 
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a &frankly**, selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-andfrankly-tutorial/tutorial_andfrankly_03.png)
3. Nella pagina della finestra di dialogo **Configurare le impostazioni dell'app**, se si desidera configurare l'applicazione in **modalità iniziata da IDP**,seguire la procedura seguente e fare clic su **Avanti**:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-andfrankly-tutorial/tutorial_andfrankly_04.png)
  1. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://andfrankly.com/saml/simplesaml/www/module.php/saml/sp/metadata.php/<tenant id>`
  2. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://andfrankly.com/saml/simplesaml/www/module.php/saml/sp/saml2-acs.php/<tenant id>`
  3. Fare clic su **Avanti**.
4. Se si desidera configurare l'applicazione in **SP initiated mode** (Modalità iniziata dal provider di servizi) nella finestra di dialogo **Configurare le impostazioni dell'app** fare clic su **"Mostra opzioni avanzate (facoltativo)"**, quindi digitare l'**URL di accesso** e fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-andfrankly-tutorial/tutorial_andfrankly_05.png)
  1. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://andfrankly.com/saml/okta/?saml_sso=<tenant id>`
  2. Fare clic su **Avanti**.
   
   >[!NOTE]
   >Si noti che questi non sono i valori reali. È necessario aggiornare questi valori con l'URL di accesso, l'ID e l'URL di risposta effettivi.Per ottenere questi valori, contattare [help@andfrankly.com](emailTo:help@andfrankly.com) .
   >  
5. Nella pagina **Configura accesso Single Sign-On in &frankly** seguire questa procedura e fare clic su **Avanti**:
   
 ![Configura accesso Single Sign-On](./media/active-directory-saas-andfrankly-tutorial/tutorial_andfrankly_06.png)
 1. Fare clic su **Scarica certificato**e quindi salvare il file nel computer. 
 2. Fare clic su **Avanti**.
6. Per ottenere l'accesso Single Sign-On configurato per l'applicazione, contattare il team di supporto Certify tramite [help@andfrankly.com](emailTo:help@andfrankly.com). Allegare il file dei metadati scaricato e condividerlo con il team &frankly per la configurazione di SSO sul relativo lato.
7. Nel portale di Azure classico selezionare la conferma della configurazione e quindi fare clic su **Avanti**.
   
    ![Single Sign-On di Microsoft Azure AD][10]
8. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
   
    ![Single Sign-On di Microsoft Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
Questa sezione descrive come creare un utente di test chiamato Britta Simon nel portale classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-andfrankly-tutorial/create_aaduser_09.png)
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-andfrankly-tutorial/create_aaduser_03.png)
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-andfrankly-tutorial/create_aaduser_04.png)
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-andfrankly-tutorial/create_aaduser_05.png)
 1. In Tipo di utente selezionare Nuovo utente nell'organizzazione.  
 2. Nella casella di testo **Nome utente** digitare **BrittaSimon**. 
 3. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura:
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-andfrankly-tutorial/create_aaduser_06.png) 
 1. Nella casella di testo **Nome** digitare **Britta**.   
 2. Nella casella di testo **Cognome** digitare **Simon**. 
 3. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**. 
 4. Nell'elenco **Ruolo** selezionare **Utente**. 
 5. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-andfrankly-tutorial/create_aaduser_07.png)
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-andfrankly-tutorial/create_aaduser_08.png) 
 1. Prendere nota del valore visualizzato in **Nuova password**. 
 2. Fare clic su **Completa**.   

### <a name="create-a-frankly-test-user"></a>Creare un utente test per &frankly
In questa sezione viene creato un utente chiamato Britta Simon in &frankly. Collaborare con il team di supporto di &frankly tramite [help@andfrankly.com](emailTo:help@andfrankly.com) per aggiungere gli utenti alla piattaforma &frankly.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD
Questa sezione descrive come abilitare Britta Simon all'uso dell'accesso SSO di Azure concedendole l'accesso a &frankly.

![Assegna utente][200]

**Per assegnare Britta Simon a &frankly, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201]
2. Nell'elenco delle applicazioni selezionare **&frankly**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-andfrankly-tutorial/tutorial_andfrankly_50.png)
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203]
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro &frankly nel pannello di accesso, si accederà automaticamente all'applicazione &frankly.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_205.png

