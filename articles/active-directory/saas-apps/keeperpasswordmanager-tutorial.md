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
ms.openlocfilehash: b70c50e7c2900f884dd4d91c6650205bc626326e
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96178042"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>Esercitazione: Integrazione di Azure Active Directory con Keeper Password Manager & Digital Vault

Questa esercitazione descrive come integrare Keeper Password Manager & Digital Vault con Azure Active Directory (Azure AD).
Questa integrazione offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Keeper Password Manager & Digital Vault.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Keeper Password Manager & Digital Vault con gli account Azure AD personali.
* È possibile gestire gli account da una posizione centrale: il portale di Azure.


## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Keeper Password Manager & Digital Vault sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere una [versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).
* Sottoscrizione di Keeper Password Manager & Digital Vault, abilitata per l'accesso Single Sign-On.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Keeper Password Manager & Digital Vault supporta l'accesso SSO avviato da SP.

* Keeper Password Manager & Digital Vault supporta il provisioning utenti JIT (Just-In-Time).

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Aggiungere Keeper Password Manager & Digital Vault dalla raccolta

Per configurare l'integrazione di Keeper Password Manager & Digital Vault in Azure AD, aggiungere l'applicazione dalla raccolta all'elenco di app SaaS (Software as a Service) gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro sinistro selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. In **Aggiungi dalla raccolta** digitare **Keeper Password Manager & Digital Vault** nella casella di ricerca.
1. Selezionare **Keeper Password Manager & Digital Vault** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-keeper-password-manager--digital-vault"></a>Configurare e testare l'accesso SSO di Azure AD per Keeper Password Manager & Digital Vault

Configurare e testare l'accesso SSO di Azure AD con Keeper Password Manager & Digital Vault usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Keeper Password Manager & Digital Vault.

Per configurare e testare l'accesso SSO di Azure AD con Keeper Password Manager & Digital Vault:

1. [Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso): per consentire agli utenti di usare questa funzionalità.

    * [Creare un utente di test di Azure AD](#create-an-azure-ad-test-user) per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
    * [Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user) per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.

1. [Configurare l'accesso Single Sign-On per Keeper Password Manager & Digital Vault](#configure-keeper-password-manager--digital-vault-sso) per configurare le impostazioni dell'accesso Single Sign-On sul lato applicazione.
    * [Creare un utente di test di Keeper Password Manager & Digital Vault](#create-a-keeper-password-manager--digital-vault-test-user) per avere una controparte di Britta Simon in Keeper Password Manager & Digital Vault collegata alla rappresentazione dell'utente in Azure AD.
1. [Testare l'accesso Single Sign-On](#test-sso): per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Keeper Password Manager & Digital Vault** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Screenshot della pagina Configura l'accesso Single Sign-On con SAML, in cui è evidenziata l'icona della matita.](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Per **URL di accesso** digitare un URL con il modello seguente:
    * Per l'accesso SSO cloud: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Per l'accesso SSO locale: `https://<KEEPER_FQDN>/sso-connect/saml/login`

    b. Per **Identificatore (ID entità)** digitare un URL con il modello seguente:
    * Per l'accesso SSO cloud: `https://keepersecurity.com/api/rest/sso/saml/<CLOUD_INSTANCE_ID>`
    * Per l'accesso SSO locale: `https://<KEEPER_FQDN>/sso-connect`

    c. Per **URL di risposta** digitare un URL con il modello seguente:
    * Per l'accesso SSO cloud: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Per l'accesso SSO locale: `https://<KEEPER_FQDN>/sso-connect/saml/sso`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'URL di accesso, l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori contattare il [team di supporto clienti di Keeper Password Manager & Digital Vault](https://keepersecurity.com/contact.html). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione Keeper Password Manager & Digital Vault prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![Screenshot di Attributi utente e attestazioni.](common/default-attributes.png)

1. L'applicazione Keeper Password Manager & Digital Vault prevede inoltre il passaggio di altri attributi nella risposta SAML, mostrati nella tabella seguente. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base alle esigenze.

    | Nome | Attributo di origine|
    | ------------| --------- |
    | First (Primo) | user.givenname |
    | Ultimi | user.surname |
    | Email | user.mail |

5. In **Configura l'accesso Single Sign-On con SAML** nella sezione **Certificato di firma SAML** selezionare **Scarica**. Il file **XML metadati federazione** verrà scaricato dalle opzioni in base al requisito specifico e verrà salvato nel computer.

    ![Screenshot di Certificato di firma SAML con Scarica evidenziato.](common/metadataxml.png)

6. In **Configura Keeper Password Manager & Digital Vault** copiare l'URL appropriato in base alle proprie esigenze.

    ![Screenshot di Configura Keeper Password Manager & Digital Vault con URL evidenziati.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD 

In questa sezione viene creato un utente di test nel portale di Azure denominato `B.Simon`.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Per **Nome** immettere `B.Simon`.  
   1. Per **Nome utente** immettere `username@companydomain.extension`. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare **Mostra password** e prendere nota del valore visualizzato.
   1. Selezionare **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Keeper Password Manager & Digital Vault.

1. Nel portale di Azure selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Keeper Password Manager & Digital Vault**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente**. In **Aggiungi assegnazione** selezionare **Utenti e gruppi**.
1. In **Utenti e gruppi** selezionare **B.Simon** nell'elenco degli utenti. Scegliere quindi **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è **Accesso predefinito**.
1. In **Aggiungi assegnazione** selezionare **Assegna**.


## <a name="configure-keeper-password-manager--digital-vault-sso"></a>Configurare l'accesso Single Sign-On per Keeper Password Manager & Digital Vault

Per configurare l'accesso Single Sign-On per l'app, vedere le linee guida nella [guida al supporto tecnico di Keeper](https://docs.keeper.io/sso-connect-guide/).

### <a name="create-a-keeper-password-manager--digital-vault-test-user"></a>Creare un utente di test di Keeper Password Manager & Digital Vault

Per consentire agli utenti di Azure AD di accedere a Keeper Password Manager & Digital Vault, è necessario effettuare il provisioning di tali utenti. L'applicazione supporta il provisioning dell'utente just-in-time e dopo l'autenticazione gli utenti vengono automaticamente creati nell'applicazione. Se si vuole eseguire la configurazione manuale degli utenti, contattare il [supporto tecnico di Keeper](https://keepersecurity.com/contact.html).

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Nel portale di Azure selezionare **Test this application** (Testa questa applicazione). Verrà eseguito il reindirizzamento all'URL di accesso per Keeper Password Manager & Digital Vault, in cui è possibile avviare l'accesso. 

* È possibile passare direttamente all'URL di accesso per l'applicazione e avviare l'accesso da lì.

* È possibile usare il Pannello di accesso Microsoft. Quando si seleziona il riquadro **Keeper Password Manager & Digital Vault** nel pannello di accesso, verrà eseguito il reindirizzamento all'URL di accesso per l'applicazione. Per altre informazioni sul pannello di accesso, vedere [Accedere e avviare app dal portale App personali](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Passaggi successivi

Dopo la configurazione di Keeper Password Manager & Digital Vault, sarà possibile imporre il controllo della sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. Per altre informazioni, vedere [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).