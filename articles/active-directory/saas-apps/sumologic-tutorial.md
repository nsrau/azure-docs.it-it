---
title: 'Esercitazione: Integrazione di Azure Active Directory con SumoLogic| Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SumoLogic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fbb76765-92d7-4801-9833-573b11b4d910
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 7548d7d7b808472b3f5446fadfe800584f61b1df
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233345"
---
# <a name="tutorial-azure-active-directory-integration-with-sumologic"></a>Esercitazione: Integrazione di Azure Active Directory con SumoLogic

Questa esercitazione descrive come integrare SumoLogic con Azure Active Directory (Azure AD).
L'integrazione di SumoLogic con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a SumoLogic.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a SumoLogic con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con SumoLogic, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di SumoLogic abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* SumoLogic supporta l'accesso SSO avviato da **SP**

## <a name="adding-sumologic-from-the-gallery"></a>Aggiunta di SumoLogic dalla raccolta

Per configurare l'integrazione di SumoLogic in Azure AD, è necessario aggiungere SumoLogic dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere SumoLogic dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **SumoLogic**, selezionare **SumoLogic** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![SumoLogic nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SumoLogic usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SumoLogic.

Per configurare e testare l'accesso Single Sign-On di Azure AD con SumoLogic, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di SumoLogic](#configure-sumologic-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di SumoLogic](#create-sumologic-test-user)** : per avere una controparte di Britta Simon in SumoLogic collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con SumoLogic, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **SumoLogic** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di SumoLogic](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<tenantname>.SumoLogic.com`

   b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente:

    | |
    |--|
    | `https://<tenantname>.us2.sumologic.com` |
    | `https://<tenantname>.sumologic.com` |
    | `https://<tenantname>.us4.sumologic.com` |
    | `https://<tenantname>.eu.sumologic.com` |
    | `https://<tenantname>.au.sumologic.com` |

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di SumoLogic](https://www.sumologic.com/contact-us/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura SumoLogic** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-sumologic-single-sign-on"></a>Configurare l'accesso Single Sign-On di SumoLogic

1. In un'altra finestra del Web browser accedere al sito aziendale di SumoLogic come amministratore.

1. Passare a **Manage (Gestisci) \> Security (Sicurezza)** .

    ![Manage](./media/sumologic-tutorial/ic778556.png "Gestisci")

1. Fare clic su **SAML**.

    ![Impostazioni di sicurezza globali](./media/sumologic-tutorial/ic778557.png "Impostazioni di sicurezza globale")

1. Dall'elenco **Select a configuration or create a new one** (Selezionare o creare una configurazione) selezionare **Azure AD**, quindi fare clic su **Configure** (Configura).

    ![Configurazione di SAML 2.0](./media/sumologic-tutorial/ic778558.png "Configura SAML 2.0")

1. Nella finestra di dialogo **Configura SAML 2.0** , seguire questa procedura:

    ![Configure SAML 2.0](./media/sumologic-tutorial/ic778559.png "Configura SAML 2.0")

    a. Nella casella di testo **Configuration Name** (Nome configurazione) digitare **Azure AD**.

    b. Selezionare **Debug Mode**.

    c. Nella casella di testo **Issuer** (Autorità di certificazione) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    d. Nella casella di testo **Authn Request URL** (URL richiesta di autenticazione) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    e. Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e incollare l’intero certificato nella casella di testo **Certificate X.509** .

    f. Per **Email Attribute** (Attributo e-mail), selezionare **Use SAML subject** (Usa oggetto SAML).  

    g. Selezionare **SP initiated Login Configuration**.

    h. Nella casella di testo **Login Path** (Percorso di accesso) digitare **Azure** e fare clic su **Salva**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

Questa sezione descrive come creare un utente di test di nome Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SumoLogic.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **SumoLogic**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **SumoLogic**.

    ![Collegamento SumoLogic nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-sumologic-test-user"></a>Creare l'utente di test di SumoLogic

Per consentire agli utenti di Azure AD di accedere a SumoLogic, è necessario effettuarne il provisioning in SumoLogic. Nel caso di SumoLogic, il provisioning è un'attività manuale.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere al tenant di **SumoLogic**.

1. Passare a **Manage (Gestisci) \> Users (Utenti)** .

    ![Utenti](./media/sumologic-tutorial/ic778561.png "Utenti")

1. Fare clic su **Aggiungi**.

    ![Utenti](./media/sumologic-tutorial/ic778562.png "Utenti")

1. Nella finestra di dialogo **New User** , seguire questa procedura:

    ![New User](./media/sumologic-tutorial/ic778563.png "Nuovo utente") 

    a. Digitare le informazioni correlate dell'account Azure AD di cui si vuole effettuare il provisioning nelle caselle di testo **First Name** (Nome), **Last Name** (Cognome) e **Email** (Posta elettronica).
  
    b. Selezionare un ruolo.
  
    c. Per **Status** (Stato) selezionare **Active** (Attivo).
  
    d. Fare clic su **Save**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente forniti da SumoLogic per effettuare il provisioning degli account utente di Azure AD.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Facendo clic sul riquadro SumoLogic nel pannello di accesso si dovrebbe accedere automaticamente all'istanza di SumoLogic per cui è stato configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

