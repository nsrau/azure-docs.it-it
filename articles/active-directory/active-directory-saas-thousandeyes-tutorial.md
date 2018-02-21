---
title: 'Esercitazione: Integrazione di Azure Active Directory con ThousandEyes | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e ThousandEyes.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/15/2017
ms.author: jeedes
ms.openlocfilehash: 81132e40eba589dff7ede0ee94fab1a289dc19d7
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-thousandeyes"></a>Esercitazione: Integrazione di Azure Active Directory con ThousandEyes

Questa esercitazione descrive come integrare ThousandEyes con Azure Active Directory (Azure AD).

L'integrazione di ThousandEyes con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a ThousandEyes.
- È possibile abilitare gli utenti per l'accesso automatico a ThousandEyes (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con ThousandEyes, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Una sottoscrizione di ThousandEyes abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese: [offerta prova](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di ThousandEyes dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-thousandeyes-from-the-gallery"></a>Aggiunta di ThousandEyes dalla raccolta
Per configurare l'integrazione di ThousandEyes in Azure AD, è necessario aggiungere ThousandEyes dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere ThousandEyes dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

4. Nella casella di ricerca digitare **ThousandEyes**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-thousandeyes-tutorial/tutorial_thousandeyes_search.png)

5. Nel pannello dei risultati selezionare **ThousandEyes** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-thousandeyes-tutorial/tutorial_thousandeyes_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con ThousandEyes usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente controparte di ThousandEyes che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in ThousandEyes.

Per stabilire la relazione di collegamento, in ThousandEyes assegnare il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con ThousandEyes, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test per ThousandEyes](#creating-a-thousandeyes-test-user)**: per avere una controparte di Britta Simon in ThousandEyes collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione ThousandEyes.

**Per configurare Single Sign-On di Azure AD con ThousandEyes, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **ThousandEyes** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/active-directory-saas-thousandeyes-tutorial/tutorial_thousandeyes_samlbase.png)

3. Nella sezione **URL e dominio ThousandEyes** seguire questa procedura:

    ![Configure Single Sign-On](./media/active-directory-saas-thousandeyes-tutorial/tutorial_thousandeyes_url.png)

    Nella casella di testo **URL di accesso** digitare l'URL come: `https://app.thousandeyes.com/login/sso`

4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configure Single Sign-On](./media/active-directory-saas-thousandeyes-tutorial/tutorial_thousandeyes_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di ThousandEyes** fare clic su **Configura ThousandEyes** per aprire la finestra **Configura accesso**. Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configure Single Sign-On](./media/active-directory-saas-thousandeyes-tutorial/tutorial_thousandeyes_configure.png) 

7. In un'altra finestra del browser Web accedere al sito aziendale di **ThousandEyes** come amministratore.

8. Nel menu in alto fare clic su **Impostazioni**.
   
    ![Impostazioni](./media/active-directory-saas-thousandeyes-tutorial/ic790066.png "Impostazioni")

9. Fare clic su **Account**
   
    ![Account](./media/active-directory-saas-thousandeyes-tutorial/ic790067.png "Account")

10. Fare clic sulla scheda **Security & Authentication** (Sicurezza e autenticazione).
   
    ![Sicurezza e autenticazione](./media/active-directory-saas-thousandeyes-tutorial/ic790068.png "Security e autenticazione")

11. Nella sezione **Setup Single Sign-On** eseguire la procedura seguente:
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-thousandeyes-tutorial/ic790069.png "Configurare l'accesso Single Sign-On")
  
    a. Selezionare **Enable Single Sign-On**.
  
    b. Nella casella di testo **Login Page URL** (URL pagina di accesso) incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure.
  
    c. Nella casella di testo **Logout Page URL** (URL pagina di disconnessione) incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure.
  
    d. Nella casella di testo **Autorità di certificazione del provider di identità** incollare l'**ID di entità SAML** copiato dal portale di Azure.
  
    e. In **Certificato di verifica** fare clic su **Scegli file** e quindi caricare il certificato scaricato dal portale di Azure.
  
    f. Fare clic su **Save**.
 
> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-thousandeyes-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-thousandeyes-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-thousandeyes-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-thousandeyes-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-thousandeyes-test-user"></a>Creazione di un utente di test di ThousandEyes

Per consentire agli utenti di Azure AD di accedere a ThousandEyes, è necessario eseguirne il provisioning in ThousandEyes.  
Nel caso di ThousandEyes, il provisioning è un'attività manuale.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da ThousandEyes per eseguire il provisioning degli account utente di Azure Active Directory.

**Per eseguire il provisioning di un account utente a ThousandEyes, eseguire la procedura seguente:**

1. Accedere al sito aziendale di ThousandEyes come amministratore.

2. Fare clic su **Impostazioni**.
   
    ![Impostazioni](./media/active-directory-saas-thousandeyes-tutorial/IC790066.png "Impostazioni")

3. Fare clic su **Account**.
   
    ![Account](./media/active-directory-saas-thousandeyes-tutorial/IC790067.png "Account")

4. Fare clic sulla scheda **Accounts & Users** (Account e utenti).
   
    ![Account e utenti](./media/active-directory-saas-thousandeyes-tutorial/IC790073.png "Account e utenti")

5. Nella sezione **Add Users & Accounts** (Account e utenti) eseguire la procedura seguente:
   
    ![Aggiungere account utente](./media/active-directory-saas-thousandeyes-tutorial/IC790074.png "Aggiungere account utente")   
  
    a. Nella casella di testo **Name** (Nome) digitare il nome dell'utente, ad esempio **Britta Simon**.

    b. Nella casella di testo **Email** (Posta elettronica) digitare l'indirizzo di posta elettronica di un utente, ad esempio **brittasimon@contoso.com**.
   
    b. Fare clic su **Add New User to Account**.
      
     >[!NOTE]
     >Il titolare dell'account Azure Active Directory riceve un messaggio di posta elettronica con un collegamento da selezionare per confermare e attivare l'account.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a ThousandEyes.

![Assegna utente][200] 

**Per assegnare Britta Simon a ThousandEyes, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **ThousandEyes**.

    ![Configure Single Sign-On](./media/active-directory-saas-thousandeyes-tutorial/tutorial_thousandeyes_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro ThousandEyes nel pannello di accesso, si dovrebbe automaticamente accedere all'applicazione ThousandEyes.

Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_203.png

