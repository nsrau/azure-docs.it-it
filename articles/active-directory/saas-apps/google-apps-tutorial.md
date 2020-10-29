---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Google Cloud (G Suite) Connector | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Google Cloud (G Suite) Connector.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: jeedes
ms.openlocfilehash: 9a5cb1e589481bb424507d08879da8cc1b14ff1c
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92448198"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-google-cloud-g-suite-connector"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Google Cloud (G Suite) Connector

Questa esercitazione descrive come integrare Google Cloud (G Suite) Connector con Azure Active Directory (Azure AD). Integrando Google Cloud (G Suite) Connector con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Google Cloud (G Suite) Connector.
* Abilitare gli utenti per l'accesso automatico a Google Cloud (G Suite) Connector con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

- Una sottoscrizione di Azure AD.
- Sottoscrizione di Google Cloud (G Suite) Connector abilitata per l'accesso Single Sign-On (SSO).
- Sottoscrizione di Google Apps o sottoscrizione di Google Cloud Platform

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione. Questo documento è stato creato usando la nuova esperienza utente Single Sign-On. Se si sta ancora usando quella precedente, la configurazione risulterà diversa. È possibile abilitare la nuova esperienza nelle impostazioni Single Sign-On dell'applicazione G Suite. Passare ad **Azure AD, Applicazioni aziendali** , selezionare **Google Cloud (G Suite) Connector** , quindi **Single Sign-On** e fare clic su **Prova la nuova esperienza** .

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).

## <a name="frequently-asked-questions"></a>Domande frequenti

1. **D: Questa integrazione supporta l'integrazione SSO di Google Cloud Platform con Azure AD?**

    R: Sì. Google Cloud Platform e Google Apps condividono la stessa piattaforma di autenticazione. Per eseguire l'integrazione di GCP, è quindi necessario configurare l'accesso SSO con Google Apps.

