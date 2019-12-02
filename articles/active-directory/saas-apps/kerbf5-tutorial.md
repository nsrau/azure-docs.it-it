---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con F5 | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e F5.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 39382eab-05fe-4dc2-8792-62d742dfb4e1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9efaeb9d3fe0ec8684f10c58897f5490d0f28cb9
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74182684"
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

2. Nella pagina **Guided Configuration** (Configurazione guidata) fare clic su **Upgrade Guided Configuration** (Aggiorna configurazione guidata) nell'angolo in alto a sinistra.

    ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure14.png) 

3. Nella schermata popup di Upgrade Guided Configuration (Aggiorna configurazione guidata) selezionare **Choose File** (Scegli file) per caricare il pacchetto dei casi d'uso e fare clic sul pulsante **Upload and Install** (Carica e installa).

    ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure15.png) 

4. Al termine dell'aggiornamento, fare clic sul pulsante **Continue** (Continua).

    ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure16.png)

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* F5 supporta l'accesso SSO avviato da **SP e IDP**
* L'accesso SSO di F5 può essere configurato in tre diversi modi.

- [Configurare l'accesso Single Sign-On di F5 per l'applicazione Kerberos](#configure-f5-single-sign-on-for-kerberos-application)

- [Configurare l'accesso Single Sign-On di F5 per l'applicazione Header Based](headerf5-tutorial.md)

- [Configurare l'accesso Single Sign-On di F5 per l'applicazione Advanced Kerberos](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>Scenari di autenticazione principali

Oltre allo supporto dell'integrazione nativa di Azure Active Directory per i protocolli di autenticazione moderni, come Open ID Connect, SAML e WS-Fed, F5 estende l'accesso sicuro per le app di autenticazione basate su scenari legacy per l'accesso interno ed esterno con Azure AD, abilitando scenari moderni (ad esempio, l'accesso senza password) per queste applicazioni. Tali scenari includono:

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

- [Configurare l'accesso Single Sign-On di F5 per l'applicazione Header Based](headerf5-tutorial.md)

- [Configurare l'accesso Single Sign-On di F5 per l'applicazione Advanced Kerberos](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-kerberos-application"></a>Configurare l'accesso Single Sign-On di F5 per l'applicazione Kerberos

### <a name="guided-configuration"></a>Configurazione guidata

1. Aprire una nuova finestra del Web browser, accedere al sito aziendale di F5 (Kerberos) come amministratore e seguire questa procedura:

1. Sarà necessario importare il Certificato dei metadati in F5 che verrà usato più avanti nel processo di configurazione.

1. Passare a **System > Certificate Management > Traffic Certificate Management > SSL Certificate List** (Sistema > Gestione certificati > Traffico gestione certificati > Elenco certificati SSL). Nell'angolo a destra selezionare **Import** (Importa). Specificare un valore in **Certificate Name** (Nome certificato), a cui verrà fatto riferimento più avanti nella configurazione. In **Certificate Source** (Origine certificato) selezionare Upload File (Carica file) e specificare il certificato scaricato da Azure durante la configurazione dell'accesso SSO SAML. Fare clic su **Importa**.

    ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure01.png) 

1. Sarà inoltre necessario specificare un **certificato SSL per il nome host dell'applicazione. Passare a System > Certificate Management > Traffic Certificate Management > SSL Certificate List** (Sistema > Gestione certificati > Traffico gestione certificati > Elenco certificati SSL). Nell'angolo a destra selezionare **Import** (Importa). L'opzione **Import Type** (Tipo di importazione) sarà impostata su **PKCS 12(IIS)** . Specificare un valore in **Key Name** (Nome chiave ), a cui verrà fatto riferimento più avanti nella configurazione, quindi specificare il file PFX. Specificare la **password** per il file PFX. Fare clic su **Importa**.

    >[!NOTE]
    >Nell'esempio il nome dell'app è `Kerbapp.superdemo.live` e viene usato un certificato Wildcard. Il nome della chiave è `WildCard-SuperDemo.live`.

    ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure02.png) 
 
