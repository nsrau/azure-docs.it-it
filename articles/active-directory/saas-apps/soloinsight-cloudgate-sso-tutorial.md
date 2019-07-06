---
title: 'Esercitazione: Integrazione di Azure Active Directory con Soloinsight-CloudGate SSO | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Soloinsight-CloudGate SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9263c241-85a4-4724-afac-0351d6275958
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2dc6dcbcdee8df93f34cf4d68b5e08453554bc8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67090006"
---
# <a name="tutorial-integrate-soloinsight-cloudgate-sso-with-azure-active-directory"></a>Esercitazione: Integrare Soloinsight-CloudGate SSO con Azure Active Directory

In questa esercitazione si apprenderà come integrare Soloinsight-CloudGate SSO con Azure Active Directory (Azure AD). Integrando Soloinsight-CloudGate SSO con Azure Active Directory è possibile:

* Controllare in Azure AD chi può accedere a Soloinsight-CloudGate SSO.
* Abilitare gli utenti per l'accesso automatico a Soloinsight-CloudGate SSO con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere una versione di valutazione gratuita per un mese [qui](https://azure.microsoft.com/pricing/free-trial/).
* Una sottoscrizione di Soloinsight-CloudGate SSO abilitata per l'accesso Single Sign-On.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Soloinsight-CloudGate SSO supporta l'accesso SSO avviato da **provider di servizi**.

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>Aggiunta di Soloinsight-CloudGate SSO dalla raccolta

Per configurare l'integrazione di Soloinsight-CloudGate SSO in Azure AD, è necessario aggiungere Soloinsight-CloudGate SSO dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Soloinsight-CloudGate SSO** nella casella di ricerca.
1. Selezionare **Soloinsight-CloudGate SSO** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

Configurare e testare l'accesso Single Sign-On di Azure AD con Soloinsight-CloudGate SSO usando un utente di test di nome **Britta Simon**. Per il corretto funzionamento del Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Soloinsight-CloudGate SSO.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Soloinsight-CloudGate SSO, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** per consentire agli utenti di usare questa funzionalità.
2. **[Configurare Soloinsight-CloudGate SSO](#configure-soloinsight-cloudgate-sso)** per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Soloinsight-CloudGate SSO](#create-soloinsight-cloudgate-sso-test-user)** per avere una controparte di Britta Simon in Soloinsight-CloudGate SSO collegata alla rappresentazione di Azure AD dell'utente.
6. **[Testare l'accesso Single Sign-On](#test-sso)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Soloinsight-CloudGate SSO** del [portale di Azure](https://portal.azure.com/) trovare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella pagina **Configurazione SAML di base** immettere i valori per i campi seguenti:

    1. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.sigateway.com/login`

    1. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.sigateway.com/process/sso`

   > [!NOTE]
   > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi, come spiegato più avanti nella sezione **Configurare l'accesso Single Sign-On per Soloinsight-CloudGate SSO** dell'esercitazione.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML**, trovare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer in uso.

   ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Soloinsight-CloudGate SSO** copiare gli URL appropriati in base alle esigenze.

   ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="configure-soloinsight-cloudgate-sso"></a>Configurare Soloinsight-CloudGate SSO

1. Per automatizzare la configurazione all'interno di Soloinsight-CloudGate SSO, è necessario installare l'**estensione del browser per l'accesso sicuro alle app personali** facendo clic su **Installa l'estensione**.

    ![Estensione My Apps](common/install-myappssecure-extension.png)

2. Dopo aver aggiunto l'estensione al browser, facendo clic su **Configura Soloinsight-CloudGate SSO** si verrà indirizzati all'applicazione Soloinsight-CloudGate SSO. Da qui, fornire le credenziali di amministratore per accedere a Soloinsight-CloudGate SSO. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 8.

    ![Eseguire la configurazione](common/setup-sso.png)

3. Se si vuole configurare manualmente Soloinsight-CloudGate SSO, aprire una nuova finestra del Web browser, accedere al sito aziendale di Soloinsight-CloudGate SSO come amministratore e completare la procedura seguente:

4. Per ottenere i valori da incollare nel portale di Azure durante la configurazione SAML di base, accedere al portale Web di CloudGate usando le proprie credenziali e quindi accedere alle impostazioni di SSO disponibili in **Home > Administration > System settings > General** (Home > Amministrazione > Impostazioni di sistema > Generale).

    ![Impostazioni di SSO per CloudGate](./media/soloinsight-cloudgate-sso-tutorial/sso-main-settings.png)

5. **URL consumer SAML**

    * Copiare i collegamenti disponibili nei campi **Saml Consumer URL** (URL consumer SAML) e **Redirect URL** (URL di reindirizzamento) e incollarli rispettivamente nei campi **Identificatore (ID entità)** e **URL di risposta** nella sezione **Configurazione SAML di base** del portale di Azure.

        ![Identificatore SAML](./media/soloinsight-cloudgate-sso-tutorial/saml-identifier.png)

6. **Certificato di firma SAML**

    * Passare all'origine del file Certificato (Base64) scaricato dagli elenchi di certificati di firma SAML del portale di Azure e fare clic su di esso con il pulsante destro del mouse. Scegliere l'opzione **Modifica con Notepad++** nell'elenco. 

        ![Certificato SAML](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * Copiare il contenuto del file Certificato (Base64) visualizzato in Notepad++.

        ![Copia del certificato](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * Incollare il contenuto nel campo **Certificate** (Certificato) delle impostazioni SSO del portale Web di CloudGate e fare clic sul pulsante Save (Salva).

        ![Portale del certificato](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

7. **Gruppo predefinito**

    * Selezionare **Business Admin** (Amministratori aziendali) nell'elenco a discesa dell'opzione **Default Group** (Gruppo predefinito) del portale Web di CloudGate.

        ![Gruppo predefinito](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

8. **Identificatore AD e URL di accesso**

    * Il valore di **URL di accesso** copiato da **Configura Soloinsight-CloudGate SSO** nel portale di Azure deve essere immesso nella sezione delle impostazioni SSO del portale Web di CloudGate.

    * Incollare il valore del collegamento **URL di accesso** del portale di Azure nel campo **AD Login URL** (URL di accesso AD) del portale Web di CloudGate.

    * Incollare il valore del collegamento **Identificatore Azure AD** del portale di Azure nel campo **AD Identifier** (Identificatore AD) del portale Web di CloudGate.

        ![Account di accesso AD](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

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

In questa sezione si abiliterà Britta Simon per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Soloinsight-CloudGate SSO.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Soloinsight-CloudGate SSO**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Creare l'utente di test di Soloinsight-CloudGate SSO

Per creare un utente di test, selezionare **Employees** (Dipendenti) nel menu principale del portale Web di CloudGate e compilare il modulo Add New employee (Aggiungi nuovo dipendente). Il livello di autorità da assegnare all'utente di test è **Business Admin** (Amministratore aziendale). Compilare tutti i campi obbligatori e quindi fare clic su **Create** (Crea).

![Test per dipendente](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

### <a name="test-sso"></a>Testare l'accesso SSO

Quando si seleziona il riquadro di Soloinsight-CloudGate SSO nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Soloinsight-CloudGate SSO per cui si è configurato l'accesso Single Sign-On. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)