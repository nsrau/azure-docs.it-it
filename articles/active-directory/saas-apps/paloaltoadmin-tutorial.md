---
title: 'Esercitazione: Integrazione di Azure Active Directory con Palo Alto Networks - Admin UI | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Palo Alto Networks - Admin UI.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/12/2020
ms.author: jeedes
ms.openlocfilehash: 8bd41034d6d4cfa444ae4c0711fd46cb2924d009
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554080"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---admin-ui"></a>Esercitazione: Integrazione di Azure Active Directory con Palo Alto Networks - Admin UI

Questa esercitazione descrive come integrare Palo Alto Networks - Admin UI con Azure Active Directory (Azure AD).
L'integrazione di Palo Alto Networks - Admin UI con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Palo Alto Networks - Admin UI.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Palo Alto Networks - Admin UI con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Palo Alto Networks - Admin UI, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Palo Alto Networks - Admin UI abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Palo Alto Networks - Admin UI supporta l'accesso SSO avviato da **SP**
* Palo Alto Networks - Admin UI supporta il **provisioning utenti Just-In-Time** (JIT)

## <a name="adding-palo-alto-networks---admin-ui-from-the-gallery"></a>Aggiunta di Palo Alto Networks - Admin UI dalla raccolta

Per configurare l'integrazione di Palo Alto Networks - Admin UI in Azure AD, è necessario aggiungere Palo Alto Networks - Admin UI dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Palo Alto Networks - Admin UI** nella casella di ricerca.
1. Selezionare **Palo Alto Networks - Admin UI** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Palo Alto Networks - Admin UI usando un utente di test di nome **B.Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Palo Alto Networks - Admin UI.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Palo Alto Networks - Admin UI, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Palo Alto Networks - Admin UI](#configure-palo-alto-networks---admin-ui-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di Palo Alto Networks - Admin UI](#create-palo-alto-networks---admin-ui-test-user)** : per avere una controparte di B.Simon in Palo Alto Networks - Admin UI collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Palo Alto Networks - Admin UI, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Palo Alto Networks - Admin UI** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

1. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<Customer Firewall FQDN>/php/login.php`

    b. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<Customer Firewall FQDN>:443/SAML20/SP`

    c. Nella casella di testo **URL di risposta** digitare l'URL del servizio consumer di asserzione (ACS) usando il formato seguente: `https://<Customer Firewall FQDN>:443/SAML20/SP/ACS`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Palo Alto Networks - Admin UI](https://support.paloaltonetworks.com/support). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.
    >
    > La porta 443 è obbligatoria in **Identificatore** e **URL di risposta** perché questi valori sono impostati come hardcoded nel firewall di Palo Alto. Se si rimuove il numero di porta, verrà generato un errore durante l'accesso.

    > La porta 443 è obbligatoria in **Identificatore** e **URL di risposta** perché questi valori sono impostati come hardcoded nel firewall di Palo Alto. Se si rimuove il numero di porta, verrà generato un errore durante l'accesso.

1. L'applicazione Palo Alto Networks - Admin UI prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione di attributi token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

   > [!NOTE]
   > Poiché i valori degli attributi sono solo esempi, associare i valori appropriati per *username* e *adminrole*. Esiste un altro attributo facoltativo, *accessdomain*, che viene usato per limitare l'accesso dell'amministratore a sistemi virtuali specifici sul firewall.

1. Oltre quelli elencati in precedenza, l'applicazione Palo Alto Networks - Admin UI prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | Nome |  Attributo di origine|
    | --- | --- |
    | username | user.userprincipalname |
    | adminrole | customadmin |
    | | |

    > [!NOTE]
    > Per altre informazioni sugli attributi, vedere gli articoli seguenti:
    > * [Administrative role profile for Admin UI (adminrole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile) (Profilo del ruolo amministrativo per Admin UI - adminrole)
    > * [Device access domain for Admin UI (accessdomain)](https://docs.paloaltonetworks.com/pan-os/8-0/pan-os-web-interface-help/device/device-access-domain.html) (Dominio di accesso al dispositivo per Admin UI - accessdomain)

1. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura Palo Alto Networks - Admin UI** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di disconnessione

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Palo Alto Networks - Admin UI.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Palo Alto Networks - Admin UI**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="configure-palo-alto-networks---admin-ui-sso"></a>Configurare l'accesso Single Sign-On di Palo Alto Networks - Admin UI

1. Aprire l'interfaccia utente di amministrazione del firewall di Palo Alto Networks come amministratore in una nuova finestra.

2. Selezionare la scheda **Device** (Dispositivo).

    ![Scheda Device (Dispositivo)](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Nel riquadro a sinistra selezionare **SAML Identity Provider** (Provider di identità SAML) e quindi **Import** (Importa) per importare il file di metadati.

    ![Pulsante di importazione del file di metadati](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Nella finestra **SAML Identity Provider Server Profile Import** (Importazione profilo server provider di identità SAML) eseguire queste operazioni:

    ![Finestra "SAML Identity Provider Server Profile Import" (Importazione profilo server provider di identità SAML)](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. Nella casella di testo **Profile Name** (Nome profilo) specificare un nome, ad esempio **AzureAD Admin UI**.

    b. In **Identity Provider Metadata** (Metadati provider di identità) selezionare **Browse** (Sfoglia) e quindi selezionare il file metadata.xml scaricato in precedenza dal portale di Azure.

    c. Deselezionare la casella di controllo **Validate Identity Provider Certificate** (Convalida certificato provider di identità).

    d. Selezionare **OK**.

    e. Per eseguire il commit delle configurazioni nel firewall, selezionare **Commit**.

5. Nel riquadro a sinistra selezionare **SAML Identity Provider** (Provider di identità SAML) e quindi selezionare il profilo del provider di identità SAML, ad esempio **AzureAD Admin UI**, creato nel passaggio precedente.

    ![Profilo del provider di identità SAML](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

6. Nella finestra **SAML Identity Provider Server Profile** (Profilo server provider di identità SAML) eseguire queste operazioni:

    ![Finestra "SAML Identity Provider Server Profile" (Profilo server provider di identità SAML)](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. Nella casella di testo **Identity Provider SLO URL** (URL SLO provider di identità) sostituire l'URL SLO precedentemente importato con questo URL: `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`
  
    b. Selezionare **OK**.

7. Nell'interfaccia utente di amministrazione del firewall di Palo Alto Networks selezionare **Device** (Dispositivo) e quindi selezionare **Admin Roles** (Ruoli amministrazione).

8. Fare clic sul pulsante **Aggiungi**.

9. Nella casella **Name** (Nome) nella finestra **Admin Role Profile** (Profilo ruolo amministratore) specificare un nome per il ruolo di amministratore, ad esempio **fwadmin**. Il nome del ruolo di amministratore deve corrispondere al nome dell'attributo del ruolo di amministratore SAML inviato dal provider di identità. Il nome e il valore del ruolo di amministratore sono stati creati nella sezione **Attributi utente** del portale di Azure.

    ![Configurare il ruolo di amministratore per Palo Alto Networks](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
10. Nell'interfaccia utente di amministrazione del firewall selezionare **Device** (Dispositivo) e quindi **Authentication Profile** (Profilo di autenticazione).

11. Fare clic sul pulsante **Aggiungi**.

12. Nella finestra **Authentication Profile** (Profilo di autenticazione) eseguire queste operazioni: 

    ![Finestra "Authentication Profile" (Profilo di autenticazione)](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. Nella casella di testo **Name** (Nome) specificare un nome, ad esempio **AzureSAML_Admin_AuthProfile**.

    b. Nell'elenco a discesa **Type** (Tipo) selezionare **SAML**. 

    c. Nell'elenco a discesa **IdP Server Profile** (Profilo server provider di identità) selezionare il profilo per il server del provider di identità SAML appropriato, ad esempio **AzureAD Admin UI**.

    c. Selezionare la casella di controllo **Enable Single Logout** (Abilita punto di disconnessione singolo).

    d. Nella casella **Admin Role Attribute** (Attributo ruolo di amministratore) immettere il nome dell'attributo, ad esempio **adminrole**.

    e. Selezionare la scheda **Advanced** (Avanzate) e quindi selezionare **Add** (Aggiungi) in **Allow List** (Elenco nomi consentiti).

    ![Pulsante Add (Aggiungi) nella scheda Advanced (Avanzate)](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)

    f. Selezionare la casella di controllo **All** (Tutti) oppure selezionare gli utenti e i gruppi che possono autenticarsi con questo profilo.  
    Quando un utente esegue l'autenticazione, il firewall trova la corrispondenza tra il nome utente o il gruppo associato e le voci incluse nell'elenco. Se non vengono aggiunte voci, nessun utente può eseguire l'autenticazione.

    g. Selezionare **OK**.

13. Per consentire agli amministratori di usare l'accesso SSO SAML tramite Azure, selezionare **Device** (Dispositivo) > **Setup** (Imposta). Nel riquadro **Setup** (Imposta) selezionare la scheda **Management** (Gestione) e quindi in **Authentication Settings** (Impostazioni autenticazione) selezionare il pulsante **Settings** (Impostazioni) raffigurato da un ingranaggio.

    ![Pulsante Settings (Impostazioni)](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

14. Selezionare il profilo di autenticazione SAML creato nella finestra Authentication Profile (Profilo di autenticazione), ad esempio **AzureSAML_Admin_AuthProfile**.

    ![Campo Authentication Profile (Profilo di autenticazione)](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

15. Selezionare **OK**.

16. Per eseguire il commit della configurazione, selezionare **Commit**.

### <a name="create-palo-alto-networks---admin-ui-test-user"></a>Creare un utente di test di Palo Alto Networks - Admin UI

Palo Alto Networks - Admin UI supporta il provisioning utenti JIT. Se non esiste già, un utente viene automaticamente creato nel sistema dopo un tentativo di autenticazione riuscito. Non è necessario creare manualmente l'utente.

### <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Palo Alto Networks - Admin UI nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Palo Alto Networks - Admin UI per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Palo Alto Networks - Admin UI con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Come proteggere Palo Alto Networks - Admin UI con visibilità e controlli avanzati](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
