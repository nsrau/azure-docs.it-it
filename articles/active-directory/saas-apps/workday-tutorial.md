---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Workday | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Workday.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: af001ddd8dc468d06706e63eaf092d1179fe3fdc
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181374"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Workday

In questa esercitazione si apprenderà come integrare Workday con Azure Active Directory (Azure AD). Integrando Workday con Azure AD è possibile:

* Controllare in Azure AD chi può accedere a Workday.
* Abilitare gli utenti per l'accesso automatico a Workday con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Una sottoscrizione di Workday abilitata per l'accesso Single Sign-On.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Workday supporta l'accesso Single Sign-On avviato da **provider di servizi**.

* È ora possibile configurare l'applicazione per dispositivi mobili Workday con Azure AD per abilitare l'accesso SSO. Per altri dettagli sulla procedura di configurazione, seguire questo [collegamento](workday-mobile-tutorial.md).

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="adding-workday-from-the-gallery"></a>Aggiunta di Workday dalla raccolta

Per configurare l'integrazione di Workday in Azure AD, è necessario aggiungere Workday dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Workday** nella casella di ricerca.
1. Selezionare **Workday** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-workday"></a>Configurare e testare l'accesso SSO di Azure AD per Workday

Configurare e testare l'accesso Single Sign-On di Azure AD con Workday usando un utente di test di nome **B.Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Workday.

