---
title: 'Esercitazione: Integrazione di Azure Active Directory con ThousandEyes | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e ThousandEyes.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43aa43d37099d69441a5dde47327e784d67fa9ac
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56189447"
---
# <a name="tutorial-azure-active-directory-integration-with-thousandeyes"></a>Esercitazione: Integrazione di Azure Active Directory con ThousandEyes

Questa esercitazione descrive come integrare ThousandEyes con Azure Active Directory (Azure AD).

L'integrazione di ThousandEyes con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a ThousandEyes.
- È possibile abilitare gli utenti per l'accesso automatico a ThousandEyes (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con ThousandEyes, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Una sottoscrizione di ThousandEyes abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si ha un ambiente di valutazione di Azure AD, è possibile ottenere una versione di valutazione di un mese qui: [Offerta per la versione di valutazione](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di ThousandEyes dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-thousandeyes-from-the-gallery"></a>Aggiunta di ThousandEyes dalla raccolta
Per configurare l'integrazione di ThousandEyes in Azure AD, è necessario aggiungere ThousandEyes dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere ThousandEyes dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

1. Nella casella di ricerca digitare **ThousandEyes**.

    ![Creazione di un utente test di Azure AD](./media/thousandeyes-tutorial/tutorial_thousandeyes_search.png)

1. Nel pannello dei risultati selezionare **ThousandEyes** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/thousandeyes-tutorial/tutorial_thousandeyes_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con ThousandEyes usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente controparte di ThousandEyes che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in ThousandEyes.

Per stabilire la relazione di collegamento, in ThousandEyes assegnare il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con ThousandEyes, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
1. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creazione di un utente test per ThousandEyes](#creating-a-thousandeyes-test-user)**: per avere una controparte di Britta Simon in ThousandEyes collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione ThousandEyes.

**Per configurare Single Sign-On di Azure AD con ThousandEyes, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **ThousandEyes** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.

    ![Configure Single Sign-On](./media/thousandeyes-tutorial/tutorial_thousandeyes_samlbase.png)

1. Nella sezione **URL e dominio ThousandEyes** seguire questa procedura:

    ![Configure Single Sign-On](./media/thousandeyes-tutorial/tutorial_thousandeyes_url.png)

    Nella casella di testo **URL di accesso** digitare l'URL come: `https://app.thousandeyes.com/login/sso`

1. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configure Single Sign-On](./media/thousandeyes-tutorial/tutorial_thousandeyes_certificate.png)

1. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/thousandeyes-tutorial/tutorial_general_400.png)

1. Nella sezione **Configurazione di ThousandEyes** fare clic su **Configura ThousandEyes** per aprire la finestra **Configura accesso**. Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configure Single Sign-On](./media/thousandeyes-tutorial/tutorial_thousandeyes_configure.png) 

1. In un'altra finestra del browser Web accedere al sito aziendale di **ThousandEyes** come amministratore.

1. Nel menu in alto fare clic su **Impostazioni**.

    ![Impostazioni](./media/thousandeyes-tutorial/ic790066.png "Impostazioni")

1. Fare clic su **Account**

    ![Account](./media/thousandeyes-tutorial/ic790067.png "Account")

1. Fare clic sulla scheda **Security & Authentication** (Sicurezza e autenticazione).

    ![Sicurezza e autenticazione](./media/thousandeyes-tutorial/ic790068.png "Security e autenticazione")

1. Nella sezione **Setup Single Sign-On** eseguire la procedura seguente:

    ![Configurare l'accesso Single Sign-On](./media/thousandeyes-tutorial/ic790069.png "Configurare l'accesso Single Sign-On")

    a. Selezionare **Enable Single Sign-On**.

    b. Nella casella di testo **URL pagina di accesso** incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure.

    c. Nella casella di testo **URL pagina di disconnessione** incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure.

    d. Nella casella di testo **Autorità di certificazione del provider di identità** incollare l'**ID di entità SAML** copiato dal portale di Azure.

    e. In **Certificato di verifica** fare clic su **Scegli file** e quindi caricare il certificato scaricato dal portale di Azure.

    f. Fare clic su **Save**.

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/thousandeyes-tutorial/create_aaduser_01.png) 

1. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/thousandeyes-tutorial/create_aaduser_02.png) 

1. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.

    ![Creazione di un utente test di Azure AD](./media/thousandeyes-tutorial/create_aaduser_03.png)

1. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:

    ![Creazione di un utente test di Azure AD](./media/thousandeyes-tutorial/create_aaduser_04.png)

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Create**(Crea).

### <a name="creating-a-thousandeyes-test-user"></a>Creazione di un utente di test di ThousandEyes

Questa sezione descrive come creare un utente chiamato Britta Simon in ThousandEyes. ThousandEyes supporta il provisioning utenti automatico, che è abilitato per impostazione predefinita. È possibile scoprire più dettagli [qui](thousandeyes-provisioning-tutorial.md) su come configurare il provisioning utenti automatico.

**Per creare un utente manualmente, seguire questa procedura:**

1. Accedere al sito aziendale di ThousandEyes come amministratore.

1. Fare clic su **Impostazioni**.

    ![Impostazioni](./media/thousandeyes-tutorial/IC790066.png "Impostazioni")

1. Fare clic su **Account**.

    ![Account](./media/thousandeyes-tutorial/IC790067.png "Account")

1. Fare clic sulla scheda **Accounts & Users** (Account e utenti).

    ![Account e utenti](./media/thousandeyes-tutorial/IC790073.png "Account e utenti")

1. Nella sezione **Add Users & Accounts** (Account e utenti) eseguire la procedura seguente:

    ![Aggiungere account utente](./media/thousandeyes-tutorial/IC790074.png "Aggiungere account utente")

    a. Nella casella di testo **Name** (Nome) digitare il nome dell'utente, ad esempio **Britta Simon**.

    b. Nella casella di testo **Email** (Posta elettronica) digitare l'indirizzo di posta elettronica di un utente, ad esempio **brittasimon@contoso.com**.

    b. Fare clic su **Add New User to Account**.

    > [!NOTE]
    > Il titolare dell'account Azure Active Directory riceve un messaggio di posta elettronica con un collegamento da selezionare per confermare e attivare l'account.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da ThousandEyes per eseguire il provisioning degli account utente di Azure Active Directory.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a ThousandEyes.

![Assegna utente][200] 

**Per assegnare Britta Simon a ThousandEyes, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco di applicazioni selezionare **ThousandEyes**.

    ![Configure Single Sign-On](./media/thousandeyes-tutorial/tutorial_thousandeyes_app.png) 

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro ThousandEyes nel pannello di accesso, si dovrebbe automaticamente accedere all'applicazione ThousandEyes.

Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
* [Configura provisioning utenti](thousandeyes-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/thousandeyes-tutorial/tutorial_general_01.png
[2]: ./media/thousandeyes-tutorial/tutorial_general_02.png
[3]: ./media/thousandeyes-tutorial/tutorial_general_03.png
[4]: ./media/thousandeyes-tutorial/tutorial_general_04.png

[100]: ./media/thousandeyes-tutorial/tutorial_general_100.png

[200]: ./media/thousandeyes-tutorial/tutorial_general_200.png
[201]: ./media/thousandeyes-tutorial/tutorial_general_201.png
[202]: ./media/thousandeyes-tutorial/tutorial_general_202.png
[203]: ./media/thousandeyes-tutorial/tutorial_general_203.png
