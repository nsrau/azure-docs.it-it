---
title: 'Esercitazione: Integrazione di Azure Active Directory con ClickTime | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e ClickTime.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d437b5ab-4d71-4c13-96d0-79018cebbbd4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea643622973456f6334333731f3a3e7be6a41089
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57889523"
---
# <a name="tutorial-azure-active-directory-integration-with-clicktime"></a>Esercitazione: Integrazione di Azure Active Directory con ClickTime

Questa esercitazione descrive come integrare ClickTime con Azure Active Directory (Azure AD).
L'integrazione di ClickTime con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a ClickTime.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a ClickTime con gli account Azure AD.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con ClickTime, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di ClickTime abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* ClickTime supporta l'accesso SSO avviato da **IDP**

## <a name="adding-clicktime-from-the-gallery"></a>Aggiunta di ClickTime dalla raccolta

Per configurare l'integrazione di ClickTime in Azure AD, è necessario aggiungere ClickTime dalla raccolta al proprio elenco delle app SaaS gestite.

**Per aggiungere ClickTime dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **ClickTime**, selezionare **ClickTime** dal pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![ClickTime nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con ClickTime usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in ClickTime.

Per configurare e testare l'accesso Single Sign-On di Azure AD con ClickTime, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per ClickTime](#configure-clicktime-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di ClickTime](#create-clicktime-test-user)**: per avere una controparte di Britta Simon in ClickTime collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con ClickTime, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **ClickTime** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella pagina **Configura l'accesso Single Sign-On con SAML** eseguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di ClickTime](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL: `https://app.clicktime.com/sp/`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente:
    
    | |
    |--|
    | `https://app.clicktime.com/Login/` |
    | `https://app.clicktime.com/App/Login/Consume.aspx` |

4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Set up ClickTime** (Configura ClickTime) copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-clicktime-single-sign-on"></a>Configurare l'accesso Single Sign-On per ClickTime

1. In un'altra finestra del Web browser accedere al sito aziendale di ClickTime come amministratore.

1. Nella barra degli strumenti in alto fare clic su **Preferences** (Preferenze) e quindi su **Security Settings** (Impostazioni di sicurezza).

1. Nella sezione di configurazione **Preferenza Single Sign-On** seguire questa procedura:
   
    ![Impostazioni di sicurezza](./media/clicktime-tutorial/tic777280.png "Impostazioni di sicurezza")
   
    a.  Selezionare **Allow** sign-in using Single Sign-On (SSO) **Azure AD** (Consenti l'accesso Single Sign-On (SSO) con Azure AD).
   
    b. Nella casella di testo **Identity Provider Endpoint** (Endpoint provider di identità) incollare il valore di **URL di accesso** copiato dal portale di Azure.
   
    c.  Aprire il **certificato con codifica Base 64** scaricato dal portale di Azure in **Blocco note**, copiarne il contenuto e quindi incollarlo nella casella di testo **X.509 Certificate** (Certificato X.509).
   
    d.  Fare clic su **Save**.

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

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure, tramite la concessione dell'accesso a ClickTime.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **ClickTime**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni, selezionare **ClickTime**.

    ![Collegamento di ClickTime nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-clicktime-test-user"></a>Creare l'utente di test di ClickTime

Per consentire agli utenti di Azure AD di accedere a ClickTime, è necessario eseguirne il provisioning in ClickTime.  
Nel caso di ClickTime, il provisioning è un'attività manuale.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da ClickTime per effettuare il provisioning degli account utente di Azure AD.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al tenant **ClickTime**.

1. Nel barra degli strumenti in alto fare clic su **Company** (Azienda) e quindi su **People** (Persone).
   
    ![Persone](./media/clicktime-tutorial/tic777282.png "Persone")

1. Fare clic su **Add Person**.
   
    ![Aggiungi persona](./media/clicktime-tutorial/tic777283.png "Aggiungi persona")

1. Nella sezione New Person seguire questa procedura:
   
    ![Persone](./media/clicktime-tutorial/tic777284.png "Persone")
   
    a.  Nella casella di testo **full name** (Nome completo) digitare il nome e cognome dell'utente, ad esempio **Britta Simon**. 
  
    b.  Nella casella di testo **email address** (Indirizzo di posta elettronica) digitare l'indirizzo di posta elettronica dell'utente, ad esempio **brittasimon\@contoso.com**.
       
    > [!NOTE]
    > È anche possibile impostare altre proprietà dell'oggetto new person.
   
    c.  Fare clic su **Save**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di ClickTime nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione ClickTime per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

