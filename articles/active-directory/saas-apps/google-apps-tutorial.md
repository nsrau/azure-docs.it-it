---
title: 'Esercitazione: Integrazione di Azure Active Directory con G Suite | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3cca5e6d5ccdac5ee9ed250edfc526919d710d83
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56208249"
---
# <a name="tutorial-azure-active-directory-integration-with-g-suite"></a>Esercitazione: Integrazione di Azure Active Directory con G Suite

Questa esercitazione descrive come integrare G Suite con Azure Active Directory (Azure AD).
L'integrazione di G Suite con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a G Suite.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a G Suite con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con G Suite, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di G Suite abilitata per l'accesso Single Sign-On
- Sottoscrizione di Google Apps o sottoscrizione di Google Cloud Platform

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione. Questo documento è stato creato usando la nuova esperienza utente Single Sign-On. Se si sta ancora usando quella precedente, la configurazione risulterà diversa. È possibile abilitare la nuova esperienza nelle impostazioni Single Sign-On dell'applicazione G Suite. Passare ad **Azure AD, applicazioni aziendali**, selezionare **G Suite**, quindi **Single Sign-On** e fare clic su **Prova la nuova esperienza**.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="frequently-asked-questions"></a>Domande frequenti

1. **D: Questa integrazione supporta l'integrazione SSO di Google Cloud Platform con Azure AD?**

    R: Sì. Google Cloud Platform e Google Apps condividono la stessa piattaforma di autenticazione. Per eseguire l'integrazione di GCP, è quindi necessario configurare l'accesso SSO con Google Apps.

