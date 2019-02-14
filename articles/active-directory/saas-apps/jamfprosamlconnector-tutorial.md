---
title: 'Esercitazione: Integrazione di Azure Active Directory con Jamf Pro | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Jamf Pro.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f1381fa16880dc06a1672626d2279456c2c33e1
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56186838"
---
# <a name="tutorial-azure-active-directory-integration-with-jamf-pro"></a>Esercitazione: Integrazione di Azure Active Directory con Jamf Pro

Questa esercitazione descrive come integrare Jamf Pro con Azure Active Directory (Azure AD).
L'integrazione di Jamf Pro con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Jamf Pro.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Jamf Pro con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Jamf Pro, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Jamf Pro abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Jamf Pro supporta l'accesso SSO avviato da **SP e IdP**

## <a name="adding-jamf-pro-from-the-gallery"></a>Aggiunta di Jamf Pro dalla raccolta

Per configurare l'integrazione di Jamf Pro in Azure AD, è necessario aggiungere Jamf Pro dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Jamf Pro dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Jamf Pro**, selezionare **Jamf Pro** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Jamf Pro nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Jamf Pro usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Jamf Pro.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Jamf Pro, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Jamf Pro](#configure-jamf-pro-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Jamf Pro](#create-jamf-pro-test-user)**: per avere una controparte di Britta Simon in Jamf Pro collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Jamf Pro, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Jamf Pro** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IdP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Jamf Pro](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<subdomain>.jamfcloud.com/saml/metadata`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<subdomain>.jamfcloud.com/saml/SSO`

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<subdomain>.jamfcloud.com`

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Jamf Pro](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Si otterrà il valore effettivo dell'identificatore dalla sezione **Single Sign-On** del portale di Jamf Pro, come illustrato più avanti nell'esercitazione. È possibile estrarre il valore effettivo di **subdomain** dal valore dell'identificatore e usare tali informazioni di **subdomain** in URL di accesso e URL di risposta. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)

### <a name="configure-jamf-pro-single-sign-on"></a>Configurare l'accesso Single Sign-On di Jamf Pro

1. Per automatizzare la configurazione all'interno di Jamf Pro, è necessario installare l'**estensione del browser per l'accesso sicuro alle app personali** facendo clic su **Installare l'estensione**.

    ![image](./media/jamfprosamlconnector-tutorial/install_extension.png)

2. Dopo aver aggiunto l'estensione al browser, fare clic su **setup Jamf Pro** per passare direttamente all'applicazione Jamf Pro. Da qui, fornire le credenziali di amministratore per accedere a Jamf Pro. L'estensione del browser configurerà automaticamente l'applicazione per l'utente e automatizzerà i passaggi da 3 a 7.

    ![image](./media/jamfprosamlconnector-tutorial/d1_saml.png)

3. Se si vuole configurare manualmente Jamf Pro, aprire una nuova finestra del Web browser, accedere al sito aziendale di Jamf Pro come amministratore e seguire questa procedura:

4. Fare clic sull'**icona Settings** (Impostazioni) nell'angolo superiore destro della pagina.

    ![Configurazione di Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

5. Fare clic su **Single Sign On**.

    ![Configurazione di Jamf Pro](./media/jamfprosamlconnector-tutorial/configure2.png)

6. Nella pagina **Single Sign-On** eseguire la procedura seguente:

    ![Single Sign-On di Jamf Pro](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. Selezionare **Jamf Pro Server** per abilitare l'accesso Single Sign-On.

    b. Se si seleziona **Allow bypass for all users** (Consenti bypass per tutti gli utenti) gli utenti non vengono reindirizzati alla pagina di accesso del provider di identità per l'autenticazione, ma possono accedere a Jamf Pro direttamente. Se un utente prova ad accedere a Jamf Pro tramite il provider di identità, l'autenticazione SSO e l'autorizzazione vengono avviate dal provider di identità.

    c. Selezionare l'opzione **NameID** (ID nome) per **USER MAPPING: SAML** (MAPPING UTENTI: SAML). Per impostazione predefinita, questa impostazione corrisponde a **NameID** (ID nome), ma è possibile definire un attributo personalizzato.

    d. Selezionare **Email** (Posta elettronica) per **USER MAPPING: JAMF PRO** (MAPPING UTENTI: JAMF PRO). Jamf Pro esegue il mapping degli attributi SAML inviati dal provider di identità nei modi seguenti: per utenti e per gruppi. Se un utente tenta di accedere a Jamf Pro, per impostazione predefinita Jamf Pro ottiene informazioni sull'utente dal provider di identità e cerca una corrispondenza tra i propri account utente. Se l'account utente in ingresso non esiste in Jamf Pro, cerca una corrispondenza tra i nomi di gruppo.

    e. Incollare il valore `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` nella casella di testo **GROUP ATTRIBUTE NAME** (Nome attributo gruppo).

7. Nella stessa pagina scorrere fino a **IDENTITY PROVIDER** (PROVIDER DI IDENTITÀ) nella sezione **Single Sign-On** e seguire questa procedura:

    ![Configurazione di Jamf Pro](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Selezionare l'opzione **Other** (Altro) nell'elenco a discesa **IDENTITY PROVIDER** (PROVIDER DI IDENTITÀ).

    b. Nella casella di testo **OTHER PROVIDER** (ALTRO PROVIDER) immettere **Azure AD**.

    c. Selezionare l'opzione **Metadata URL** (URL dei metadati) nell'elenco a discesa **IDENTITY PROVIDER METADATA SOURCE** (ORIGINE METADATI DEL PROVIDER DI IDENTITÀ) e nella casella di testo seguente incollare il valore di **URL dei metadati di federazione dell'app** copiato dal portale di Azure.

    d. Copiare il valore di **Entity ID** (ID entità) e incollarlo nella casella di testo **Identificatore (ID entità)** nella sezione **URL e dominio Jamf Pro** del portale di Azure.

    > [!NOTE]
    > Il valore disattivato visualizzato corrisponde alla parte relativa al sottodominio. Usare questo valore per completare l'URL di accesso e l'URL di risposta nella sezione **URL e dominio Jamf Pro** del portale di Azure.

    e. Fare clic su **Save**.

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

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a Jamf Pro.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Jamf Pro**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Jamf Pro**.

    ![Collegamento di Jamf Pro nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-jamf-pro-test-user"></a>Creare un utente di test di Jamf Pro

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

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Jamf Pro nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Jamf Pro per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
