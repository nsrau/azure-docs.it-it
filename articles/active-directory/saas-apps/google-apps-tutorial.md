---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con G Suite | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e4449ac3519757bb9670d2d7fec53cb5f3ce152
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71948289"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-g-suite"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con G Suite

Questa esercitazione descrive come integrare G Suite con Azure Active Directory (Azure AD). Integrando G Suite con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a G Suite.
* Abilitare gli utenti per l'accesso automatico a G Suite con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

- Una sottoscrizione di Azure AD.
- Sottoscrizione di G Suite abilitata per l'accesso Single Sign-On (SSO).
- Sottoscrizione di Google Apps o sottoscrizione di Google Cloud Platform

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione. Questo documento è stato creato usando la nuova esperienza utente Single Sign-On. Se si sta ancora usando quella precedente, la configurazione risulterà diversa. È possibile abilitare la nuova esperienza nelle impostazioni Single Sign-On dell'applicazione G Suite. Passare ad **Azure AD, applicazioni aziendali**, selezionare **G Suite**, quindi **Single Sign-On** e fare clic su **Prova la nuova esperienza**.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).

## <a name="frequently-asked-questions"></a>Domande frequenti

1. **D: Questa integrazione supporta l'integrazione SSO di Google Cloud Platform con Azure AD?**

    R: Sì. Google Cloud Platform e Google Apps condividono la stessa piattaforma di autenticazione. Per eseguire l'integrazione di GCP, è quindi necessario configurare l'accesso SSO con Google Apps.

