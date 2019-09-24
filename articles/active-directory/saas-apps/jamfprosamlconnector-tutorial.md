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
ms.openlocfilehash: 91c8a2fb278515306848f46206db67b7f37ea2ac
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034254"
---
# <a name="tutorial-azure-active-directory-sso-integration-with-jamf-pro"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Jamf Pro

Questa esercitazione descrive come integrare Jamf Pro con Azure Active Directory (Azure AD). Integrando Jamf Pro con Azure AD, è possibile:

* Usare Azure AD per controllare chi può accedere a Jamf Pro.
* Abilitare gli utenti per l'accesso automatico a Jamf Pro con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Jamf Pro abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Jamf Pro supporta l'accesso SSO avviato da **SP** e **IdP**.

## <a name="add-jamf-pro-from-the-gallery"></a>Aggiungere Jamf Pro dalla raccolta

Per configurare l'integrazione di Jamf Pro in Azure AD, è necessario aggiungere Jamf Pro dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con l'account Microsoft personale.
1. Nel riquadro sinistro selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** immettere *Jamf Pro* nella casella di ricerca.
1. Selezionare **Jamf Pro** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-sso-in-azure-ad-for-jamf-pro"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Jamf Pro

Configurare e testare l'accesso Single Sign-On di Azure AD con Jamf Pro usando un utente di test di nome B.Simon. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Jamf Pro.

In questa sezione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD con Jamf Pro.

