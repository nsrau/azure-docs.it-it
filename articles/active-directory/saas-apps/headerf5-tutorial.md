---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con F5 | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e F5.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 59a87abb-1ec1-4438-be07-5b115676115f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5eb5cedf14af9a013a5b6a1eba5df40d665cbad5
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74181328"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con F5

Questa esercitazione descrive come integrare F5 con Azure Active Directory (Azure AD). Integrando F5 con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a F5.
* Abilitare gli utenti per l'accesso automatico a F5 con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).

* Sottoscrizione di F5 abilitata per l'accesso Single Sign-On (SSO).

* Per distribuire la soluzione congiunta è necessaria la licenza seguente:

    * Bundle Best di F5 BIG-IP® Best oppure 

    * Licenza autonoma di F5 BIG-IP Access Policy Manager™ (APM) 

    * Licenza del componente aggiuntivo F5 BIG-IP Access Policy Manager™ (APM) su un'istanza esistente di BIG-IP F5 BIG-IP® Local Traffic Manager™ (LTM).

    * Oltre alla licenza precedente, il sistema F5 può anche essere concesso in licenza con: 

        * Una sottoscrizione di filtro degli URL per l'uso del database di categoria URL

        * Una sottoscrizione di F5 IP Intelligence per rilevare e bloccare utenti malintenzionati noti e traffico dannoso
     
        * Un modulo di protezione hardware di rete per proteggere e gestire le chiavi digitali per l'autenticazione avanzata

* Con il sistema F5 BIG-IP viene effettuato il provisioning dei moduli APM (LTM è facoltativo)

* Anche se facoltativo, è consigliabile distribuire i sistemi F5 in un [gruppo di dispositivi di sincronizzazione/failover](https://techdocs.f5.com/content/techdocs/en-us/bigip-14-1-0/big-ip-device-service-clustering-administration-14-1-0.html), che include la coppia attiva in standby, con un indirizzo IP mobile per la disponibilità elevata. Per ottenere una maggiore ridondanza dell'interfaccia, usare il protocollo LACP (Link Aggregation Control Protocol). LACP gestisce le interfacce fisiche connesse come una singola interfaccia virtuale (gruppo di aggregazione) e rileva eventuali errori di interfaccia nel gruppo.

* Per le applicazioni Kerberos un account del servizio AD locale per la delega vincolata.  Per informazioni sulla creazione dell'account di delega AD, vedere la [documentazione di F5](https://support.f5.com/csp/article/K43063049).

## <a name="access-guided-configuration"></a>Configurazione guidata dell'accesso

* La configurazione guidata dell'accesso è supportata in F5 TMOS a partire dalla versione 13.1.0.8. Se il sistema BIG-IP esegue una versione precedente alla 13.1.0.8, vedere la sezione **Configurazione avanzata**.

* La configurazione guidata dell'accesso offre un'esperienza innovativa e semplificata. Questa architettura basata sul flusso di lavoro offre procedure di configurazione intuitive e ripetitive personalizzate per la topologia selezionata.

* Prima di procedere alla configurazione, aggiornare la configurazione guidata scaricando il pacchetto di casi d'uso più recente da [downloads.f5.com](https://login.f5.com/resource/login.jsp?ctx=719748). Per eseguire l'aggiornamento, seguire la procedura descritta di seguito.

    >[!NOTE]
    >Gli screenshot seguenti si riferiscono all'ultima versione rilasciata (BIG-IP 15.0 con AGC versione 5.0). La procedura di configurazione seguente è valida per questo caso d'uso dalla versione 13.1.0.8 fino alla versione più recente di BIG-IP.

1. Nell'interfaccia utente Web di F5 BIG-IP fare clic su **Access >> Configuration** (Accesso >> Configurazione).

1. Nella pagina **Guided Configuration** (Configurazione guidata) fare clic su **Upgrade Guided Configuration** (Aggiorna configurazione guidata) nell'angolo in alto a sinistra.

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure14.png) 

1. Nella schermata popup di Upgrade Guided Configuration (Aggiorna configurazione guidata) selezionare **Choose File** (Scegli file) per caricare il pacchetto dei casi d'uso e fare clic sul pulsante **Upload and Install** (Carica e installa).

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure15.png) 

1. Al termine dell'aggiornamento, fare clic sul pulsante **Continue** (Continua).

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure16.png)

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* L'accesso SSO di F5 può essere configurato in tre diversi modi.

