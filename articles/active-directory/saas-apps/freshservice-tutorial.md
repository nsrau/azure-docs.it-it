---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Freshservice | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Freshservice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3ba2f59799629873553fe261abebf59529ad94d
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2019
ms.locfileid: "73570601"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshservice"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Freshservice

Questa esercitazione descrive come integrare Freshservice con Azure Active Directory (Azure AD). Integrando Freshservice con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Freshservice.
* Abilitare gli utenti per l'accesso automatico a Freshservice con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Freshservice abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Freshservice supporta l'accesso SSO avviato da **SP**

## <a name="adding-freshservice-from-the-gallery"></a>Aggiunta di Freshservice dalla raccolta

Per configurare l'integrazione di Freshservice in Azure AD è necessario aggiungere Freshservice dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Freshservice** nella casella di ricerca.
1. Selezionare **Freshservice** nel riquadro dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-freshservice"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Freshservice

Configurare e testare l'accesso Single Sign-On di Azure AD con Freshservice usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Freshservice.

Per configurare e testare l'accesso SSO di Azure AD con Freshservice, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Freshservice](#configure-freshservice-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Freshservice](#create-freshservice-test-user)** : per avere una controparte di B.Simon in Freshservice collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Freshservice** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<democompany>.freshservice.com`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://<democompany>.freshservice.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori contattare il [team di supporto clienti di Freshservice](https://support.freshservice.com/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML**, trovare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Per il corretto funzionamento dell'accesso SSO, Freshservice richiede l'impronta digitale SHA-256. Per ottenere l'impronta digitale SHA-256, seguire questa procedura:

    ![FingerPrint](./media/freshservice-tutorial/ic790821.png "FingerPrint")

    1. Aprire il [collegamento](https://www.samltool.com/fingerprint.php) in un altro Web browser.

    1. Aprire il certificato scaricato (Base64) nel Blocco note e incollarne il contenuto nella casella di testo **X.509 cert** (Certificato X.509).

    1. Per l'algoritmo selezionare **sha256** nell'elenco a discesa.

    1. Fare clic su **CALCULATE FINGERPRINT** (CALCOLA IMPRONTA DIGITALE).

    1. Fare clic sull'icona di copia per copiare il valore generato di **FingerPrint** (Impronta digitale) e salvarlo nel computer.

1. Nella sezione **Configura Freshservice** del **portale di Azure** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Freshservice.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Freshservice**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-freshservice-sso"></a>Configurare l'accesso Single Sign-On per Freshservice

1. Per automatizzare la configurazione all'interno di Freshservice, è necessario installare l'**estensione del browser per l'accesso sicuro alle app personali** facendo clic su **Installa estensione**.

    ![Estensione per App personali](common/install-myappssecure-extension.png)

2. Dopo aver aggiunto l'estensione al browser, fare clic su **Setup Freshservice** (Configura Freshservice) per passare direttamente all'applicazione Freshservice. Da qui, fornire le credenziali di amministratore per accedere a Freshservice. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 6.

    ![Eseguire la configurazione](common/setup-sso.png)

3. Se si vuole configurare manualmente Freshservice, aprire una nuova finestra del Web browser, accedere al sito aziendale di Freshservice come amministratore e seguire questa procedura:

4. Nel menu in alto fare clic su **Admin**.

    ![Amministratore](./media/freshservice-tutorial/ic790814.png "Amministratore")

5. Nel **portale dei clienti** fare clic su **Security** (Sicurezza).

    ![Sicurezza](./media/freshservice-tutorial/ic790815.png "Sicurezza")

6. Nella sezione **Security** seguire questa procedura:

    ![Single Sign-On](./media/freshservice-tutorial/ic790816.png "Single Sign-On")

    a. Passare a **Single Sign-On**.

    b. Selezionare **SAML SSO**.

    c. Nella casella di testo **SAML Login URL** (URL di accesso SAML) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    d. Nella casella di testo **Logout URL** (URL disconnessione) incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure.

    e. Nella casella di testo **Security Certificate Fingerprint** (Impronta digitale certificato di protezione) incollare il valore di **FingerPrint** (Impronta digitale) generato in precedenza.

    f. Fare clic su **Save** (Salva).

### <a name="create-freshservice-test-user"></a>Creare l'utente di test di Freshservice

Per consentire agli utenti di Azure AD di accedere a Freshservice, è necessario eseguire il provisioning degli utenti in Freshservice. Nel caso di FreshService, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di **FreshService** come amministratore.

2. Nel menu in alto fare clic su **Admin**.

    ![Amministratore](./media/freshservice-tutorial/ic790814.png "Amministratore")

3. Nella sezione **User Management** (Gestione utenti) fare clic su **Requesters** (Richiedenti).

    ![Requesters](./media/freshservice-tutorial/ic790818.png "Requesters")

4. Fare clic su **Nuovo Requester**.

    ![New Requesters](./media/freshservice-tutorial/ic790819.png "New Requesters")

5. Nella sezione **New Requester** seguire questa procedura:

    ![New Requester](./media/freshservice-tutorial/ic790820.png "New Requester")  

    a. Nelle caselle **First Name** e **Email** immettere il nome e l'indirizzo di posta elettronica di un account Azure Active Directory valido di cui si vuole eseguire il provisioning.

    b. Fare clic su **Save**.

    > [!NOTE]
    > Il titolare dell'account Azure Active Directory riceve un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.
    >  

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da FreshService per eseguire il provisioning degli account utente di AAD.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Freshservice nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Freshservice per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Freshservice con Azure AD](https://aad.portal.azure.com/)