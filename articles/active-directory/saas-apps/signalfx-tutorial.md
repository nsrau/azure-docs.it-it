---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con SignalFx | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SignalFx.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/24/2020
ms.author: jeedes
ms.openlocfilehash: 989b41d4fc55241a5fa75a0eed9ea8f4ebaeee67
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91856824"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-signalfx"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con SignalFx

Questa esercitazione descrive come integrare SignalFx con Azure Active Directory (Azure AD). Integrando SignalFx con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a SignalFx.
* Abilitare gli utenti per l'accesso automatico a SignalFx con gli account Azure AD personali.
* Gestire gli account in un'unica posizione: il portale di Azure.

Per altre informazioni sull'integrazione di applicazioni SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario:

* Sottoscrizione di Azure AD
    * Se non si ha una sottoscrizione, è possibile ottenere un account gratuito [qui](https://azure.microsoft.com/free/).
* Sottoscrizione di SignalFx abilitata per l'accesso Single Sign-On (SSO)

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione verranno eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* SignalFx supporta l'accesso SSO avviato da **IDP**
* SignalFx supporta il provisioning utenti **JIT**
* Dopo aver configurato SignalFx, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="step-1-add-the-signalfx-application-in-azure"></a>Passaggio 1: Aggiungere l'applicazione SignalFx in Azure

Usare queste istruzioni per aggiungere l'applicazione SignalFx all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nel menu di spostamento sinistro selezionare **Azure Active Directory**.
1. Selezionare **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** immettere e selezionare **SignalFx** nella casella di ricerca.
     * Potrebbe essere necessario attendere alcuni minuti prima che l'applicazione venga aggiunta al tenant.
1. Lasciare aperto il portale di Azure e quindi aprire una nuova scheda Web.    

## <a name="step-2-begin-signalfx-sso-configuration"></a>Passaggio 2: Avviare la configurazione dell'accesso Single Sign-On per SignalFx

Usare queste istruzioni per avviare il processo di configurazione dell'accesso Single Sign-On per SignalFx.

1. Nella scheda appena aperta accedere all'interfaccia utente di SignalFx. 
1. Nel menu in alto fare clic su **Integrations** (Integrazioni). 
1. Nel campo di ricerca immettere e selezionare **Azure Active Directory**.
1. Fare clic su **Create New Integration** (Crea nuova integrazione).
1. In **Name** (Nome) immettere un nome facilmente riconoscibile dagli utenti.
1. Contrassegnare **Show on login page** (Mostra nella pagina di accesso).
    * Con questa funzionalità nella pagina di accesso viene visualizzato un pulsante personalizzato su cui gli utenti possono fare clic. 
    * Sul pulsante vengono visualizzate le informazioni immesse in **Name** (Nome). Di conseguenza, immettere un **nome** riconoscibile dagli utenti. 
    * Questa opzione funziona solo se per l'applicazione SignalFx si usa un sottodominio personalizzato, ad esempio **yourcompanyname.signalfx.com**. Per ottenere un sottodominio personalizzato, contattare il supporto SignalFx. 
1. Copiare l'**ID integrazione**. Queste informazioni saranno necessarie in un passaggio successivo. 
1. Lasciare aperta l'interfaccia utente di SignalFx. 

## <a name="step-3-configure-azure-ad-sso"></a>Passaggio 3: Configurare l'accesso SSO di Azure AD

Usare queste istruzioni per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure.

1. Tornare al [portale di Azure](https://portal.azure.com/) e nella pagina di integrazione dell'applicazione **SignalFx** individuare la sezione **Gestione** e quindi selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona di modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella pagina **Configura l'accesso Single Sign-On con SAML** completare i campi seguenti: 

    a. In **Identificatore** immettere l'URL `https://api.<realm>.signalfx.com/v1/saml/metadata` e sostituire `<realm>` con l'area di autenticazione di SignalFx. 

    b. In **URL di risposta** immettere l'URL `https://api.<realm>.signalfx.com/v1/saml/acs/<integration ID>` e sostituire `<realm>` con l'area di autenticazione di SignalFx, nonché `<integration ID>` con l'**ID integrazione** copiato in precedenza dall'interfaccia utente di SignalFx.

1. L'applicazione SignalFx prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. 
    
1. Esaminare e verificare che le attestazioni seguenti siano associate agli attributi di origine popolati in Active Directory. 

    | Nome |  Attributo di origine|
    | ------------------- | -------------------- |
    | User.FirstName  | user.givenname |
    | User.email  | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | user.surname    |

    > [!NOTE]
    > Per questo processo Active Directory deve essere configurato con almeno un dominio personalizzato verificato e avere accesso agli account di posta elettronica in questo dominio. Se non si è sicuri o si necessita di assistenza per questa configurazione, contattare il supporto di SignalFx.  

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e quindi selezionare **Scarica**. Scaricare il certificato e salvarlo nel computer. Copiare quindi il valore di **URL dei metadati di federazione dell'app**. Queste informazioni sono necessarie in un passaggio successivo nell'interfaccia utente di SignalFx. 

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura SignalFx** copiare il valore di **Identificatore Azure AD**. Queste informazioni saranno necessarie in un passaggio successivo nell'interfaccia utente di SignalFx. 

## <a name="step-4-create-an-azure-ad-test-user"></a>Passaggio 4: Creare un utente di test di Azure AD

Usare queste istruzioni per creare un utente di test di nome **B.Simon** nel portale di Azure.

1. Nella finestra di spostamento sinistra del portale di Azure selezionare **Azure Active Directory**, quindi **Utenti** e infine **Tutti gli utenti**.
1. Nella parte superiore della pagina selezionare **Nuovo utente**.
1. Nelle proprietà **Utente**:
   1. In **Nome utente** immettere `username@companydomain.extension`, ad esempio `b.simon@contoso.com`.
   1. In **Nome** immettere `B.Simon`.
   1. Contrassegnare **Mostra password** e quindi copiare il valore visualizzato in **Password**. Queste informazioni saranno necessarie in un passaggio successivo per testare l'integrazione. 
   1. Fare clic su **Crea**.

## <a name="step-5-assign-the-azure-ad-test-user"></a>Passaggio 5: Assegnare l'utente di test di Azure AD

Usare queste istruzioni per consentire all'utente di test di usare l'accesso Single Sign-On di Azure per SignalFx.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **SignalFx**.
1. Nella pagina di panoramica dell'app individuare la sezione **Gestione** e quindi selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nell'elenco **Utenti** della finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** e quindi fare clic su **Seleziona** nella parte inferiore della pagina.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Seleziona ruolo** selezionare il ruolo appropriato per l'utente nell'elenco e quindi fare clic su **Seleziona** nella parte inferiore della pagina.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic su **Assegna**.

## <a name="step-6-complete-the-signalfx-sso-configuration"></a>Passaggio 6: Completare la configurazione dell'accesso Single Sign-On per SignalFx 

1. Aprire la scheda precedente e tornare all'interfaccia utente di SignalFx per visualizzare la pagina corrente dell'integrazione di Azure Active Directory. 
1. Accanto a **Certificate (Base64)** (Certificato (Base64)) fare clic su **Upload File** (Carica file) e quindi individuare il file del **certificato con codifica Base64** scaricato in precedenza dal portale di Azure.
1. Accanto a **Azure AD Identifier** (Identificatore Azure AD), incollare il valore di **Identificatore Azure AD** copiato in precedenza dal portale di Azure. 
1. Accanto a **Federation Metadata URL** (URL dei metadati della federazione) incollare il valore di **URL dei metadati di federazione dell'app** copiato in precedenza dal portale di Azure. 
1. Fare clic su **Salva**.

## <a name="step-7-test-sso"></a>Passaggio 7: Testare l'accesso SSO

Esaminare le informazioni seguenti relative a come testare l'accesso Single Sign-On, nonché le aspettative di accesso a SignalFx per la prima volta. 

### <a name="test-logins"></a>Testare gli account di accesso

* Per testare l'account di accesso, è necessario usare una finestra privata/in incognito oppure è possibile disconnettersi dal portale di Azure. In caso contrario, i cookie per l'utente che ha configurato l'applicazione interferiranno e impediranno l'accesso con l'utente di test.

* Quando un nuovo utente di test si connette per la prima volta, Azure richiede forzatamente la modifica della password. In tal caso, il processo di accesso SSO non verrà completato e l'utente di test verrà indirizzato al portale di Azure. Per risolvere i problemi, l'utente di test deve modificare la password e passare alla pagina di accesso di SignalFx o al pannello di accesso e riprovare.
    * Quando si fa clic sul riquadro di SignalFx nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di SignalFx. 
        * Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

* È possibile accedere all'applicazione SignalFx dal pannello di accesso o tramite una pagina di accesso personalizzata assegnata all'organizzazione. L'utente di test deve testare l'integrazione a partire da una di queste posizioni.
    * L'utente di test può usare le credenziali create in precedenza in questo processo per **b.simon\@contoso.com**.

### <a name="first-time-logins"></a>Primo accesso

* Quando un utente accede a SignalFx dall'accesso SSO SAML per la prima volta, riceve un messaggio di posta elettronica di SignalFx contenente un collegamento. L'utente deve fare clic sul collegamento per completare l'autenticazione. Questa convalida della posta elettronica viene eseguita solo per gli utenti che accedono per la prima volta. 

* SignalFx supporta la creazione di utenti **JIT**, di conseguenza se un utente non esiste in SignalFx, l'account dell'utente verrà creato dopo il primo tentativo di accesso.

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Provare SignalFx con Azure AD](https://aad.portal.azure.com/)
