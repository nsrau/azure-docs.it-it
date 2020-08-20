---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con EasySSO for BitBucket | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e EasySSO for BitBucket.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: jeedes
ms.openlocfilehash: bd15798e9b642f3abc617c0fa224c530d90b5dd6
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88555566"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-bitbucket"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con EasySSO for BitBucket

Questa esercitazione descrive come integrare EasySSO for BitBucket con Azure Active Directory (Azure AD). Integrando EasySSO for BitBucket con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a EasySSO for BitBucket.
* Abilitare gli utenti per l'accesso automatico a EasySSO for BitBucket con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Una sottoscrizione di EasySSO for BitBucket abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* EasySSO for BitBucket supporta l'accesso SSO avviato da SP e IdP.
* EasySSO for BitBucket supporta il provisioning utenti JIT.
* Dopo aver configurato EasySSO for BitBucket, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-easysso-for-bitbucket-from-the-gallery"></a>Aggiungere EasySSO for BitBucket dalla raccolta

Per configurare l'integrazione di EasySSO for BitBucket in Azure AD, è necessario aggiungere EasySSO for BitBucket dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **EasySSO for BitBucket** nella casella di ricerca.
1. Selezionare **EasySSO for BitBucket** nei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-easysso-for-bitbucket"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per EasySSO for BitBucket

Configurare e testare l'accesso SSO di Azure AD con EasySSO for BitBucket usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in EasySSO for BitBucket.

Per configurare e testare l'accesso SSO di Azure AD con EasySSO for BitBucket, completare i passaggi seguenti:

