---
title: 'Esercitazione: Integrazione di Azure Active Directory con Displayr | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Displayr.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b739b4e3-1a37-4e3c-be89-c3945487f4c1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fdb4cc26a435ba161532b324ae5a04fed8eb9437
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65157309"
---
# <a name="tutorial-azure-active-directory-integration-with-displayr"></a>Esercitazione: Integrazione di Azure Active Directory con Displayr

Questa esercitazione descrive come integrare Displayr con Azure Active Directory (Azure AD).
L'integrazione di Displayr con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Displayr.
* È possibile abilitare gli utenti per l'accesso automatico a Displayr (Single Sign-On) con gli account Azure AD.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Displayr, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Displayr abilitata per l'accesso Single Sign-On.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Displayr supporta l'accesso SSO avviato dal **provider di servizi**

## <a name="adding-displayr-from-the-gallery"></a>Aggiunta di Displayr dalla raccolta

Per configurare l'integrazione di Displayr in Azure AD, è necessario aggiungere Displayr dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Displayr dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Displayr**, selezionare **Displayr** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Displayr nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Displayr usando un utente di test di nome **Britta Simon**.
Per il funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Displayr.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Displayr, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Displayr](#configure-displayr-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Displayr](#create-displayr-test-user)**: per avere una controparte di Britta Simon in Displayr collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Displayr, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Displayr** nel [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** eseguire il passaggio seguente:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Displayr](common/sp-intiated.png)

    Nella casella di testo **URL di accesso** digitare un URL: `https://app.displayr.com/Login`

5. Nella sezione **Certificato di firma SAML** fare clic su **Scarica** per scaricare il file **Certificato (base)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificateraw.png)

6. L'applicazione Displayr prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti. Fare clic su  **Modifica** per aprire la finestra di dialogo Attributi utente.

    ![image](common/edit-attribute.png)

7. Oltre a quanto elencato in precedenza, l'applicazione Displayr prevede il passaggio di altri attributi nella risposta SAML. Nella sezione  **Attributi utente e attestazioni**  della finestra di dialogo  **Attestazioni dei gruppi (anteprima)**  eseguire questa procedura:

    a. Fare clic sulla **penna** accanto a **Gruppi restituiti nell'attestazione**.

    ![image](./media/displayr-tutorial/config04.png)

    ![image](./media/displayr-tutorial/config05.png)

    b. Selezionare **Tutti i gruppi** dall'elenco dei pulsanti di opzione.

    c. Per **Attributo di origine** selezionare **ID gruppo**.

    d. Selezionare **Personalizza il nome dell'attestazione basata su gruppo**.

    e. Selezionare **Crea gruppi come attestazioni dei ruoli**.

    f. Fare clic su **Save**.

8. Nella sezione **Configura Displayr** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-displayr-single-sign-on"></a>Configurare l'accesso Single Sign-On di Displayr

1. In un'altra finestra del Web browser accedere a Displayr come amministratore.

2. Fare clic su **Settings** (Impostazioni) e quindi passare ad **Account**.

    ![Configurazione](./media/displayr-tutorial/config01.png)

3. Passare a **Settings** (Impostazioni) dal menu in alto e scorrere la pagina verso il basso per fare clic su **Configure Single Sign On (SAML)** (Configura Single Sign-On - SAML).

    ![Configurazione](./media/displayr-tutorial/config02.png)

4. Nella pagina **Single Sign-On (SAML)** seguire questa procedura:

    ![Configurazione](./media/displayr-tutorial/config03.png)

    a. Selezionare la casella **Enable Single Sign On (SAML)** (Abilita Single Sign-On - SAML).

    b. Nella casella di testo **Issuer** (Autorità di certificazione) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    c. Nella casella di testo **Login URL** (URL di accesso) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    d. Nella casella di testo **Logout URL** (URL di disconnessione) incollare il valore di **URL disconnessione** copiato dal portale di Azure.

    e. Aprire il certificato (base) nel Blocco note, copiarne il contenuto e incollarlo nella casella di testo **Certificate** (Certificato).

    f. I valori di **Group mappings** (Mapping dei gruppi) sono facoltativi.

    g. Fare clic su **Save**.  

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare `brittasimon@yourcompanydomain.extension`. Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Displayr.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Displayr**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Displayr**.

    ![Collegamento di Displayr nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-displayr-test-user"></a>Creare un utente di test di Displayr

Per consentire agli utenti di Azure AD di accedere a Displayr, è necessario effettuarne il provisioning in Displayr. Nel caso di Displayr il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere a Displayr come amministratore.

2. Fare clic su **Settings** (Impostazioni) e quindi passare ad **Account**.

    ![Configurazione di Displayr](./media/displayr-tutorial/config01.png)

3. Passare a **Settings** (Impostazioni) dal menu in alto e scorrere la pagina verso il basso fino alla sezione **Users** (Utenti) e quindi fare clic su **New User** (Nuovo utente).

    ![Configurazione di Displayr](./media/displayr-tutorial/config07.png)

4. Nella pagina **New User** (Nuovo utente) seguire questa procedura:

    ![Configurazione di Displayr](./media/displayr-tutorial/config06.png)

    a. Nella casella di testo **Name** (Nome) immettere il nome dell'utente, ad esempio **Brittasimon**.

    b. Nella casella di testo **E-mail** (Posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio `Brittasimon@contoso.com`.

    c. Selezionare i valori appropriati per **Group membership** (Appartenenza a gruppi).

    d. Fare clic su **Save**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Displayr nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Displayr per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

