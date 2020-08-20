---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Trend Micro Web Security (TMWS)"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Trend Micro Web Security (TMWS).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/21/2020
ms.author: jeedes
ms.openlocfilehash: f2a6598cc28c39719d73be333bd74c24fce9371b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551899"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trend-micro-web-security-tmws"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Trend Micro Web Security (TMWS)

Questa esercitazione descrive come integrare Trend Micro Web Security (TMWS) con Azure Active Directory (Azure AD). Integrando TMWS con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a TMWS.
* Abilitare gli utenti per l'accesso automatico a TMWS con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di TMWS abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* TMWS supporta l'accesso SSO avviato da SP.
* Dopo aver configurato TMWS, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. Per informazioni su come applicare il controllo sessione con Microsoft Cloud App Security, vedere [Eseguire l'onboarding e distribuire Controllo app per l'accesso condizionale per qualsiasi app](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-tmws-from-the-gallery"></a>Aggiungere TMWS dalla raccolta

Per configurare l'integrazione di TMWS in Azure AD, è necessario aggiungere TMWS dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro sinistro selezionare il servizio **Azure Active Directory**.
1. Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** immettere **Trend Micro Web Security (TMWS)** nella casella di ricerca.
1. Selezionare **Trend Micro Web Security (TMWS)** nei risultati della ricerca e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-tmws"></a>Configurare e testare l'accesso SSO di Azure AD per TMWS

Verrà configurato e testato l'accesso SSO di Azure AD con TMWS usando un utente di test di nome B.Simon. Per il corretto funzionamento dell'accesso Single Sign-On, è necessario stabilire un collegamento tra un utente di Azure AD e l'utente correlato in TMWS.

Per configurare e testare l'accesso SSO di Azure AD con TMWS, è necessario completare le procedure di base seguenti:

1. [Configurare l'accesso SSO di Azure AD](#configure-azure-ad-sso) per abilitare la funzionalità per gli utenti.
    1. [Creare un utente di Azure AD](#create-an-azure-ad-test-user) per testare l'accesso Single Sign-On di Azure AD.
    1. [Concedere all'utente di test di Azure AD](#grant-the-azure-ad-test-user-access-to-tmws) l'accesso a TMWS.
    1. [Configurare le impostazioni di sincronizzazione di utenti e gruppi in Azure AD](#configure-user-and-group-synchronization-settings-in-azure-ad).
1. [Configurare l'accesso SSO di TMWS](#configure-tmws-sso) sul lato applicazione.
1. [Testare l'accesso SSO](#test-sso) per verificare la configurazione.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, completare questa procedura.

1. Nella pagina di integrazione dell'applicazione **Trend Micro Web Security (TMWS)** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante a forma di penna relativo a **Configurazione SAML di base** per modificare le impostazioni:

   ![Modificare le impostazioni di Configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori nelle caselle seguenti:

    a. Nella casella di testo **Identificatore (ID entità)** immettere un URL nel formato seguente:

    `https://auth.iws-hybrid.trendmicro.com/([0-9a-f]{16})`

    b. Nella casella **URL di risposta** immettere l'URL seguente:

    `https://auth.iws-hybrid.trendmicro.com/simplesaml/module.php/saml/sp/saml2-acs.php/ics-sp`

    > [!NOTE]
    > Il valore dell'identificatore nel passaggio precedente non è il valore da immettere, ma deve essere sostituito dall'identificatore effettivo. È possibile ottenere questo valori nella sezione **Service Provider Settings for the Azure Admin Portal** (Impostazioni del provider di servizi per il portale di amministrazione di Azure) nella pagina **Authentication Method** (Metodo di autenticazione) per Azure AD visualizzata facendo clic su **Administration > Directory Services** (Amministrazione > Servizi di directory).

1. TMWS prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Questo screenshot mostra gli attributi predefiniti:

    ![Attributi predefiniti](common/default-attributes.png)

1. Oltre a quelli indicati nello screenshot precedente, TMWS prevede il passaggio di altri due attributi nella risposta SAML. Tali attributi sono illustrati nella tabella seguente. Gli attributi vengono prepopolati, ma è possibile modificarli in base alle esigenze.
    
    | Nome | Attributo di origine|
    | --------------- | --------- |
    | sAMAccountName | user.onpremisessamaccountname |
    | uPN | user.userprincipalname |

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** . Selezionare il collegamento **Scarica** accanto al nome di questo certificato per scaricare il certificato e salvarlo nel computer:

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Trend Micro Web Security (TMWS)** copiare gli URL appropriati in base alle esigenze:

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**. Selezionare **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nella casella **Nome** immettere `B.Simon`.  
   1. Nella casella **Nome utente** immettere ***nomeutente *@* dominioaziendale *.* estensione***. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Selezionare **Create** (Crea).

### <a name="grant-the-azure-ad-test-user-access-to-tmws"></a>Concedere all'utente di test di Azure AD l'accesso a TMWS

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a TMWS.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Trend Micro Web Security (TMWS)** .
1. Nella sezione **Gestione** della pagina di panoramica dell'app selezionare **Utenti e gruppi**:

   ![Selezionare Utenti e gruppi](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Selezionare Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** nell'elenco **Utenti** e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

### <a name="configure-user-and-group-synchronization-settings-in-azure-ad"></a>Configurare le impostazioni di sincronizzazione di utenti e gruppi in Azure AD

1. Nel riquadro sinistro selezionare **Azure Active Directory**.

1. In **Gestione** selezionare **Registrazioni app** e quindi fare clic sulla nuova applicazione aziendale in **Tutte le applicazioni**.

1. In **Gestisci**, selezionare **Certificati e segreti**.

1. Nell'area **Segreti client** selezionare **Nuovo segreto client**.

1. Nella schermata **Aggiungi un segreto client** aggiungere facoltativamente una descrizione e selezionare un periodo di scadenza per il segreto client e quindi fare clic su **Aggiungi**. Il nuovo segreto client viene visualizzato nell'area **Segreti client**.

1. Prendere nota del valore del segreto client perché successivamente sarà necessario immetterlo in TMWS.

1. In **Gestisci** selezionare **Autorizzazioni API**. 

1. Nella finestra **Autorizzazioni API** selezionare **Aggiungi un'autorizzazione**.

1. Nella scheda **API Microsoft** della finestra **Richiedi le autorizzazioni dell'API** selezionare **Microsoft Graph** e quindi **Autorizzazioni applicazione**.

1. Individuare e aggiungere le autorizzazioni seguenti: 

    * Group.Read.All
    * User.Read.All

1. Selezionare **Aggiungi autorizzazioni**. Viene visualizzato un messaggio per confermare che le impostazioni sono state salvate. Le nuove autorizzazioni vengono visualizzate nella finestra **Autorizzazioni API**.

1. Nell'area **Fornisci il consenso** selezionare **Concedi consenso amministratore per *account amministratore* (Directory predefinita)** e quindi **Sì**. Viene visualizzato un messaggio per confermare che l'amministratore ha concesso il consenso per le autorizzazioni richieste.

1. Selezionare **Panoramica**. 

1. Prendere nota dell'**ID applicazione (client)** e dell'**ID directory (tenant)** visualizzati nel riquadro destro perché successivamente sarà necessario immetterli in TMWS. È anche possibile selezionare **Nomi di dominio personalizzati** in **Azure Active Directory > Gestisci** e prendere nota del nome di dominio visualizzato nel riquadro destro.

## <a name="configure-tmws-sso"></a>Configurare l'accesso SSO di TMWS

Completare questa procedura per configurare l'accesso SSO di TMWS sul lato applicazione.

1. Accedere alla console di gestione di TMWS e passare ad **Administration** > **USERS & AUTHENTICATION** > **Directory Services** (Amministrazione > UTENTI E AUTENTICAZIONE > Servizi di directory).

1. Selezionare **here** (qui) nell'area superiore della schermata.

1. Nella pagina **Authentication Method** (Metodo di autenticazione) selezionare **Azure AD**.

1. Fare clic su **On** (Sì) o **Off** (No) per scegliere se consentire agli utenti di Azure AD dell'organizzazione di visitare i siti Web tramite TMWS quando i relativi dati non sono sincronizzati con TMWS.

    > [!NOTE]
    > Gli utenti che non sono sincronizzati da Azure AD possono essere autenticati solo tramite gateway TMWS noti o la porta dedicata per l'organizzazione.

1. Nella sezione **Identity Provider Settings** (Impostazioni provider di identità) completare questa procedura:

    a. Nella casella **Service URL** (URL del servizio) immettere il valore di **URL di accesso** copiato dal portale di Azure.

    b. Nella casella **Logon name attribute** (Attributo nome di accesso) immettere il **nome dell'attestazione utente** con l'attributo di origine **user.onpremisessamaccountname** del portale di Azure.

    c. Nella casella **Public SSL certificate** (Certificato SSL pubblico) usare il **certificato (Base64)** scaricato dal portale di Azure.

1. Nella sezione **Synchronization Settings** (Impostazioni di sincronizzazione) completare questa procedura:

    a. Nella casella **Tenant** immettere il valore di **ID della directory (tenant)** o di **Nome dominio personalizzato** del portale di Azure.

    b. Nella casella **Application ID** (ID applicazione) immettere il valore di **ID applicazione (client)** del portale di Azure.

    c. Nella casella **Client secret** (Segreto client) immettere il valore di **Segreto client** del portale di Azure.

    d. Selezionare **Synchronization schedule** (Pianificazione sincronizzazione) per eseguire la sincronizzazione con Azure AD manualmente o in base a una pianificazione. Se si seleziona **Manually** (Manualmente), ogni volta che vengono apportate modifiche alle informazioni degli utenti di Active Directory, ricordarsi di tornare alla pagina **Directory Services** (Servizi di directory) ed eseguire la sincronizzazione manuale in modo che le informazioni in TMWS rimangano aggiornate.

    e. Selezionare **Test Connection** (Testa connessione) per verificare se la connessione al servizio Azure AD viene eseguita correttamente.
    
    f. Selezionare **Save** (Salva).
 
 > [!NOTE]
 > Per altre informazioni su come configurare TMWS con Azure AD, vedere l'articolo sulla [configurazione delle impostazioni di Azure AD in TMWS](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/directory-services/azure-active-directo/configuring-azure-ad.aspx).

## <a name="test-sso"></a>Testare l'accesso SSO 

Dopo aver configurato il servizio Azure AD e aver specificato Azure AD come metodo di autenticazione utente, è possibile accedere al server proxy TMWS per verificare la configurazione. Dopo la verifica dell'account all'accesso di Azure AD, è possibile visitare siti Internet.

> [!NOTE]
> In TMWS non è possibile testare l'accesso Single Sign-On dal portale di Azure AD, in **Panoramica** > **Single Sign-On** > **Configura l'accesso Single Sign-On con SAML** > **Test** della nuova applicazione aziendale.

1. Cancellare tutti i cookie nel browser e quindi riavviarlo. 

1. Impostare il browser in modo che punti al server proxy TMWS. Per informazioni dettagliate, vedere l'articolo sull'[inoltro del traffico con file PAC](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/pac-files/traffic-forwarding-u.aspx#GUID-A4A83827-7A29-4596-B866-01ACCEDCC36B).

1. Visitare qualsiasi sito Web Internet. TMWS reindirizzerà l'utente al portale TMWS di tipo Captive.

1. Specificare un account Active Directory (formato: *dominio*\\*nomeaccountSAM* o *nomeaccountSAM*@*dominio*), indirizzo di posta elettronica o UPN e quindi selezionare **Log On** (Accedi). TMWS reindirizza l'utente alla finestra di accesso di Azure AD.

1. Nella finestra di accesso di Azure AD immettere le credenziali dell'account Azure AD. Ora dovrebbe essere stato effettuato l'accesso TMWS.

## <a name="additional-resources"></a>Risorse aggiuntive

- [Esercitazioni su come integrare le app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Trend Micro Web Security con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Come proteggere Trend Micro Web Security con visibilità e controlli avanzati](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

