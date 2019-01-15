---
title: 'Esercitazione: Integrazione di Azure Active Directory con Keeper Password Manager & Digital Vault | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Keeper Password Manager & Digital Vault.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e1a98f6a-2dae-4734-bdbf-4fba742a61d2
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.openlocfilehash: 4e96e2387797ed11768de9cd0b75261dfb89c674
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/07/2019
ms.locfileid: "54065512"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>Esercitazione: Integrazione di Azure Active Directory con Keeper Password Manager & Digital Vault

Questa esercitazione descrive come integrare Keeper Password Manager & Digital Vault con Azure Active Directory (Azure AD).
L'integrazione di Keeper Password Manager & Digital Vault con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Keeper Password Manager & Digital Vault.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Keeper Password Manager & Digital Vault con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Keeper Password Manager & Digital Vault sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Keeper Password Manager & Digital Vault abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Keeper Password Manager & Digital Vault supporta l'accesso SSO avviato da **SP**

* Keeper Password Manager & Digital Vault supporta il provisioning utenti **JIT**

## <a name="adding-keeper-password-manager--digital-vault-from-the-gallery"></a>Aggiunta di Keeper Password Manager & Digital Vault dalla raccolta

Per configurare l'integrazione di Keeper Password Manager & Digital Vault in Azure AD è necessario aggiungere Keeper Password Manager & Digital Vault dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Keeper Password Manager & Digital Vault dalla raccolta seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Keeper Password Manager & Digital Vault**, selezionare **Keeper Password Manager & Digital Vault** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Keeper Password Manager & Digital Vault nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Keeper Password Manager & Digital Vault usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Keeper Password Manager & Digital Vault.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Keeper Password Manager & Digital Vault è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Keeper Password Manager & Digital Vault](#configure-keeper-password-manager-&-digital-vault-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Keeper Password Manager & Digital Vault](#create-keeper-password-manager--digital-vault-test-user)**: per avere una controparte di Britta Simon in Keeper Password Manager & Digital Vault collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Keeper Password Manager & Digital Vault, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Keeper Password Manager & Digital Vault** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Keeper Password Manager & Digital Vault](common/sp-identifier-reply.png)

    a. Nella casella di testo **URL di accesso** digitare un URL usando il modello seguente: `https://{SSO CONNECT SERVER}/sso-connect/saml/login`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL usando il modello seguente: `https://{SSO CONNECT SERVER}/sso-connect`

    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://{SSO CONNECT SERVER}/sso-connect/saml/sso`

    > [!NOTE]
    > Questi valori non sono reali. Aggiornarli con l'URL di accesso, l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori contattare il [team di supporto clienti di Keeper Password Manager & Digital Vault](https://keepersecurity.com/contact.html). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML dei metadati della federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura Keeper Password Manager & Digital Vault** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL disconnessione

### <a name="configure-keeper-password-manager--digital-vault-single-sign-on"></a>Configurare l'accesso Single Sign-On per Keeper Password Manager & Digital Vault

Per configurare l'accesso Single Sign-On sul lato **Configurazione Keeper Password Manager & Digital Vault**, seguire le istruzioni specificate nella [guida per il supporto di Keeper](https://keepersecurity.com/assets/pdf/KeeperSSOConnect_v11.pdf).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD 

Questa sezione descrive come creare un utente di test denominato Britta Simon nel portale di Azure.

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

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Keeper Password Manager & Digital Vault.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Keeper Password Manager & Digital Vault**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Keeper Password Manager & Digital Vault**.

    ![Collegamento di Keeper Password Manager & Digital Vault nell'elenco Applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-keeper-password-manager--digital-vault-test-user"></a>Creare l'utente di test di Keeper Password Manager & Digital Vault

Per consentire agli utenti di Azure AD di accedere a Keeper Password Manager & Digital Vault, è necessario eseguire il provisioning degli utenti in Keeper Password Manager & Digital Vault. L'applicazione supporta il provisioning utenti JIT e dopo l'autenticazione gli utenti verranno automaticamente creati nell'applicazione. Per configurare manualmente gli utenti, contattare il [supporto di Keeper](https://keepersecurity.com/contact.html).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Keeper Password Manager & Digital Vault nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Keeper Password Manager & Digital Vault per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

