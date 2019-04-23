---
title: 'Esercitazione: Integrazione di Azure Active Directory con Intacct | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Intacct.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 92518e02-a62c-4b1b-a8e9-2803eb2b49ac
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 31bc5622c6c6c3dd00bc59b5d8f3aa349055d125
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59279245"
---
# <a name="tutorial-azure-active-directory-integration-with-intacct"></a>Esercitazione: Integrazione di Azure Active Directory con Intacct

Questa esercitazione descrive come integrare Intacct con Azure Active Directory (Azure AD).
L'integrazione di Intacct con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Intacct.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Intacct con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Intacct, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Intacct abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Intacct supporta l'accesso SSO avviato da **IDP**

## <a name="adding-intacct-from-the-gallery"></a>Aggiunta di Intacct dalla raccolta

Per configurare l'integrazione di Intacct in Azure AD, è necessario aggiungere Intacct dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Intacct dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Intacct**, selezionare **Intacct** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Intacct nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Intacct con un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Intacct.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Intacct, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Intacct](#configure-intacct-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Intacct](#create-intacct-test-user)**: per avere una controparte di Britta Simon in Intacct collegata alla relativa rappresentazione in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Intacct, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Intacct** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Intacct](common/idp-reply.png)

    Nella casella di testo **URL di risposta** digitare un URL nel formato seguente:
    | |
    |--|
    | `https://<companyname>.intacct.com/ia/acct/sso_response.phtml`|
    | `https://www.intacct.com/ia/acct/sso_response.phtml` |

    > [!NOTE]
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di risposta effettivo. Per ottenere tale valore, contattare il [team di supporto clienti Intacct](https://us.intacct.com/support). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Intacct** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-intacct-single-sign-on"></a>Configurare l'accesso Single Sign-On di Intacct

1. In un'altra finestra del Web browser accedere al sito aziendale di Intacct come amministratore.

1. Fare clic sulla scheda **Company** (Azienda) e quindi su **Company Info** (Informazioni sull'azienda).

    ![Azienda](./media/intacct-tutorial/ic790037.png "Azienda")

1. Fare clic sulla scheda **Security** (Sicurezza) e quindi su **Edit** (Modifica).

    ![Sicurezza](./media/intacct-tutorial/ic790038.png "Sicurezza")

1. Nella sezione **Single sign on (SSO)** seguire questa procedura:

    ![Single sign on](./media/intacct-tutorial/ic790039.png "single sign on")

    a. Selezionare **Abilita Single Sign-On**.

    b. In **Identity provider type** (Tipo di provider di identità) selezionare **SAML 2.0**.

    c. Nella casella di testo **Issuer URL** (URL autorità di certificazione) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    d. Nella casella di testo **Login URL** (URL di accesso) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    e. Aprire il certificato con codifica **Base 64** nel Blocco note, copiarne il contenuto negli Appunti e incollarlo nella casella **Certificato**.

    f. Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

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

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Intacct.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Intacct**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Intacct**.

    ![Collegamento Intacct nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-intacct-test-user"></a>Creare un utente di test di Intacct

Per consentire agli utenti di Azure AD di accedere a Intacct, è necessario eseguirne il provisioning in Intacct. In Intacct il provisioning è un'attività manuale.

**Per eseguire il provisioning degli account utente, seguire questa procedura:**

1. Accedere al tenant di **Intacct**.

1. Fare clic sulla scheda **Company** (Azienda) e quindi su **Users** (Utenti).

    ![Utenti](./media/intacct-tutorial/ic790041.png "Utenti")

1. Fare clic sulla scheda **Add** (Aggiungi).

    ![Aggiungi](./media/intacct-tutorial/ic790042.png "Aggiungi")

1. Nella sezione **Informazioni utente** seguire questa procedura:

    ![Informazioni utente](./media/intacct-tutorial/ic790043.png "Informazioni utente")

    a. Nelle caselle di testo **User ID**, **Last name**, **First name**, **Email address**, **Title** e **Phone** immettere l'ID utente, il cognome, il nome, l'indirizzo di posta elettronica, la posizione e il numero di telefono di un account Azure AD di cui si vuole eseguire il provisioning nella sezione **Informazioni utente**.

    b. Selezionare i **privilegi di amministratore** di un account Azure AD di cui si vuole eseguire il provisioning.

    c. Fare clic su **Save**. Il titolare dell'account Azure AD riceve un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.

> [!NOTE]
> Per eseguire il provisioning degli account utente di Azure AD, è possibile usare altri strumenti o API per la creazione di account utente Intacct forniti da Intacct.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Intacct nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Intacct per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

