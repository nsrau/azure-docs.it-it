---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Jamf Pro | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Jamf Pro.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24f0d067351ab2e24b103624c8534ac8d6f2e757
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305405"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jamf-pro"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Jamf Pro

Questa esercitazione descrive come integrare Jamf Pro con Azure Active Directory (Azure AD). Integrando Jamf Pro con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Jamf Pro.
* Abilitare gli utenti per l'accesso automatico a Jamf Pro con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Jamf Pro (SSO) abilitata per l'accesso Single Sign-On.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Jamf Pro supporta l'accesso SSO avviato da **SP e IdP**

## <a name="adding-jamf-pro-from-the-gallery"></a>Aggiunta di Jamf Pro dalla raccolta

Per configurare l'integrazione di Jamf Pro in Azure AD, è necessario aggiungere Jamf Pro dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Jamf Pro** nella casella di ricerca.
1. Selezionare **Jamf Pro** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-jamf-pro"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Jamf Pro

Configurare e testare l'accesso SSO di Azure AD con Jamf Pro usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Jamf Pro.

Per configurare e testare l'accesso SSO di Azure AD con Jamf Pro, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Jamf Pro](#configure-jamf-pro-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Jamf Pro](#create-jamf-pro-test-user)** : per avere una controparte di B.Simon in Jamf Pro collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Jamf Pro** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<subdomain>.jamfcloud.com/saml/metadata`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<subdomain>.jamfcloud.com/saml/SSO`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<subdomain>.jamfcloud.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Si otterrà il valore effettivo dell'identificatore dalla sezione **Single Sign-On** del portale di Jamf Pro, come illustrato più avanti nell'esercitazione. È possibile estrarre il valore effettivo di **subdomain** dal valore dell'identificatore e usare tali informazioni di **subdomain** in URL di accesso e URL di risposta. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Jamf Pro.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Jamf Pro**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-jamf-pro-sso"></a>Configurare l'accesso Single Sign-On di Jamf Pro

1. Per automatizzare la configurazione all'interno di Jamf Pro, è necessario installare l'**estensione del browser per l'accesso sicuro alle app personali** facendo clic su **Installare l'estensione**.

    ![Estensione per App personali](common/install-myappssecure-extension.png)

2. Dopo aver aggiunto l'estensione al browser, fare clic su **setup Jamf Pro** per passare direttamente all'applicazione Jamf Pro. Da qui, fornire le credenziali di amministratore per accedere a Jamf Pro. L'estensione del browser configurerà automaticamente l'applicazione per l'utente e automatizzerà i passaggi da 3 a 7.

    ![Eseguire la configurazione](common/setup-sso.png)

3. Se si vuole configurare manualmente Jamf Pro, aprire una nuova finestra del Web browser, accedere al sito aziendale di Jamf Pro come amministratore e seguire questa procedura:

4. Fare clic sull'**icona Settings** (Impostazioni) nell'angolo superiore destro della pagina.

    ![Configurazione di Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

5. Fare clic su **Single Sign On**.

    ![Configurazione di Jamf Pro](./media/jamfprosamlconnector-tutorial/configure2.png)

6. Nella pagina **Single Sign-On** eseguire la procedura seguente:

    ![Configurazione di Jamf Pro](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Selezionare **Enable Single Sign-On Authentication** (Abilita autenticazione Single Sign-On).

    b. Selezionare **Other** (Altro) come opzione nell'elenco a discesa **IDENTITY PROVIDER** (PROVIDER DI IDENTITÀ).

    c. Nella casella di testo **OTHER PROVIDER** (ALTRO PROVIDER) immettere **Azure AD**.

    d. Copiare il valore di **ENTITY ID** (ID entità) e incollarlo nella casella di testo **Identificatore (ID entità)** nella sezione **Configurazione SAML di base** del portale di Azure.

    > [!NOTE]
    > Nella parte `<SUBDOMAIN>` è necessario usare questo valore per completare l'URL di accesso e l'URL di accesso e l'URL di risposta nella sezione **Configurazione SAML di base** del portale di Azure.

    e. Selezionare **Metadata URL** (URL metadati) come opzione nell'elenco a discesa **IDENTITY PROVIDER METADATA SOURCE** (ORIGINE METADATI DEL PROVIDER DI IDENTITÀ), incollare il valore dell'**URL dei metadati di federazione dell'app** copiato dal portale di Azure.

7. Nella stessa pagina scorrere fino alla sezione **User Mapping** (Mapping utente) e seguire questa procedura: 

    ![Single Sign-On di Jamf Pro](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. Selezionare l'opzione **NameID** (ID nome) per **IDENTITY PROVIDER USER MAPPING** (MAPPING UTENTE DEL PROVIDER DI IDENTITÀ). Per impostazione predefinita, questa impostazione corrisponde a **NameID** (ID nome), ma è possibile definire un attributo personalizzato.

    b. Selezionare **Email** (Posta elettronica) per  **JAMF PRO USER MAPPING** (MAPPING UTENTE DI JAMF PRO). Jamf Pro esegue il mapping degli attributi SAML inviati dal provider di identità nei modi seguenti: per utenti e per gruppi. Se un utente prova ad accedere a Jamf Pro, per impostazione predefinita Jamf Pro ottiene informazioni sull'utente dal provider di identità e cerca una corrispondenza tra i propri account utente. Se l'account utente in ingresso non esiste in Jamf Pro, cerca una corrispondenza tra i nomi di gruppo.

    c. Incollare il valore `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` nella casella di testo  **IDENTITY PROVIDER GROUP ATTRIBUTE NAME** (NOME ATTRIBUTO GRUPPO DEL PROVIDER DI IDENTITÀ).

    d. Se si seleziona **Allow users to bypass the Single Sign-On authentication** (Consenti agli utenti di ignorare l'autenticazione Single Sign-On) gli utenti non vengono reindirizzati alla pagina di accesso del provider di identità per l'autenticazione, ma possono accedere direttamente a Jamf Pro. Se un utente prova ad accedere a Jamf Pro tramite il provider di identità, l'autenticazione SSO e l'autorizzazione vengono avviate dal provider di identità.

    e. Fare clic su **Save**.

### <a name="create-jamf-pro-test-user"></a>Creare l'utente di test di Jamf Pro

Per consentire agli utenti di Azure AD di accedere a Jamf Pro, è necessario effettuarne il provisioning in Jamf Pro. Nel caso di Jamf Pro, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di Jamf Pro come amministratore.

2. Fare clic sull'**icona Settings** (Impostazioni) nell'angolo superiore destro della pagina.

    ![Aggiungere un dipendente](./media/jamfprosamlconnector-tutorial/configure1.png)

3. Fare clic su **Jamf Pro User Accounts & Groups** (Gruppi e account utente di Jamf Pro).

    ![Aggiungere un dipendente](./media/jamfprosamlconnector-tutorial/user1.png)

4. Fare clic su **Nuovo**.

    ![Aggiungere un dipendente](./media/jamfprosamlconnector-tutorial/user2.png)

5. Fare clic su **Create Standard Account** (Crea account standard).

    ![Aggiungere un dipendente](./media/jamfprosamlconnector-tutorial/user3.png)

6. Nella finestra di dialogo **New Account** (Nuovo account) seguire questa procedura:

    ![Aggiungere un dipendente](./media/jamfprosamlconnector-tutorial/user4.png)

    a. Nella casella di testo **USERNAME** (NOME UTENTE) digitare il nome e cognome BrittaSimon.

    b. Selezionare le opzioni appropriate per l'organizzazione per **ACCESS LEVEL** (LIVELLO DI ACCESSO), **PRIVILEGE SET** (SET DI PRIVILEGI) e **ACCESS STATUS** (STATO ACCESSO).

    c. Nella casella di testo **FULL NAME** (NOME COMPLETO) digitare il nome e cognome di Britta Simon.

    d. Nella casella di testo **EMAIL ADDRESS** (INDIRIZZO DI POSTA ELETTRONICA) digitare l'indirizzo di posta elettronica dell'account di Britta Simon.

    e. Nella casella di testo **PASSWORD** digitare la password per l'utente.

    f. Nella casella di testo **VERIFY PASSWORD** (VERIFICA PASSWORD) digitare la password per l'utente.

    g. Fare clic su **Save**.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Jamf Pro nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Jamf Pro per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Jamf Pro con Azure AD](https://aad.portal.azure.com/)