- [Configurare l'accesso Single Sign-On di F5 per l'applicazione Header Based](#configure-f5-single-sign-on-for-header-based-application)

- [Configurare l'accesso Single Sign-On di F5 per l'applicazione Kerberos](kerbf5-tutorial.md)

- [Configurare l'accesso Single Sign-On di F5 per l'applicazione Advanced Kerberos](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>Scenari di autenticazione principali

* Oltre allo supporto dell'integrazione nativa di Azure Active Directory per i protocolli di autenticazione moderni, come Open ID Connect, SAML e WS-Fed, F5 estende l'accesso sicuro per le app di autenticazione basate su scenari legacy per l'accesso interno ed esterno con Azure AD, abilitando scenari moderni (ad esempio, l'accesso senza password) per queste applicazioni. Tali scenari includono:

* App di autenticazione basata su intestazione

* App di autenticazione Kerberos

* Autenticazione anonima oppure nessuna app di autenticazione incorporata

* App di autenticazione NTLM (protezione con doppio prompt per l'utente)

* Applicazione basata su moduli (protezione con doppio prompt per l'utente)

## <a name="adding-f5-from-the-gallery"></a>Aggiunta di F5 dalla raccolta

Per configurare l'integrazione di F5 in Azure AD, è necessario aggiungere F5 dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **F5** nella casella di ricerca.
1. Selezionare **F5** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per F5

Configurare e testare l'accesso SSO di Azure AD con F5 usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in F5.

Per configurare e testare l'accesso SSO di Azure AD con F5, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di F5](#configure-f5-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di F5](#create-f5-test-user)** : per avere una controparte di B.Simon in F5 collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **F5** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<YourCustomFQDN>.f5.com/`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<YourCustomFQDN>.f5.com/`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e il **certificato (Base64)** , quindi selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura F5** copiare gli URL appropriati in base alle esigenze.

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

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a F5.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **F5**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.
1. Fare clic su **Accesso condizionale**.
1. Fare clic su **Nuovi criteri**.
1. È ora possibile visualizzare l'app F5 come risorsa per i criteri della CA e applicare qualsiasi accesso condizionale, tra cui autenticazione a più fattori, controllo di accesso in base al dispositivo o criteri di protezione delle identità.

## <a name="configure-f5-sso"></a>Configurare l'accesso Single Sign-On di F5

- [Configurare l'accesso Single Sign-On di F5 per l'applicazione Kerberos](kerbf5-tutorial.md)

- [Configurare l'accesso Single Sign-On di F5 per l'applicazione Advanced Kerberos](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-header-based-application"></a>Configurare l'accesso Single Sign-On di F5 per l'applicazione Header Based

### <a name="guided-configuration"></a>Configurazione guidata

1. Aprire una nuova finestra del Web browser, accedere al sito aziendale di F5 (Header Based) come amministratore e seguire questa procedura:

1. Passare a **System > Certificate Management > Traffic Certificate Management > SSL Certificate List** (Sistema > Gestione certificati > Traffico gestione certificati > Elenco certificati SSL). Nell'angolo a destra selezionare **Import** (Importa). Specificare un valore in **Certificate Name** (Nome certificato), a cui verrà fatto riferimento più avanti nella configurazione. In **Certificate Source** (Origine certificato) selezionare Upload File (Carica file) e specificare il certificato scaricato da Azure durante la configurazione dell'accesso SSO SAML. Fare clic su **Importa**.

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure12.png)
 
1. Sarà inoltre necessario specificare un **certificato SSL per il nome host dell'applicazione. Passare a System > Certificate Management > Traffic Certificate Management > SSL Certificate List** (Sistema > Gestione certificati > Traffico gestione certificati > Elenco certificati SSL). Nell'angolo a destra selezionare **Import** (Importa). L'opzione **Import Type** (Tipo di importazione) sarà impostata su **PKCS 12(IIS)** . Specificare un valore in **Key Name** (Nome chiave ), a cui verrà fatto riferimento più avanti nella configurazione, quindi specificare il file PFX. Specificare la **password** per il file PFX. Fare clic su **Importa**.

    >[!NOTE]
    >Nell'esempio il nome dell'app è `Headerapp.superdemo.live` e viene usato un certificato Wildcard. Il nome della chiave è `WildCard-SuperDemo.live`.

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure13.png)

1. Verrà usata l'esperienza guidata per configurare la federazione e l'accesso alle applicazioni di Azure AD. Passare a **Main** (Principale) in F5 BIG-IP e selezionare **Access > Guided Configuration > Federation > SAML Service Provider** (Accesso > Configurazione guidata > Federazione > Provider di servizi SAML). Fare clic su **Next** (Avanti) e quindi di nuovo su **Next** (Avanti) per avviare la configurazione.

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure01.png)

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure02.png)
 
