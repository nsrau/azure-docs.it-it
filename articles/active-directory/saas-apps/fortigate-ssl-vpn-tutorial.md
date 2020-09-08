---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con FortiGate SSL VPN | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e FortiGate SSL VPN.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 18a3d9d5-d81c-478c-be7e-ef38b574cb88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecb53d661b1171f9c1b18d37d0bb35952645ba7e
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299712"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortigate-ssl-vpn"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con FortiGate SSL VPN

Questa esercitazione descrive come integrare FortiGate SSL VPN con Azure Active Directory (Azure AD). Integrando FortiGate SSL VPN con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a FortiGate SSL VPN.
* Abilitare gli utenti per l'accesso automatico a FortiGate SSL VPN con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di FortiGate SSL VPN abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* FortiGate SSL VPN supporta l'accesso SSO avviato da **SP**
* Dopo aver configurato FortiGate SSL VPN, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-fortigate-ssl-vpn-from-the-gallery"></a>Aggiunta di FortiGate SSL VPN dalla raccolta

Per configurare l'integrazione di FortiGate SSL VPN in Azure AD, è necessario aggiungere FortiGate SSL VPN dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **FortiGate SSL VPN** nella casella di ricerca.
1. Selezionare **FortiGate SSL VPN** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-fortigate-ssl-vpn"></a>Configurare e testare l'accesso SSO di Azure AD per FortiGate SSL VPN

Configurare e testare l'accesso SSO di Azure AD con FortiGate SSL VPN usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in FortiGate SSL VPN.