2. **D: I dispositivi Chromebooks e altri dispositivi Chrome sono compatibili con Single Sign-On di Azure AD?**
  
    R: Sì, gli utenti possono accedere ai dispositivi Chromebook con le credenziali di Azure AD. Per informazioni sui motivi per cui agli utenti può essere richiesto di immettere le credenziali due volte, vedere questo [articolo del supporto tecnico di Google Cloud (G Suite) Connector](https://support.google.com/chrome/a/answer/6060880).

3. **D: Se si abilita il Single Sign-On, gli utenti potranno usare le credenziali di Azure AD per accedere a qualsiasi prodotto di Google, ad esempio Google Classroom, GMail, Google Drive, YouTube e così via?**

    R: Sì, a seconda dell'istanza di [Google Cloud (G Suite) Connector](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) che si sceglie di abilitare o disabilitare per la propria organizzazione.

4. **D: È possibile abilitare l'accesso Single Sign-On solo per una parte degli utenti di Google Cloud (G Suite) Connector?**

    R: No, quando si attiva l'accesso Single Sign-On, tutti gli utenti di Google Cloud (G Suite) Connector devono immediatamente autenticarsi con le proprie credenziali di Azure AD. Poiché Google Cloud (G Suite) Connector non supporta più provider di identità, il provider di identità per l'ambiente di Google Cloud (G Suite) Connector può essere AD Azure o Google, ma non entrambi contemporaneamente.

5. **D: Se un utente ha eseguito l'accesso tramite Windows, viene autenticato automaticamente in Google Cloud (G Suite) Connector senza che venga richiesta una password?**

    R: Sono disponibili due opzioni per l'abilitazione di questo scenario. Nel primo caso gli utenti possono accedere ai dispositivi Windows 10 tramite [Aggiunta ad Azure Active Directory](../devices/overview.md). In alternativa, gli utenti possono accedere ai dispositivi Windows appartenenti a un dominio di Active Directory locale abilitato per il Single Sign-On in Azure AD tramite una distribuzione di [Active Directory Federation Services (AD FS)](../hybrid/plan-connect-user-signin.md). Per entrambe le opzioni è necessario seguire la procedura descritta nell'esercitazione seguente per abilitare l'accesso Single Sign-On tra Azure AD e Google Cloud (G Suite) Connector.

6. **D: Cosa occorre fare quando si riceve il messaggio di errore "L'indirizzo di posta elettronica non è valido"?**

    R: Per questa configurazione, l'attributo di posta elettronica viene richiesto agli utenti per effettuare l'accesso. Non è possibile impostare manualmente questo attributo.

    L'attributo di posta elettronica viene compilato automaticamente per tutti gli utenti con una licenza valida di Exchange. Se l'utente non è abilitato alla posta elettronica, si riceverà questo errore in quanto l'applicazione deve ottenere questo attributo per concedere l'accesso.

    È possibile passare a portal.office.com con un account amministratore, quindi fare clic su Fatturazione e Sottoscrizioni nell'interfaccia di amministrazione, selezionare l'abbonamento a Microsoft 365 e quindi fare clic su Assegna a utenti, selezionare gli utenti di cui si vuole controllare la sottoscrizione e nel riquadro destro fare clic su Modifica licenze.

    Dopo l'assegnazione, l'applicazione della licenza di Microsoft 365 potrebbero richiedere alcuni minuti. Successivamente, l'attributo user.mail sarà compilato automaticamente e il problema dovrebbe essere risolto.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Google Cloud (G Suite) Connector supporta l'accesso SSO avviato da **SP**

* Google Cloud (G Suite) Connector supporta il [provisioning utenti **automatico**](./google-apps-provisioning-tutorial.md)
* Dopo aver configurato Google Cloud (G Suite) Connector, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-google-cloud-g-suite-connector-from-the-gallery"></a>Aggiunta di Google Cloud (G Suite) Connector dalla raccolta

Per configurare l'integrazione di Google Cloud (G Suite) Connector in Azure AD, è necessario aggiungere Google Cloud (G Suite) Connector dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory** .
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni** .
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** .
1. Nella sezione **Aggiungi dalla raccolta** digitare **Google Cloud (G Suite) Connector** nella casella di ricerca.
1. Selezionare **Google Cloud (G Suite) Connector** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-google-cloud-g-suite-connector"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Google Cloud (G Suite) Connector

Configurare e testare l'accesso SSO di Azure AD con Google Cloud (G Suite) Connector usando un utente di test di nome **B.Simon** . Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Google Cloud (G Suite) Connector.

Per configurare e testare l'accesso SSO di Azure AD con Google Cloud (G Suite) Connector, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Google Cloud (G Suite) Connector](#configure-google-cloud-g-suite-connector-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Google Cloud (G Suite) Connector](#create-google-cloud-g-suite-connector-test-user)** : per avere una controparte di B.Simon in Google Cloud (G Suite) Connector collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Google Cloud (G Suite) Connector** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On** .
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML** .
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** , per eseguire la configurazione per **Gmail** , seguire questa procedura:

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. Nella casella di testo **Identificatore** digitare un URL nel formato seguente:

    ```http
    google.com/a/<yourdomain.com>
    google.com
    https://google.com
    https://google.com/a/<yourdomain.com>
    ```

    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: 

    ```http
    https://www.google.com
    https://www.google.com/a/<yourdomain.com>
    ```

1. Nella sezione **Configurazione SAML di base** , per eseguire la configurazione per **Google Cloud Platform** , seguire questa procedura:

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com`

    b. Nella casella di testo **Identificatore** digitare un URL nel formato seguente:
    
    ```http
    google.com/a/<yourdomain.com>
    google.com
    https://google.com
    https://google.com/a/<yourdomain.com>
    ```
    
    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: 
    
    ```http
    https://www.google.com
    https://www.google.com/a/<yourdomain.com>
    ```

    > [!NOTE]
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Google Cloud (G Suite) Connector non fornisce il valore di ID entità/Identificatore nella configurazione dell'accesso Single Sign-On, di conseguenza quando si deseleziona l'opzione **domain specific issuer** (certificazione specifica del dominio), il valore di Identificatore sarà `google.com`. Se si seleziona l'opzione **domain specific issuer** (certificazione specifica del dominio), il valore sarà `google.com/a/<yourdomainname.com>`. Per selezionare/deselezionare l'opzione **domain specific issuer** (certificazione specifica del dominio), è necessario passare alla sezione **Configurare l'accesso Single Sign-On di Google Cloud (G Suite) Connector** , descritta più avanti nell'esercitazione. Per altre informazioni, contattare il [team di supporto clienti di Google Cloud (G Suite) Connector](https://www.google.com/contact/).

1. L'applicazione Google Cloud (G Suite) Connector prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. La schermata seguente illustra un esempio relativo a questa operazione. Il valore predefinito di **Identificatore univoco dell'utente** è **user.userprincipalname** , ma Google Cloud (G Suite) Connector prevede che venga associato all'indirizzo di posta elettronica dell'utente. A tale scopo è possibile usare l'attributo **user.mail** dall'elenco oppure usare il valore di attributo appropriato in base alla configurazione dell'organizzazione.

    ![image](common/default-attributes.png)


1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Google Cloud (G Suite) Connector** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory** , **Utenti** e quindi **Tutti gli utenti** .
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password** .
   1. Fare clic su **Crea** .

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Google Cloud (G Suite) Connector.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni** .
1. Nell'elenco delle applicazioni selezionare **Google Cloud (G Suite) Connector** .
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi** .

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione** .

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna** .

## <a name="configure-google-cloud-g-suite-connector-sso"></a>Configurare l'accesso Single Sign-On di Google Cloud (G Suite) Connector

1. Aprire una nuova scheda nel browser e accedere alla [console di amministrazione di Google Cloud (G Suite) Connector](https://admin.google.com/) con l'account di amministratore.

2. Fare clic su **Security** . Se non viene visualizzato il collegamento, può essere nascosto sotto il menu **More Controls** nella parte inferiore della schermata.

    ![Fare clic su sicurezza.][10]

3. Nella pagina **Security** (Sicurezza) fare clic su **Set up single sign-on (SSO)** (Configurazione Single Sign-On (SSO)).

    ![Fare clic su SSO.][11]

4. Eseguire le seguenti modifiche di configurazione:

    ![Configurare SSL][12]

    a. Selezionare **Setup SSO with third party identity provider** (Configurare l'accesso SSO con un provider di terze parti).

    b. Nel campo **Sign-in page URL** (URL pagina di accesso) di Google Cloud (G Suite) Connector incollare il valore dell' **URL di accesso** copiato dal portale di Azure.

    c. Nel campo **Sign-out page URL** (URL pagina di disconnessione) di Google Cloud (G Suite) Connector incollare il valore dell' **URL di accesso** copiato dal portale di Azure.

    > [!NOTE]
    > Google Cloud (G Suite) si basa sul protocollo di disconnessione SAML. Quindi, nel campo **Sign-out page URL** (URL pagina di disconnessione) è necessario usare l'URL di disconnessione SAML, ovvero l'URL di accesso visto che il valore è lo stesso.

    d. In Google Cloud (G Suite) Connector per **Verification certificate** (Certificato di verifica) caricare il certificato scaricato dal portale di Azure.   

    e. Selezionare/deselezionare l'opzione **Use a domain specific issuer** (Usa un'autorità di certificazione specifica del dominio) in base alla nota menzionata della sezione **Configurazione SAML di base** in Azure AD.

    f. Nel campo **Change password URL** (URL di modifica della password) di Google Cloud (G Suite) Connector incollare il valore dell' **URL di modifica della password** copiato dal portale di Azure.

    g. Fare clic su **Salva** .

### <a name="create-google-cloud-g-suite-connector-test-user"></a>Creare l'utente di test di Google Cloud (G Suite) Connector

Questa sezione descrive come [creare in Google Cloud (G Suite) Connector](https://support.google.com/a/answer/33310?hl=en) un utente di nome B.Simon. Dopo che è stato creato manualmente in Google Cloud (G Suite) Connector, l'utente potrà accedere usando le credenziali di accesso di Microsoft 365.

Google Cloud (G Suite) Connector supporta anche il provisioning utenti automatico. Per configurare questa funzionalità, è prima necessario [configurare Google Cloud (G Suite) Connector per il provisioning utenti automatico](./google-apps-provisioning-tutorial.md).

> [!NOTE]
> Assicurarsi che l'utente esista già in Google Cloud (G Suite) Connector se non è stato attivato il provisioning in Azure AD prima del test dell'accesso Single Sign-on.

> [!NOTE]
> Se è necessario creare un utente manualmente, contattare il [team di supporto di Google](https://www.google.com/contact/).

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Google Cloud (G Suite) Connector nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Google Cloud (G Suite) Connector per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)

- [Configura provisioning utenti](./google-apps-provisioning-tutorial.md)

- [Provare Google Cloud (G Suite) Connector con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad)

- [Come proteggere Google Cloud (G Suite) Connector con visibilità e controlli avanzati](/cloud-app-security/protect-gsuite)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png