1. Verrà usata l'esperienza guidata per configurare la federazione e l'accesso alle applicazioni di Azure AD. Passare a **Main** (Principale) in F5 BIG-IP e selezionare **Access > Guided Configuration > Federation > SAML Service Provider** (Accesso > Configurazione guidata > Federazione > Provider di servizi SAML). Fare clic su **Next** (Avanti) e quindi di nuovo su **Next** (Avanti) per avviare la configurazione.

    ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure03.png) 

    ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure04.png)

1. Specificare un valore in **Configuration Name** (Nome configurazione). Specificare il valore di **Entity ID** (ID entità), indicando lo stesso valore specificato nella configurazione dell'applicazione di Azure AD. Specificare un valore in **Host name** (Nome host). Aggiungere una **descrizione** per riferimento. Accettare le voci predefinite rimanenti e quindi fare clic su **Save & Next** (Salva e avanti).

    ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure05.png) 

1. In questo esempio viene creato un nuovo server virtuale 192.168.30.200 con la porta 443. Specificare l'indirizzo IP del server virtuale in **Destination Address** (Indirizzo di destinazione). Selezionare il **profilo SSL** del client e quindi selezionare Create new (Crea nuovo). Specificare il certificato dell'applicazione caricato in precedenza (il certificato Wildcard in questo esempio) e la chiave associata e quindi fare clic su **Save & Next** (Salva e avanti).

    >[!NOTE]
    >In questo esempio il server Web interno è in esecuzione sulla porta 80 e si vuole pubblicarlo con la porta 443.

    ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure06.png)

1. In **Select method to configure your IdP connector** (Selezionare il metodo per configurare il connettore IdP), specificare Metadata (Metadati), fare clic su Choose File (Scegli file) e caricare il file XML dei metadati scaricato in precedenza da Azure AD. Specificare un valore **Name** (Nome) univoco per il connettore IDP SAML. Scegliere il **certificato di firma dei metadati** caricato in precedenza. Fare clic su **Save & Next** (Salva e avanti).

    ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure07.png)  

1. In **Select a Pool** (Seleziona un pool) specificare **Create New** (Crea nuovo); in alternativa, selezionare un pool se è già esistente. Non modificare gli altri valori predefiniti. In Pool Servers (Server pool) digitare l'indirizzo IP in **IP Address/Node Name** (Indirizzo IP/Nome del nodo). Specificare la **porta**. Fare clic su **Save & Next** (Salva e avanti).
 
    ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure08.png)

1. Nella schermata Single Sign-On Settings (Impostazioni Single Sign-On) selezionare **Enable Single Sign-On** (Abilita Single Sign-On). In **Selected Single Sign-On Type** (Tipo di accesso Single Sign-On selezionato) scegliere **Kerberos**. Sostituire **session.saml.last.Identity** con **session.saml.last.attr.name.Identity** in **Username Source** (Origine nome utente). Questa variabile viene impostata usando il mapping di attestazioni in Azure AD. Selezionare **Show Advanced Setting** (Mostra impostazione avanzata). In **Kerberos Realm** (Area di autenticazione Kerberos) digitare il nome di dominio. In **Account Name/ Account Password** (Nome account/Password account) specificare l'account e la password di delega APM. Specificare l'indirizzo IP del controller di dominio nel campo **KDC**. Fare clic su **Save & Next** (Salva e avanti).

    ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure09.png)   

1. Ai fini di questa guida i controlli degli endpoint verranno ignorati.  Per informazioni dettagliate, vedere la documentazione di F5.  Nella schermata selezionare **Save & Next** (Salva e avanti).

    ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure10.png) 

1. Accettare le impostazioni predefinite e fare clic su **Save & Next** (Salva e avanti). Per informazioni dettagliate sulle impostazioni di gestione delle sessioni SAML, consultare la documentazione di F5.


    ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure11.png) 
 
