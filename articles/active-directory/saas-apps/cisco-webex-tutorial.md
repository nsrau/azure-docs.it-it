---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Cisco Webex Meetings | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Cisco Webex Meetings.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 141d3062f231e198b3587efcdf4ae6e9a1c97ed6
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70162670"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex-meetings"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Cisco Webex Meetings

Questa esercitazione descrive come integrare Cisco Webex Meetings con Azure Active Directory (Azure AD). Integrando Cisco Webex Meetings con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Cisco Webex Meetings.
* Abilitare gli utenti per l'accesso automatico a Cisco Webex Meetings con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Cisco Webex Meetings abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Cisco Webex Meetings supporta l'accesso SSO avviato da **SP e IDP**

* Cisco Webex Meetings supporta il provisioning utenti **Just-In-Time**

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>Aggiunta di Cisco Webex Meetings dalla raccolta

Per configurare l'integrazione di Cisco Webex Meetings in Azure AD, è necessario aggiungere Cisco Webex Meetings dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Cisco Webex Meetings** nella casella di ricerca.
1. Selezionare **Cisco Webex Meetings** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex-meetings"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Cisco Webex Meetings

Configurare e testare l'accesso SSO di Azure AD con Cisco Webex Meetings usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Cisco Webex Meetings.

Per configurare e testare l'accesso SSO di Azure AD con Cisco Webex Meetings, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
2. **[Configurare l'accesso Single Sign-On di Cisco Webex Meetings](#configure-cisco-webex-meetings-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Cisco Webex Meetings](#create-cisco-webex-meetings-test-user)** : per avere una controparte di B.Simon in Cisco Webex Meetings collegata alla rappresentazione dell'utente in Azure AD.
3. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Cisco Webex Meetings** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** è possibile configurare l'applicazione in modalità avviata da  **IDP**  caricando il file di **metadati del provider di servizi** come descritto di seguito:

    a. Fare clic su **Carica il file di metadati**.

    b. Fare clic su **logo cartella** per selezionare il file di metadati e fare quindi clic su **Upload**.

    c. Al termine del caricamento del file di metadati del provider di servizi, i valori di **Identificatore** e **URL di risposta** vengono popolati automaticamente nella sezione **Configurazione SAML di base**.

    >[!Note]
    >Si otterrà il file di metadati del provider di servizi in base alla procedura descritta più avanti nella sezione **Configurare l'accesso Single Sign-On di Cisco Webex Meetings** dell'esercitazione. 

1. Per configurare l'applicazione in modalità avviata da **SP**, seguire questa procedura:  

    a. Nella sezione **Configurazione SAML di base** fare clic sull'icona Modifica (la penna).

   ![Modificare la configurazione SAML di base](common/edit-urls.png)
    
    b. Nella casella di testo **URL di accesso** digitare l'URL nel formato seguente: ` https://<customername>.my.webex.com`

5. L'applicazione Cisco Webex Meetings prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti. Fare clic su  **Modifica** per aprire la finestra di dialogo Attributi utente.

    ![image](common/edit-attribute.png)

6. Oltre quelli elencati in precedenza, l'applicazione Cisco Webex Meetings prevede il passaggio di qualche altro attributo nella risposta SAML. Nella sezione Attestazioni utente della finestra di dialogo Attributi utente eseguire la procedura seguente per aggiungere l'attributo del token SAML come illustrato nella tabella seguente: 

    | NOME | Attributo di origine|
    | ---------------|  --------- |
    |   firstname    | user.givenname |
    |   lastname    | user.surname |
    |   email       | user.mail |
    |   uid    | user.mail |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga dall'elenco a discesa.

    f. Fare clic su **Save**.

4. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura Cisco Webex Meetings** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
    1. Nel campo **Nome** immettere `B.Simon`.  
    1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
    1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
    1. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Cisco Webex Meetings.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Cisco Webex Meetings**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-cisco-webex-meetings-sso"></a>Configurare l'accesso Single Sign-On di Cisco Webex Meetings

1. Passare all'URL `https://<customername>.webex.com/admin` con le credenziali di amministrazione.

2. Passare a **Common Site Settings** (Impostazioni sito comune) e quindi a **SSO Configuration** (Configurazione dell'accesso Single Sign-On).
 
    ![Configura accesso Single Sign-On](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

3. Nella pagina **Webex Administration** (Amministrazione di Webex) seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

    a. Selezionare **SAML 2.0** come valore di **Federation Protocol** (Protocollo di federazione).

    b. Fare clic sul link **Import SAML Metadata** (Importa metadati SAML) per caricare il file di metadati scaricato dal portale di Azure.

    c. Fare clic su **Esporta** per scaricare il file di metadati del provider di servizi e caricarlo nella sezione **Configurazione SAML di base** del portale di Azure.

    d. Nella casella di testo **AuthContextClassRef**, digitare `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified` e se si desidera abilitare l'autenticazione a più fattori con Azure AD digitare i due valori, ad esempio `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`

    e. Selezionare **Auto Account Creation** (Creazione automatica dell'account).

    >[!NOTE]
    >Per l'abilitazione del provisioning utenti **just-in-time** è necessario controllare la **Auto Account Creation** (Creazione automatica dell'account). Inoltre, gli attributi token SAML devono essere passati nella risposta SAML.

    f. Fare clic su **Save**.

    >[!NOTE]
    >Questa configurazione è valida solo per i clienti che usano Webex UserID nel formato dell'indirizzo di posta elettronica.

### <a name="create-cisco-webex-meetings-test-user"></a>Creare l'utente di test di Cisco Webex Meetings

Questa sezione descrive come creare un utente di nome B.Simon in Cisco Webex Meetings. Cisco Webex Meetings supporta il provisioning **just-in-time**, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Cisco Webex Meetings, ne viene creato uno nuovo quando si prova ad accedere a Cisco Webex Meetings.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Cisco Webex Meetings nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Cisco Webex Meetings per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare ServiceNow con Azure AD](https://aad.portal.azure.com)
