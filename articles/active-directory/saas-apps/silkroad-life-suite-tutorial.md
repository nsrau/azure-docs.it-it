---
title: 'Esercitazione: Integrazione di Azure Active Directory con SilkRoad Life Suite | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SilkRoad Life Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: bcad6232de7fa257b58fe6d84f2c2ff794b64589
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60005418"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Esercitazione: Integrazione di Azure Active Directory con SilkRoad Life Suite

Questa esercitazione descrive come integrare SilkRoad Life Suite con Azure Active Directory (Azure AD).
L'integrazione di SilkRoad Life Suite con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a SilkRoad Life Suite.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a SilkRoad Life Suite con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con SilkRoad Life Suite, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di SilkRoad Life Suite abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* SilkRoad Life Suite supporta l'accesso SSO avviato da **SP**

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>Aggiunta di SilkRoad Life Suite dalla raccolta

Per configurare l'integrazione di SilkRoad Life Suite in Azure AD, è necessario aggiungere SilkRoad Life Suite dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere SilkRoad Life Suite dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **SilkRoad Life Suite**, selezionare **SilkRoad Life Suite** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![SilkRoad Life Suite nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SilkRoad Life Suite con un utente di test di nome **Britta Simon**.
Per il funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SilkRoad Life Suite.

Per configurare e testare l'accesso Single Sign-On di Azure AD con SilkRoad Life Suite, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di SilkRoad Life Suite](#configure-silkroad-life-suite-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di SilkRoad Life Suite](#create-silkroad-life-suite-test-user)**: per avere una controparte di Britta Simon in SilkRoad Life Suite collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare Single Sign-On di Azure AD con SilkRoad Life Suite, eseguire i passaggi seguenti:

1. Nella pagina di integrazione dell'applicazione **SilkRoad Life Suite** del [portale di Azure](https://portal.azure.com/) fare clic su **Single sign-on**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base**, se si dispone di un **file di metadati di un provider di servizi**, seguire questa procedura:

    > [!NOTE]
    > Si otterrà il **file di metadati del provider di servizi** descritto più avanti in questa esercitazione.

    a. Fare clic su **Carica il file di metadati**.

    ![image](common/upload-metadata.png)

    b. Fare clic su **logo cartella** per selezionare il file di metadati e fare quindi clic su **Upload**.

    ![image](common/browse-upload-metadata.png)

    c. Dopo il caricamento del file di metadati, il valore di **Identificatore** e **URL di risposta** viene inserito automaticamente nella sezione Configurazione SAML di base:

    ![image](common/sp-identifier-reply.png)

    > [!Note]
    > Se i valori di **Identificatore** e **URL di risposta** non vengono popolati automaticamente, inserirli manualmente in base alle proprie esigenze.

    d. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<subdomain>.silkroad-eng.com/Authentication/`

5. Nella sezione **Configurazione SAML di base**, se non si dispone di un **file di metadati di un provider di servizi**, eseguire i passaggi seguenti:

    ![Informazioni su URL e dominio per Single Sign-On di SilkRoad Life Suite](common/sp-identifier-reply.png)

    a. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<subdomain>.silkroad-eng.com/Authentication/`

    b. Nella casella di testo **Identificatore** digitare un URL nel formato seguente:

    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/SP`|
    | `https://<subdomain>.silkroad.com/Authentication/SP`|

    c. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente:

    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/`|
    | `https://<subdomain>.silkroad.com/Authentication/`|

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di SilkRoad Life Suite](https://www.silkroad.com/locations/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

7. Nella sezione **Configura SilkRoad Life Suite** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-silkroad-life-suite-single-sign-on"></a>Configurare Single Sign-On per SilkRoad Life Suite

1. Accedere al sito aziendale di SilkRoad come amministratore.

    > [!NOTE]
    > Per ottenere l'accesso all'applicazione SilkRoad Life Suite Authentication per configurare la federazione con Microsoft Azure AD, contattare il supporto SilkRoad o il rappresentante dei servizi SilkRoad.

1. Passare a **Service Provider** (Provider di servizi) e quindi fare clic su **Federation Details** (Dettagli federazione).

    ![Single Sign-On di Microsoft Azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_06.png)

1. Fare clic su **Download Federation Metadata**(Scarica metadati federazione) e quindi salvare il file di metadati nel computer. Usare il file dei metadati di federazione scaricato come un **file di metadati del provider di servizi** nella sezione **Configurazione SAML di base** nel portale di Azure.

    ![Single Sign-On di Microsoft Azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_07.png)

1. Nell'applicazione **SilkRoad**, fare clic su **Authentication Sources** (Origini autenticazione).

    ![Single Sign-On di Microsoft Azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_08.png) 

1. Fare clic su **Add Authentication Source**(Aggiungi origine autenticazione).

    ![Single Sign-On di Microsoft Azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_09.png)

1. Nella sezione **Add Authentication Source** (Aggiungi origine autenticazione) seguire questa procedura:

    ![Single Sign-On di Microsoft Azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_10.png)
  
    a. In **Option 2 - Metadata File** (Opzione 2 - File di metadati) fare clic su **Browse** (Sfoglia) per caricare il file di metadati scaricato dal portale di Azure.
  
    b. Fare clic su **Create Identity Provider using File Data**.

1. Nella sezione **Authentication Sources** (Origini autenticazione) fare clic su **Edit** (Modifica).

    ![Single Sign-On di Microsoft Azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_11.png)

1. Nella finestra di dialogo **Edit Authentication Source** (Modifica origine autenticazione) seguire questa procedura:

    ![Single Sign-On di Microsoft Azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_12.png)

    a. In **Enabled** (Attivo) selezionare **Yes** (Sì).

    b. Nella casella di testo **EntityId** incollare il valore dell'**Identificatore di Azure AD** copiato dal portale di Azure.

    c. Nella casella di testo **IdP Description** (Descrizione IdP) digitare una descrizione per la configurazione, ad esempio *Azure AD SSO*.

    d. Nella casella di testo **Metadata File** (File di metadati) caricare il file di **metadati** scaricato dal portale di Azure.
  
    e. Nella casella di testo **IdP Name** (Nome IdP) digitare un nome specifico per la configurazione, ad esempio *Azure SP*.
  
    f. Nella casella di testo **URL servizio punto di disconnessione** incollare il valore di **URL disconnessione** copiato dal portale di Azure.

    g. Nella casella di testo **URL servizio Single Sign-On** incollare il valore di **URL di accesso** copiato dal portale di Azure.

    h. Fare clic su **Save**.

1. Disabilitare tutte le altre origini di autenticazione.

    ![Single Sign-On di Microsoft Azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_13.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare `brittasimon@yourcompanydomain.extension`.  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SilkRoad Life Suite.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **SilkRoad Life Suite**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **SilkRoad Life Suite**.

    ![Collegamento SilkRoad Life Suite nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-silkroad-life-suite-test-user"></a>Creare un utente test di SilkRoad Life Suite

In questa sezione viene creato un utente chiamato Britta Simon in SilkRoad Life Suite. Contattare il [team di supporto clienti di SilkRoad Life Suite](https://www.silkroad.com/locations/) per aggiungere gli utenti nella piattaforma SilkRoad Life Suite. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di SilkRoad Life Suite nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione SilkRoad Life Suite per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
