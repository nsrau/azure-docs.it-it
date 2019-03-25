---
title: 'Esercitazione: Integrazione di Azure Active Directory con Image Relay | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Image Relay.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 65bb5990-07ef-4244-9f41-cd28fc2cb5a2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63fbab5fffbcc30d0242d223fd8a6b5796c2aeae
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57901900"
---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>Esercitazione: Integrazione di Azure Active Directory con Image Relay

Questa esercitazione descrive come integrare Image Relay con Azure Active Directory (Azure AD).
L'integrazione di Image Relay con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Image Relay.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Image Relay con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Image Relay, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Image Relay abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Image Relay supporta l'accesso SSO avviato da **SP**

## <a name="adding-image-relay-from-the-gallery"></a>Aggiunta di Image Relay dalla raccolta

Per configurare l'integrazione di Image Relay in Azure AD, è necessario aggiungere Image Relay dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Image Relay dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Image Relay**, selezionare **Image Relay** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Image Relay nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Image Relay usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Image Relay.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Image Relay, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Image Relay](#configure-image-relay-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Image Relay](#create-image-relay-test-user)**: per avere una controparte di Britta Simon in Image Relay collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Image Relay, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com/), nella pagina di integrazione dell'applicazione **Image Relay**, selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Image Relay](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<companyname>.imagerelay.com/`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://<companyname>.imagerelay.com/sso/metadata`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori contattare il [team di supporto clienti di Image Relay](http://support.imagerelay.com/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Image Relay** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-image-relay-single-sign-on"></a>Configurare l'accesso Single Sign-On di Image Relay

1. In un'altra finestra del browser accedere al sito aziendale di Image Relay come amministratore.

2. Nel barra degli strumenti in alto fare clic sul carico di lavoro **Users & Permissions** (Utenti e autorizzazioni).

    ![Configure Single Sign-On](./media/imagerelay-tutorial/tutorial_imagerelay_06.png) 

3. Fare clic su **Create New Permission**(Crea nuova autorizzazione).

    ![Configure Single Sign-On](./media/imagerelay-tutorial/tutorial_imagerelay_08.png)

4. Nel carico di lavoro **Impostazioni Single Sing-On** selezionare la casella di controllo **This Group can only sign-in via Single Sign On** (Questo gruppo può accedere solo con Single Sign-On) e quindi fare clic su **Salva**.

    ![Configure Single Sign-On](./media/imagerelay-tutorial/tutorial_imagerelay_09.png) 

5. Passare ad **Account Settings**(Impostazioni account).

    ![Configure Single Sign-On](./media/imagerelay-tutorial/tutorial_imagerelay_10.png) 

6. Selezionare il carico di lavoro **Single Sign On Settings** (Impostazioni Single Sing-On).

    ![Configure Single Sign-On](./media/imagerelay-tutorial/tutorial_imagerelay_11.png)

7. Nella finestra di dialogo **SAML Settings** (Impostazioni SAML) seguire questa procedura:

    ![Configure Single Sign-On](./media/imagerelay-tutorial/tutorial_imagerelay_12.png)

    a. Nella casella di testo **Login URL** (URL di accesso) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    b. Nella casella di testo **URL disconnessione** incollare il valore di **URL disconnessione** copiato dal portale di Azure.

    c. Come **Name Id Format** (Formato ID nome) selezionare**urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.

    d. Come **Binding Options for Requests from the Service Provider (Image Relay)** (Opzioni di associazione per le richieste dal provider di servizi - Inoltro immagini) selezionare **POST Binding** (Associazione POST).

    e. In **Certificato X.509** fare clic su **Aggiorna certificato**.

    ![Configure Single Sign-On](./media/imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. Aprire il certificato scaricato nel Blocco note, copiarne il contenuto e incollarlo nella casella di testo **X.509 Certificate** (Certificato X.509).

    ![Configure Single Sign-On](./media/imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. Nella sezione **Just-In-Time User Provisioning** (Provisioning utenti Just-In-Time) selezionare la casella di controllo **Enable Just-In-Time User Provisioning** (Abilita provisioning utenti Just-In-Time).

    ![Configure Single Sign-On](./media/imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Selezionare il gruppo di autorizzazioni, ad esempio, **SSO Basic**(SSO di base) a cui sarà consentito l'accesso solo tramite Single Sign-On.

    ![Configure Single Sign-On](./media/imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

Questa sezione descrive come creare un utente di test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon\@dominioaziendale.estensione**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Image Relay.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Image Relay**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Image Relay**.

    ![Collegamento a Image Relay nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-image-relay-test-user"></a>Creare l'utente di test di Image Relay

Questa sezione descrive come creare un utente chiamato Britta Simon in Image Relay.

**Per creare un utente chiamato Britta Simon in Image Relay, seguire questa procedura:**

1. Accedere al sito aziendale di Image Relay come amministratore.

2. Andare in **Users & Permissions** (Utenti e autorizzazioni) e selezionare **Create SSO User** (Crea utente SSO).

    ![Configure Single Sign-On](./media/imagerelay-tutorial/tutorial_imagerelay_21.png) 

3. Immettere i valori necessari nei campi **Email** (Posta elettronica), **First Name** (Nome), **Last Name** (Cognome) e **Company** (Società) dell'utente di cui si vuole effettuare il provisioning e selezionare il gruppo di autorizzazioni (ad esempio SSO Basic) che potrà eseguire l'accesso solo tramite Single Sign-On.

    ![Configure Single Sign-On](./media/imagerelay-tutorial/tutorial_imagerelay_22.png)

4. Fare clic su **Create**(Crea).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Image Relay nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Image Relay per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)