1. Esaminare la schermata di riepilogo e selezionare **Deploy** (Distribuisci) per configurare BIG-IP.
 
    ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure12.png)

1. Dopo aver configurato l'applicazione, fare clic su **Finish** (Fine).

    ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure13.png)

## <a name="advanced-configuration"></a>Configurazione avanzata

>[!NOTE]
>Per informazioni di riferimento, fare clic [qui](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)

### <a name="configuring-an-active-directory-aaa-server"></a>Configurazione di un server AAA di Active Directory

Per specificare i controller di dominio e le credenziali per Access Policy Manager (APM) da usare per l'autenticazione degli utenti, è necessario configurare un server AAA di Active Directory in APM.

1.  Nella scheda Main (Principale) fare clic su **Access Policy > AAA Servers > Active Directory** (Criteri di accesso > Server AAA > Active Directory). Viene visualizzata la schermata dell'elenco Active Directory Servers (Server Active Directory).

2.  Fare clic su **Create**(Crea). Viene visualizzata la schermata New Server properties (Proprietà del nuovo server).

3.  Nel campo **Name** (Nome) digitare un nome univoco per il server di autenticazione.

4.  Nel campo **Domain Name** (Nome di dominio) digitare il nome di dominio di Windows.

5.  Per l'impostazione **Server Connection** (Connessione server) selezionare una di queste opzioni:

    * Selezionare **Use Pool** (Usa pool) per configurare la disponibilità elevata per il server AAA.

    * Selezionare **Direct** (Diretto) per configurare il server AAA per la funzionalità autonoma.

6.  Se è stato selezionato **Direct** (Diretto), digitare un nome nel campo **Domain Controller** (Controller di dominio).

7.  Se è stata selezionata l'opzione **Use Pool** (Usa pool), configurare il pool:

    * Digitare un nome nel campo **Domain Controller Pool Name** (Nome del pool del controller di dominio).

    * Specificare i **controller di dominio** nel pool digitando l'indirizzo IP e il nome host di ognuno e quindi facendo clic sul pulsante **Add** (Aggiungi).

    * Per monitorare lo stato del server AAA, è possibile selezionare un monitoraggio dello stato. In questo caso è appropriato solo il monitoraggio **gateway_icmp**, che può essere selezionato nell'elenco **Server Pool Monitor** (Monitoraggio pool di server).

8.  Nel campo **Admin Name** (Nome amministratore) digitare un nome con distinzione tra maiuscole e minuscole per un amministratore che dispone delle autorizzazioni amministrative di Active Directory. APM usa le informazioni contenute nei campi **Admin Name** (Nome amministratore) e **Admin Password** (Password amministratore) per la query AD. Se Active Directory è configurato per le query anonime, non è necessario specificare alcun nome di amministratore. In caso contrario, APM necessita di un account con privilegi sufficienti per eseguire il binding a un server Active Directory, recuperare informazioni sui gruppi di utenti e recuperare i criteri password di Active Directory per supportare la funzionalità correlata alle password. APM deve recuperare i criteri password, ad esempio, se si seleziona l'opzione Prompt user to change password before expiration (Richiedi all'utente di modificare la password prima della scadenza) in un'azione di query di Active Directory. Se non si specificano le informazioni sull'account amministratore in questa configurazione, APM usa l'account utente per recuperare le informazioni. Questo approccio funziona se l'account utente dispone di privilegi sufficienti.

9.  Nel campo **Admin Password** (Password amministratore) digitare la password dell'amministratore associata al nome di dominio.

10. Nel campo **Verify Admin Password** (Verifica password amministratore) digitare la password dell'amministratore associata all'impostazione **Domain Name** (Nome di dominio).

11. Nel campo **Group Cache Lifetime** (Durata cache di gruppo) digitare il numero di giorni. La durata predefinita è 30 giorni.

12. Nel campo **Password Security Object Cache Lifetime** (Durata cache oggetti di sicurezza password) digitare il numero di giorni. La durata predefinita è 30 giorni.

