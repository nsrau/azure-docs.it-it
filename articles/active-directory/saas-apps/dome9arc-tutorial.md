---
title: 'Esercitazione: Integrazione di Azure Active Directory con Dome9 Arc | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Dome9 Arc.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/31/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91ef20398bc3e15b1ee47c6e7c28795f4d9bc08c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56190943"
---
# <a name="tutorial-azure-active-directory-integration-with-dome9-arc"></a>Esercitazione: Integrazione di Azure Active Directory con Dome9 Arc

Questa esercitazione descrive come integrare Dome9 Arc con Azure Active Directory (Azure AD).
L'integrazione di Dome9 Arc con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Dome9 Arc.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Dome9 Arc con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Dome9 Arc, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Dome9 Arc abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Dome9 Arc supporta l'accesso SSO avviato da **SP** e **IDP**

## <a name="adding-dome9-arc-from-the-gallery"></a>Aggiunta di Dome9 Arc dalla raccolta

Per configurare l'integrazione di Dome9 Arc in Azure AD, è necessario aggiungere Dome9 Arc dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Dome9 Arc dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Dome9 Arc**, selezionare **Dome9 Arc** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Dome9 Arc nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Dome9 Arc usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Dome9 Arc.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Dome9 Arc, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per Dome9 Arc](#configure-dome9-arc-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Dome9 Arc](#create-dome9-arc-test-user)**: per avere una controparte di Britta Simon in Dome9 Arc collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Dome9 Arc, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Dome9 Arc** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare l'accesso Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Dome9 Arc](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://secure.dome9.com/`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://secure.dome9.com/sso/saml/yourcompanyname`

    > [!NOTE]
    > Selezionare il valore del nome della società nel portale di amministrazione di Dome9 Arc illustrato più avanti nell'esercitazione.

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Dome9 Arc](common/metadata-upload-additional-signon.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://secure.dome9.com/sso/saml/<yourcompanyname>`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Dome9 Arc](mailto:support@dome9.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

6. L'applicazione Dome9 Arc prevede un formato specifico per le asserzioni SAML. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![image](common/edit-attribute.png)

7. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** modificare le attestazioni usando l'**icona Modifica** o aggiungere le attestazioni usando l'opzione **Aggiungi nuova attestazione** per configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura: 

    | Nome |  Attributo di origine|
    | ---------------| --------------- |
    | memberof | user.assignedroles |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    f. Fare clic su **Ok**

    g. Fare clic su **Save**.

8. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

9. Nella sezione **Configura Dome9 Arc** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-dome9-arc-single-sign-on"></a>Configurare l'accesso Single Sign-On per Dome9 Arc

1. In un'altra finestra del Web browser accedere al sito aziendale di Dome9 Arc come amministratore.

2. Fare clic su **Impostazioni del profilo** nell'angolo superiore destro e quindi fare clic su **Impostazioni account**. 

    ![Configurazione di Dome9 Arc](./media/dome9arc-tutorial/configure1.png)

3. Passare a **SSO** e quindi fare clic su **Abilita**.

    ![Configurazione di Dome9 Arc](./media/dome9arc-tutorial/configure2.png)

4. Nella sezione di configurazione di SSO eseguire la procedura seguente:

    ![Configurazione di Dome9 Arc](./media/dome9arc-tutorial/configure3.png)

    a. Immettere il nome della società nella casella di testo **ID account**. Questo valore viene usato nell'URL di risposta riportato nella sezione URL del portale Azure.

    b. Nella casella di testo **Issuer** (Autorità di certificazione) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    c. Nella casella di testo **Idp endpoint url** (URL endpoint IdP) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    d. Aprire il certificato con codifica Base64 scaricato nel Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **X.509 certificate** (Certificato X.509).

    e. Fare clic su **Save**.

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

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Dome9 Arc.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Dome9 Arc**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Dome9 Arc**.

    ![Collegamento di Dome9 Arc nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-dome9-arc-test-user"></a>Creare l'utente di test di Dome9 Arc

Per consentire agli utenti di Azure AD di accedere a Dome9 Arc, è necessario effettuarne il provisioning nell'applicazione. Dome9 Arc supporta il provisioning JIT, ma per garantire il corretto funzionamento di tale provisioning è necessario selezionare un **ruolo** specifico e assegnarlo all'utente.

   >[!Note]
   >Per la creazione del **ruolo** e per altre informazioni, contattare il [team di supporto clienti di Dome9 Arc](https://dome9.com/about/contact-us/).

**Per eseguire il provisioning di un account utente manualmente, seguire questa procedura:**

1. Accedere al sito aziendale di Dome9 Arc come amministratore.

2. Fare clic su **Utenti e ruoli** e quindi su **Utenti**.

    ![Aggiungere un dipendente](./media/dome9arc-tutorial/user1.png)

3. Fare clic su **ADD USER** (AGGIUNGI UTENTE).

    ![Aggiungere un dipendente](./media/dome9arc-tutorial/user2.png)

4. Nella sezione **Create User** (Crea utente) eseguire la procedura seguente:

    ![Aggiungere un dipendente](./media/dome9arc-tutorial/user3.png)

    a. Nella casella di testo **Email** (Posta elettronica) digitare l'indirizzo di posta elettronica di un utente, ad esempio Brittasimon@contoso.com.

    b. Digitare il nome dell'utente, ad esempio Britta, nella casella di testo **First Name** (Nome).

    c. Digitare il cognome dell'utente, ad esempio Simon, nella casella di testo **Last Name** (Cognome).

    d. Impostare l'opzione **SSO User** (Utente SSO) su **On** (Abilitato).

    e. Fare clic su **CREATE** (CREA).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Dome9 Arc nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Dome9 Arc per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

