---
title: 'Esercitazione: Integrazione di Azure Active Directory con Jitbit Helpdesk | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Jitbit Helpdesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 15ce27d4-0621-4103-8a34-e72c98d72ec3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 71b7cfb92b4caded75e0723564a09fae9f10858c
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58359774"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Esercitazione: Integrazione di Azure Active Directory con Jitbit Helpdesk

Questa esercitazione descrive come integrare Jitbit Helpdesk con Azure Active Directory (Azure AD).
L'integrazione di Jitbit Helpdesk con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Jitbit Helpdesk.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Jitbit Helpdesk con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Jitbit Helpdesk, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Jitbit Helpdesk abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Jitbit Helpdesk supporta l'accesso SSO avviato da **SP**

## <a name="adding-jitbit-helpdesk-from-the-gallery"></a>Aggiunta di Jitbit Helpdesk dalla raccolta

Per configurare l'integrazione di Jitbit Helpdesk in Azure AD, è necessario aggiungere Jitbit Helpdesk dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Jitbit Helpdesk dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Jitbit Helpdesk**, selezionare **Jitbit Helpdesk** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Jitbit Helpdesk nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Jitbit Helpdesk usando un utente test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Jitbit Helpdesk.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Jitbit Helpdesk, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Jitbit Helpdesk](#configure-jitbit-helpdesk-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Jitbit Helpdesk](#create-jitbit-helpdesk-test-user)**: per avere una controparte di Britta Simon in Jitbit Helpdesk collegata alla relativa rappresentazione in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Jitbit Helpdesk, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com/), nella pagina di integrazione dell'applicazione **Jitbit Helpdesk**, selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Jitbit Helpdesk](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente:
    | |
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere tale valore, contattare il [team di supporto clienti di Jitbit Helpdesk](https://www.jitbit.com/support/).

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL come segue: `https://www.jitbit.com/web-helpdesk/`

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Jitbit Helpdesk** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-jitbit-helpdesk-single-sign-on"></a>Configurare l'accesso Single Sign-On di Jitbit Helpdesk

1. In un'altra finestra del Web browser accedere al sito aziendale di Jitbit Helpdesk come amministratore.

1. Nel barra degli strumenti in alto fare clic su **Administration**.

    ![Amministrazione](./media/jitbit-helpdesk-tutorial/ic777681.png "Amministrazione")

1. Fare clic su **General settings**.

    ![Users, companies and permissions](./media/jitbit-helpdesk-tutorial/ic777680.png "Users, companies and permissions")

1. Nella sezione di configurazione **Authentication settings** seguire questa procedura:

    ![Authentication settings](./media/jitbit-helpdesk-tutorial/ic777683.png "Authentication settings")

    a. Selezionare **Enable SAML 2.0 single sign on** (Abilita Single Sign-On SAML 2.0) per l'accesso Single Sign-On con **OneLogin**.

    b. Nella casella di testo **URL endpoint** incollare il valore di **URL di accesso** copiato dal portale di Azure.

    c. Aprire il certificato con codifica **base 64** nel blocco note, copiarne il contenuto negli appunti e incollarlo nella casella di testo **Certificato X.509**

    d. Fare clic su **Salva modifiche**.

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

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Jitbit Helpdesk.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Jitbit Helpdesk**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Jitbit Helpdesk**.

    ![Collegamento a Jitbit Helpdesk nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-jitbit-helpdesk-test-user"></a>Creare un utente di test di Jitbit Helpdesk

Per consentire agli utenti di Azure AD di accedere a Jitbit Helpdesk, è necessario eseguirne il provisioning in Jitbit Helpdesk. Nel caso di Jitbit Helpdesk, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al tenant di **Jitbit Helpdesk**.

1. Scegliere **Amministrazione**dal menu disponibile nella parte superiore.

    ![Amministrazione](./media/jitbit-helpdesk-tutorial/ic777681.png "Amministrazione")

1. Fare clic su **Users, companies and permissions**.

    ![Users, companies and permissions](./media/jitbit-helpdesk-tutorial/ic777682.png "Users, companies and permissions")

1. Fare clic su **Add User**.

    ![Aggiungere un utente](./media/jitbit-helpdesk-tutorial/ic777685.png "Aggiungere un utente")

1. Nella sezione Create immettere i dati dell'account di Azure AD di cui si desidera eseguire il provisioning come indicato di seguito:

    ![Creare](./media/jitbit-helpdesk-tutorial/ic777686.png "Creare")

   a. Nella casella di testo **Username** (Nome utente) digitare il nome dell'utente, ad esempio **BrittaSimon**.

   b. Nella casella di testo **Email** (Indirizzo di posta elettronica) digitare l'indirizzo di posta elettronica dell'utente, ad esempio **BrittaSimon@contoso.com**.

   c. Digitare il nome dell'utente, ad esempio **Britta**, nella casella di testo **First Name** (Nome).

   d. Digitare il cognome dell'utente, ad esempio **Simon**, nella casella di testo **Last Name** (Cognome).

   e. Fare clic su **Create**(Crea).

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Jitbit Helpdesk per eseguire il provisioning degli account utente di Azure AD.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Jitbit Helpdesk nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Jitbit Helpdesk per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
