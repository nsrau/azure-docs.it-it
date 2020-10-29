---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Oracle PeopleSoft - Protected by F5 BIG-IP APM | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Oracle PeopleSoft - Protected by F5 BIG-IP APM.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: jeedes
ms.openlocfilehash: 3b7c8e024ac8361c08cc41195531a114bb12fcb4
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92522292"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-oracle-peoplesoft---protected-by-f5-big-ip-apm"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Oracle PeopleSoft - Protected by F5 BIG-IP APM

Questa esercitazione descrive come integrare Oracle PeopleSoft - Protected by F5 BIG-IP APM con Azure Active Directory (Azure AD). Integrando Oracle PeopleSoft - Protected by F5 BIG-IP APM con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Oracle PeopleSoft - Protected by F5 BIG-IP APM.
* Abilitare gli utenti per l'accesso automatico a Oracle PeopleSoft - Protected by F5 BIG-IP APM con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.
* Questa esercitazione illustra le istruzioni per Oracle PeopleSoft ELM.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

1. Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
1. Sottoscrizione di Oracle PeopleSoft - Protected by F5 BIG-IP APM abilitata per l'accesso Single Sign-On (SSO).

1. Per distribuire la soluzione congiunta è necessaria la licenza seguente:

    1. Bundle Best di F5 BIG-IP® Best oppure 
    2. Licenza autonoma di F5 BIG-IP Access Policy Manager™ (APM) 
    3. Licenza del componente aggiuntivo F5 BIG-IP Access Policy Manager™ (APM) su un'istanza esistente di BIG-IP F5 BIG-IP® Local Traffic Manager™ (LTM).
    4. Oltre alla licenza precedente, il sistema F5 può anche essere concesso in licenza con: 
        * Una sottoscrizione di filtro degli URL per l'uso del database di categoria URL 
        * Una sottoscrizione di F5 IP Intelligence per rilevare e bloccare utenti malintenzionati noti e traffico dannoso 
        * Un modulo di protezione hardware di rete per proteggere e gestire le chiavi digitali per l'autenticazione avanzata
1. Con il sistema F5 BIG-IP viene effettuato il provisioning dei moduli APM (LTM è facoltativo) 
1. Anche se facoltativo, è consigliabile distribuire i sistemi F5 in un [gruppo di dispositivi di sincronizzazione/failover](https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/big-ip-device-service-clustering-administration-14-1-0.html), che include la coppia attiva in standby, con un indirizzo IP mobile per la disponibilità elevata. Per ottenere una maggiore ridondanza dell'interfaccia, usare il protocollo LACP (Link Aggregation Control Protocol). LACP gestisce le interfacce fisiche connesse come una singola interfaccia virtuale (gruppo di aggregazione) e rileva eventuali errori di interfaccia nel gruppo.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Oracle PeopleSoft - Protected by F5 BIG-IP APM supporta l'accesso SSO avviato da **SP e IDP**

## <a name="adding-oracle-peoplesoft---protected-by-f5-big-ip-apm-from-the-gallery"></a>Aggiunta di Oracle PeopleSoft - Protected by F5 BIG-IP APM dalla raccolta

Per configurare l'integrazione di Oracle PeopleSoft - Protected by F5 BIG-IP APM in Azure AD, è necessario aggiungere Oracle PeopleSoft - Protected by F5 BIG-IP APM dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory** .
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni** .
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** .
1. Nella sezione **Aggiungi dalla raccolta** digitare **Oracle PeopleSoft - Protected by F5 BIG-IP APM** nella casella di ricerca.
1. Selezionare **Oracle PeopleSoft - Protected by F5 BIG-IP APM** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-oracle-peoplesoft---protected-by-f5-big-ip-apm"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Oracle PeopleSoft - Protected by F5 BIG-IP APM

Configurare e testare l'accesso SSO di Azure AD con Oracle PeopleSoft - Protected by F5 BIG-IP APM usando un utente di test di nome **B.Simon** . Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Oracle PeopleSoft - Protected by F5 BIG-IP APM.

