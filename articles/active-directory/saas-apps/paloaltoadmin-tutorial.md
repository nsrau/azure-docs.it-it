---
title: 'Esercitazione: Integrazione di Azure Active Directory con Palo Alto Networks - Admin UI | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Palo Alto Networks - Admin UI.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: a826eaec-15af-4c85-8855-8a3374d1efb9
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82560d0767b6865dded3e14e661fe89b7132ab95
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56869854"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---admin-ui"></a>Esercitazione: Integrazione di Azure Active Directory con Palo Alto Networks - Admin UI

Questa esercitazione descrive come integrare Palo Alto Networks - Admin UI con Azure Active Directory (Azure AD).
L'integrazione di Palo Alto Networks - Admin UI con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Palo Alto Networks - Admin UI.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Palo Alto Networks - Admin UI con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
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

**Per aggiungere Palo Alto Networks - Admin UI dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Palo Alto Networks - Admin UI**, selezionare **Palo Alto Networks - Admin UI** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Palo Alto Networks - Admin UI nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Palo Alto Networks - Admin UI usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Palo Alto Networks - Admin UI.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Palo Alto Networks - Admin UI, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Palo Alto Networks - Admin UI](#configure-palo-alto-networks---admin-ui-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Palo Alto Networks - Admin UI](#create-palo-alto-networks---admin-ui-test-user)**: per avere una controparte di Britta Simon in Palo Alto Networks - Admin UI collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Palo Alto Networks - Admin UI, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Palo Alto Networks - Admin UI** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Palo Alto Networks - Admin UI](common/sp-identifier-reply.png)

    a. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<Customer Firewall FQDN>/php/login.php`

    b. Nella casella di testo **Identificatore** digitare un URL usando il modello seguente: `https://<Customer Firewall FQDN>:443/SAML20/SP`

    c. Nella casella di testo **URL di risposta** digitare l'URL del servizio consumer di asserzione (ACS) usando il formato seguente: `https://<Customer Firewall FQDN>:443/SAML20/SP/ACS`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Palo Alto Networks - Admin UI](https://support.paloaltonetworks.com/support). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. L'applicazione Palo Alto Networks - Admin UI prevede un formato specifico per le asserzioni SAML. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![image](common/edit-attribute.png)

   > [!NOTE]
   > Poiché i valori degli attributi sono solo esempi, associare i valori appropriati per *username* e *adminrole*. Esiste un altro attributo facoltativo, *accessdomain*, che viene usato per limitare l'accesso dell'amministratore a sistemi virtuali specifici sul firewall.
   >

6. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura:

    | NOME |  Source Attribute|
    | --- | --- |
    | username | user.userprincipalname |
    | adminrole | customadmin |
    | | |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    f. Fare clic su **Ok**

    g. Fare clic su **Save**.

    > [!NOTE]
    > Per altre informazioni sugli attributi, vedere gli articoli seguenti:
    > * [Administrative role profile for Admin UI (adminrole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile) (Profilo del ruolo amministrativo per Admin UI - adminrole)
    > * [Device access domain for Admin UI (accessdomain)](https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain) (Dominio di accesso al dispositivo per Admin UI - accessdomain)

7. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

8. Nella sezione **Configura Palo Alto Networks - Admin UI** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-palo-alto-networks---admin-ui-single-sign-on"></a>Configurare l'accesso Single Sign-On di Palo Alto Networks - Admin UI

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

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

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

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Palo Alto Networks - Admin UI.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Palo Alto Networks - Admin UI**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Palo Alto Networks - Admin UI**.

    ![Collegamento di Palo Alto Networks - Admin UI nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-palo-alto-networks---admin-ui-test-user"></a>Creare un utente di test di Palo Alto Networks - Admin UI

Palo Alto Networks - Admin UI supporta il provisioning utenti JIT. Se non esiste già, un utente viene automaticamente creato nel sistema dopo un tentativo di autenticazione riuscito. Non è necessario creare manualmente l'utente.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Palo Alto Networks - Admin UI nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Palo Alto Networks - Admin UI per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
