---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con HRworks Single Sign-On | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e HRworks Single Sign-On.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2020
ms.author: jeedes
ms.openlocfilehash: 1a0fff21c85104498895c9de20de9961a77cffeb
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91820499"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hrworks-single-sign-on"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con HRworks Single Sign-On

Questa esercitazione descrive come integrare HRworks Single Sign-On con Azure Active Directory (Azure AD). Integrando HRworks Single Sign-On con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a HRworks Single Sign-On.
* Abilitare gli utenti per l'accesso automatico a HRworks Single Sign-On con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di HRworks Single Sign-On abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* HRworks Single Sign-On supporta l'accesso SSO avviato da **SP**

## <a name="adding-hrworks-single-sign-on-from-the-gallery"></a>Aggiunta di HRworks Single Sign-On dalla raccolta

Per configurare l'integrazione di HRworks Single Sign-On in Azure AD, è necessario aggiungere HRworks Single Sign-On dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **HRworks Single Sign-On** nella casella di ricerca.
1. Selezionare **HRworks Single Sign-On** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-hrworks-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per HRworks Single Sign-On

Configurare e testare l'accesso SSO di Azure AD con HRworks Single Sign-On usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in HRworks Single Sign-On.

Per configurare e testare l'accesso SSO di Azure AD con HRworks Single Sign-On, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di HRworks Single Sign-On](#configure-hrworks-single-sign-on-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di HRworks Single Sign-On](#create-hrworks-single-sign-on-test-user)** : per avere una controparte di B.Simon in HRworks Single Sign-On collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **HRworks Single Sign-On** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://login.hrworks.de/?companyId=<companyId>&directssologin=true`

    > [!NOTE]
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere il valore contattare il [team di supporto clienti di HRworks Single Sign-On](mailto:nadja.sommerfeld@hrworks.de). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura HRworks Single Sign-On** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a HRworks Single Sign-On.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **HRworks Single Sign-On**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-hrworks-single-sign-on-sso"></a>Configurare l'accesso Single Sign-On di HRworks Single Sign-On

1. Per automatizzare la configurazione all'interno di HRworks Single Sign-On, è necessario installare l'**estensione del browser per l'accesso sicuro ad App personali** facendo clic su **Installa l'estensione**.

    ![Estensione MyApps](common/install-myappssecure-extension.png)

1. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura HRworks Single Sign-On** per passare direttamente all'applicazione HRworks Single Sign-On. Nell'applicazione specificare le credenziali di amministratore per accedere a HRworks Single Sign-On. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 4.

    ![Eseguire la configurazione](common/setup-sso.png)

1. Se si vuole configurare manualmente HRworks Single Sign-On, aprire una nuova finestra del Web browser, accedere al sito aziendale di HRworks Single Sign-On come amministratore e seguire questa procedura:

1. Fare clic su **Administrator (Amministratore)**  > **Basics (Generale)**  > **Security (Sicurezza)**  > **Single Sign-on** sulla barra dei menu a sinistra e seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/hrworks-single-sign-on-tutorial/configure01.png)

    a. Selezionare la casella **Use Single Sign-On** (Usa Single Sign-On).

    b. Selezionare **XML Metadata** (Metadati XML) per **Meta data input method** (Metodo di input metadati).

    c. Selezionare **Individual NameID identifier** (Identificatore NameID singolo) per **Value for NameID** (Valore di NameID).

    d. Nel Blocco note aprire il file XML metadati scaricato dal portale di Azure, copiarne il contenuto e quindi incollarlo nella casella di testo **Metadati**.

    e. Fare clic su **Salva**.

### <a name="create-hrworks-single-sign-on-test-user"></a>Creare l'utente di test di HRworks Single Sign-On

Per consentire agli utenti di Azure di AD di accedere a HRworks Single Sign-On, è necessario eseguirne il provisioning in HRworks Single Sign-On. In HRworks Single Sign-On il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere a HRworks Single Sign-On come amministratore.

1. Fare clic su **Administrator (Amministratore)**  > **Persons (Persone)**  > **Persons (Persone)**  > **New person (Nuova persona)** sulla barra dei menu a sinistra.

     ![Screenshot che mostra la pagina di HRworks con le opzioni Persons e New person selezionate.](./media/hrworks-single-sign-on-tutorial/configure02.png)

1. Nella finestra popup fare clic su **Avanti**.

    ![Screenshot che mostra un elenco di paesi da cui scegliere per la persona.](./media/hrworks-single-sign-on-tutorial/configure03.png)

1. Nella finestra popup **Create new person with country for legal terms** (Crea una nuova persona con il paese per le note legali) compilare i rispettivi dettagli, come **First name** (Nome), **Last name** (Cognome), e fare clic su **Create** (Crea).

    ![Screenshot che mostra le caselle di testo in cui immettere nome e cognome della persona.](./media/hrworks-single-sign-on-tutorial/configure04.png)

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di HRworks Single Sign-On nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione HRworks Single Sign-On per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare HRworks Single Sign-On con Azure AD](https://aad.portal.azure.com/)