---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Firmex VDR | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Firmex VDR.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/21/2020
ms.author: jeedes
ms.openlocfilehash: 6dbd39b5c56192ad2ca957c5500338b50e8c8963
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92453385"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-firmex-vdr"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Firmex VDR

Questa esercitazione descrive come integrare Firmex VDR con Azure Active Directory (Azure AD). Integrando Firmex VDR con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Firmex VDR.
* Abilitare gli utenti per l'accesso automatico a Firmex VDR con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Firmex VDR abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Firmex VDR supporta l'accesso SSO avviato da **SP e IDP**

* Dopo aver configurato Firmex, è possibile applicare i controlli sessione che consentono di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. I controlli sessione costituiscono un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-firmex-vdr-from-the-gallery"></a>Aggiunta di Firmex VDR dalla raccolta

Per configurare l'integrazione di Firmex VDR in Azure AD, è necessario aggiungere Firmex VDR dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory** .
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni** .
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** .
1. Nella sezione **Aggiungi dalla raccolta** digitare **Firmex VDR** nella casella di ricerca.
1. Selezionare **Firmex VDR** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-firmex-vdr"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Firmex VDR

Configurare e testare l'accesso SSO di Azure AD con Firmex VDR usando un utente di test di nome **B.Simon** . Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Firmex VDR.

Per configurare e testare l'accesso SSO di Azure AD con Firmex VDR, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Firmex VDR](#configure-firmex-vdr-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di Firmex VDR](#create-firmex-vdr-test-user)** : per avere una controparte di B.Simon in Firmex VDR collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Firmex VDR** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On** .
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML** .
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** l'utente non deve eseguire alcuna operazione perché l'app è già preintegrata in Azure.

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP** :

    Nella casella di testo **URL di accesso** digitare l'URL: `https://login.firmex.com`

1. Fare clic su **Salva** .

1. L'applicazione Firmex VDR prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre quelli elencati in precedenza, l'applicazione Firmex VDR prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | Nome | Attributo di origine|
    | ------------ | --------- |
    | email | user.mail |

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura Firmex VDR** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Firmex VDR.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni** .
1. Nell'elenco delle applicazioni selezionare **Firmex VDR** .
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi** .

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione** .

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna** .

## <a name="configure-firmex-vdr-sso"></a>Configurare l'accesso Single Sign-On di Firmex VDR

### <a name="before-you-get-started"></a>Prima di iniziare

#### <a name="what-youll-need"></a>Prerequisiti

-   Una sottoscrizione attiva di Firmex
-   Azure AD impostato come servizio SSO
-   Amministratore IT per configurare l'accesso SSO
-   Dopo l'abilitazione dell'accesso SSO, tutti gli utenti aziendali devono accedere a Firmex tramite SSO e non usando un account di accesso/password.

#### <a name="how-long-will-this-take"></a>Tempo richiesto

L'implementazione dell'accesso SSO richiede alcuni minuti. Il tempo che intercorre tra l'abilitazione dell'accesso SSO per il sito da parte del supporto di Firmex e l'autenticazione degli utenti aziendali tramite SSO è irrisorio. Basta seguire questa procedura.

### <a name="step-1---identify-your-companys-domains"></a>Passaggio 1: identificare i domini aziendali

Identificare i domini con cui gli utenti aziendali eseguono l'accesso.

Ad esempio:

- @firmex.com
- @firmex.ca

### <a name="step-2---contact-firmex-support-with-your-domains"></a>Passaggio 2: contattare il supporto di Firmex per comunicare i domini

Inviare un messaggio di posta elettronica al [team di supporto di Firmex](mailto:support@firmex.com) oppure chiamare il numero 1888 688 4042 x.11 per parlare con il supporto. Comunicare le informazioni sui domini. Il supporto di Firmex aggiungerà i domini a VDR come **domini richiesti** . A questo punto l'amministratore deve configurare l'accesso SSO.

Avviso: fino a quando l'amministratore del sito non configura i domini richiesti, gli utenti aziendali non potranno accedere a VDR. Gli utenti non aziendali, ovvero gli utenti guest, possono comunque accedere usando l'indirizzo di posta elettronica e la password. La configurazione dovrebbe richiedere qualche minuto.

### <a name="step-3---configure-the-claimed-domains"></a>Passaggio 3: configurare i domini richiesti

1. Accedere a Firmex come amministratore del sito.
1. Nell'angolo in alto a sinistra fare clic sul logo della società.
1. Selezionare la scheda **SSO** . Selezionare quindi **SSO Configuration** (Configurazione SSO). Fare clic sul dominio da configurare.

    ![Domini richiesti](./media/firmex-vdr-tutorial/edit-sso.png)  

1. Chiedere all'amministratore IT di compilare i campi seguenti. I valori dei campi devono essere ricavati dal provider di identità:  

    ![SSO Configuration](./media/firmex-vdr-tutorial/SSO-config.png)

    a. Nella casella di testo **Entity ID** (ID entità) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    b. Nella casella di testo **Identity Provider URL** (URL del provider di identità) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    c. **Public Key Certificate** (Certificato chiave pubblica): ai fini dell'autenticazione, un messaggio SAML può essere firmato digitalmente dall'autorità di certificazione. Per verificare la firma del messaggio, il destinatario del messaggio usa una chiave pubblica che appartiene sicuramente all'autorità di certificazione. Analogamente, per crittografare un messaggio, l'autorità di certificazione deve conoscere una chiave di crittografia pubblica appartenente al destinatario finale. In entrambe le situazioni, ovvero la firma e la crittografia, le chiavi pubbliche attendibili devono essere condivise in anticipo.  Si tratta del valore **X509Certificate** (Certificato X509) incluso nel file **XML dei metadati della federazione**

    d. Fare clic su **Save** (Salva) per completare la configurazione dell'accesso SSO. Le modifiche hanno effetto immediato.

1. A questo punto, l'accesso SSO è abilitato per il sito.

### <a name="create-firmex-vdr-test-user"></a>Creare l'utente di test di Firmex VDR

In questa sezione viene creato un utente di nome B.Simon in Firmex. Collaborare con il [team di supporto di Firmex](mailto:support@firmex.com) per aggiungere gli utenti alla piattaforma Firmex. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Firmex VDR nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Firmex VDR per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)

- [Provare Firmex VDR con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad)

- [Come proteggere Firmex con visibilità e controlli avanzati](/cloud-app-security/proxy-intro-aad)