1. [Configurare l'accesso Single Sign-On di Azure AD](#configure-sso-in-azure-ad): per consentire agli utenti di usare questa funzionalità.
    1. [Creare un utente di test di Azure AD](#create-an-azure-ad-test-user): per testare l'accesso Single Sign-On di Azure AD con l'account di B.Simon.
    1. [Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user): per consentire a B.Simon di usare l'accesso Single Sign-On di Azure AD.
1. [Configurare l'accesso Single Sign-On in Jamf Pro](#configure-sso-in-jamf-pro): per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. [Creare l'utente di test di Jamf Pro](#create-a-jamf-pro-test-user): per avere una controparte di B.Simon in Jamf Pro collegata alla rappresentazione dell'utente in Azure AD.
1. [Testare la configurazione dell'accesso Single Sign-On](#test-the-sso-configuration): per verificare se la configurazione funziona.

## <a name="configure-sso-in-azure-ad"></a>Configurare l'accesso Single Sign-On in Azure AD

In questa sezione si abilita l'accesso Single Sign-On di Azure AD nel portale di Azure.

1. Nella pagina di integrazione dell'applicazione **Jamf Pro** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** selezionare sull'icona della penna relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la pagina Configurazione SAML di base.](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità **avviata da IDP**:

    a. Nella casella di testo **Identificatore** immettere un URL nel formato seguente: `https://<subdomain>.jamfcloud.com/saml/metadata`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<subdomain>.jamfcloud.com/saml/SSO`

1. Selezionare **Imposta URL aggiuntivi**. Se si vuole configurare l'applicazione in modalità **avviata da SP** nella casella di testo **URL di accesso** immettere un URL nel formato seguente: `https://<subdomain>.jamfcloud.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Si otterrà il valore effettivo dell'identificatore dalla sezione **Single Sign-On** del portale di Jamf Pro, come illustrato più avanti nell'esercitazione. È possibile estrarre il valore effettivo di subdomain dal valore dell'identificatore e usare tali informazioni di subdomain nell'URL di accesso e nell'URL di risposta. È anche possibile fare riferimento ai formati illustrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** selezionare il pulsante **Copia** per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer in uso.

    ![Collegamento al download del certificato di firma SAML](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione viene creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere [nome]@[dominioaziendale].[estensione]. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Selezionare **Create** (Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si concede a B.Simon l'accesso a Jamf Pro.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Jamf Pro**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Selezionare Utenti e gruppi](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Selezionare il pulsante Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi selezionare il pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente. Fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.

## <a name="configure-sso-in-jamf-pro"></a>Configurare l'accesso Single Sign-On in Jamf Pro 

1. Per automatizzare la configurazione all'interno di Jamf Pro, installare l'**estensione del browser My Apps Secure Sign-in** selezionando **Installa estensione**.

    ![Estensione del browser My Apps Secure Sign-in](common/install-myappssecure-extension.png)

2. Dopo aver aggiunto l'estensione al browser, selezionare **Configura Jamf Pro**. Quando si apre l'applicazione Jamf Pro, specificare le credenziali di amministratore per eseguire l'accesso. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 7.

    ![Pagina di configurazione in Jamf Pro](common/setup-sso.png)

3. Per configurare manualmente Jamf Pro, aprire un'altra finestra del Web browser e accedere al sito aziendale di Jamf Pro come amministratore. Seguire quindi questa procedura.

4. Selezionare l'icona **Settings** (Impostazioni) nell'angolo in alto a destra nella pagina.

    ![Selezionare l'icona Settings (Impostazioni) in Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

5. Selezionare **Single Sign-On**.

    ![Selezionare Single Sign-On in Jamf Pro](./media/jamfprosamlconnector-tutorial/configure2.png)

6. Nella pagina **Single Sign-On** seguire questa procedura.

    ![Pagina Single Sign-On in Jamf Pro](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Selezionare la casella di testo **Enable Single Sign-On Authentication** (Abilita autenticazione Single Sign-On).

    b. Selezionare **Other** (Altro) come opzione nell'elenco a discesa **IDENTITY PROVIDER** (PROVIDER DI IDENTITÀ).

    c. Nel campo **OTHER PROVIDER** (ALTRO PROVIDER) immettere **Azure AD**.

    d. Copiare il valore di **ENTITY ID** (ID ENTITÀ) e incollarlo nel campo **Identificatore (ID entità)** nella sezione **Configurazione SAML di base** del portale di Azure.

    > [!NOTE]
    > Usare questo valore nel campo `<SUBDOMAIN>` per completare l'URL di accesso e l'URL di risposta nella sezione **Configurazione SAML di base** del portale di Azure.

    e. Selezionare **Metadata URL** (URL metadati) nel menu a discesa **IDENTITY PROVIDER METADATA SOURCE** (ORIGINE METADATI DEL PROVIDER DI IDENTITÀ). Nel campo visualizzato incollare il valore dell'**URL dei metadati di federazione dell'app** copiato dal portale di Azure.

7. Nella stessa pagina scorrere fino alla sezione **User Mapping** (Mapping utente). Seguire quindi questa procedura.   

    ![Sezione User Mapping (Mapping utente) della pagina Single Sign-On in Jamf Pro.](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. Selezionare l'opzione **NameID** (ID nome) per **IDENTITY PROVIDER USER MAPPING** (MAPPING UTENTE DEL PROVIDER DI IDENTITÀ). Per impostazione predefinita, questa opzione è impostata su **NameID** (ID nome), ma è possibile definire un attributo personalizzato.

    b. Selezionare **Email** (Posta elettronica) per  **JAMF PRO USER MAPPING** (MAPPING UTENTE DI JAMF PRO). Jamf Pro esegue il mapping degli attributi SAML inviati dall'IdP prima per utenti e poi per gruppi. Se un utente prova ad accedere a Jamf Pro, l'applicazione recupera le informazioni sull'utente dal provider di identità e cerca una corrispondenza tra tutti gli account utente. Se l'account utente in ingresso non viene trovato, Jamf Pro cerca una corrispondenza in base al nome del gruppo.

    c. Incollare il valore `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` nel campo  **IDENTITY PROVIDER GROUP ATTRIBUTE NAME** (NOME ATTRIBUTO GRUPPO DEL PROVIDER DI IDENTITÀ).

    d. Selezionare **Allow users to bypass the Single Sign-On authentication** (Consenti agli utenti di ignorare l'autenticazione Single Sign-On). In questo modo, gli utenti non vengono reindirizzati alla pagina di accesso del provider di identità per l'autenticazione e possono accedere direttamente a Jamf Pro. Se un utente prova ad accedere a Jamf Pro tramite il provider di identità, l'autenticazione SSO e l'autorizzazione vengono avviate dal provider di identità.

    e. Selezionare **Salva**.

### <a name="create-a-jamf-pro-test-user"></a>Creare un utente di test di Jamf Pro

Per consentire agli utenti di Azure AD di accedere a Jamf Pro, è necessario effettuarne il provisioning in Jamf Pro. Nel caso di Jamf Pro il provisioning è un'attività manuale.

Per effettuare il provisioning di un account utente, seguire questa procedura:

1. Accedere al sito aziendale di Jamf Pro come amministratore.

2. Selezionare l'icona **Impostazioni** nell'angolo superiore destro della pagina.

    ![Icona Settings (Impostazioni) in Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

3. Selezionare **Jamf Pro User Accounts & Groups** (Gruppi e account utente di Jamf Pro).

    ![Icona Jamf Pro User Accounts & Groups (Gruppi e account utente di Jamf Pro) nelle impostazioni di Jamf Pro](./media/jamfprosamlconnector-tutorial/user1.png)

4. Selezionare **Nuovo**.

    ![Pagina delle impostazioni del sistema per Jamf Pro User Accounts & Groups (Gruppi e account utente di Jamf Pro)](./media/jamfprosamlconnector-tutorial/user2.png)

5. Fare clic su **Create Standard Account** (Crea account standard).

    ![Opzione Create Standard Account (Crea account standard) nella pagina Jamf Pro User Accounts & Groups (Gruppi e account utente di Jamf Pro)](./media/jamfprosamlconnector-tutorial/user3.png)

6. Nella finestra di dialogo **New Account** (Nuovo account) seguire questa procedura.

    ![Opzioni di configurazione del nuovo account nelle impostazioni di sistema di Jamf Pro](./media/jamfprosamlconnector-tutorial/user4.png)

    a. Nel campo **USERNAME** (NOME UTENTE) immettere `Britta Simon`, il nome completo dell'utente di test.

    b. Selezionare le opzioni per **ACCESS LEVEL** (LIVELLO DI ACCESSO), **PRIVILEGE SET** (SET DI PRIVILEGI) e **ACCESS STATUS** (STATO ACCESSO) conformemente all'organizzazione.

    c. Nel campo **FULL NAME** (NOME E COGNOME) immettere `Britta Simon`.

    d. Nel campo **EMAIL ADDRESS** (INDIRIZZO DI POSTA ELETTRONICA) immettere l'indirizzo di posta elettronica dell'account di Britta Simon.

    e. Nel campo **PASSWORD** immettere la password dell'utente.

    f. Nel campo **VERIFY PASSWORD** (VERIFICA PASSWORD) digitare nuovamente la password dell'utente.

    g. Selezionare **Salva**.

## <a name="test-the-sso-configuration"></a>Testare la configurazione dell'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro di Jamf Pro nel pannello di accesso, si dovrebbe accedere automaticamente all'account di Jamf Pro per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Esercitazioni per l'integrazione di applicazioni SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Jamf Pro con Azure AD](https://aad.portal.azure.com/)

