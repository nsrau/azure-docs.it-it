---
title: 'Esercitazione: Integrazione di Azure Active Directory con Mixpanel | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Mixpanel.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a2df26ef-d441-44ac-a9f3-b37bf9709bcb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/28/2019
ms.author: jeedes
ms.openlocfilehash: ae93c9911034cf9cba1fdcf8c9472e4d3332bcad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67097005"
---
# <a name="tutorial-azure-active-directory-integration-with-mixpanel"></a>Esercitazione: Integrazione di Azure Active Directory con Mixpanel

Questa esercitazione descrive come integrare Mixpanel con Azure Active Directory (Azure AD).
L'integrazione di Mixpanel con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Mixpanel.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Mixpanel con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Mixpanel, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Mixpanel abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Mixpanel supporta l'accesso SSO avviato da **SP**

## <a name="adding-mixpanel-from-the-gallery"></a>Aggiunta di Mixpanel dalla raccolta

Per configurare l'integrazione di Mixpanel in Azure AD, è necessario aggiungere Mixpanel dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Mixpanel dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Mixpanel**, selezionare **Mixpanel** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Mixpanel nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Mixpanel con un utente test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Mixpanel.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Mixpanel, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Mixpanel](#configure-mixpanel-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test Mixpanel](#create-mixpanel-test-user)** : per avere una controparte di Britta Simon in Mixpanel collegata alla rispettiva rappresentazione in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Mixpanel, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione [Mixpanel](https://portal.azure.com/) del **portale di Azure** selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Mixpanel](common/sp-signonurl.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://mixpanel.com/login/`

    > [!NOTE]
    > Eseguire la registrazione nella pagina [https://mixpanel.com/register/](https://mixpanel.com/register/) per configurare le credenziali di accesso e contattare il [team di supporto di Mixpanel](mailto:support@mixpanel.com) per abilitare le impostazioni SSO per il tenant. Se necessario, è possibile ottenere anche il valore per l'URL di accesso dal team di supporto di Mixpanel. 

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Mixpanel** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-mixpanel-single-sign-on"></a>Configurare l'accesso Single Sign-On di Mixpanel

1. In una finestra del browser diversa accedere all'applicazione Mixpanel come amministratore.

2. Nella parte inferiore della pagina fare clic sull'icona a forma di piccolo **ingranaggio** nell'angolo sinistro. 
   
    ![Accesso Single Sign-On di Mixpanel](./media/mixpanel-tutorial/tutorial_mixpanel_06.png) 

3. Fare clic sulla scheda **Access security** (Sicurezza accesso) e quindi su **Change settings** (Modifica impostazioni).
   
    ![Impostazioni di Mixpanel](./media/mixpanel-tutorial/tutorial_mixpanel_08.png) 

4. Nella finestra di dialogo **Change your certificate** (Modifica certificato) fare clic su **Choose file** (Scegli file) per caricare il certificato scaricato e quindi su **NEXT** (Avanti).
   
    ![Impostazioni di Mixpanel](./media/mixpanel-tutorial/tutorial_mixpanel_09.png) 

5.  Nella casella di testo per l'URL di autenticazione della finestra di dialogo **Modifica l'URL di autenticazione**, incollare il valore dell'**URL di accesso** copiato dal portale di Azure e quindi fare clic su **NEXT** (Avanti).
   
    ![Impostazioni di Mixpanel](./media/mixpanel-tutorial/tutorial_mixpanel_10.png) 

6. Fare clic su **Done**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon\@dominioaziendale.estensione**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Mixpanel.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Mixpanel**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Mixpanel**.

    ![Collegamento di Mixpanel nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-mixpanel-test-user"></a>Creare un utente di test Mixpanel

Questa sezione descrive come creare un utente chiamato Britta Simon in Mixpanel. 

1. Accedere al sito aziendale di Mixpanel come amministratore.

2. Nella parte inferiore della pagina fare clic sul pulsante a forma di piccolo ingranaggio nell'angolo sinistro per aprire la finestra **Settings** .

3. Fare clic sulla scheda **Team** .

4. Nella casella di testo **team member** digitare l'indirizzo di posta elettronica di Britta disponibile in Azure.
   
    ![Impostazioni di Mixpanel](./media/mixpanel-tutorial/tutorial_mixpanel_11.png) 

5. Fare clic su **Invita**. 

> [!Note]
> L'utente riceverà un messaggio di posta elettronica per la configurazione del profilo.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Mixpanel nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Mixpanel per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

