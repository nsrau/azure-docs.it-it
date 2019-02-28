---
title: 'Esercitazione: Integrazione di Azure Active Directory con Central Desktop | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Central Desktop.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50ef84c9795065d4186fae9542c18d4df06c85c2
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56880377"
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Esercitazione: Integrazione di Azure Active Directory con Central Desktop

Questa esercitazione descrive come integrare Central Desktop con Azure Active Directory (Azure AD).
L'integrazione di Central Desktop con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Central Desktop.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Central Desktop con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Central Desktop, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Central Desktop abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Central Desktop supporta l'accesso SSO avviato da **SP**

## <a name="adding-central-desktop-from-the-gallery"></a>Aggiunta di Central Desktop dalla raccolta

Per configurare l'integrazione di Central Desktop in Azure AD, è necessario aggiungere Central Desktop dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Central Desktop dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Central Desktop**, selezionare **Central Desktop** nel pannello dei risultati e fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Central Desktop nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Central Desktop usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Central Desktop.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Central Desktop, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per Central Desktop](#configure-central-desktop-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Central Desktop](#create-central-desktop-test-user)**: per avere una controparte di Britta Simon in Central Desktop collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Central Desktop, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Central Desktop** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Central Desktop](common/sp-identifier-reply.png)

    a. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<companyname>.centraldesktop.com`

    b. Nella casella di testo **Identificatore** digitare un URL nel formato seguente:
    | |
    |--|
    | `https://<companyname>.centraldesktop.com/saml2-metadata.php`|
    | `https://<companyname>.imeetcentral.com/saml2-metadata.php`|
    | |

    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<companyname>.centraldesktop.com/saml2-assertion.php`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Central Desktop](https://imeetcentral.com/contact-us). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **Certificato (base)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificateraw.png)

6. Nella sezione **Configura Central Desktop** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-central-desktop-single-sign-on"></a>Configurare l'accesso Single Sign-On per Central Desktop

1. Accedere al tenant di **Central Desktop**.

2. Passare a **Impostazioni**. Selezionare **Advanced** (Avanzate) e quindi selezionare **Single Sign On** (Single Sign-On).

    ![Installazione - Avanzate](./media/central-desktop-tutorial/ic769563.png "Installazione - Avanzate")

3. Nella pagina **Single Sign-On Settings** (Impostazioni Single Sign-On) seguire questa procedura:

    ![Impostazioni di Single Sign-On](./media/central-desktop-tutorial/ic769564.png "Impostazioni di Single Sign-On")

    a. Selezionare **Enable SAML v2 Single Sign On**.

    b. Nella casella **SSO URL** (URL SSO) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    c. Nella casella **SSO Login URL** (URL di accesso SSO) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    d. Nella casella **SSO Logout URL** (URL disconnessione SSO) incollare il valore di **URL disconnessione** copiato dal portale di Azure.

4. Nella sezione **Message Signature Verification Method** (Metodo di verifica della firma del messaggio) seguire questa procedura:

    ![Message Signature Verification Method](./media/central-desktop-tutorial/ic769565.png "Message Signature Verification Method")
    
    a. Selezionare **Certificate**.

    b. Nell'elenco **SSO Certificate** (Certificato SSO) selezionare **RSH SHA256**.

    c. Aprire il certificato scaricato nel Blocco note. Copiare quindi il contenuto del certificato e incollarlo nel campo **SSO Certificate** (Certificato SSO).

    d. Selezionare **Display a link to your SAMLv2 login page**.

    e. Selezionare **Aggiorna**.

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

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Central Desktop.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Central Desktop**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Central Desktop**.

    ![Collegamento di Central Desktop nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-central-desktop-test-user"></a>Creare l'utente di test di Central Desktop

Per consentire agli utenti di Azure AD di accedere, è necessario eseguirne il provisioning nell'applicazione Central Desktop. Questa sezione descrive come creare account utente di Azure AD in Central Desktop.

> [!NOTE]
> Per effettuare il provisioning degli account utente di Azure AD, è possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Central Desktop.

**Per eseguire il provisioning degli account utente in Central Desktop:**

1. Accedere al tenant di Central Desktop.

2. Selezionare **People** (Persone) e quindi **Add Internal Members** (Aggiungi membri interni).

    ![Persone](./media/central-desktop-tutorial/ic781051.png "Persone")

3. Nella casella **Email Address of New Members** (Indirizzo posta elettronica nuovi membri) digitare un account Azure AD di cui si vuole effettuare il provisioning e quindi selezionare **Next** (Avanti).

    ![Email Addresses of New Members (Indirizzi di posta elettronica dei nuovi membri)](./media/central-desktop-tutorial/ic781052.png "Email Addresses of New Members (Indirizzi di posta elettronica dei nuovi membri)")

4. Selezionare **Add Internal member(s)** (Aggiungi membri interni).

    ![Aggiungere un membro interno](./media/central-desktop-tutorial/ic781053.png "Aggiungere un membro interno")
  
   > [!NOTE]
   > Gli utenti aggiunti ricevono un messaggio di posta elettronica che include un collegamento di conferma per attivare gli account.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Central Desktop nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Central Desktop per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
