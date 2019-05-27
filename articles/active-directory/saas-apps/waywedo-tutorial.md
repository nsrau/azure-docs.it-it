---
title: 'Esercitazione: integrazione di Azure Active Directory con Way We Do | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Way We Do.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 84fc4f36-ecd1-42c6-8a70-cb0f3dc15655
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 541be5466b65705daa0485976eab3df8eb3d707f
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65905320"
---
# <a name="tutorial-azure-active-directory-integration-with-way-we-do"></a>Esercitazione: integrazione di Azure Active Directory con Way We Do

Questa esercitazione descrive come integrare Way We Do con Azure Active Directory (Azure AD).
L'integrazione di Way We Do con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Way We Do.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Way We Do con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Way We Do, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Way We Do abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Way We Do supporta l'accesso SSO avviato da **SP**

* Way We Do supporta il provisioning utenti **JIT**

## <a name="adding-way-we-do-from-the-gallery"></a>Aggiunta di Way We Do dalla raccolta

Per configurare l'integrazione di Way We Do in Azure AD, è necessario aggiungere Way We Do dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Way We Do dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Way We Do**, selezionare **Way We Do** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Way We Do nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Way We Do usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Way We Do.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Way We Do, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Way We Do](#configure-way-we-do-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Way We Do](#create-way-we-do-test-user)**: per avere una controparte di Britta Simon in Way We Do collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Way We Do, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Way We Do** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Way We Do](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL usando il modello seguente: `https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori contattare il [team di supporto clienti di Way We Do](mailto:support@waywedo.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **Certificato (base)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificateraw.png)

6. Nella sezione **Configura Way We Do** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-way-we-do-single-sign-on"></a>Configurare l'accesso Single Sign-On di Way We Do

1. In un'altra finestra del Web browser accedere a Way We Do come amministratore della sicurezza.

2. Fare clic sull'**icona della persona** nell'angolo in alto a destra di qualsiasi pagina in Way We Do e quindi fare clic su **Account** nel menu a discesa.

    ![Account Way We Do](./media/waywedo-tutorial/tutorial_waywedo_account.png) 

3. Fare clic sull'**icona del menu** per aprire il menu di navigazione e fare clic su **Single Sign On**.

    ![Accesso Single Sign-On Way We Do](./media/waywedo-tutorial/tutorial_waywedo_single.png)

4. Nella pagina **Single sign-on setup** (Configurazione Single Sign-On) seguire questa procedura:

    ![Salvataggio Way We Do](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    a. Impostare **Turn on single sign-on** (Attiva Single Sign-On) su **Yes** (Sì) per abilitare l'accesso Single Sign-On.

    b. Nella casella di testo **Single sign-on name** (Nome Single Sign-On) immettere il proprio nome.

    c. Nella casella di testo **Entity ID** (ID entità) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    d. Nella casella di testo **SAML SSO URL** (URL SSO SAML) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    e. Caricare il certificato facendo clic sul pulsante **Select** (Seleziona) accanto a **Certificate** (Certificato).

    f. **Impostazioni facoltative** -
    
    * Enable Passwords (Abilita le password): quando questa opzione è disabilitata, la normale password per Way We Do consente solo l'accesso Single Sign-on degli utenti.

    * Enable Auto-provisioning (Abilita il provisioning automatico): quando questa opzione è abilitata, l'indirizzo di posta elettronica usato per l'accesso viene confrontato automaticamente con l'elenco degli utenti in Way We Do. Se l'indirizzo di posta elettronica non corrisponde a un utente attivo in Way We Do, aggiunge automaticamente un nuovo account utente per la persona che esegue l'accesso, chiedendo eventuali informazioni mancanti.

      > [!NOTE]
      > Gli utenti aggiunti tramite Single Sign-On vengono aggiunti come utenti generici e non hanno un ruolo assegnato nel sistema. Un amministratore può accedere e modificare il ruolo di sicurezza degli utenti in qualità di editor o amministratore e può anche assegnare uno o più ruoli dell'organigramma. 

    g. Per salvare le impostazioni, fare clic su **Save** (Salva).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare `brittasimon@yourcompanydomain.extension`. Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Way We Do.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Way We Do**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Way We Do**.

    ![Collegamento di Way We Do nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-way-we-do-test-user"></a>Creare l'utente di test di Way We Do

In questa sezione viene creato un utente di nome Britta Simon in Way We Do. Way We Do supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Way We Do, ne viene creato uno nuovo dopo l'autenticazione.

> [!Note]
> Per creare un utente manualmente, contattare il [team del supporto clienti di Way We Do](mailto:support@waywedo.com).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Way We Do nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Way We Do per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