13. Nell'elenco **Kerberos Preauthentication Encryption Type** (Tipo di crittografia preautenticazione Kerberos) selezionare un tipo di crittografia. L'impostazione predefinita è **None** (Nessuna). Se si specifica un tipo di crittografia, il sistema BIG-IP include i dati di preautenticazione Kerberos all'interno del primo pacchetto della richiesta del servizio di autenticazione (AS-REQ).

14. Nel campo **Timeout** digitare un intervallo di timeout (in secondi) per il server AAA. Questa impostazione è facoltativa.

15. Fare clic su **Finished** (Operazione completata). Il nuovo server viene visualizzato nell'elenco. Il nuovo server Active Directory verrà aggiunto all'elenco dei server di Active Directory.

    ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure17.png)

### <a name="saml-configuration"></a>SAML Configuration

1. Sarà necessario importare il Certificato dei metadati in F5 che verrà usato più avanti nel processo di configurazione. Passare a **System > Certificate Management > Traffic Certificate Management > SSL Certificate List** (Sistema > Gestione certificati > Traffico gestione certificati > Elenco certificati SSL). Nell'angolo a destra selezionare **Import** (Importa).

    ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure18.png)

2. Per la configurazione dell'IDP SAML, passare a **Access > Federation > SAML: Service Provider > External Idp Connectors** (Accesso> Federazione > SAML: Provider di servizi > Connettori IdP esterni) e fare clic su **Create > From Metadata** (Crea > Da metadati).

    ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure19.png)

    ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure20.png)

    ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure21.png)

    ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure22.png)

    ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure23.png)

    ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure24.png)

1. Per la configurazione del provider di servizi SAML, passare a **Access > Federation > SAML Service Provider > Local SP Services** (Accesso> Federazione > Provider di servizi SAML > Servizi SP locali) e fare clic su **Create** (Crea). Completare le informazioni seguenti e fare clic su **OK**.

    * Name (Nome): KerbApp200SAML
    * Entity ID* (ID entità): https://kerbapp200.superdemo.live
    * SP Name Settings (Impostazioni nome SP)
    * Scheme (Schema): https
    * Host: kerbapp200.superdemo.live
    * Description (Descrizione): kerbapp200.superdemo.live

     ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure25.png)

     b. Selezionare la configurazione SP KerbApp200SAML e fare clic su **Bind/UnBind IdP Connectors** (Associa/Dissocia connettori IdP).

     ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure26.png)

     ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure27.png)

     c. Fare clic su **Add New Row** (Aggiungi nuova riga) e selezionare il valore di **External IdP connector** (Connettore IdP esterno) creato nel passaggio precedente, fare clic su **Update** (Aggiorna) e quindi su **OK**.

     ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure28.png)

1. Per configurare l'accesso SSO per Kerberos, passare a **Access > Single Sign-on > Kerberos** (Accesso > Single Sign-on > Kerberos), completare le informazioni e fare clic su **Finished** (Operazione completata).

    >[!Note]
    > Sarà necessario creare e specificare l'account di delega Kerberos. Fare riferimento alla sezione KCD (vedere l'appendice relativa ai riferimenti per le variabili)

    * **Username Source** (Origine nome utente): session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname

    * **User Realm Source** (Origine area di autenticazione utente): session.logon.last.domain

        ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure29.png)

1. Per la configurazione del profilo di accesso, passare a **Access > Profile/Policies > Access Profile (per session policies)** (Accesso > Profilo/Criteri > Profilo di accesso (per criteri sessione)), fare clic su **Create** (Crea), completare le informazioni seguenti e fare clic su **Finished** (Operazione completata).

    * Nome: KerbApp200
    * Profile Type (Tipo di profilo): Tutti
    * Profile Scope (Ambito del profilo): Profilo
    * Linguaggi: Inglese

        ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure30.png)

1. Fare clic sul nome KerbApp200, completare le informazioni seguenti e fare clic su **Update** (Aggiorna).

    * Domain Cookie (Cookie di dominio): superdemo.live
    * SSO Configuration (Configurazione SSO): KerAppSSO_sso

        ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure31.png)