1. [Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso): per consentire agli utenti di usare questa funzionalità.
    1. [Creare un utente di test di Azure AD](#create-an-azure-ad-test-user) per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. [Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user) per consentire a B.Simon di usare l'accesso Single Sign-On di Azure AD.
1. [Configurare l'accesso Single Sign-On di EasySSO for BitBucket](#configure-easysso-for-bitbucket-sso): per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. [Creare un utente di test di EasySSO for BitBucket](#create-an-easysso-for-bitbucket-test-user): per avere una controparte di B.Simon in EasySSO for BitBucket collegata alla rappresentazione dell'utente in Azure AD.
1. [Testare l'accesso Single Sign-On](#test-sso): per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **EasySSO for BitBucket** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Screenshot della pagina Configura l'accesso Single Sign-On con SAML, in cui è evidenziata l'icona della matita](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IdP**:

    a. Nella casella di testo **Identificatore** digitare l'URL che usa il modello seguente:`https://<server-base-url>/plugins/servlet/easysso/saml`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<server-base-url>/plugins/servlet/easysso/saml`

1. Selezionare **Impostare URL aggiuntivi** e quindi seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    - Nella casella di testo **URL accesso** digitare un URL che usa il modello seguente: `https://<server-base-url>/login.jsp`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. In caso di dubbi, contattare il [team di supporto di EasySSO](mailto:support@techtime.co.nz) per ottenere questi valori. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione EasySSO for BitBucket prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![Screenshot degli attributi predefiniti](common/default-attributes.png)

1. L'applicazione EasySSO for BitBucket prevede inoltre il passaggio di altri attributi nella risposta SAML. Tali attributi sono elencati nella tabella seguente. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base alle esigenze.
    
    | Nome | Attributo di origine|
    | ---------------| --------- |
    | urn:oid:0.9.2342.19200300.100.1.1 | user.userprincipalname |
    | urn:oid:0.9.2342.19200300.100.1.3 | user.mail |
    | urn:oid:2.16.840.1.113730.3.1.241 | user.displayname |
    | urn:oid:2.5.4.4 | user.surname |
    | urn:oid:2.5.4.42 | user.givenname |
    
    Se **sAMAccountName** è configurato per gli utenti di Azure AD, è necessario eseguire il mapping di **urn:oid:0.9.2342.19200300.100.1.1** all'attributo **sAMAccountName**.
    
1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** selezionare i collegamenti di download relativi alle opzioni **Certificato (Base64)** o **XML metadati federazione** e salvare una o entrambi nel computer. Tali dati saranno necessari in seguito per configurare EasySSO per BitBucket.

    ![Screenshot della sezione Certificato di firma SAML, in cui sono evidenziati i collegamenti di download](./media/easysso-for-bitbucket-tutorial/certificate.png)
    
    Se si prevede di configurare manualmente EasySSO for BitBucket con un certificato, è anche necessario copiare il valore di **URL di accesso** e **Identificatore Azure AD** e salvarli nel computer.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Per **Nome** immettere `B.Simon`.  
   1. Per **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi annotare la password.
   1. Selezionare **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a EasySSO for BitBucket.

1. Nel portale di Azure selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **EasySSO for BitBucket**.
1. Nella pagina di panoramica dell'app individuare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Screenshot della sezione Gestione con Utenti e gruppi evidenziato](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente**. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Utenti e gruppi**.

    ![Screenshot della pagina Utenti e gruppi, in cui è evidenziato il pulsante Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** nell'elenco **Utenti** e quindi scegliere **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco. Scegliere quindi **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

## <a name="configure-easysso-for-bitbucket-sso"></a>Configurare l'accesso Single Sign-On di EasySSO for BitBucket

1. Accedere all'istanza di Atlassian BitBucket con privilegi di amministratore e passare alla sezione **Administration** (Amministrazione). 

    ![Screenshot dell'istanza di BitBucket, con l'icona a forma di ingranaggio evidenziata](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-1.png)
1. Individuare e selezionare **EasySSO**.

    ![Screenshot dell'opzione EasySSO](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-2.png)

1. Selezionare **SAML**. Verrà visualizzata la sezione relativa alla configurazione SAML.

    ![Screenshot della pagina di amministrazione di EasySSO con l'opzione SAML evidenziata](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-3.png)

1. Selezionare la scheda **Certificates** (Certificati). Verrà visualizzata la schermata seguente:

    ![Screenshot della scheda Certificates con varie opzioni evidenziate](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-4.png)

1. Individuare il **certificato (Base64)** o il **file di metadati** salvato nella sezione precedente di questa esercitazione. È possibile procedere in uno dei modi seguenti:

    - Usare il **file dei metadati** di federazione dell'app scaricato come file locale nel computer. Selezionare il pulsante di opzione **Upload** (Carica) e seguire il percorso specifico del sistema operativo corrente.

    - Aprire il **file di metadati** di federazione dell'app in qualsiasi editor di testo normale per visualizzarne il contenuto e copiarlo negli Appunti. Selezionare **Input** e incollare il contenuto degli Appunti nel campo di testo.
 
    - Eseguire una configurazione completamente manuale. Aprire il **certificato (Base64)** di federazione dell'app in qualsiasi editor di testo normale per visualizzarne il contenuto. Copiarlo negli Appunti e incollarlo nel campo di testo **IdP Token Signing Certificates** (Certificati per la firma di token IdP). Passare quindi alla scheda **General** (Generale) e compilare i campi **POST Binding URL** (URL di associazione POST) e **Entity ID** (ID entità) con i valori rispetti di **URL di accesso** e **Identificatore Azure AD** salvati in precedenza.
 
1. Selezionare **Save** (Salva) nella parte inferiore della pagina. Si noterà che il contenuto dei file dei metadati o di certificato viene analizzato nei campi di configurazione. La configurazione di EasySSO for BitBucket è stata completata.

1. Per testare la configurazione, passare alla scheda **Look & Feel** (Aspetto) e selezionare **SAML Login Button** (Pulsante di accesso SAML). In questo modo nella schermata di accesso di BitBucket si abiliterà un pulsante separato per testare l'integrazione SAML di Azure AD end-to-end. È possibile lasciare attivato questo pulsante e configurarne la posizione, il colore e la traduzione anche per la modalità di produzione.

    ![Screenshot della scheda Look & Feel della pagina SAML con il pulsante SAML Login Button evidenziato](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-5.png)
    > [!NOTE]
    >Se si verificano problemi, contattare il [team di supporto di EasySSO](mailto:support@techtime.co.nz).

### <a name="create-an-easysso-for-bitbucket-test-user"></a>Creare un utente di test di EasySSO for BitBucket

In questa sezione viene creato un utente di nome Britta Simon in BitBucket. EasySSO for BitBucket supporta il provisioning utenti JIT, che è disabilitato per impostazione predefinita. Per abilitarlo, è necessario selezionare in modo esplicito l'opzione **Create user on successful login** (Crea utente in caso di accesso riuscito) nella sezione **General** (Generale) della configurazione del plug-in EasySSO. Se non esiste già un utente in BitBucket, ne viene creato uno nuovo dopo l'autenticazione.

Se però non si vuole abilitare il provisioning utenti automatico al primo accesso, gli utenti devono essere già presenti nelle directory utente usate dall'istanza di BitBucket. Questa directory potrebbe, ad esempio, essere LDAP o Atlassian Crowd.

![Screenshot della sezione General della configurazione del plug-in EasySSO, con l'opzione Create user on successful login evidenziata](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-6.png)

## <a name="test-sso"></a>Testare l'accesso SSO 

### <a name="idp-initiated-workflow"></a>Flusso di lavoro avviato da IdP

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro di EasySSO for BitBucket, si dovrebbe accedere automaticamente all'istanza di BitBucket per cui si è configurato l'accesso SSO. Per altre informazioni, vedere [Accedere e avviare app dal portale App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

### <a name="sp-initiated-workflow"></a>Flusso di lavoro avviato da SP

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pulsante **SAML Login** (Accesso SAML) di BitBucket.

![Screenshot della schermata di accesso con il pulsante SAML evidenziato](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-7.png)

Questo scenario presuppone che sia stata abilitata l'opzione **SAML Login Button** (Pulsante di accesso SAML) nella scheda **Look & Feel** (Aspetto) nella pagina di configurazione di EasySSO per BitBucket. Aprire l'URL di accesso di BitBucket nella modalità in incognito del browser per evitare interferenze con le sessioni esistenti. Selezionare **SAML Login** (Accesso SAML) per essere reindirizzati al flusso di autenticazione utente di Azure AD. Al termine dell'operazione, si verrà reindirizzati di nuovo all'istanza di BitBucket come utente autenticato tramite SAML.

Dopo il reindirizzamento da Azure AD, potrebbe essere visualizzata la schermata seguente:

![Screenshot della schermata di errore EasySSO, con il numero di riferimento evidenziato](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-8.png)

In questo caso seguire le [istruzioni disponibili in questa pagina](https://techtime.co.nz/display/TECHTIME/EasySSO+How+to+get+the+logs#EasySSOHowtogetthelogs-RETRIEVINGTHELOGS) per accedere al file **atlassian-bitbucket.log**. I dettagli dell'errore saranno disponibili in base all'ID di riferimento indicato nella pagina di errore EasySSO.

Se si verificano problemi, contattare il [team di supporto di EasySSO](mailto:support@techtime.co.nz).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Esercitazioni per l'integrazione di applicazioni SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare EasySSO for BitBucket con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Proteggere EasySSO for BitBucket con visibilità e controlli avanzati](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
