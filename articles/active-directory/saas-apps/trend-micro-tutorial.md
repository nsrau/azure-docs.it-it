---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Trend Micro Web Security (TMWS) | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Trend Micro Web Security (TMWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 827285d3-8e65-43cd-8453-baeda32ef174
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/21/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f4dc7223d64fd299da70375329260f7b4f8b322
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083394"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trend-micro-web-securitytmws"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Trend Micro Web Security (TMWS)

Questa esercitazione descrive come integrare Trend Micro Web Security (TMWS) con Azure Active Directory (Azure AD). Integrando Trend Micro Web Security (TMWS) con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Trend Micro Web Security (TMWS).
* Abilitare gli utenti per l'accesso automatico a Trend Micro Web Security (TMWS) con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Trend Micro Web Security (TMWS) abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Trend Micro Web Security (TMWS) supporta l'accesso SSO avviato da **SP**
* Dopo aver configurato Trend Micro Web Security (TMWS), è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-trend-micro-web-securitytmws-from-the-gallery"></a>Aggiunta di Trend Micro Web Security (TMWS) dalla raccolta

Per configurare l'integrazione di Trend Micro Web Security (TMWS) in Azure AD, è necessario aggiungere Trend Micro Web Security (TMWS) dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Trend Micro Web Security (TMWS)** nella casella di ricerca.
1. Selezionare **Trend Micro Web Security (TMWS)** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-trend-micro-web-securitytmws"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Trend Micro Web Security (TMWS)

Configurare e testare l'accesso SSO di Azure AD con Trend Micro Web Security (TMWS) usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Trend Micro Web Security (TMWS).

Per configurare e testare l'accesso SSO di Azure AD con Trend Micro Web Security (TMWS), completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
    1. **[Configurare le impostazioni di sincronizzazione di utenti e gruppi in Azure AD](#configure-user-and-group-synchronization-settings-in-azure-ad)** : per configurare le impostazioni di sincronizzazione di utenti e gruppi in Azure AD
1. **[Configurare l'accesso Single Sign-On di Trend Micro Web Security (TMWS)](#configure-trend-micro-web-security-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Trend Micro Web Security (TMWS)** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://auth.iws-hybrid.trendmicro.com/([0-9a-f]{16})`

    b. Nella casella di testo **URL di risposta** digitare un URL: `https://auth.iws-hybrid.trendmicro.com/simplesaml/module.php/saml/sp/saml2-acs.php/ics-sp`

    > [!NOTE]
    > Il valore dell'identificatore non è reale. Aggiornarlo con l'identificatore effettivo. È possibile ottenere questi valori nell'area **Service Provider Settings for the Azure Admin Portal** (Impostazioni del provider di servizi per il portale di amministrazione di Azure) nella schermata **Authentication Method** (Metodo di autenticazione) per Azure AD visualizzata facendo clic su **Administration > Directory Services** (Amministrazione > Servizi di directory).

1. L'applicazione Trend Micro Web Security (TMWS) prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre quelli elencati in precedenza, l'applicazione Trend Micro Web Security (TMWS) prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.
    
    | Nome | Attributo di origine|
    | --------------- | --------- |
    | sAMAccountName | user.onpremisessamaccountname |
    | uPN | user.userprincipalname |

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Trend Micro Web Security (TMWS)** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Trend Micro Web Security (TMWS).

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Trend Micro Web Security (TMWS)** .
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="configure-user-and-group-synchronization-settings-in-azure-ad"></a>Configurare le impostazioni di sincronizzazione di utenti e gruppi in Azure AD

1. Nel menu di spostamento a sinistra fare clic su **Azure Active Directory**.

1. In **Gestione**fare clic su **Registrazioni app** e quindi fare clic sulla nuova applicazione aziendale nell'area **Tutte le applicazioni**.

1. In **Gestione** fare clic su **Certificati e segreti**.

1. Nell'area Segreti client visualizzata fare clic su **Nuovo segreto client**.

1. Nella schermata Aggiungi un segreto client visualizzata aggiungere facoltativamente una descrizione e selezionare un periodo di scadenza per questo segreto client e quindi fare clic su **Aggiungi**. Il segreto client appena aggiunto viene visualizzato nell'area Segreti client.

1. Annotare il valore. In seguito sarà necessario digitare queste informazioni in TMWS.

1. In **Gestisci** fare clic su **Autorizzazioni API**. 

1. Nella schermata Autorizzazioni API visualizzata fare clic su **Aggiungi un'autorizzazione**.

1. Nella scheda API Microsoft della schermata Richiedi le autorizzazioni dell'API visualizzata fare clic su **Microsoft Graph** e quindi su **Autorizzazioni applicazione**.

1. Individuare e aggiungere le autorizzazioni seguenti: 

    * Group.Read.All
    * User.Read.All

1. Fare clic su **Aggiungi autorizzazioni**. Viene visualizzato un messaggio per confermare che le impostazioni sono state salvate correttamente. Le autorizzazioni appena aggiunte vengono visualizzate nella schermata Autorizzazioni API.

1. Nell'area Fornisci il consenso fare clic su **Concedi consenso amministratore per <account amministratore> (Directory predefinita)** e quindi su **Sì**. Viene visualizzato un messaggio per confermare che l'amministratore ha concesso il consenso per le autorizzazioni richieste.

1. Fare clic su **Panoramica**. 

1. Nel riquadro destro visualizzato prendere nota dell'ID applicazione (client) e dell'ID directory (tenant). In seguito sarà necessario digitare queste informazioni in TMWS. È anche possibile fare clic su **Nomi di dominio personalizzati** in **Azure Active Directory > Gestisci** e prendere nota del nome di dominio visualizzato nel riquadro destro.

## <a name="configure-trend-micro-web-security-sso"></a>Configurare l'accesso Single Sign-On di Trend Micro Web Security (TMWS)

1. Accedere alla console di gestione di TMWS e passare a **Administration** > **USERS & AUTHENTICATION** > **Directory Services** (Amministrazione > UTENTI E AUTENTICAZIONE > Servizi di directory).

1. Fare clic in questo punto nell'area superiore della schermata.

1. Nella schermata Authentication Method (Metodo di autenticazione) che viene visualizzata fare clic su **Azure AD**.

1. Fare clic su **On** (Sì) o **Off** (No) per stabilire se consentire agli utenti di Active Directory dell'organizzazione di visitare i siti Web tramite TMWS se i dati non sono sincronizzati con TMWS.

    > [!NOTE]
    > Gli utenti non sincronizzati da Azure AD possono essere autenticati solo tramite gateway TMWS noti o la porta dedicata per l'organizzazione.

1. Nella sezione **Identity Provider Settings** (Impostazioni provider di identità) seguire questa procedura:

    a. Nel campo **Service URL** (URL del servizio) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    b. Nel campo **Logon name attribute** (Attributo nome di accesso) incollare il nome dell'attestazione utente con l'attributo di origine **user.onpremisessamaccountname** del portale di Azure.

    c. Nel campo **Public SSL certificate** (Certificato SSL pubblico) usare il **certificato (Base64)** scaricato dal portale di Azure.

1. Nella sezione **Synchronization Settings** (Impostazioni di sincronizzazione) seguire questa procedura:

    a. Nel campo **Tenant** usare il valore di **ID della directory (tenant)** o di **Nome dominio personalizzato** del portale di Azure.

    b. Nel campo **Application ID** (ID applicazione) copiare il valore di **ID applicazione (client)** del portale di Azure.

    c. Nel campo **Client secret** (Segreto client) usare il valore di **Segreto client** del portale di Azure.

    d. Nel campo **Synchronization schedule** (Pianificazione sincronizzazione) scegliere se eseguire la sincronizzazione con Azure AD manualmente o in base a una pianificazione. Se si sceglie l'opzione Manually (Manualmente), ogni volta che vengono apportate modifiche alle informazioni degli utenti di Active Directory, ricordarsi di tornare alla schermata Directory Services (Servizi di directory) ed eseguire la sincronizzazione manuale in modo che le informazioni in TMWS rimangano aggiornate.

    e. Fare clic su **Test Connection** (Testa connessione) per verificare se la connessione al servizio Azure AD viene eseguita correttamente. 
    
    f. Fare clic su **Save** (Salva).
 
 > [!NOTE]
 > Per altre informazioni su come configurare Trend Micro Web Security con Azure AD, vedere [questo](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/directory-services/azure-active-directo/configuring-azure-ad.aspx) documento.

## <a name="test-sso"></a>Testare l'accesso SSO 

Dopo aver configurato correttamente il servizio Azure AD e aver specificato Azure AD come metodo di autenticazione utente, è possibile accedere al server proxy TMWS per verificare la configurazione. Dopo la verifica dell'account all'accesso di Azure AD, è possibile visitare siti Internet.

> [!NOTE]
> In TMWS non è possibile testare l'accesso Single Sign-On dal portale di Azure AD, in Panoramica > Single Sign-On > Configura l'accesso Single Sign-On con SAML > Test della nuova applicazione aziendale.

1. Cancellare tutti i cookie nel browser e quindi riavviarlo. 

1. Impostare il browser in modo che punti al server proxy TMWS. Per informazioni dettagliate, vedere l'articolo sull'[inoltro del traffico con file PAC](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/pac-files/traffic-forwarding-u.aspx#GUID-A4A83827-7A29-4596-B866-01ACCEDCC36B).

1. Visitare qualsiasi sito Web Internet. TMWS reindirizzerà l'utente al portale TMWS di tipo Captive.

1. Specificare un account Active Directory in formato dominio\nomeaccountSAM o sAMAccountName@domain, oppure un indirizzo di posta elettronica o un UPN e quindi fare clic su **Log On** (Accedi). TMWS reindirizza l'utente all'interfaccia di accesso di Azure AD.

1. Nell'interfaccia di accesso di Azure AD digitare le credenziali dell'account AD. Dovrebbe essere possibile accedere a TMWS.

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Trend Micro Web Security (TMWS) con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessione in Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Come proteggere Trend Micro Web Security (TMWS) con visibilità e controlli avanzati](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