1. Specificare un valore in **Configuration Name** (Nome configurazione). Specificare il valore di **Entity ID** (ID entità), indicando lo stesso valore specificato nella configurazione dell'applicazione di Azure AD. Specificare un valore in **Host name** (Nome host). Aggiungere una **descrizione** per riferimento. Accettare le voci predefinite rimanenti e quindi fare clic su **Save & Next** (Salva e avanti).

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure03.png) 

1. In questo esempio viene creato un nuovo server virtuale 192.168.30.20 con la porta 443. Specificare l'indirizzo IP del server virtuale in **Destination Address** (Indirizzo di destinazione). Selezionare il **profilo SSL** del client e quindi selezionare Create new (Crea nuovo). Specificare il certificato dell'applicazione caricato in precedenza (il certificato Wildcard in questo esempio) e la chiave associata e quindi fare clic su **Save & Next** (Salva e avanti).

    >[!NOTE]
    >In questo esempio il server Web interno è in esecuzione sulla porta 888 e si vuole pubblicarlo con la porta 443.

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure04.png) 

1. In **Select method to configure your IdP connector** (Selezionare il metodo per configurare il connettore IdP), specificare Metadata (Metadati), fare clic su Choose File (Scegli file) e caricare il file XML dei metadati scaricato in precedenza da Azure AD. Specificare un valore **Name** (Nome) univoco per il connettore IDP SAML. Scegliere il **certificato di firma dei metadati** caricato in precedenza. Fare clic su **Save & Next** (Salva e avanti).

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure05.png)
 
1. In **Select a Pool** (Seleziona un pool) specificare **Create New** (Crea nuovo); in alternativa, selezionare un pool se è già esistente. Non modificare gli altri valori predefiniti. In Pool Servers (Server pool) digitare l'indirizzo IP in **IP Address/Node Name** (Indirizzo IP/Nome del nodo). Specificare la **porta**. Fare clic su **Save & Next** (Salva e avanti).

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure06.png)

1. Nella schermata Single Sign-On Settings (Impostazioni Single Sign-On) selezionare **Enable Single Sign-On** (Abilita Single Sign-On). In Selected Single Sign-On Type (Tipo di accesso Single Sign-On selezionato) scegliere **HTTP header-based** (Basato su intestazione HTTP). Sostituire **session.saml.last.Identity** con **session.saml.last.attr.name.Identity** in Username Source (Origine nome utente). Questa variabile viene impostata usando il mapping di attestazioni in Azure AD. In SSO Headers (Intestazioni SSO) specificare:

    * **Header Name (Nome intestazione): MyAuthorization**

    * **Header Value (Valore intestazione): %{session.saml.last.attr.name.Identity}**

    * Fare clic su **Save & Next** (Salva e avanti).

    Fare riferimento all'appendice per l'elenco completo delle variabili e dei valori. Se necessario, è possibile aggiungere altre intestazioni.

    >[!NOTE]
    >Il nome account è l'account di delega F5 creato (consultare la documentazione di F5).

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure07.png) 

1. Ai fini di questa guida i controlli degli endpoint verranno ignorati.  Per informazioni dettagliate, vedere la documentazione di F5. Selezionare **Save & Next** (Salva e avanti).

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure08.png)

1. Accettare le impostazioni predefinite e fare clic su **Save & Next** (Salva e avanti). Per informazioni dettagliate sulle impostazioni di gestione delle sessioni SAML, vedere la documentazione di F5.

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure09.png)

1. Esaminare la schermata di riepilogo e selezionare **Deploy** (Distribuisci) per configurare BIG-IP. Fare clic su **Finish** (Fine).

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure10.png)

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure11.png)

## <a name="advanced-configuration"></a>Configurazione avanzata

Usare questa sezione se non è possibile usare la configurazione guidata oppure se si intende aggiungere/modificare parametri aggiuntivi. Sarà necessario specificare un certificato SSL per il nome host dell'applicazione.

1. Passare a **System > Certificate Management > Traffic Certificate Management > SSL Certificate List** (Sistema > Gestione certificati > Traffico gestione certificati > Elenco certificati SSL). Nell'angolo a destra selezionare **Import** (Importa). L'opzione **Import Type** (Tipo di importazione) sarà impostata su **PKCS 12(IIS)** . Specificare un valore in **Key Name** (Nome chiave ), a cui verrà fatto riferimento più avanti nella configurazione, quindi specificare il file PFX. Specificare la **password** per il file PFX. Fare clic su **Importa**.

    >[!NOTE]
    >Nell'esempio il nome dell'app è `Headerapp.superdemo.live` e viene usato un certificato Wildcard. Il nome della chiave è `WildCard-SuperDemo.live`.
  
    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure17.png)

