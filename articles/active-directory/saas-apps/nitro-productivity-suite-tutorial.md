---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Nitro Productivity Suite | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Nitro Productivity Suite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: 3280c97e735e68aa36f018a8de59964ade9567b7
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181916"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nitro-productivity-suite"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Nitro Productivity Suite

Questa esercitazione descrive come integrare Nitro Productivity Suite con Azure Active Directory (Azure AD). Integrando Nitro Productivity Suite con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Nitro Productivity Suite.
* Abilitare gli utenti per l'accesso automatico a Nitro Productivity Suite con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Una sottoscrizione di Nitro Productivity Suite di livello [Enterprise](https://www.gonitro.com/pricing).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Nitro Productivity Suite supporta l'accesso SSO avviato da **SP** e **IDP**.
* Nitro Productivity Suite supporta il provisioning utenti **JIT**.

## <a name="add-nitro-productivity-suite-from-the-gallery"></a>Aggiungere Nitro Productivity Suite dalla raccolta

Per configurare l'integrazione di Nitro Productivity Suite in Azure AD, è necessario aggiungere Nitro Productivity Suite dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro sinistro selezionare **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Nitro Productivity Suite** nella casella di ricerca.
1. Selezionare **Nitro Productivity Suite** nei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-nitro-productivity-suite"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Nitro Productivity Suite

Configurare e testare l'accesso SSO di Azure AD con Nitro Productivity Suite usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Nitro Productivity Suite.

Per configurare e testare l'accesso SSO di Azure AD con Nitro Productivity Suite, completare le procedure di base seguenti:

1. [Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso): per consentire agli utenti di usare questa funzionalità.

    a. [Creare un utente di test di Azure AD](#create-an-azure-ad-test-user) per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    
    b. [Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user) per consentire a B.Simon di usare l'accesso Single Sign-On di Azure AD.
    
2. [Creare un utente di test di Nitro Productivity Suite](#create-a-nitro-productivity-suite-test-user): per avere una controparte di B.Simon in Nitro Productivity Suite collegata alla rappresentazione dell'utente in Azure AD.
1. [Testare l'accesso Single Sign-On](#test-sso): per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Nitro Productivity Suite** del portale di Azure individuare la sezione **Gestione**. e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella sezione **Certificato di firma SAML** individuare il **certificato (Base64)** . Selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Screenshot della sezione Certificato di firma SAML con il collegamento Scarica evidenziato](common/certificatebase64.png)
    
1. Nella sezione **Configura Nitro Productivity Suite** selezionare l'icona di copia accanto a **URL di accesso**.
    
    ![Screenshot della sezione Configura Nitro Productivity Suite con URL e icone di copia evidenziati](common/copy-configuration-urls.png)
    
1. Nella pagina **Enterprise Settings** (Impostazioni dell'azienda) del [portale di amministrazione di Nitro](https://admin.gonitro.com/) individuare la sezione **Single Sign-On**. Selezionare **Setup SAML SSO** (Configura SSO SAML).

    a. Incollare il valore di **URL di accesso** del passaggio precedente nel campo **Sign In URL** (URL di accesso).
    
    b. Caricare il **certificato (Base64)** del passaggio precedente nel campo **X509 Signing Certificate** (Certificato di firma X509).
    
    c. Selezionare **Submit** (Invia).
    
    d. Selezionare **Enable Single Sign-On**.


1. Tornare al [portale di Azure](https://portal.azure.com/). Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Screenshot della pagina Configura l'accesso Single Sign-On con SAML, in cui è evidenziata l'icona della matita](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** copiare e incollare il valore del campo **SAML Entity ID** (ID entità SAML) del [portale di amministrazione di Nitro](https://admin.gonitro.com/). Il formato dovrebbe essere simile al seguente: `urn:auth0:gonitro-prod:<ENVIRONMENT>`

    b. Nella casella di testo **URL di risposta** copiare e incollare il valore del campo **ACS URL** (URL ACS) del [portale di amministrazione di Nitro](https://admin.gonitro.com/). Il formato dovrebbe essere simile al seguente: `https://gonitro-prod.eu.auth0.com/login/callback?connection=<ENVIRONMENT>`

1. Selezionare **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL di accesso** digitare l'URL: `https://sso.gonitro.com/login`

1. Selezionare **Salva**.

1. L'applicazione Nitro Productivity Suite prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![Screenshot degli attributi predefiniti](common/default-attributes.png)

1. Oltre a quelli elencati in precedenza, l'applicazione Nitro Productivity Suite prevede il passaggio di qualche altro attributo nella risposta SAML. Questi attributi vengono prepopolati, ma è possibile modificarli in base alle esigenze.
    
    | Nome  |  Attributo di origine|
    | ---------------| --------------- |
    | employeeNumber |  user.objectid |


### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione viene creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi annotare la password.
   1. Selezionare **Create** (Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Nitro Productivity Suite.

1. Nel portale di Azure selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Nitro Productivity Suite**.
1. Nella pagina di panoramica dell'app individuare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente**. Nella finestra di dialogo **Aggiungi assegnazione** selezionare quindi **Utenti e gruppi**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** nell'elenco degli utenti. Scegliere quindi **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

### <a name="create-a-nitro-productivity-suite-test-user"></a>Creare un utente di test di Nitro Productivity Suite

Nitro Productivity Suite supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario eseguire altre azioni. Se non esiste già un utente in Nitro Productivity Suite, ne viene creato uno nuovo dopo l'autenticazione.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

1. Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Nitro Productivity Suite, in cui è possibile avviare il flusso di accesso.  

2. Passare direttamente all'URL di accesso di Nitro Productivity Suite e avviare il flusso di accesso da questa posizione.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Dopo aver fatto clic su **Test this application** (Testa questa applicazione) nel portale di Azure, si dovrebbe accedere automaticamente all'istanza di Nitro Productivity Suite per cui si è configurato l'accesso SSO 

È anche possibile usare il pannello di accesso Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro di Nitro Productivity Suite nel pannello di accesso, se è stato configurato in modalità SP, si dovrebbe essere reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso; se invece è configurato in modalità IDP, si dovrebbe accedere automaticamente all'istanza di Nitro Productivity Suite per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Nitro Productivity Suite, è possibile applicare i controlli sessione che consentono di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. I controlli sessione costituiscono un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).