2. **D: I dispositivi Chromebooks e altri dispositivi Chrome sono compatibili con Single Sign-On di Azure AD?**
  
    R: Sì, gli utenti possono accedere ai dispositivi Chromebook con le credenziali di Azure AD. Per informazioni sui motivi per cui agli utenti può essere richiesto di immettere le credenziali due volte, vedere questo [articolo del supporto tecnico di G Suite](https://support.google.com/chrome/a/answer/6060880).

3. **D: Se si abilita il Single Sign-On, gli utenti potranno usare le credenziali di Azure AD per accedere a qualsiasi prodotto di Google, ad esempio Google Classroom, GMail, Google Drive, YouTube e così via?**

    R: Sì, a seconda della [G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) che si sceglie di abilitare o disabilitare per la propria organizzazione.

4. **D: È possibile abilitare Single Sign-On solo per un subset di utenti di G Suite?**

    R: No, l'attivazione di Single Sign-On richiede immediatamente a tutti gli utenti di G Suite di autenticarsi con le proprie credenziali di Azure AD. Poiché G Suite non supporta più provider di identità, il provider di identità per l'ambiente di G Suite può essere AD Azure o Google, ma non entrambi contemporaneamente.

5. **D: Se un utente ha eseguito l'accesso tramite Windows, viene autenticano automaticamente in G Suite senza che venga richiesta una password?**

    R: Sono disponibili due opzioni per l'abilitazione di questo scenario. Nel primo caso gli utenti possono accedere ai dispositivi Windows 10 tramite [Aggiunta ad Azure Active Directory](../device-management-introduction.md). In alternativa, gli utenti possono accedere ai dispositivi Windows appartenenti a un dominio di Active Directory locale abilitato per il Single Sign-On in Azure AD tramite una distribuzione di [Active Directory Federation Services (AD FS)](../hybrid/plan-connect-user-signin.md) . Per entrambe le opzioni è necessario eseguire la procedura descritta nell'esercitazione seguente per abilitare Single Sign-On tra Azure AD e G Suite.

6. **D: Cosa occorre fare quando si riceve il messaggio di errore "L'indirizzo di posta elettronica non è valido"?**

    R: Per questa configurazione, l'attributo di posta elettronica viene richiesto agli utenti per effettuare l'accesso. Non è possibile impostare manualmente questo attributo.

    L'attributo di posta elettronica viene compilato automaticamente per tutti gli utenti con una licenza valida di Exchange. Se l'utente non è abilitato alla posta elettronica, si riceverà questo errore in quanto l'applicazione deve ottenere questo attributo per concedere l'accesso.

    È possibile passare a portal.office.com con un account amministratore, quindi fare clic nell'interfaccia di amministrazione, fatturazione e sottoscrizioni, selezionare l'abbonamento a Office 365 e quindi fare clic su Assegna a utenti, selezionare gli utenti di cui si desidera controllare la sottoscrizione e nel riquadro destro fare clic su modifica licenze.

    Una volta assegnata la licenza O365, l'applicazione potrebbe richiedere alcuni minuti. Successivamente, l'attributo user.mail sarà compilato automaticamente e il problema dovrebbe essere risolto.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* G Suite supporta l'accesso SSO avviato da **SP**

* G Suite supporta il [provisioning utenti](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial) **automatico**

## <a name="adding-g-suite-from-the-gallery"></a>Aggiunta di G Suite dalla raccolta

Per configurare l'integrazione di G Suite in Azure AD, è necessario aggiungere G Suite dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **G Suite** nella casella di ricerca.
1. Selezionare **G Suite** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-g-suite"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per G Suite

Configurare e testare l'accesso SSO di Azure AD con G Suite usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in G Suite.

Per configurare e testare l'accesso SSO di Azure AD con G Suite, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di G Suite](#configure-g-suite-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di G Suite](#create-g-suite-test-user)** : per avere una controparte di B.Simon in G Suite collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **G Suite** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base**, per eseguire la configurazione per **Gmail**, seguire questa procedura:

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. Nella casella di testo **Identificatore** digitare un URL nel formato seguente:

    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

1. Nella sezione **Configurazione SAML di base**, per eseguire la configurazione per **Google Cloud Platform**, seguire questa procedura:

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com`

    b. Nella casella di testo **Identificatore** digitare un URL nel formato seguente:
    
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |
    
    > [!NOTE]
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. G.Suite non offre il valore ID entità/Identificatore nella configurazione Single Sign-On, quindi quando si deseleziona l'opzione **domain specific issuer** (certificazione specifica del dominio), il valore dell'identificatore sarà `google.com`. Se si seleziona l'opzione **domain specific issuer** (certificazione specifica del dominio), il valore sarà `google.com/a/<yourdomainname.com>`. Per selezionare/deselezionare l'opzione **domain specific issuer** (certificazione specifica del dominio), è necessario passare alla sezione **Configurare l'accesso Single Sign-On di G Suite**, descritta più avanti nell'esercitazione. Per altre informazioni, contattare il [team di supporto clienti di G Suite](https://www.google.com/contact/).

1. L'applicazione G Suite prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. La schermata seguente illustra un esempio relativo a questa operazione. Il valore predefinito di **Identificatore univoco dell'utente** è **user.userprincipalname**, ma G Suite prevede che venga mappato all'indirizzo di posta elettronica dell'utente. A tale scopo è possibile usare l'attributo **user.mail** dall'elenco oppure usare il valore di attributo appropriato in base alla configurazione dell'organizzazione.

    ![image](common/edit-attribute.png)

1. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** modificare le attestazioni usando l'**icona Modifica** o aggiungere le attestazioni usando l'opzione **Aggiungi nuova attestazione** per configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura:

    | Nome | Attributo di origine |
    | ---------------| --------------- |
    | Identificatore univoco dell'utente | User.mail |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    f. Fare clic su **OK**.

    g. Fare clic su **Save**.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura G Suite** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a G Suite.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco di applicazioni selezionare **G Suite**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-g-suite-sso"></a>Configurare l'accesso Single Sign-On di G Suite

1. Aprire una nuova scheda nel browser e accedere a [Console di amministrazione di G Suite](https://admin.google.com/) usando l'account amministratore.

2. Fare clic su **Security**. Se non viene visualizzato il collegamento, può essere nascosto sotto il menu **More Controls** nella parte inferiore della schermata.

    ![Fare clic su sicurezza.][10]

3. Nella pagina **Security** (Sicurezza) fare clic su **Set up single sign-on (SSO)** (Configurazione Single Sign-On (SSO)).

    ![Fare clic su SSO.][11]

4. Eseguire le seguenti modifiche di configurazione:

    ![Configurare SSL][12]

    a. Selezionare **Setup SSO with third party identity provider** (Configurare l'accesso SSO con un provider di terze parti).

    b. Nel campo **Sign-in page URL** (URL pagina di accesso) di G Suite incollare il valore dell'**URL di accesso** copiato dal portale di Azure.

    c. Nel campo **Sign-out page URL** (URL pagina di disconnessione) di G Suite incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure.

    d. Nel campo **Change password URL** (URL di modifica della password) di G Suite incollare il valore dell'**URL di modifica della password** copiato dal portale di Azure.

    e. In G Suite, per il **certificato di verifica**, caricare il certificato che è stato scaricato dal portale di Azure.

    f. Selezionare/deselezionare l'opzione **Use a domain specific issuer** (Usa un'autorità di certificazione specifica del dominio) in base alla nota menzionata della sezione **Configurazione SAML di base** in Azure AD.

    g. Fare clic su **Salva modifiche**.

### <a name="create-g-suite-test-user"></a>Creare l'utente di test di G Suite

Questa sezione descrive come creare un utente di nome B.Simon in G Suite Software. G Suite supporta il provisioning automatico abilitato per impostazione predefinita. Non è necessaria alcuna azione dell'utente in questa sezione. Se un utente non esiste in G Suite, ne viene creato uno nuovo quando si prova ad accedere a G Suite Software.

> [!NOTE]
> Assicurarsi che l'utente esiste già in G Suite se non è stato attivato il provisioning in Azure AD prima del test dell'accesso Single Sign-on.

> [!NOTE]
> Se è necessario creare un utente manualmente, contattare il [team di supporto di Google](https://www.google.com/contact/).

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di G Suite nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione G Suite per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Configura provisioning utenti](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)
- [Provare G Suite con Azure AD](https://aad.portal.azure.com/)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png