### <a name="adding-a-new-web-server-to-bigip-f5"></a>Aggiunta di un nuovo server Web a BigIP-F5

1. Fare clic su **Main > IApps > Application Services > Application > Create** (Principale > IApps > Servizi applicazione > Applicazione > Crea).

1. Specificare un valore in **Name** (Nome) e in **Template** (Modello) scegliere **f5.http**.
 
    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure18.png)

1. In questo caso l'app HeaderApp2 verrà pubblicata esternamente come HTTPS. In **How should the BIG-IP system handle SSL Traffic?** (In che modo il sistema BIG-IP gestirà il traffico SSL?) specificare **Terminate SSL from Client, Plaintext to servers (SSL Offload)** (Termina SSL da client, testo non crittografato in server (offload SSL)). Specificare il certificato e la chiave in Which SSL certificate do you want to use? (Quale certificato SSL si vuole usare) e in **Which SSL private key do you want to use?** (Quale chiave privata SSL si vuole usare?). Specificare l'indirizzo IP del server virtuale in **What IP Address do you want to use for the Virtual Server?** (Quale indirizzo IP si vuole usare per il server virtuale?). 

    * **Specificare altri dettagli**

        * Nome di dominio completo  

        * Specificare il pool di app di uscita o crearne uno nuovo.

        * Se si crea un nuovo server app specificare l'**indirizzo IP interno** e il **numero di porta**.

        ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure19.png) 

1. Fare clic su **Finished** (Operazione completata).

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure20.png) 

1. Assicurarsi che le proprietà dell'app siano modificabili. Fare clic su **Main > IApps > Application Services: Applications >> HeaderApp2** (Principale > IApps > Servizi applicazione: Applicazioni >> HeaderApp2). Deselezionare **Strict Updates** (Aggiornamenti restrittivi). Alcune impostazioni verranno modificate all'esterno dell'interfaccia utente grafica. Fare clic sul pulsante **Update** (Aggiorna).

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure21.png) 

1. A questo punto dovrebbe essere possibile sfogliare il server virtuale.

### <a name="configuring-f5-as-sp-and-azure-as-idp"></a>Configurazione di F5 come SP e di Azure come IDP

1.  Fare clic su **Access > Federation> SAML Service Provider > Local SP Service** (Accesso > Federazione > Provider di servizi SAML > Servizio SP locale) e quindi fare clic su Create (Crea) o sul segno +.

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure22.png)

1. Specificare i dettagli per il servizio del provider di servizi. Specificare il **nome** che rappresenta la configurazione SP di F5. Specificare l'**ID entità** (in genere uguale all'URL dell'applicazione).

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure23.png)

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure24.png)

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure25.png)

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure26.png)

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure27.png)

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure28.png)

### <a name="create-idp-connector"></a>Creare il connettore IdP

