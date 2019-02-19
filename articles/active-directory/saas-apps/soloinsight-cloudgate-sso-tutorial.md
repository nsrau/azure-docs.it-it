---
title: 'Esercitazione: Integrazione di Azure Active Directory con Soloinsight-CloudGate SSO | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Soloinsight-CloudGate SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9263c241-85a4-4724-afac-0351d6275958
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5839d7212958cffe45fae19f1e502ecf04e744a3
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56162052"
---
# <a name="tutorial-azure-active-directory-integration-with-soloinsight-cloudgate-sso"></a>Esercitazione: Integrazione di Azure Active Directory con Soloinsight-CloudGate SSO

Questa esercitazione descrive come integrare Soloinsight-CloudGate SSO con Azure Active Directory (Azure AD).
L'integrazione di Soloinsight-CloudGate SSO con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Soloinsight-CloudGate SSO.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Soloinsight-CloudGate SSO con gli account Azure AD.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Soloinsight-CloudGate SSO, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Soloinsight-CloudGate SSO abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Soloinsight-CloudGate SSO supporta l'accesso SSO avviato da **SP**

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>Aggiunta di Soloinsight-CloudGate SSO dalla raccolta

Per configurare l'integrazione di Soloinsight-CloudGate SSO in Azure AD, è necessario aggiungere Soloinsight-CloudGate SSO dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Soloinsight-CloudGate SSO dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Soloinsight-CloudGate SSO**, selezionare **Soloinsight-CloudGate SSO** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Soloinsight-CloudGate SSO nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Soloinsight-CloudGate SSO usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Soloinsight-CloudGate SSO.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Soloinsight-CloudGate SSO, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per Soloinsight-CloudGate SSO](#configure-soloinsight-cloudgate-sso-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Soloinsight-CloudGate SSO](#create-soloinsight-cloudgate-sso-test-user)**: per avere una controparte di Britta Simon in Soloinsight-CloudGate SSO collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Soloinsight-CloudGate SSO, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Soloinsight-CloudGate SSO** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare l'accesso Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Soloinsight-CloudGate SSO](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.sigateway.com/login`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.sigateway.com/process/sso`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi, come spiegato più avanti nella sezione **Configurare l'accesso Single Sign-On per Soloinsight-CloudGate SSO** dell'esercitazione.

4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Soloinsight-CloudGate SSO** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-soloinsight-cloudgate-sso-single-sign-on"></a>Configurare l'accesso Single Sign-On per Soloinsight-CloudGate SSO

1. Per ottenere i valori da incollare nel portale di Azure durante la configurazione SAML di base, accedere al portale Web di CloudGate usando le credenziali e quindi accedere alle impostazioni di SSO disponibili in **Home > Administration > System settings > General** (Home > Amministrazione > Impostazioni di sistema > Generale).

    ![Impostazioni di SSO per CloudGate](./media/soloinsight-cloudgate-sso-tutorial/sso-main-settings.png)

2. **URL consumer SAML**

    * Copiare i collegamenti disponibili nei campi **Saml Consumer URL** (URL consumer SAML) e **Redirect URL** (URL di reindirizzamento) e incollarli rispettivamente nei campi **Identificatore (ID entità)** e **URL di risposta** nella sezione **Configurazione SAML di base** del portale di Azure.

        ![Identificatore SAML](./media/soloinsight-cloudgate-sso-tutorial/saml-identifier.png)

3. **Certificato di firma SAML**

    * Passare all'origine del file Certificato (Base64) scaricato dagli elenchi del certificato di firma SAML del portale di Azure e fare clic su di esso con il pulsante destro del mouse. Scegliere l'opzione **Modifica con Notepad++** nell'elenco. 

        ![Certificato SAML](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * Copiare il contenuto del file Certificato (Base64) visualizzato in Notepad++.

        ![Copia del certificato](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * Incollare il contenuto nel campo **Certificate** (Certificato) delle impostazioni SSO del portale Web di CloudGate e fare clic sul pulsante Save (Salva).

        ![Portale del certificato](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

4. **Gruppo predefinito**

    * Selezionare **Business Admin** (Amministratori aziendali) nell'elenco a discesa dell'opzione **Default Group** (Gruppo predefinito) del portale Web di CloudGate.

        ![Gruppo predefinito](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

5. **Identificatore AD e URL di accesso**

    * Il valore di **URL di accesso** copiato da **Configura Soloinsight-CloudGate SSO** del portale di Azure deve essere immesso nella sezione delle impostazioni SSO del portale Web di CloudGate. 

    * Incollare il valore del collegamento **URL di accesso** del portale di Azure nel campo **AD Login URL** (URL di accesso AD) del portale Web di CloudGate.
     
    * Incollare il valore del collegamento **Identificatore Azure AD** del portale di Azure nel campo **AD Identifier** (Identificatore AD) del portale Web di CloudGate.

        ![Account di accesso AD](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

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

    d. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Soloinsight-CloudGate SSO.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Soloinsight-CloudGate SSO**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Soloinsight-CloudGate SSO**.

    ![Collegamento di Soloinsight-CloudGate SSO nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Creare l'utente di test di Soloinsight-CloudGate SSO

Per creare un utente di test, selezionare **Employees** (Dipendenti) nel menu principale del portale Web di CloudGate e compilare il modulo Add New employee (Aggiungi nuovo dipendente). Il livello di autorità da assegnare all'utente di test è **Business Admin** (Amministratore aziendale). Compilare tutti i campi obbligatori e quindi fare clic su **Create** (Crea).

![Test per dipendente](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Soloinsight-CloudGate SSO nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Soloinsight-CloudGate SSO per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