Per configurare e testare l'accesso SSO di Azure AD con FortiGate SSL VPN, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di FortiGate SSL VPN](#configure-fortigate-ssl-vpn-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **Creare l'utente di test di FortiGate SSL VPN** : per avere una controparte di B.Simon in FortiGate SSL VPN collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **FortiGate SSL VPN** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella pagina **Configura l'accesso Single Sign-On con SAML** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<FQDN>/remote/login`

    b. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<FQDN>/remote/saml/metadata`

    c. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://><FQDN/remote/saml/login`

    d. Nella casella di testo **URL di disconnessione** digitare un URL nel formato seguente: `https://<FQDN>/remote/saml/logout`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con quelli effettivi relativi a URL di accesso, identificatore, URL di risposta e URL di disconnessione. Per ottenere questi valori contattare il [team di supporto clienti di FortiGate SSL VPN](mailto:tac_amer@fortinet.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione FortiGate SSL VPN prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre quelli elencati in precedenza, l'applicazione FortiGate SSL VPN prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.
    
    | Nome |  Attributo di origine|
    | ------------ | --------- |
    | username | user.userprincipalname |
    | gruppo | user.groups |

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura FortiGate SSL VPN** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a FortiGate SSL VPN.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **FortiGate SSL VPN**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-a-security-group-for-the-test-user"></a>Creare un gruppo di sicurezza per l'utente test

In questa sezione verrà creato un gruppo di sicurezza in Azure Active Directory per l'utente test. Questo gruppo di sicurezza verrà usato da FortiGate per concedere all'utente l'accesso alla rete tramite la VPN.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory** e quindi selezionare **Gruppi**.
1. Selezionare **Nuovo gruppo** nella parte superiore della schermata.
1. Nelle proprietà per **Nuovo gruppo** seguire questa procedura:
   1. Nel campo **Tipo di gruppo** selezionare **Sicurezza**.
   1. Nel campo **Nome** immettere `FortiGateAccess`.
   1. Immettere `Group for granting FortiGate VPN access` nel campo **Descrizione gruppo**.
   1. Per le impostazioni **I ruoli di Azure AD possono essere assegnati al gruppo (anteprima)** selezionare **No**.
   1. Nel campo **Tipo di appartenenza** selezionare **Assegnato**.
   1. In **Membri** selezionare **Nessun membro selezionato**.
   1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
   1. Selezionare **Crea**.
1. Dopo essere tornati al riquadro **Gruppi** in Azure Active Directory, individuare il gruppo **FortiGateAccess** e prendere nota dell'**ID oggetto** per usarlo in seguito.

## <a name="configure-fortigate-ssl-vpn-sso"></a>Configurare l'accesso Single Sign-On di FortiGate SSL VPN

### <a name="upload-the-base64-saml-certificate-to-the-fortigate-appliance"></a>Caricare il certificato SAML Base64 nell'appliance FortiGate

Dopo aver completato la configurazione SAML dell'app FortiGate nel tenant, è stato scaricato il certificato SAML con codifica Base64. Questo certificato deve essere caricato nell'appliance FortiGate:

1. Accedere al portale di gestione dell'appliance FortiGate.
1. Scegliere **System** (Sistema) dal menu a sinistra.
1. In **System** (Sistema) fare clic su **Certificates** (Certificati).
1. Fare clic su **Import** -> **Remote Certificate** (Importa, Certificato remoto).
1. Passare al certificato scaricato dalla distribuzione dell'app FortiGate nel tenant di Azure, selezionarlo e fare clic su **OK**

Al termine del caricamento del certificato, prendere nota del nome in **System** > **Certificates** > **Remote Certificate** (Sistema, Certificati, Certificato remoto). Per impostazione predefinita, il nome sarà REMOTE_Cert_**N** dove **N** è un numero intero.

### <a name="perform-fortigate-command-line-configuration"></a>Eseguire la configurazione della riga di comando di FortiGate

La procedura seguente richiede la configurazione dell'URL di disconnessione di Azure. Questo URL contiene un punto interrogativo (?). Per inviare correttamente questo carattere, sono necessari passaggi speciali. Non è possibile eseguire questi passaggi dalla console dell'interfaccia della riga di comando di FortiGate. Stabilire invece una sessione SSH con l'appliance FortiGate usando uno strumento come PuTTY. Se l'appliance FortiGate è una macchina virtuale di Azure, è possibile eseguire la procedura seguente dalla console seriale della macchina virtuale di Azure.

Per eseguire questi passaggi, saranno necessari i valori registrati in precedenza:

- ID entità
- URL di risposta
- URL di chiusura sessione
- URL di accesso ad Azure
- Identificatore di Azure AD
- URL di disconnessione da Azure
- Nome del certificato SAML Base64 (REMOTE_Cert_N)

1. Stabilire una sessione SSH con l'appliance FortiGate e accedere con un account amministratore di FortiGate.
1. Eseguire i comandi seguenti:

   ```console
    config user saml
    edit azure
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert <Base64 SAML Certificate Name>
    set user-name username
    set group-name group
    end

   ```

   > [!NOTE]
   > L'**URL di disconnessione da Azure** contiene un carattere `?`. È necessario immettere una sequenza di tasti speciale per passare correttamente l'URL alla console seriale di FortiGate. L'URL è in genere `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`.
   >
   > Per immettere l'URL di disconnessione di Azure nella console seriale, immettere `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation`.
   > 
   > Selezionare quindi CTRL+V e incollare il resto dell'URL per completare la riga: `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`.

### <a name="configure-fortigate-for-group-matching"></a>Configurare FortiGate per la corrispondenza dei gruppi

In questa sezione FortiGate verrà configurato per riconoscere l'ID oggetto del gruppo di sicurezza in cui si trova l'utente test. Questo consentirà a FortiGate di prendere decisioni di accesso in base a questa appartenenza al gruppo.

Per eseguire questa procedura, sarà necessario l'ID oggetto del gruppo di sicurezza **FortiGateAccess** creato in precedenza

1. Stabilire una sessione SSH con l'appliance FortiGate e accedere con un account amministratore di FortiGate.
1. Eseguire i comandi seguenti:

   ```
    config user group
    edit FortiGateAccess
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <Object Id>
    next
    end
    next
    end
   ```

### <a name="create-fortigate-vpn-portals-and-firewall-policy"></a>Creare portali VPN e criteri firewall per FortiGate

In questa sezione vengono configurati i portali VPN e i criteri firewall per FortiGate, che concedono l'accesso al gruppo di sicurezza **FortiGateAccess** creato in precedenza.

Collaborare con il  [team di supporto di FortiGate](mailto:tac_amer@fortinet.com) per aggiungere i portali VPN e i criteri firewall alla piattaforma VPN FortiGate. Questa procedura deve essere completata prima di usare Single Sign-On.

## <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di FortiGate SSL VPN nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di FortiGate SSL VPN per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

Microsoft e FortiGate consigliano di usare il client VPN Fortinet FortiClient per un'esperienza utente ottimale.

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare FortiGate SSL VPN con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