Per configurare e testare l'accesso SSO di Azure AD con Oracle PeopleSoft - Protected by F5 BIG-IP APM, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Oracle PeopleSoft-Protected by F5 BIG-IP APM](#configure-oracle-peoplesoft-protected-by-f5-big-ip-apm-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Oracle PeopleSoft-Protected by F5 BIG-IP APM](#create-oracle-peoplesoft-protected-by-f5-big-ip-apm-test-user)** : per avere una controparte di B.Simon in Oracle PeopleSoft - Protected by F5 BIG-IP APM collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Oracle PeopleSoft - Protected by F5 BIG-IP APM** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On** .
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML** .
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP** :

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<FQDN>.peoplesoft.f5.com`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<FQDN>.peoplesoft.f5.com/saml/sp/profile/post/acs`

    c. Nella casella di testo **URL di disconnessione** digitare un URL nel formato seguente: `https://<FQDN>.peoplesoft.f5.com/saml/sp/profile/redirect/slr`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP** :

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<FQDN>.peoplesoft.f5.com/`

    > [!NOTE]
    >Poiché questi non sono i valori reali, aggiornarli con quelli effettivi relativi a URL di accesso, identificatore, URL di risposta e URL di disconnessione. Per ottenere tali valori, contattare il [team di supporto clienti di Oracle PeopleSoft - Protected by F5 BIG-IP APM](https://support.f5.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione Oracle PeopleSoft - Protected by F5 BIG-IP APM prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre quelli elencati in precedenza, l'applicazione Oracle PeopleSoft - Protected by F5 BIG-IP APM prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.
    
    | Nome |  Attributo di origine|
    | ------------------ | --------- |
    | EMPLID | user.employeeid |

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** scaricare il file **XML dei metadati di federazione** e il **certificato (Base64)** e salvarli nel computer.

    ![Collegamento di download del certificato](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/both-certificate.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory** , **Utenti** e quindi **Tutti gli utenti** .
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password** .
   1. Fare clic su **Crea** .

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Oracle PeopleSoft - Protected by F5 BIG-IP APM.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni** .
1. Nell'elenco delle applicazioni selezionare **Oracle PeopleSoft - Protected by F5 BIG-IP APM** .
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi** .
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione** .
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo** . Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna** .

## <a name="configure-oracle-peoplesoft-protected-by-f5-big-ip-apm-sso"></a>Configurare l'accesso Single Sign-On di Oracle PeopleSoft-Protected by F5 BIG-IP APM

### <a name="f5-saml-sp-configuration"></a>Configurazione SP SAML di F5

Importare in F5 il certificato dei metadati che verrà usato più avanti nel processo di configurazione. Passare a **System > Certificate Management > Traffic Certificate Management > SSL Certificate List** (Sistema > Gestione certificati > Traffico gestione certificati > Elenco certificati SSL). Nell'angolo a destra selezionare **Import** (Importa).

![Configurazione SP SAML di F5](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sp-configuration.png)

#### <a name="setup-the-saml-idp-connector"></a>Configurare il connettore IDP SAML 

1. Passare a **Access > Federation > SAML: Service Provider > External Idp Connectors** (Accesso> Federazione > SAML: Provider di servizi > Connettori IdP esterni) e fare clic su **Create > From Metadata** (Crea > Da metadati).

    ![Connettore IDP SAML di F5](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/saml-idp-connector.png)

1. Nella pagina seguente fare clic su **Sfoglia** per caricare il file XML.

1. Nella casella di testo **Identity Provider Name** (Nome del provider di identità) specificare un nome valido e fare clic su **OK** .

    ![Nuovo connettore IDP SAML](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/new-saml-idp.png)

1. Eseguire i passaggi richiesti nella scheda **Security Settings** (Impostazioni di sicurezza) e fare clic su **OK** .

    ![Modificare il connettore IDP SAML](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/edit-saml-idp.png)

#### <a name="setup-the-saml-sp"></a>Configurare il provider di servizi SAML

1. Passare a **Access > Federation > SAML Service Provider > Local SP Services** (Accesso> Federazione > Provider di servizi SAML > Servizi SP locali) e fare clic su **Create** (Crea). Completare le informazioni seguenti e fare clic su **OK** .

    * Nome: `<Name>`
    * Entity ID (ID entità): `https://<FQDN>.peoplesoft.f5.com`
    * SP Name Settings (Impostazioni nome SP)
        * Scheme (Schema): `https`
        * Host: `<FQDN>.peoplesoft.f5.com`
        * Description (Descrizione): `<Description>`

    ![Nuovi servizi SP SAML](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/new-saml-sp-service.png)

1. Selezionare la configurazione del provider di servizi PeopleSoftAppSSO e fare clic su **Bind/UnBind IdP Connectors** (Associa/Dissocia connettori IdP).
Fare clic su **Add New Row** (Aggiungi nuova riga) e selezionare il valore di **External IdP connector** (Connettore IdP esterno) creato nel passaggio precedente, fare clic su **Update** (Aggiorna) e quindi su **OK** .

    ![Creare servizi SP SAML](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/edit-saml-idp-use-sp.png)

## <a name="configuring-application"></a>Configurazione dell'applicazione

### <a name="create-a-new-pool"></a>Creare un nuovo pool
1. Passare a **Local Traffic > Pools > Pool List** (Traffico locale > Pool > Elenco pool), fare clic su **Create** (Crea), completare le informazioni seguenti e quindi fare clic su **Finished** (Operazione completata).

    * Nome: `<Name>`
    * Description (Descrizione): `<Description>`
    * Health Monitors (Monitoraggi integrità): `http`
    * Address (Indirizzo): `<Address>`
    * Service Port (Porta del servizio): `<Service Port>`

    ![Creazione di un nuovo pool](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/create-pool.png)


