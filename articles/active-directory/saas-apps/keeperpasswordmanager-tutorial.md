---
title: 'Esercitazione: Integrazione di Azure Active Directory con Keeper Password Manager & Digital Vault | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Keeper Password Manager & Digital Vault.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/13/2020
ms.author: jeedes
ms.openlocfilehash: 88f84fba43959ee5e5b8d93446e4985a75697813
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94685869"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>Esercitazione: Integrazione di Azure Active Directory con Keeper Password Manager & Digital Vault

Questa esercitazione descrive come integrare Keeper Password Manager & Digital Vault con Azure Active Directory (Azure AD).
L'integrazione di Keeper Password Manager & Digital Vault con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Keeper Password Manager & Digital Vault.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Keeper Password Manager & Digital Vault con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.


## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Keeper Password Manager & Digital Vault sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Keeper Password Manager & Digital Vault abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Keeper Password Manager & Digital Vault supporta l'accesso SSO avviato da **SP**

* Keeper Password Manager & Digital Vault supporta il provisioning utenti **JIT**

## <a name="adding-keeper-password-manager--digital-vault-from-the-gallery"></a>Aggiunta di Keeper Password Manager & Digital Vault dalla raccolta

Per configurare l'integrazione di Keeper Password Manager & Digital Vault in Azure AD è necessario aggiungere Keeper Password Manager & Digital Vault dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Keeper Password Manager & Digital Vault** nella casella di ricerca.
1. Selezionare **Keeper Password Manager & Digital Vault** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-keeper-password-manager--digital-vault"></a>Configurare e testare l'accesso SSO di Azure AD per Keeper Password Manager & Digital Vault

Configurare e testare l'accesso SSO di Azure AD con Keeper Password Manager & Digital Vault usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Keeper Password Manager & Digital Vault.

Per configurare e testare l'accesso SSO di Azure AD con Keeper Password Manager & Digital Vault, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.

    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.

1. **[Configurare l'accesso Single Sign-On per Keeper Password Manager & Digital Vault](#configure-keeper-password-manager--digital-vault-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di Keeper Password Manager & Digital Vault](#create-keeper-password-manager--digital-vault-test-user)**: per avere una controparte di Britta Simon in Keeper Password Manager & Digital Vault collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Keeper Password Manager & Digital Vault** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: 
    * Per l'**accesso SSO cloud**: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Per l'**accesso SSO locale**: `https://<KEEPER_FQDN>/sso-connect/saml/login`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente:
    * Per l'**accesso SSO cloud**: `https://keepersecurity.com/api/rest/sso/saml/<CLOUD_INSTANCE_ID>`
    * Per l'**accesso SSO locale**: `https://<KEEPER_FQDN>/sso-connect`

    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: 
    * Per l'**accesso SSO cloud**: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Per l'**accesso SSO locale**: `https://<KEEPER_FQDN>/sso-connect/saml/sso`

    > [!NOTE]
    > Poiché questi non sono i valori reali, Aggiornarli con l'URL di accesso, l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori contattare il [team di supporto clienti di Keeper Password Manager & Digital Vault](https://keepersecurity.com/contact.html). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione Keeper Password Manager & Digital Vault prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre quelli elencati in precedenza, l'applicazione Keeper Password Manager & Digital Vault prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | Nome | Attributo di origine|
    | ------------| --------- |
    | First (Primo) | user.givenname |
    | Ultimi | user.surname |
    | Email | user.mail |

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura Keeper Password Manager & Digital Vault** copiare gli URL appropriati in base alle proprie esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Keeper Password Manager & Digital Vault.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Keeper Password Manager & Digital Vault**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.


## <a name="configure-keeper-password-manager--digital-vault-sso"></a>Configurare l'accesso Single Sign-On per Keeper Password Manager & Digital Vault

Per configurare l'accesso Single Sign-On sul lato **Configurazione Keeper Password Manager & Digital Vault**, seguire le istruzioni specificate nella [Guida per il supporto di Keeper](https://docs.keeper.io/sso-connect-guide/).

### <a name="create-keeper-password-manager--digital-vault-test-user"></a>Creare l'utente di test di Keeper Password Manager & Digital Vault

Per consentire agli utenti di Azure AD di accedere a Keeper Password Manager & Digital Vault, è necessario eseguire il provisioning degli utenti in Keeper Password Manager & Digital Vault. L'applicazione supporta il provisioning dell'utente just-in-time e dopo l'autenticazione gli utenti verranno automaticamente creati nell'applicazione. Per configurare manualmente gli utenti, contattare il [supporto di Keeper](https://keepersecurity.com/contact.html).

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Keeper Password Manager & Digital Vault, in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso di Keeper Password Manager & Digital Vault e avviare il flusso di accesso da questa posizione.

* È possibile usare il Pannello di accesso Microsoft. Quando si fa clic sul riquadro di Keeper Password Manager & Digital Vault nel pannello di accesso, si verrà reindirizzati all'URL di accesso di Keeper Password Manager & Digital Vault. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Keeper Password Manager & Digital Vault, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)