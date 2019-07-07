---
title: 'Esercitazione: Integrazione di Azure Active Directory con Infinite Campus | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Infinite Campus.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3995b544-e751-4e0f-ab8b-c9a3862da6ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: d54769c1f3265e2cee619520044313fca46855a5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67100361"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>Esercitazione: Integrazione di Azure Active Directory con Infinite Campus

Questa esercitazione descrive come integrare Infinite Campus con Azure Active Directory (Azure AD).
L'integrazione di Infinite Campus con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Infinite Campus.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Infinite Campus con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Infinite Campus, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Infinite Campus abilitata per l'accesso Single Sign-On
* Per completare la configurazione, è necessario essere almeno un amministratore di Azure Active Directory e avere un ruolo di sicurezza "Student Information System (SIS)" del prodotto Campus.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Infinite Campus supporta l'accesso SSO avviato da **SP**

## <a name="adding-infinite-campus-from-the-gallery"></a>Aggiunta di Infinite Campus dalla raccolta

Per configurare l'integrazione di Infinite Campus in Azure AD, è necessario aggiungere Infinite Campus dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Infinite Campus dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Infinite Campus**, selezionare **Infinite Campus** nel pannello dei risultati, quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Infinite Campus nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Infinite Campus usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Infinite Campus.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Infinite Campus, è necessario completare i passaggi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per Infinite Campus](#configure-infinite-campus-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Infinite Campus](#create-infinite-campus-test-user)** : per avere una controparte di Britta Simon in Infinite Campus collegata alla relativa rappresentazione in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Infinite Campus, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com/), nella pagina di integrazione dell'applicazione **Infinite Campus**, selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione Configurazione SAML di base eseguire la procedura seguente. Tenere presente che il dominio varia in base al modello di hosting, ma il valore **FULLY-QUALIFIED-DOMAIN** deve corrispondere all'installazione di Infinite Campus:

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>`

    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

    ![Informazioni sull'accesso Single Sign-On per URL e dominio di Infinite Campus](common/sp-identifier-reply.png)

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)

### <a name="configure-infinite-campus-single-sign-on"></a>Configurare l'accesso Single Sign-On per Infinite Campus

1. In un'altra finestra del Web browser accedere a Infinite Campus come amministratore della sicurezza.

2. Dal menu a sinistra scegliere **System Administration** (Amministrazione sistema).

    ![Amministrazione](./media/infinitecampus-tutorial/tutorial_infinitecampus_admin.png)

3. Passare a **User Security** > **SAML Management** > **SSO Service Provider Configuration** (Sicurezza utente > Gestione SAML > Configurazione del provider di servizi SAML).

    ![SAML](./media/infinitecampus-tutorial/tutorial_infinitecampus_saml.png)

4. Nella pagina **SSO Service Provider Configuration** (Configurazione del provider di servizi SAML) seguire questa procedura:

    ![SSO](./media/infinitecampus-tutorial/tutorial_infinitecampus_sso.png)

    a. Selezionare **Enable SAML Single Sign On** (Abilita SAML Single Sign On).

    b. Modificare il campo **Optional Attribute Name** (Nome attributo facoltativo) in modo che contenga **name**

    c. Nella sezione **Select an option to retrieve Identity Provider (IDP) server data** (Selezionare un'opzione per recuperare i dati del server del provider di identità) selezionare **Metadata URL** (URL dei metadati), incollare nella casella l'**URL dei metadati di federazione dell'app** copiato dal portale di Azure e quindi fare clic su **Sync** (Sincronizza).

    d. Dopo aver fatto clic su **Sync** (Sincronizza), i valori vengono automaticamente popolati nella pagina **SSO Service Provider Configuration** (Configurazione del provider di servizi SAML). È possibile verificare che questi valori corrispondono a quelli visti nel passaggio 4 precedente.

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
  
    b. Nel campo **Nome utente** digitare `brittasimon@yourcompanydomain.extension`. Ad esempio: BrittaSimon@contoso.com.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

> [!NOTE]
> Sei si vuole concedere a tutti gli utenti di Azure l'accesso Single Sign-On a Infinite Campus e controllare l'accesso con il sistema interno di autorizzazioni di Infinite Campus internal, è possibile impostare la proprietà **User Assignment Required** (Assegnazione utenti obbligatoria) dell'applicazione su No e ignorare i passaggi seguenti.

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Infinite Campus.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Infinite Campus**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Infinite Campus**.

    ![Collegamento a Infinite Campus nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-infinite-campus-test-user"></a>Creare un utente di test di Infinite Campus

Infinite Campus ha un'architettura basata sui dati demografici. Contattare il [team di supporto di Infinite Campus](mailto:sales@infinitecampus.com) per aggiungere gli utenti nella piattaforma Infinite Campus.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Infinite Campus nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Infinite Campus per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