### <a name="create-a-new-client-ssl-profile"></a>Creare un profilo SSL del client

Passare a **Local Traffic > Profiles > SSL > Client > +** (Traffico locale > Profili > SSL > Client > +), completare le informazioni seguenti e quindi fare clic su **Finished** (Operazione completata).

* Nome: `<Name>`
* Certificate (Certificato): `<Certificate>`
* Chiave: `<Key>`

![Nuovo profilo SSL del client](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/client-ssl-profile.png)

### <a name="create-a-new-virtual-server"></a>Creare un nuovo server virtuale

1. Passare a **Local Traffic > Virtual Servers > Virtual Server List > +** (Traffico locale > Server virtuali > Elenco server virtuali > +), completare le informazioni seguenti e quindi fare clic su **Finished** (Operazione completata).
    * Nome: `<Name>`
    * Destination Address/Mask (Indirizzo/maschera di destinazione): `<Address>`
    * Service Port (Porta del servizio): Port (Porta) 443 HTTPS
    * HTTP Profile (Client) (Profilo HTTP - client): http

    ![Creare un nuovo server virtuale](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/virtual-server-list.png)

1. Inserire questi valori nella pagina seguente:

    * SSL Profile (Client) (Profilo SSL - client): `<SSL Profile>`
    * Source Address Translation (Conversione indirizzo di origine): Auto Map (Mapping automatico)
    * Access Profile (Profilo di accesso): `<Access Profile>`
    * Default Pool (Pool predefinito): `<Pool>`


    ![Creare un nuovo pool di server virtuali PeopleSoft](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/virtual-server-people-soft.png)

## <a name="setting-up-peoplesoft-application-to-support-f5-big-ip-apm-as-the-single-sign-on-solution"></a>Configurazione dell'applicazione PeopleSoft per supportare F5 Big-IP APM come soluzione per l'accesso Single Sign-On

>[!Note]
> Fare riferimento a https://docs.oracle.com/cd/E12530_01/oam.1014/e10356/people.htm

1. Accedere a Peoplesoft Console `https://<FQDN>.peoplesoft.f5.com/:8000/psp/ps/?cmd=start` usando le credenziali di amministratore, ad esempio PS/PS.

    ![Manager Self Service](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-console.png)

1. Nell'applicazione PeopleSoft creare un nuovo profilo utente **OAMPSFT** e associarvi un ruolo di sicurezza di livello inferiore, ad esempio **PeopleSoft User** (Utente PeopleSoft).
Passare a **PeopleTools > Security > User Profiles > User Profiles** (PeopleTools > Sicurezza > Profili utente > Profili utente) per creare un nuovo profilo utente, ad esempio **OAMPSFT** e aggiungere **Peoplesoft User** (Utente PeopleSoft).

    ![PeopleSoft User](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/user-profile.png)

1. Accedere al profilo Web e immettere **OAMPSFT** come **ID utente** per l'accesso pubblico.

    ![Profili utente](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/web-profile-configuration.png)

1. In **PeopleTools Application Designer** (Progettazione applicazioni PeopleTools) aprire il record **FUNCLIB_LDAP** .

    ![Configurazione del profilo Web](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/funclib.png)

1. Aggiornare l'intestazione utente con **PS_SSO_UID** per la funzione **OAMSSO_AUTHENTICATION** .
Nella funzione **getWWWAuthConfig()** sostituire il valore assegnato a **&defaultUserId** con **OAMPSFT** , definito nel profilo Web. Salvare la definizione del record.

    ![OAMSSO_AUTHENTICATION](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/record.png)

1. Accedere alla pagina **Signon PeopleCode** (Accesso PeopleCode) facendo clic su PeopleTools, Security, Security Objects, Signon PeopleCode (PeopleTools > Sicurezza > Oggetti di sicurezza > Accesso PeopleCode) e abilitare la funzione **OAMSSO_AUTHENTICATION** , l'accesso PeopleCode per l'accesso Single Sign-On di Oracle Access Manager.

    ![OAMSSO_AUTHENTICATION ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sign-on-people-soft.png)

## <a name="setting-up-f5-big-ip-apm-to-populate-ps_sso_uid-http-header-with-the-peoplesoft-user-id"></a>Configurazione di F5 Big-IP APM per la compilazione dell'intestazione HTTP "PS_SSO_UID" con l'ID utente PeopleSoft