2. **D: I dispositivi Chromebooks e altri dispositivi Chrome sono compatibili con Single Sign-On di Azure AD?**
  
    R: Sì, gli utenti possono accedere ai dispositivi Chromebook con le credenziali di Azure AD. Per informazioni sui motivi per cui agli utenti può essere richiesto di immettere le credenziali due volte, vedere questo [articolo del supporto tecnico di G Suite](https://support.google.com/chrome/a/answer/6060880).

3. **D: Se si abilita l'accesso Single Sign-On, gli utenti potranno usare le credenziali di Azure AD per accedere a qualsiasi prodotto di Google, ad esempio Google Classroom, GMail, Google Drive, YouTube e così via?**

    R: Sì, a seconda della [G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) che si sceglie di abilitare o disabilitare per la propria organizzazione.

4. **D: È possibile abilitare Single Sign-On solo per un subset di utenti di G Suite?**

    R: No, l'attivazione di Single Sign-On richiede immediatamente a tutti gli utenti di G Suite di autenticarsi con le proprie credenziali di Azure AD. Poiché G Suite non supporta più provider di identità, il provider di identità per l'ambiente di G Suite può essere AD Azure o Google, ma non entrambi contemporaneamente.

5. **D: Se un utente ha eseguito l'accesso tramite Windows, viene autenticano automaticamente in G Suite senza che venga richiesta una password?**

    R: Sono disponibili due opzioni per l'abilitazione di questo scenario. Nel primo caso gli utenti possono accedere ai dispositivi Windows 10 tramite [Aggiunta ad Azure Active Directory](../device-management-introduction.md). In alternativa, gli utenti possono accedere ai dispositivi Windows appartenenti a un dominio di Active Directory locale abilitato per l'accesso Single Sign-On in Azure AD tramite una distribuzione di [Active Directory Federation Services (AD FS)](../hybrid/plan-connect-user-signin.md). Per entrambe le opzioni è necessario eseguire la procedura descritta nell'esercitazione seguente per abilitare Single Sign-On tra Azure AD e G Suite.

6. **D: Cosa occorre fare quando si riceve il messaggio di errore "L'indirizzo di posta elettronica non è valido"?**

    R: Per questa configurazione, l'attributo di posta elettronica viene richiesto agli utenti per effettuare l'accesso. Non è possibile impostare manualmente questo attributo.

    L'attributo di posta elettronica viene compilato automaticamente per tutti gli utenti con una licenza valida di Exchange. Se l'utente non è abilitato alla posta elettronica, si riceverà questo errore in quanto l'applicazione deve ottenere questo attributo per concedere l'accesso.

    È possibile passare a portal.office.com con un account amministratore, quindi fare clic nell'interfaccia di amministrazione, fatturazione e sottoscrizioni, selezionare l'abbonamento a Office 365 e quindi fare clic su Assegna a utenti, selezionare gli utenti di cui si desidera controllare la sottoscrizione e nel riquadro destro fare clic su modifica licenze.

    Una volta assegnata la licenza O365, l'applicazione potrebbe richiedere alcuni minuti. Successivamente, l'attributo user.mail sarà compilato automaticamente e il problema dovrebbe essere risolto.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* G Suite supporta l'accesso SSO avviato da **SP**
* G Suite supporta il **[provisioning utenti automatico](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)**

## <a name="adding-g-suite-from-the-gallery"></a>Aggiunta di G Suite dalla raccolta

Per configurare l'integrazione di G Suite in Azure AD, è necessario aggiungere G Suite dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere G Suite dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **G Suite**, selezionare **G Suite** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![G Suite nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con G Suite usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in G Suite.

Per configurare e testare l'accesso Single Sign-On di Azure AD con G Suite, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per G Suite](#configure-g-suite-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di G Suite](#create-g-suite-test-user)**: per avere una controparte di Britta Simon in G Suite collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con G Suite, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **G Suite** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare l'accesso Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base**, per eseguire la configurazione per **Gmail**, seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di G Suite](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. Nella casella di testo **Identificatore** digitare un URL nel formato seguente:
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |

    > [!NOTE]
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di G Suite](https://www.google.com/contact/).

5. Nella sezione **Configurazione SAML di base**, per eseguire la configurazione per **Google Cloud Platform**, seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di G Suite](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com `

    b. Nella casella di testo **Identificatore** digitare un URL nel formato seguente:
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |
    
    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di G Suite](https://www.google.com/contact/).

6. L'applicazione G Suite prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. La schermata seguente illustra un esempio relativo a questa operazione. Il valore predefinito di **Identificatore univoco dell'utente** è **user.userprincipalname**, ma G Suite prevede che venga mappato all'indirizzo di posta elettronica dell'utente. A tale scopo è possibile usare l'attributo **user.mail** dall'elenco oppure usare il valore di attributo appropriato in base alla configurazione dell'organizzazione.

    ![image](common/edit-attribute.png)

7. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** modificare le attestazioni usando l'**icona Modifica** o aggiungere le attestazioni usando l'opzione **Aggiungi nuova attestazione** per configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura:

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

    f. Fare clic su **Ok**

    g. Fare clic su **Save**.

8. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

9. Nella sezione **Configura G Suite** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-g-suite-single-sign-on"></a>Configurare l'accesso Single Sign-On per G Suite

1. Aprire una nuova scheda nel browser e accedere a [Console di amministrazione di G Suite](http://admin.google.com/) usando l'account amministratore.

2. Fare clic su **Security** (Sicurezza). Se non viene visualizzato il collegamento, può essere nascosto sotto il menu **More Controls** (Altri controlli) nella parte inferiore della schermata.

    ![Fare clic su Security (Sicurezza).][10]

3. Nella pagina **Security** (Sicurezza) fare clic su **Set up single sign-on (SSO)** (Configurazione Single Sign-On (SSO)).

    ![Fare clic su SSO.][11]

4. Eseguire le seguenti modifiche di configurazione:

    ![Configurare SSL][12]

    a. Selezionare **Setup SSO with third party identity provider** (Configurare l'accesso SSO con un provider di terze parti).

    b. Nel campo **Sign-in page URL** (URL pagina di accesso) di G Suite incollare il valore dell' **URL di accesso** copiato dal portale di Azure.

    c. Nel campo **Sign-out page URL** (URL pagina di disconnessione) di G Suite incollare il valore dell' **URL di disconnessione** copiato dal portale di Azure.

    d. Nel campo **Change password URL** (URL di modifica della password) di G Suite incollare il valore dell' **URL di modifica della password** copiato dal portale di Azure.

    e. In G Suite, per il **certificato di verifica**, caricare il certificato che è stato scaricato dal portale di Azure.

    f. Selezionare **Use a domain specific issuer** (Usa un'autorità di certificazione specifica del dominio).

    g. Fare clic su **Salva modifiche**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

Questa sezione descrive come creare un utente di test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a G Suite.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **G Suite**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni digitare e selezionare **G Suite**.

    ![Collegamento di G Suite nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-g-suite-test-user"></a>Creare l'utente di test di G Suite

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in G Suite. G Suite supporta il provisioning automatico abilitato per impostazione predefinita. Non è necessaria alcuna azione dell'utente in questa sezione. Se un utente non esiste in G Suite, ne viene creato uno nuovo quando si prova ad accedere a G Suite Software.

> [!NOTE]
> Assicurarsi che l'utente esiste già in G Suite se non è stato attivato il provisioning in Azure AD prima del test dell'accesso Single Sign-on.

> [!NOTE]
> Se è necessario creare un utente manualmente, contattare il [team di supporto di Google](https://www.google.com/contact/).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di G Suite nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione G Suite per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Configura provisioning utenti](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png
