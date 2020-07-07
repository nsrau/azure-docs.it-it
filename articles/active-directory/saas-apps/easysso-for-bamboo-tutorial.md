---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con EasySSO for Bamboo | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e EasySSO for Bamboo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f594613d-0cd8-4046-a7c5-2da2971afecd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/28/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e10daab315fd20fb1f82ada2e17cef28bb6045f
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85608807"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-bamboo"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con EasySSO for Bamboo

Questa esercitazione descrive come integrare EasySSO for Bamboo con Azure Active Directory (Azure AD). Integrando EasySSO for Bamboo con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Bamboo.
* Abilitare gli utenti per l'accesso automatico a Bamboo con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di EasySSO for Bamboo abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* EasySSO for Bamboo supporta l'accesso SSO avviato da **SP e IDP**
* EasySSO for Bamboo supporta il provisioning utenti **JIT**
* Dopo aver configurato EasySSO for Bamboo, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-easysso-for-bamboo-from-the-gallery"></a>Aggiunta di EasySSO for Bamboo dalla raccolta

Per configurare l'integrazione di EasySSO for Bamboo in Azure AD, è necessario aggiungere EasySSO for Bamboo dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **EasySSO for Bamboo** nella casella di ricerca.
1. Selezionare **EasySSO for Bamboo** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-easysso-for-bamboo"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per EasySSO for Bamboo

Configurare e testare l'accesso SSO di Azure AD con EasySSO for Bamboo usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in EasySSO for Bamboo.

Per configurare e testare l'accesso SSO di Azure AD con EasySSO for Bamboo, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di EasySSO for Bamboo](#configure-easysso-for-bamboo-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di EasySSO for Bamboo](#create-easysso-for-bamboo-test-user)** : per avere una controparte di B.Simon in EasySSO for Bamboo collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **EasySSO for Bamboo** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<server-base-url>/plugins/servlet/easysso/saml`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<server-base-url>/plugins/servlet/easysso/saml`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<server-base-url>/login.jsp`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. In caso di dubbi, contattare il [team di supporto di EasySSO](mailto:support@techtime.co.nz) per ottenere questi valori. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione EasySSO for Bamboo prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre quelli elencati in precedenza, l'applicazione EasySSO for Bamboo prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.
    
    | Nome | Attributo di origine |
    | ---------------| --------- |
    | urn:oid:0.9.2342.19200300.100.1.1 | user.userprincipalname |
    | urn:oid:0.9.2342.19200300.100.1.3 | user.mail |
    | urn:oid:2.16.840.1.113730.3.1.241 | user.displayname |
    | urn:oid:2.5.4.4 | user.surname |
    | urn:oid:2.5.4.42 | user.givenname |
    
    Se **sAMAccountName** è configurato per gli utenti di Azure AD, è necessario eseguire il mapping di **urn:oid:0.9.2342.19200300.100.1.1** all'attributo **sAMAccountName**.
    
1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** fare clic sui collegamenti di **download** relativi alle opzioni **Certificato (Base64)** o **XML metadati federazione** e salvare una o entrambi nel computer. Tali dati saranno necessari in seguito per configurare EasySSO for Bamboo.

    ![Collegamento di download del certificato](./media/easysso-for-bamboo-tutorial/certificate.png)
    
    Se si prevede di eseguire manualmente la configurazione di EasySSO for Bamboo con il certificato, è anche necessario copiare il valore di **URL di accesso** e **Identificatore Azure AD** dalla sezione seguente e salvarli nel computer.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a EasySSO for Bamboo.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **EasySSO for Bamboo**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-easysso-for-bamboo-sso"></a>Configurare l'accesso Single Sign-On di EasySSO for Bamboo

1. Accedere all'istanza di Atlassian Bamboo con privilegi di amministratore e passare alla sezione **Manage Apps** (Gestisci app). 

    ![Manage Apps](./media/easysso-for-bamboo-tutorial/bamboo-admin-1.png)

2. Sul lato sinistro individuare **EasySSO** e fare clic su di esso.

    ![Easy SSO](./media/easysso-for-bamboo-tutorial/bamboo-admin-2.png)

3. Selezionare l'opzione **SAML**. Verrà visualizzata la sezione di configurazione SAML.

    ![SAML](./media/easysso-for-bamboo-tutorial/bamboo-admin-3.png)

4. Selezionare la scheda **Certificates** (Certificati) nella parte superiore. Verrà visualizzata la schermata seguente:

    ![URL dei metadati](./media/easysso-for-bamboo-tutorial/bamboo-admin-4.png)

