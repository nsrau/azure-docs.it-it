---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Coda | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Coda.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/23/2020
ms.author: jeedes
ms.openlocfilehash: 718afec8b36a27bfd36e2a018b39f480144bf822
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88545353"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-coda"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Coda

Questa esercitazione descrive come integrare Coda con Azure Active Directory (Azure AD). Integrando Coda con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Coda.
* Abilitare gli utenti per l'accesso automatico a Coda con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione (Enterprise) di Coda abilitata per l'accesso Single Sign-On (SSO) con integrazione GDrive disabilitata. Contattare il [team di supporto di Coda](mailto:support@coda.io) per disabilitare l'integrazione GDrive per l'organizzazione se è attualmente abilitata.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Coda supporta l'accesso SSO avviato da **IDP**

* Coda supporta il provisioning utenti **JIT**

* Dopo aver configurato Coda, è possibile applicare i controlli sessione che consentono di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. I controlli sessione costituiscono un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-coda-from-the-gallery"></a>Aggiunta di Coda dalla raccolta

Per configurare l'integrazione di Coda in Azure AD, è necessario aggiungere Coda dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Coda** nella casella di ricerca.
1. Selezionare **Coda** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-coda"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Coda

Configurare e testare l'accesso SSO di Azure AD con Coda usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Coda.

Per configurare e testare l'accesso SSO di Azure AD con Coda, completare le procedure di base seguenti:

1. **[Avviare la configurazione dell'accesso Single Sign-On per Coda](#begin-configuration-of-coda-sso)** : per avviare la configurazione dell'accesso Single Sign-On in Coda.
1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
   * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
   * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Coda](#configure-coda-sso)** : per completare la configurazione delle impostazioni di Single Sign-On in Coda.
   * **[Creare l'utente di test di Coda](#create-coda-test-user)** : per avere una controparte di B.Simon in Coda collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="begin-configuration-of-coda-sso"></a>Avviare la configurazione dell'accesso Single Sign-On per Coda

Seguire questa procedura in Coda per iniziare.

1. In Coda aprire il pannello **Organization settings** (Impostazioni organizzazione).

   ![Apertura di Organization settings](media/coda-tutorial/org-settings.png)

1. Assicurarsi che l'integrazione GDrive sia disattivata per l'organizzazione. Se è attualmente abilitata, contattare il [team di supporto di Coda](mailto:support@coda.io) per assistenza con la migrazione da GDrive.

   ![GDrive disabilitato](media/coda-tutorial/gdrive-off.png)

1. In **Authenticate with SSO (SAML)** (Esegui autenticazione con SSO (SAML)) selezionare l'opzione **Configure SAML** (Configura SAML).

   ![Impostazioni SAML](media/coda-tutorial/saml-settings-link.png)

1. Prendere nota dei valori relativi a **Entity ID** (ID entità) e **SAML Response URL** (URL della risposta SAML) perché saranno necessari nei passaggi successivi.

   ![ID entità e URL della risposta SAML da usare in Azure](media/coda-tutorial/azure-settings.png)

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Coda** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella pagina **Configura l'accesso Single Sign-On con SAML** immettere i valori per i campi seguenti:

   a. Nella casella di testo **Identificatore** immettere il valore di "Entity ID" (ID entità) annotato in precedenza. Tale valore deve essere conforme al formato `https://coda.io/samlId/<CUSTOMID>`

   b. Nella casella di testo **URL di risposta** immettere il valore di "SAML Response URL" (URL della risposta SAML) annotato in precedenza. Tale valore deve essere conforme al formato `https://coda.io/login/sso/saml/<CUSTOMID>/consume`

   > [!NOTE]
   > I valori effettivi sono diversi rispetto a quanto indicato sopra. È possibile individuarli nella console "Configure SAML" (Configura SAML) di Coda. è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

   ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Coda** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Coda.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Coda**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

   ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-coda-sso"></a>Configurare l'accesso Single Sign-On di Coda

Per completare la configurazione, immettere i valori di Azure Active Directory nel pannello **Configure Saml** (Configura SAML) di Coda.

1. In Coda aprire il pannello **Organization settings** (Impostazioni organizzazione).
1. In **Authenticate with SSO (SAML)** (Esegui autenticazione con SSO (SAML)) selezionare l'opzione **Configure SAML** (Configura SAML).
1. Impostare **SAML Provider** (Provider SAML) su **Azure Active Directory**.
1. In **Identity Provider Login URL** (URL di accesso del provider di identità) incollare il valore di **URL di accesso** della console di Azure.
1. In **Identity Provider Issuer** (Autorità di certificazione del provider di identità) incollare il valore di **Identificatore Azure AD** della console di Azure.
1. In **Identity Provider Public Certificate** (Certificato pubblico del provider di identità) selezionare l'opzione **Upload Certificate** (Carica certificato) e selezionare il file scaricato in precedenza.
1. Selezionare **Salva**.

La procedura di configurazione della connessione SSO SAML è stata completata.

### <a name="create-coda-test-user"></a>Creare l'utente di test di Coda

In questa sezione viene creato un utente di nome Britta Simon in Coda. Coda supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Coda, ne viene creato uno nuovo dopo l'autenticazione.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Coda nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Coda per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Coda con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Come proteggere Coda con visibilità e controlli avanzati](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