1. Fare clic su **Access Policy** (Criteri di accesso) e quindi fare clic su **Edit Access Policy** (Modifica criteri di accesso) per il profilo KerbApp200.

    ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure32.png)

    ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure33.png)

    ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure34.png)

    * **session.logon.last.usernameUPN   expr {[mcget {session.saml.last.identity}]}**

    * **session.ad.lastactualdomain  TEXT superdemo.live**

        ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure35.png)

    * **(userPrincipalName=%{session.logon.last.usernameUPN})**

        ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure36.png)

        ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure37.png)

    * **session.logon.last.username  expr { "[mcget {session.ad.last.attr.sAMAccountName}]" }**

        ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure38.png)

    * **mcget {session.logon.last.username}**
    * **mcget {session.logon.last.password**

1. Per aggiungere un nuovo nodo, passare a **Local Traffic > Nodes > Node List** (Traffico locale > Nodi > Elenco nodi), fare clic su Create (Crea), completare le informazioni seguenti e quindi fare clic su **Finished** (Operazione completata).

    * Nome: KerbApp200
    * Descrizione: KerbApp200
    * Indirizzo: 192.168.20.200

        ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure39.png)

1. Per creare un nuovo pool, passare a **Local Traffic > Pools > Pool List** (Traffico locale > Pool > Elenco pool), fare clic su Create (Crea), completare le informazioni seguenti e quindi fare clic su **Finished** (Operazione completata).

    * Nome: KerbApp200-Pool
    * Descrizione: KerbApp200-Pool
    * Health Monitors (Monitoraggi dello stato): http
    * Indirizzo: 192.168.20.200
    * Service Port (Porta del servizio): 81

        ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure40.png)

1. Per creare un nuovo server virtuale, passare a **Local Traffic > Virtual Servers > Virtual Server List > +** (Traffico locale > Server virtuali > Elenco server virtuali > +), completare le informazioni seguenti e quindi fare clic su **Finished** (Operazione completata).

    * Nome: KerbApp200
    * Destination Address/Mask (Indirizzo/maschera di destinazione): Host 192.168.30.200
    * Service Port (Porta del servizio): Port (Porta) 443 HTTPS
    * Access Profile (Profilo di accesso): KerbApp200
    * Specificare il profilo di accesso creato nel passaggio precedente.

        ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure41.png)

        ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure42.png)

### <a name="setting-up-kerberos-delegation"></a>Configurazione della delega Kerberos 

>[!NOTE]
>Per informazioni di riferimento, fare clic [qui](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf)

*  **Passaggio 1:** Creare un account di delega

    **Esempio:**
    * Domain Name (Nome di dominio): **superdemo.live**

    * Sam Account Name (Nome dell'account SAM): **big-ipuser**

    * New-ADUser -Name "APM Delegation Account" -UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live -SamAccountName "big-ipuser" -PasswordNeverExpires $true -Enabled $true -AccountPassword (Read-Host -AsSecureString "Password!1234")

* **Passaggio 2:** Configurare SPN (sull'account di delega APM)

    **Esempio:**
    * setspn –A **host/big-ipuser.superdemo.live** big-ipuser

* **Passaggio 3:** Delega SPN (per l'account del servizio app). Configurare la delega appropriata per l'account di delega F5.
    Nell'esempio seguente l'account di delega APM viene configurato per KCD per l'app FRP-App1.superdemo. live.

    ![Configurazione di F5 (Kerberos)](./media/kerbf5-tutorial/configure43.png)

* Specificare i dettagli come indicato nel documento di riferimento precedente disponibile [qui](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html).

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

- [Configurare l'accesso Single Sign-On di F5 per l'applicazione Header Based](headerf5-tutorial.md)

- [Configurare l'accesso Single Sign-On di F5 per l'applicazione Advanced Kerberos](advance-kerbf5-tutorial.md)