5. A questo punto, individuare il **certificato (Base64)** o il **file di metadati** salvato nei passaggi precedenti della configurazione dell'**accesso Single Sign-On di Azure AD**. Per procedere, è possibile scegliere tra diverse opzioni:

    a. Usare il **file dei metadati** di federazione dell'app scaricato come file locale nel computer. Selezionare il pulsante di opzione **Upload** (Carica) e seguire la finestra di dialogo di caricamento file specifica del sistema operativo.

    **OR**

    b. Aprire il **file dei metadati di federazione dell'app** per visualizzare il contenuto del file in qualsiasi editor di testo normale e copiarlo negli Appunti. Selezionare l'opzione **Input** e incollare il contenuto degli Appunti nel campo di testo.
 
    **OR**

    c.  Configurazione completamente manuale. Aprire il **certificato (Base64)** di federazione dell'app per visualizzare il contenuto del file in qualsiasi editor di testo normale e copiarlo negli Appunti. Incollarlo nel campo di testo **IdP Token Signing Certificates** (Certificati per la firma di token IdP). Passare quindi alla scheda **General** (Generale) e compilare i campi **POST Binding URL** (URL di associazione POST) e **Entity ID** (ID entità) con i valori rispetti di **URL di accesso** e **Identificatore Azure AD** salvati in precedenza.
 
6. Fare clic sul pulsante **Save** (Salva) nella parte inferiore della pagina. Si noterà che il contenuto dei file dei metadati o di certificato viene analizzato nei campi di configurazione. La configurazione di EasySSO for Bamboo è stata completata.

7. Per un'esperienza di test ottimale, passare alla scheda **Look & Feel** (Aspetto) e attivare l'opzione **SAML Login Button** (Pulsante di accesso SAML). In questo modo nella schermata di accesso di Bamboo si abiliterà un pulsante separato per testare l'integrazione SAML di Azure AD end-to-end. È possibile lasciare attivato questo pulsante e configurarne la posizione, il colore e la traduzione anche per la modalità di produzione.

    ![Look & Feel](./media/easysso-for-bamboo-tutorial/bamboo-admin-5.png)

    > [!NOTE]
    > In caso di problemi, contattare il [team di supporto di EasySSO](mailto:support@techtime.co.nz).

### <a name="create-easysso-for-bamboo-test-user"></a>Creare l'utente di test di EasySSO for Bamboo

In questa sezione viene creato un utente di nome Britta Simon in Bamboo. EasySSO for Bamboo supporta il provisioning utenti JIT, che è **disabilitato** per impostazione predefinita. Per abilitare il provisioning utenti, è necessario selezionare in modo esplicito l'opzione **Create user on successful login** (Crea utente in caso di accesso riuscito) nella sezione General (Generale) della configurazione del plug-in EasySSO. Se non esiste già un utente in Bamboo, ne viene creato uno nuovo dopo l'autenticazione.

Se, però, non si vuole abilitare il provisioning utenti automatico al primo accesso dell'utente, gli utenti devono esistere già nelle directory utente back-end usate dall'istanza di Bamboo, ad esempio LDAP o Atlassian Crowd.

![Provisioning utenti](./media/easysso-for-bamboo-tutorial/bamboo-admin-6.png)

## <a name="test-sso"></a>Testare l'accesso SSO 

### <a name="idp-initiated-workflow"></a>Flusso di lavoro avviato da IdP

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di EasySSO for Bamboo nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Bamboo per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

### <a name="sp-initiated-workflow"></a>Flusso di lavoro avviato da SP

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pulsante **SAML Login** (Accesso SAML) di Bamboo.

![Accesso SAML utente](./media/easysso-for-bamboo-tutorial/bamboo-admin-7.png)

Questo scenario presuppone che sia stata abilitata l'opzione **SAML Login Button** (Pulsante di accesso SAML) nella scheda **Look & Feel** (Aspetto) nella pagina di configurazione di EasySSO per Bamboo (vedere sopra). Aprire l'URL di accesso di Bamboo nella modalità in incognito del browser per evitare interferenze con le sessioni esistenti. Fare clic sul pulsante **SAML Login** (Accesso SAML). Si verrà reindirizzati al flusso di autenticazione utente di Azure AD. Al termine dell'operazione, si verrà reindirizzati di nuovo all'istanza di Bamboo come utente autenticato tramite SAML.

Dopo il reindirizzamento da Azure AD, potrebbe essere visualizzata la schermata seguente

![Schermata di errore di EasySSO](./media/easysso-for-bamboo-tutorial/bamboo-admin-8.png)

In questo caso seguire le [istruzioni disponibili in questa pagina]( https://techtime.co.nz/display/TECHTIME/EasySSO+How+to+get+the+logs#EasySSOHowtogetthelogs-RETRIEVINGTHELOGS) per accedere al file **atlassian-bamboo.log**. I dettagli dell'errore saranno disponibili in base all'ID di riferimento indicato nella pagina di errore EasySSO.

In caso di problemi relativi al digest dei messaggi di log, contattare il [team di supporto di EasySSO](mailto:support@techtime.co.nz).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare EasySSO for Bamboo con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Come proteggere EasySSO for Bamboo con visibilità e controlli avanzati](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)