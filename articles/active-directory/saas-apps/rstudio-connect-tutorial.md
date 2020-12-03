---
title: 'Esercitazione: Integrazione di Azure Active Directory con RStudio Connect SAML Authentication | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e RStudio Connect SAML Authentication.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/21/2020
ms.author: jeedes
ms.openlocfilehash: 84e8c7fc1d2655ea0685ac79841a9c467bf766cf
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182392"
---
# <a name="tutorial-azure-active-directory-integration-with-rstudio-connect-saml-authentication"></a>Esercitazione: Integrazione di Azure Active Directory con RStudio Connect SAML Authentication

Questa esercitazione descrive come integrare RStudio Connect SAML Authentication con Azure Active Directory (Azure AD).
L'integrazione di RStudio Connect SAML Authentication con Azure AD offre i vantaggi seguenti:

* You can Controllare in Azure AD chi può accedere a RStudio Connect SAML Authentication.
* You can Abilitare gli utenti per l'accesso automatico a RStudio Connect SAML Authentication (Single Sign-On) con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con RStudio Connect SAML Authentication, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* RStudio Connect SAML Authentication. È disponibile una [versione di valutazione gratuita di 45 giorni](https://www.rstudio.com/products/connect/).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* RStudio Connect SAML Authentication supporta l'accesso SSO avviato da **SP e IDP**

* RStudio Connect SAML Authentication supporta il provisioning utenti **JIT**

## <a name="adding-rstudio-connect-saml-authentication-from-the-gallery"></a>Aggiunta di RStudio Connect SAML Authentication dalla raccolta

Per configurare l'integrazione di RStudio Connect SAML Authentication in Azure AD, è necessario aggiungere RStudio Connect SAML Authentication dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **RStudio Connect SAML Authentication** nella casella di ricerca.
1. Selezionare **RStudio Connect SAML Authentication** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-rstudio-connect-saml-authentication"></a>Configurare e testare l'accesso SSO di Azure AD per RStudio Connect SAML Authentication

Configurare e testare l'accesso SSO di Azure AD con RStudio Connect SAML Authentication usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in RStudio Connect SAML Authentication.

Per configurare e testare l'accesso SSO di Azure AD con RStudio Connect SAML Authentication, completare la procedura seguente:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
2. **[Configurare l'accesso Single Sign-On di RStudio Connect SAML Authentication](#configure-rstudio-connect-saml-authentication-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di RStudio Connect SAML Authentication](#create-rstudio-connect-saml-authentication-test-user)** : per avere una controparte di Britta Simon in RStudio Connect SAML Authentication collegata alla rappresentazione dell'utente in Azure AD.
3. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **RStudio Connect SAML Authentication** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**, sostituendo `<example.com>` con l'indirizzo e la porta del proprio server RStudio Connect SAML Authentication:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di RStudio Connect SAML Authentication](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<example.com>/__login__/saml`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<example.com>/__login__/saml/acs`

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Caricamento dei metadati di RStudio Connect SAML Authentication](common/metadata-upload-additional-signon.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<example.com>/`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Tali valori si possono ricavare dall'indirizzo del server RStudio Connect SAML Authentication (`https://example.com` negli esempi precedenti). Per eventuali problemi, contattare il [team di supporto di RStudio Connect SAML Authentication](mailto:support@rstudio.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

6. L'applicazione RStudio Connect SAML Authentication prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. La schermata seguente illustra l'elenco degli attributi predefiniti in cui **nameidentifier** è associato a **user.userprincipalname**. L'applicazione RStudio Connect SAML Authentication prevede il mapping di **nameidentifier** a **user.mail**, di conseguenza per modificare il mapping degli attributi, è necessario fare clic sull'icona **Modifica**.

    ![image](common/edit-attribute.png)

7. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a RStudio Connect SAML Authentication.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **RStudio Connect SAML Authentication**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-rstudio-connect-saml-authentication-sso"></a>Configurare l'accesso Single Sign-On di RStudio Connect SAML Authentication

Per configurare l'accesso Single Sign-On per **RStudio Connect SAML Authentication**, è necessario usare l'**URL dei metadati di federazione dell'app** e l'**indirizzo del server** usati sopra. Questa operazione viene effettuata nel file di configurazione di RStudio Connect SAML Authentication in `/etc/rstudio-connect.rstudio-connect.gcfg`.

Ecco un file di configurazione di esempio:

```
[Server]
SenderEmail =

; Important! The user-facing URL of your RStudio Connect SAML Authentication server.
Address = 

[Http]
Listen = :3939

[Authentication]
Provider = saml

[SAML]
Logging = true

; Important! The URL where your IdP hosts the SAML metadata or the path to a local copy of it placed in the RStudio Connect SAML Authentication server.
IdPMetaData = 

IdPAttributeProfile = azure
SSOInitiated = IdPAndSP
```

Archiviare l'**indirizzo del server** nel valore `Server.Address` e l'**URL dei metadati di federazione dell'app** nel valore `SAML.IdPMetaData`. Tenere presente che questa configurazione di esempio usa una connessione HTTP non crittografata, mentre Azure AD richiede l'uso di una connessione HTTPS crittografata. È possibile usare un [proxy inverso](https://docs.rstudio.com/connect/admin/proxy/) prima di RStudio Connect SAML Authentication o configurare RStudio Connect SAML Authentication in modo da [usare direttamente HTTPS](https://docs.rstudio.com/connect/admin/appendix/configuration/#HTTPS). 

In caso di problemi con la configurazione, è possibile vedere [RStudio Connect SAML Authentication Admin Guide](https://docs.rstudio.com/connect/admin/authentication/saml/) (Guida dell'amministratore di RStudio Connect SAML Authentication) o inviare un messaggio di posta elettronica al [team di supporto di RStudio](mailto:support@rstudio.com) per richiedere assistenza.

### <a name="create-rstudio-connect-saml-authentication-test-user"></a>Creare l'utente di test di RStudio Connect SAML Authentication

In questa sezione viene creato un utente di nome Britta Simon in RStudio Connect SAML Authentication. RStudio Connect SAML Authentication supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in RStudio Connect SAML Authentication, ne viene creato uno nuovo quando si prova ad accedere a RStudio Connect SAML Authentication.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di RStudio Connect SAML Authentication, in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso di RStudio Connect SAML Authentication e avviare il flusso di accesso da questa posizione.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Dopo aver fatto clic su **Test this application** (Testa questa applicazione) nel portale di Azure, si dovrebbe accedere automaticamente all'istanza di RStudio Connect SAML Authentication per cui si è configurato l'accesso SSO 

È anche possibile usare il pannello di accesso Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro di RStudio Connect SAML Authentication nel pannello di accesso, se è stato configurato in modalità SP, si dovrebbe essere reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso; se invece è configurato in modalità IDP, si dovrebbe accedere automaticamente all'istanza di RStudio Connect SAML Authentication per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato RStudio Connect SAML Authentication, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).