1. Fare clic sul pulsante **Bind/Unbind IdP Connectors** (Associa/Dissocia connettori IdP), selezionare **Create New IdP Connector** (Crea nuovo connettore IdP) e scegliere **From Metadata** (Da metadati), quindi seguire questa procedura:
 
    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure29.png)

    a. Individuare il file metadata.xml scaricato da Azure AD e specificare un **nome del provider di identità**.

    b. Fare clic su **OK**.

    c. Il connettore viene creato e il certificato è generato automaticamente dal file XML dei metadati.
    
    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure30.png)

    d. Configurare F5 BIG-IP in modo da inviare tutte le richieste ad Azure AD.

    e. Fare clic su **Add New Row** (Aggiungi nuova riga) scegliere **AzureIDP** (creato nei passaggi precedenti), quindi specificare le impostazioni seguenti: 

    f. **Matching Source (Origine corrispondente) = %{session.server.landinguri}** 

    g. **Matching Value (Valore corrispondente) = /** *

    h. Fare clic su **Update** (Aggiorna).

    i. Fare clic su **OK**.

    j. **La configurazione dell'IDP SAML è stata completata**
    
    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure31.png)

### <a name="configure-f5-policy-to-redirect-users-to-azure-saml-idp"></a>Configurare i criteri di F5 per reindirizzare gli utenti all'IDP SAML di Azure

1. Per configurare i criteri di F5 in modo da reindirizzare all'IDP SAML di Azure, seguire questa procedura:

    a. Fare clic su **Main > Access > Profile/Policies > Access Profiles** (Principale > Accesso > Profilo/Criteri > Criteri di accesso).

    b. Fare clic sul pulsante **Crea**.

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure32.png)
 
    c. Specificare il **nome** (per questo esempio HeaderAppAzureSAMLPolicy).

    d. Per personalizzare altre impostazioni, vedere la documentazione di F5.

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure33.png)

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure34.png) 

    e. Fare clic su **Finished** (Operazione completata).

    f. Dopo aver completato la creazione dei criteri, fare clic sui criteri e passare alla scheda **Access Policy** (Criteri di accesso).

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure35.png)
 
    g. Fare clic su **Visual Policy editor** (Editor dei criteri visivo) e modificare il collegamento **Access Policy for Profile** (Criteri di accesso per il profilo).

    h. Fare clic sul segno + nell'editor dei criteri visivo e scegliere **SAML Auth** (Autenticazione SAML).

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure36.png)

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure37.png)
 
    i. Fare clic su **Add Item** (Aggiungi elemento).

    j. In **Properties** (Proprietà) specificare il **nome** e in **AAA Server** (Server AAA) selezionare il provider di servizi configurato in precedenza e fare clic su **SAVE** (SALVA).
 
    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure38.png)

    k. I criteri di base sono pronti ed è possibile personalizzarli per incorporare origini/archivi di attributi aggiuntivi.

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure39.png)
 
    l. Assicurarsi di fare clic sul collegamento **Apply Access Policy** (Applica criteri di accesso) in alto.

### <a name="apply-access-profile-to-the-virtual-server"></a>Applicare il profilo di accesso al server virtuale

1. Assegnare il profilo di accesso al server virtuale per consentire all'APM di F5 BIG-IP di applicare le impostazioni del profilo al traffico in ingresso ed eseguire i criteri di accesso definiti in precedenza.

    a. Fare clic su **Main** (Principale)  > **Local Traffic** (Traffico locale)  > **Virtual Servers** (Server virtuali).

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure40.png)
 
    b. Fare clic sul server virtuale, scorrere fino alla sezione **Access Policy** (Criteri di accesso) e nell'elenco a discesa **Access Profile** (Profilo di accesso) selezionare i criteri SAML creati (nell'esempio HeaderAppAzureSAMLPolicy).

    c. Fare clic su **Update** (Aggiorna).
 
    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure41.png)

    d. Creare una iRule® di F5 BIG-IP per estrarre gli attributi SAML personalizzati dall'asserzione in arrivo e passarli come intestazioni HTTP all'applicazione di test back-end. Fare clic su **Main > Local Traffic > iRules > iRule List** (Principale > Traffico locale > IRules > Elenco di iRule) e quindi fare clic su Create (Crea).

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure42.png)
 
    e. Incollare il testo della iRule di F5 BIG-IP riportato di seguito nella finestra Definition (Definizione).

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure43.png)
 
    when RULE_INIT {  set static::debug 0  }  when ACCESS_ACL_ALLOWED {

    set AZUREAD_USERNAME [ACCESS::session data get "session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"] if { $static::debug } { log local0. "AZUREAD_USERNAME = $AZUREAD_USERNAME" } if { !([HTTP::header exists "AZUREAD_USERNAME"]) } { HTTP::header insert "AZUREAD_USERNAME" $AZUREAD_USERNAME }

    set AZUREAD_DISPLAYNAME [ACCESS::session data get "session.saml.last.attr.name. http://schemas.microsoft.com/identity/claims/displayname"] if { $static::debug } { log local0. "AZUREAD_DISPLAYNAME = $AZUREAD_DISPLAYNAME" } if { !([HTTP::header exists "AZUREAD_DISPLAYNAME"]) } { HTTP::header insert "AZUREAD_DISPLAYNAME" $AZUREAD_DISPLAYNAME }

    set AZUREAD_EMAILADDRESS [ACCESS::session data get "session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress"] if { $static::debug } { log local0. "AZUREAD_EMAILADDRESS = $AZUREAD_EMAILADDRESS" } if { !([HTTP::header exists "AZUREAD_EMAILADDRESS"]) } { HTTP::header insert "AZUREAD_EMAILADDRESS" $AZUREAD_EMAILADDRESS }}

    **Esempio di output**

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure44.png)
 
### <a name="create-f5-test-user"></a>Creare l'utente di test di F5

In questa sezione viene creato un utente di nome B.Simon in F5. Collaborare con il  [team di supporto clienti di F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) per aggiungere gli utenti alla piattaforma F5. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On. 

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di F5 nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di F5 per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare F5 con Azure AD](https://aad.portal.azure.com/)

- [Configurare l'accesso Single Sign-On di F5 per l'applicazione Kerberos](kerbf5-tutorial.md)

- [Configurare l'accesso Single Sign-On di F5 per l'applicazione Advanced Kerberos](advance-kerbf5-tutorial.md)

