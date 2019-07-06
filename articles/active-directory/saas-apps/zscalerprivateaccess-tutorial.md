---
title: 'Esercitazione: Integrazione di Azure Active Directory con Zscaler Private Access (ZPA) | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Zscaler Private Access (ZPA).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 83711115-1c4f-4dd7-907b-3da24b37c89e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0a1538f640bb4722eca1d4f3a80125837593bab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67085686"
---
# <a name="tutorial-integrate-zscaler-private-access-zpa-with-azure-active-directory"></a>Esercitazione: Integrare Zscaler Private Access (ZPA) con Azure Active Directory

Questa esercitazione descrive come integrare Zscaler Private Access (ZPA) con Azure Active Directory (Azure AD). Integrando Zscaler Private Access (ZPA) con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Zscaler Private Access (ZPA).
* Abilitare gli utenti per l'accesso automatico a Zscaler Private Access (ZPA) con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Zscaler Private Access (ZPA) abilitata per l'accesso Single Sign-On.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Zscaler Private Access (ZPA) supporta l'accesso SSO avviato da **SP**.

## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>Aggiunta di Zscaler Private Access (ZPA) dalla raccolta

Per configurare l'integrazione di Zscaler Private Access (ZPA) in Azure AD, è necessario aggiungere Zscaler Private Access (ZPA) dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Zscaler Private Access (ZPA)** nella casella di ricerca.
1. Selezionare **Zscaler Private Access (ZPA)** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

Configurare e testare l'accesso Single Sign-On di Azure AD con Zscaler Private Access (ZPA) usando un utente di test di nome **Britta Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Zscaler Private Access (ZPA).

Per configurare e testare l'accesso SSO di Azure AD con Zscaler Private Access (ZPA), completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** per consentire agli utenti di usare questa funzionalità.
2. **[Configurare Zscaler Private Access (ZPA)](#configure-zscaler-private-access-zpa)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Zscaler Private Access (ZPA)](#create-zscaler-private-access-zpa-test-user)** : per avere una controparte di Britta Simon in Zscaler Private Access (ZPA) collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-sso)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Zscaler Private Access (ZPA)** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella pagina **Configurazione SAML di base** immettere i valori per i campi seguenti:

    1. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    1. Nella casella di testo **Identificatore (ID entità)** digitare un URL: `https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE]
    > Poiché il valore **URL accesso** non è reale, È necessario aggiornare il valore con l'URL di accesso effettivo. Contattare il [team di supporto clienti di Zscaler Private Access (ZPA)](https://help.zscaler.com/zpa-submit-ticket) per ottenere il valore. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **XML metadati federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer in uso.

   ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura Zscaler Private Access (ZPA)** copiare gli URL appropriati in base alle esigenze.

   ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="configure-zscaler-private-access-zpa"></a>Configurare Zscaler Private Access (ZPA)

1. Per automatizzare la configurazione all'interno di Zscaler Private Access (ZPA), è necessario installare l'**estensione del browser per l'accesso sicuro My Apps** facendo clic su **Install the extension** (Installa l'estensione).

    ![Estensione My Apps](common/install-myappssecure-extension.png)

2. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura Zscaler Private Access (ZPA)** per passare direttamente all'applicazione Zscaler Private Access (ZPA). Nell'applicazione fornire le credenziali di amministratore per accedere a Zscaler Private Access (ZPA). L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 6.

    ![Eseguire la configurazione](common/setup-sso.png)

3. Se si vuole configurare manualmente Zscaler Private Access (ZPA), aprire una nuova finestra del Web browser, accedere al sito aziendale di Zscaler Private Access (ZPA) come amministratore e seguire questa procedura:

4. Nel lato sinistro del menu fare clic su **Administration** (Amministrazione) e passare alla sezione **AUTHENTICATION** (AUTENTICAZIONE), quindi fare clic su **IdP Configuration** (Configurazione IdP).

    ![Amministrazione dell'amministratore di Zscaler Private Access](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-administration.png)

5. Nell'angolo superiore destro, fare clic su **Aggiungi configurazione IdP**. 

    ![IdP dell'amministratore di Zscaler Private Access](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-idp.png)

6. Nella pagina **Aggiungi configurazione IdP**, eseguire la procedura seguente:
 
    ![Selezione dell'amministratore di Zscaler Private Access](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-select.png)

    a. Fare clic su **Seleziona file** per caricare il file di metadati scaricati da Azure AD nel campo **Caricamento file di metadati IdP**.

    b. Vengono letti i **metadati IdP** da Azure AD e vengono compilate tutte le informazioni dei campi, come mostrato di seguito.

    ![Configurazione dell'amministratore di Zscaler Private Access](./media/zscalerprivateaccess-tutorial/config.png)

    c. Selezionare il dominio dal campo **Domini**.
    
    d. Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione verrà creato un utente di test di nome Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `Britta Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `BrittaSimon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abiliterà Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Zscaler Private Access (ZPA).

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni, selezionare **Zscaler Private Access (ZPA)** .
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-zscaler-private-access-zpa-test-user"></a>Creare l'utente di test di Zscaler Private Access (ZPA)

In questa sezione viene creato un utente di nome Britta Simon in a Zscaler Private Access (ZPA). Consultare il [team di supporto di Zscaler Private Access (ZPA)](https://help.zscaler.com/zpa-submit-ticket) per aggiungere utenti sulla piattaforma a Zscaler Private Access (ZPA).

### <a name="test-sso"></a>Testare l'accesso SSO

Quando si seleziona il riquadro di Zscaler Private Access (ZPA) nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Zscaler Private Access (ZPA) per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)