### <a name="configuring-per-request-policy"></a>Configurazione del criterio per singola richiesta
1. Passare a **Access > Profile/Policies > Per-Request Policies** (Accesso > Profilo/Criteri > Criteri per singola richiesta), fare clic su **Create** (Crea), completare le informazioni seguenti e fare clic su **Finished** (Operazione completata).

    * Nome: `<Name>`
    * Profile Type (Tipo di profilo): Tutti
    * Languages (Lingue): `<Language>`

    ![Configurazione del criterio per singola richiesta ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/per-request.png)

1. Fare clic su **Edit** (Modifica) per il criterio per singola richiesta `<Name>` ![Modifica del criterio per singola richiesta PeopleSoftSSO ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-sso.png)

    `Header Name: <Header Name>`   
    `Header Value: <Header Value>`

### <a name="assign-per-request-policy-to-the-virtual-server"></a>Assegnare il criterio per singola richiesta al server virtuale

Passare a **Local Traffic > Virtual Servers > Virtual Server List > PeopleSoftApp** (Traffico locale > Server virtuali > Elenco server virtuali > PeopleSoftApp). Specificare `<Name>` come criterio per singola richiesta.

![PeopleSoftSSO come criterio per singola richiesta ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-sso-1.png)

## <a name="setting-up-f5-big-ip-apm-to-support-single-logout-from-peoplesoft-application"></a>Configurazione di F5 Big-IP APM per supportare la disconnessione singola dall'applicazione PeopleSoft

Per aggiungere il supporto per la disconnessione singola per tutti gli utenti PeopleSoft, seguire questa procedura:

1. Determinare l'URL di disconnessione corretto per il portale PeopleSoft
    * Per determinare l'indirizzo usato dall'applicazione PeopleSoft per terminare una sessione utente, è necessario aprire il portale con un qualsiasi Web browser e abilitare gli strumenti di debug del browser, come illustrato nell'esempio seguente:

        ![URL di disconnessione per il portale PeopleSoft ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sign-out.png)

    * Trovare l'elemento con l'ID `PT_LOGOUT_MENU` e salvare il percorso dell'URL con i parametri di query. In questo esempio il valore ottenuto è: `/psp/ps/?cmd=logout`

1. Creare la regola LTM iRule per reindirizzare gli utenti all'URL di disconnessione di APM: `/my.logout.php3`

    * Passare a **Local Traffic > iRule** (Traffico locale > Regola iRule), fare clic su **Create** (Crea), completare le informazioni seguenti e quindi fare clic su **Finished** (Operazione completata).

        Nome: `<Name>`  
        Definizione:  
                    _when HTTP_REQUEST { switch -glob -- [HTTP::uri] { `/psp/ps/?cmd=logout` { HTTP::redirect `/my.logout.php3` } } }_

1. Assegnare la regola iRule creata al server virtuale

    * Passare a **Local Traffic > Virtual Servers > Virtual Server List > PeopleSoftApp > Resources** (Traffico locale > Server virtuali > Elenco server virtuali > PeopleSoftApp > Risorse). Fare clic sul pulsante **Manage…** (Gestisci) :   

    * Specificare `<Name>` come regola iRule abilitata e fare clic su **Finished** (Operazione completata)

        ![_iRule_PeopleSoftApp ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/irule-people-soft.png)

    * Nella casella di testo **Name** (Nome) specificare il valore `<Name>` 

        ![Definizione di _iRule_PeopleSoftApp completata](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/common-irule.png)


### <a name="create-oracle-peoplesoft-protected-by-f5-big-ip-apm-test-user"></a>Creare l'utente di test di Oracle PeopleSoft-Protected by F5 BIG-IP APM

In questa sezione viene creato un utente di nome B.Simon in Oracle PeopleSoft-Protected by F5 BIG-IP APM. Collaborare con il [team di supporto di Oracle PeopleSoft-Protected by F5 BIG-IP APM](https://support.f5.com) per aggiungere gli utenti alla piattaforma Oracle PeopleSoft-Protected by F5 BIG-IP APM. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Oracle PeopleSoft-Protected by F5 BIG-IP APM, in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso di Oracle PeopleSoft-Protected by F5 BIG-IP APM e avviare il flusso di accesso da questa posizione.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Dopo aver fatto clic su **Test this application** (Testa questa applicazione) nel portale di Azure, si dovrebbe accedere automaticamente all'istanza di Oracle PeopleSoft-Protected by F5 BIG-IP APM per cui si è configurato l'accesso SSO 

È anche possibile usare il pannello di accesso Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro di Oracle PeopleSoft-Protected by F5 BIG-IP APM nel pannello di accesso, se è stato configurato in modalità SP, si dovrebbe essere reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso; se invece è configurato in modalità IDP, si dovrebbe accedere automaticamente all'istanza di Oracle PeopleSoft-Protected by F5 BIG-IP APM per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Oracle PeopleSoft - Protected by F5 BIG-IP APM, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).