Per configurare e testare l'accesso SSO di Azure AD con Workday, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** per consentire a B.Simon di usare l'accesso Single Sign-On di Azure AD.
2. **[Configurare Workday](#configure-workday)** per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Workday](#create-workday-test-user)** per avere una controparte di B.Simon in Workday collegata alla rappresentazione dell'utente in Azure AD.
3. **[Testare l'accesso Single Sign-On](#test-sso)** per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Workday** del portale di Azure trovare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella pagina **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://impl.workday.com/<tenant>/login-saml2.flex`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://impl.workday.com/<tenant>/login-saml.htmld`

    c. Nella casella di testo **URL di disconnessione** digitare un URL nel formato seguente: `https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con i valori effettivi di URL di accesso, URL di risposta e URL di disconnessione. L'URL di risposta deve avere un sottodominio, ad esempio www, wd2, wd3, wd3-impl, wd5, wd5-impl.
    > L'uso di qualcosa di simile a `http://www.myworkday.com` funziona, ma `http://myworkday.com` non funziona. Per ottenere tali valori, contattare il [team di supporto clienti di Workday](https://www.workday.com/en-us/partners-services/services/support.html). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione Workday prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi token SAML. La schermata seguente illustra l'elenco degli attributi predefiniti in cui **nameidentifier** è associato a **user.userprincipalname**. L'applicazione Workday prevede che **nameidentifier** sia mappato a **user.mail**, **UPN** e così via. Di conseguenza è necessario modificare il mapping degli attributi facendo clic sull'icona **Modifica**.

    ![Screenshot che mostra il riquadro Attributi utente con l'icona di modifica selezionata.](common/edit-attribute.png)

    > [!NOTE]
    > Qui abbiamo mappato l'ID del nome con UPN (user.userprincipalname) come valore predefinito. Per il corretto funzionamento dell'accesso Single Sign-On, è necessario mappare l'ID del nome con l'ID utente effettivo nell'account Workday (indirizzo di posta elettronica, UPN e così via).

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML**, trovare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer in uso.

   ![Collegamento di download del certificato](common/certificatebase64.png)

1. Per modificare le opzioni di **Firma** in base alle esigenze, fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Certificato di firma SAML**.

    ![Certificato](common/edit-certificate.png) 

    ![Certificato di firma SAML](./media/workday-tutorial/signing-option.png)

    a. In **Opzione di firma** selezionare **Firma asserzione e risposta SAML**.

    b. Fare clic su **Save** (Salva).

1. Nella sezione **Configura Workday** copiare gli URL appropriati in base alle esigenze.

   ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Workday.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco di applicazioni selezionare **Workday**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se non è stato configurato alcun ruolo per questa app, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-workday"></a>Configurare Workday

1. In un'altra finestra del Web browser accedere al sito aziendale di Workday come amministratore.

1. Nella **casella di ricerca** in alto a sinistra della home page eseguire una ricerca di **Edit Tenant Setup - Security** (Modifica configurazione tenant - Sicurezza).

    ![Edit Tenant Security](./media/workday-tutorial/IC782925.png "Edit Tenant Security")


1. Nella sezione **SAML Setup** eseguire la procedura seguente:

    ![SAML Setup](./media/workday-tutorial/IC782926.png "SAML Setup")

    a.  Selezionare **Enable SAML Authentication**.

    b.  Fare clic su **Aggiungi riga**.

1. Nella sezione **SAML Identity Providers** (Provider di identità SAML) eseguire le azioni seguenti per la riga appena creata.

    a. Eseguire le azioni seguenti per i campi indicati di seguito.

    ![SAML Identity Providers 1](./media/workday-tutorial/IC7829271.png "SAML Identity Providers")

    * Nella casella di testo **Identity Provider Name** (Nome provider di identità) digitare il nome del provider, ad esempio *SPInitiatedSSO*).

    * Nella sezione **Configurazione Workday** del portale di Azure copiare il valore dell'**identificatore Azure AD** e incollarlo nella casella di testo **Autorità di certificazione**.

    * Aprire nel Blocco note il **certificato** scaricato dal portale di Azure e incollarne il contenuto nella casella di testo **x.509 Certificate** (Certificato X.509).

    b. Eseguire le azioni seguenti per i campi indicati di seguito.

    ![SAML Identity Providers 2](./media/workday-tutorial/saml-identity-provider-2.png "SAML Identity Providers")

    * Fare clic sulla casella di controllo **Enable IDP Initiated Logout** (Abilita disconnessione avviata da IDP).

    * Nella casella di testo **Logout Response URL** (URL di risposta di disconnessione) digitare **http://www.workday.com** .

    * Nella casella di testo **Logout Request URL** (URL di richiesta di disconnessione) incollare il valore di **URL disconnessione** copiato dal portale di Azure.

    * Fare clic sulla casella di controllo **SP Initiated** (Avviato da SP).

    * Nella casella di testo **Service Provider ID** (ID provider di servizi) digitare **http://www.workday.com**.


    * Selezionare **Do Not Deflate SP-initiated Authentication Request**.

    c. Eseguire le azioni seguenti per i campi indicati di seguito.

    ![SAML Identity Providers 3](./media/workday-tutorial/saml-identity-provider-3.png "SAML Identity Providers")

    * Nella sezione **Configurazione Workday** del portale di Azure copiare il valore dell'**URL di accesso** e incollarlo nella casella di testo **URL del servizio SSO IdP**.

    * Nella casella di testo **Used for Environments** (Usato per gli ambienti) selezionare i nomi degli ambienti appropriati nell'elenco a discesa.

1. Eseguire questa procedura nell'immagine seguente.

    ![Workday](./media/workday-tutorial/service-provider.png "SAML Identity Providers")

    a. Nella casella di testo **Service Provider ID (Will be Deprecated)** (ID provider di servizi, verrà deprecato) digitare **http://www.workday.com** .

    b. Nella casella di testo **IDP SSO Service URL (Will be Deprecated)** (URL del servizio SSO IDP, verrà deprecato) digitare il valore di **URL di accesso**.

    c. Selezionare **Do Not Deflate SP-initiated Authentication Request (Will be Deprecated)** (Non comprimere la richiesta di autenticazione avviata da SP, verrà deprecato).

    d. In **Authentication Request Signature Method** (Metodi di firma della richiesta di autenticazione) selezionare **SHA256**.

    e. Fare clic su **OK**.

    > [!NOTE]
    > Assicurarsi di aver configurato il single sign-on in modo corretto. Nel caso in cui il single sign-on sia stato configurato in modo errato, potrebbe non essere possibile accedere all'applicazione con le proprie credenziali. In questo caso, Workday fornisce un backup dell’url del log-in da cui gli utenti possono accedere usando il normale nome utente e la password nel formato seguente: [Your Workday URL]/login.flex?redirect=n

### <a name="create-workday-test-user"></a>Creare un utente di test di Workday

1. Accedere al sito aziendale di Workday come amministratore.

1. Fare clic su **Profile** (Profilo) nell'angolo in alto a destra, selezionare **Home** e fare clic su **Directory** nella scheda **Applications** (Applicazioni). 

1. Nella pagina **Directory** selezionare **Find Workers** (Trova dipendenti) nella scheda View (Visualizza).

    ![Find workers](./media/workday-tutorial/user-directory.png)

1.  Nella pagina **Find Workers** (Trova dipendenti) selezionare l'utente nei risultati.

1. Nella pagina seguente selezionare **Job > Worker Security** (Mansione > Sicurezza del dipendente). Il valore di **Workday Account** (Account Workday) deve corrispondere al valore di **ID nome** di Azure Active Directory.

    ![Worker Security](./media/workday-tutorial/worker-security.png)

> [!NOTE]
> Per altre informazioni su come creare un utente di test di Workday, contattare il [team di supporto clienti di Workday](https://www.workday.com/en-us/partners-services/services/support.html).

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

1. Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Workday, in cui è possibile avviare il flusso di accesso. 

2. Passare direttamente all'URL di accesso di Workday e avviare il flusso di accesso.

3. È possibile usare il Pannello di accesso Microsoft. Quando si fa clic sul riquadro di Workday nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Workday per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Workday, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)