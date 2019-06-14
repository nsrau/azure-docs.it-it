---
title: Pianificare una distribuzione di Proxy applicazione Azure Active Directory
description: Una guida end-to-end per la pianificazione della distribuzione del proxy dell'applicazione all'interno dell'organizzazione
services: active-directory
documentationcenter: azure
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: baselden
ms.reviewer: ''
ms.openlocfilehash: 24429c5596494082b526b9648a1405bc397b9d2f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108473"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>Pianificare una distribuzione di Proxy applicazione Azure AD

Proxy di applicazione di Azure Active Directory (Azure AD) è una soluzione di accesso remoto sicuro e conveniente per applicazioni locali. Fornisce un percorso di transizione immediata "Cloud First" alle organizzazioni di gestire l'accesso a legacy applicazioni locali che non sono ancora in grado di utilizzare protocolli moderni. Per altre informazioni introduttive, vedere [What ' s Proxy di applicazione](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy).

Proxy dell'applicazione è consigliato per concedere agli utenti remoti l'accesso alle risorse interne. Proxy dell'applicazione sostituisce la necessità di una rete VPN o il proxy inverso per questi casi d'uso di accesso remoto. Non è destinato per gli utenti nella rete aziendale. Questi utenti che usano Proxy di applicazione per l'accesso intranet potrebbero verificarsi problemi delle prestazioni.

Questo articolo include le risorse che necessarie per pianificare, operare e gestire Azure AD Application Proxy. 

## <a name="plan-your-implementation"></a>Pianificare l'implementazione

La sezione seguente offre una visione più ampia della chiave di elementi che si imposteranno per un'esperienza di distribuzione efficienti di pianificazione. 

### <a name="prerequisites"></a>Prerequisiti

È necessario soddisfare i prerequisiti seguenti prima di iniziare l'implementazione. È possibile visualizzare altre informazioni su come configurare l'ambiente, compresi i prerequisiti, in questo [esercitazione](application-proxy-add-on-premises-application.md).

* **I connettori**: I connettori sono agenti semplici che è possibile distribuire in:
   * Hardware fisico in locale
   * Una macchina virtuale ospitata all'interno di qualsiasi soluzione di hypervisor
   * Una macchina virtuale ospitata in Azure per abilitare la connessione in uscita al servizio Proxy di applicazione.

* Visualizzare [comprendere Proxy applicazione Azure AD connettori](application-proxy-connectors.md) per una panoramica più dettagliata.

     * Connettore macchine devono [essere abilitato per TLS 1.2](application-proxy-add-on-premises-application.md) prima di installare i connettori.

     * Se possibile, distribuire i connettori il [stessa rete](application-proxy-network-topology.md) e nello stesso segmento come i server applicazioni back-end web. È consigliabile distribuire i connettori dopo aver completato un'individuazione di applicazioni.
     * È consigliabile che ogni gruppo di connettori abbia almeno due connettori per assicurare scalabilità e disponibilità elevata. Con tre connettori è ottimale nel caso in cui potrebbe essere necessario un computer in qualsiasi punto del servizio. Rivedere le [tabella capacità connettore](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#capacity-planning) alla assistenza decidere quale tipo di computer per installare i connettori in. Il connettore sarà possibile maggiore nel computer più buffer e ad alte prestazioni.

* **Le impostazioni di accesso di rete**: I connettori di Azure AD Application Proxy [connettersi ad Azure tramite HTTPS (porta TCP 443) e HTTP (porta TCP 80)](application-proxy-add-on-premises-application.md). 

   * Connettore di terminazione del traffico TLS non è supportata e impedirà i connettori di stabilire un canale sicuro con i rispettivi endpoint Proxy App di Azure.

   * Evitare di tutte le forme di ispezione inline nelle comunicazioni TLS in uscita tra i connettori e Azure. Il controllo interno tra un connettore e back-end di applicazioni viene possibile, ma potrebbe influire negativamente l'esperienza utente e di conseguenza, non è consigliato.

   * Il bilanciamento del carico dei connettori di se stessi inoltre non è supportato, o persino necessari.

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>Considerazioni importanti prima di configurare Proxy applicazione Azure AD

Devono essere soddisfatti i requisiti di base seguenti per configurare e implementare Proxy applicazione Azure AD.

*  **L'onboarding di Azure**: Prima di distribuire il proxy di applicazione, le identità utente devono essere sincronizzate da una directory in locale o create direttamente all'interno di tenant di Azure AD. La sincronizzazione delle identità consente ad Azure AD di preautenticare gli utenti prima di concedere loro l'accesso alle applicazioni pubblicate tramite proxy di applicazione e di ottenere le informazioni degli ID utente necessarie per eseguire l'accesso Single Sign-On (SSO).

* **Requisiti di accesso condizionali**: Non è consigliabile tramite Proxy di applicazione per l'accesso intranet, perché ciò aggiunge latenza che hanno un impatto degli utenti. È consigliabile usare il Proxy di applicazione con la preautenticazione e criteri di accesso condizionale per l'accesso remoto da internet.  Un approccio per fornire l'accesso condizionale per l'uso di intranet è per modernizzare le applicazioni in modo da poter diretly eseguire l'autenticazione con AAD. Fare riferimento a [risorse per la migrazione alle applicazioni di AAD](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) per altre informazioni. 

* **Limiti dei servizi**: Per proteggere il consumo eccessivo di risorse di base a singoli tenant presenti sono limitazioni impostate per ogni tenant e applicazioni. Per visualizzare questi limiti si intende [restrizioni e limiti del servizio Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions). Queste soglie di limitazione si basano su un benchmark notevolmente superiori volume di utilizzo tipici e fornisce un buffer molto ampio per la maggior parte delle distribuzioni.

* **Certificato pubblico**: Se si utilizzano nomi di dominio personalizzato, è necessario ottenere un certificato pubblico generato da un'autorità di certificazione attendibili non Microsoft. A seconda delle esigenze dell'organizzazione, come ottenere un certificato può richiedere molto tempo ed è consigliabile iniziare il processo di quanto prima possibile. Proxy applicazione di Azure supporta standard, [jolly](application-proxy-wildcard.md), o i certificati basati su SAN.

* **Requisiti di dominio**: Single sign-on alle applicazioni pubblicate mediante la delega vincolata Kerberos (KCD) richiede che un host del connettore sia aggiunto al dominio allo stesso dominio Active Directory come le applicazioni in fase di pubblicazione. Per informazioni dettagliate sull'argomento, vedere [delega vincolata Kerberos per single sign-on](application-proxy-configure-single-sign-on-with-kcd.md) con Proxy dell'applicazione. Il servizio del connettore viene eseguito nel contesto di sistema locale e non deve essere configurato per usare un'identità personalizzata.

* **Record DNS per gli URL**

   * Prima di usare domini personalizzati nel Proxy di applicazione è necessario creare un record CNAME nel DNS pubblico, che consente ai client di risolvere l'URL esterno definito personalizzato per l'indirizzo del Proxy di applicazione predefinito. Errori durante la creazione di un record CNAME per un'applicazione che usa un dominio personalizzato impedirà agli utenti remoti di connettersi all'applicazione. Passaggi necessari per aggiungere i record CNAME possono variare da DNS provider a provider, quindi, informazioni su come [gestire i set di record e record DNS usando il portale di Azure](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-portal).

   * Analogamente, gli host del connettore devono essere in grado di risolvere l'URL interno delle applicazioni da pubblicare.

* **Ruoli e diritti amministrativi**

   * **Installazione del connettore** richiede diritti di amministratore locale nel server di Windows che viene installata in. È inoltre richiesto un minimo di un *amministratore dell'applicazione* ruolo per autenticare e registrare l'istanza di connector per tenant di Azure AD. 

   * **Pubblicazione dell'applicazione e l'amministrazione** richiedono il *amministratore applicazione* ruolo. Gli amministratori dell'applicazione possono gestire tutte le applicazioni nella directory comprese le registrazioni, le impostazioni SSO, utente e le assegnazioni di gruppi e licenze, le impostazioni del Proxy di applicazione e il consenso. Non consente tuttavia di gestire l'accesso condizionale. Il *amministratore applicazione Cloud* ruolo ha tutte le capacità dell'amministratore dell'applicazione, ad eccezione del fatto che non consente la gestione delle impostazioni del Proxy di applicazione.

* **Licenze**: Proxy dell'applicazione è disponibile tramite la sottoscrizione di Azure AD Basic. Vedere le [pagina dei prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/) per un elenco completo di opzioni e funzionalità del servizio licenze.  

### <a name="application-discovery"></a>Individuazione delle applicazioni

Compilare un inventario di tutte le applicazioni nell'ambito che vengono pubblicate tramite Proxy di applicazione, raccogliendo le informazioni seguenti:

| Tipo di informazioni| Informazioni da raccogliere |
|---|---|
| Tipo di servizio| Ad esempio:  SharePoint, SAP, CRM, applicazioni Web personalizzato, API |
| Piattaforma applicativa | Ad esempio:  Windows IIS, Apache in Linux, Tomcat, NGINX |
| Appartenenza al dominio| Nome di dominio completo del server Web (FQDN) |
| Percorso dell'applicazione | Dove si trova il server web o la farm dell'infrastruttura |
| Accesso interno | L'URL esatto usato per accedere all'applicazione internamente. <br> Se una farm, il tipo di bilanciamento del carico è in uso? <br> Indica se l'applicazione crea contenuto da origini diverso da se stesso.<br> Determinare se l'applicazione viene eseguita tramite WebSockets. |
| Accesso esterno | La soluzione per fornitori che l'applicazione è già esposta esternamente a. <br> L'URL da usare per l'accesso esterno. Se SharePoint, verificare i mapping di accesso alternativo siano configurati per ogni [questo materiale sussidiario](https://docs.microsoft.com/SharePoint/administration/configure-alternate-access-mappings). In caso contrario, sarà necessario definire l'URL esterno. |
| Certificato pubblico | Se si usa un dominio personalizzato, ottenere un certificato con un nome soggetto corrispondente. Se è presente un certificato si noti il numero di serie e il percorso da dove può essere ottenuto. |
| Tipo di autenticazione| Il tipo di autenticazione supportato per il supporto dell'applicazione, ad esempio Basic, l'autenticazione integrata di Windows, basata su form, basato su intestazione e le attestazioni. <br>Se l'applicazione è configurata per l'esecuzione con un account di dominio specifico, si noti il nome di dominio completo (FQDN) dell'account del servizio.<br> Se basato su SAML, gli URL di risposta e identificatore. <br> Se basato su intestazione, la soluzione di fornitore e il requisito specifico per la gestione dell'autenticazione. |
| Nome del gruppo di connettore | Il nome logico per il gruppo di connettori che verrà contrassegnato per fornire il canale e da SSO a questa applicazione back-end. |
| Accesso di utenti o gruppi | Gli utenti o gruppi di utenti verranno concesso l'accesso esterno all'applicazione. |
| Requisiti aggiuntivi | Esaminare gli accesso remoto aggiuntivo o i requisiti di sicurezza devono essere sottoposte a factoring nelle pubblicazione dell'applicazione. |

È possibile scaricare questo [foglio di calcolo di inventario applicazione](https://aka.ms/appdiscovery) per creare un inventario delle app.

### <a name="define-organizational-requirements"></a>Definire i requisiti dell'organizzazione

Di seguito sono le aree per cui è necessario definire i requisiti aziendali dell'organizzazione. Ciascuna area contiene esempi di requisiti

 **Accedere**

* Gli utenti remoti con appartenenti a un dominio o gli utenti di dispositivi aggiunti ad Azure AD possono accedere alle applicazioni pubblicate in modo sicuro con seamless single sign-on (SSO).

* Gli utenti remoti con dispositivi personali approvati in modo sicuro possono accedere alle applicazioni pubblicate purché siano registrati in MFA e avere registrato l'app Microsoft Authenticator nel telefono cellulare come metodo di autenticazione.

**Governance** 

* Gli amministratori possono definire e monitorare il ciclo di vita delle assegnazioni utente alle applicazioni pubblicate tramite Proxy dell'applicazione.

**Sicurezza**

* Solo gli utenti assegnati ad applicazioni tramite l'appartenenza al gruppo o singolarmente possono accedere a tali applicazioni.

**Prestazioni**

* Non è disponibile alcuna riduzione delle prestazioni dell'applicazione rispetto all'accesso all'applicazione dalla rete interna.

**Esperienza utente**

* Gli utenti sono a conoscenza di come accedere alle applicazioni con URL familiare aziendali su qualsiasi piattaforma per dispositivi.

**Controllo**
* Gli amministratori sono in grado di controllare l'attività di accesso utente.


### <a name="best-practices-for-a-pilot"></a>Procedure consigliate per un progetto pilota

Determinare la quantità di tempo e impegno necessario per completamente commission una singola applicazione per l'accesso remoto con Single sign-on (SSO). Eseguire questa operazione tramite l'esecuzione di un programma pilota che prende in considerazione relativa individuazione iniziale, pubblicazione e il test generale. Usando un'applicazione web basata su IIS semplice che è già preconfigurata per l'autenticazione di Windows integrata (IWA) consente di stabilire una linea di base, come programma di installazione richiede uno sforzo minimo per SSO e accesso remoto è stata pilota.

I seguenti elementi di progettazione è necessario aumentare il successo del progetto pilota implementazione direttamente in un tenant di produzione.  

**Gestione connettori**:  

* I connettori svolgono un ruolo determinante nel fornire il canale locale alle applicazioni. Usando il **predefinito** gruppo di connettori è adeguata per pilota test iniziale delle applicazioni pubblicate prima della loro la messa in funzione nell'ambiente di produzione. È stato testato le applicazioni possono essere spostate quindi ai gruppi di connettori di produzione.

**Gestione delle applicazioni**:

* La propria forza lavoro è più facile ricordare che un URL esterno è familiari e più attinenti. Evitare la pubblicazione dell'applicazione utilizzando i suffissi msappproxy.net o onmicrosoft.com predefiniti. In alternativa, fornire un dominio verificato principale familiare, con preceduto con il nome host logici, ad esempio *intranet. < customers_domain >. com*.

* Limitare la visibilità dell'icona dell'applicazione pilota per un gruppo pilota, nascondendo sua forma di icona di avvio nel portale MyApps di Azure. Quando si è pronti per la produzione è possibile definire l'ambito di app per le rispettive destinatari, nello stesso tenant di pre-produzione, o anche pubblicando l'applicazione nel tenant di produzione.

**L'accesso Single sign-on di impostazioni**: Alcune impostazioni SSO presentano dipendenze specifiche che possono richiedere tempo per configurare, pertanto è consigliabile evitare ritardi verificando le dipendenze vengono indirizzati anticipatamente il controllo delle modifiche. Sono inclusi gli host del connettore per eseguire l'accesso SSO mediante la delega vincolata Kerberos (KCD) e avendo cura di altre attività necessarie lunghe procedure di aggiunta al dominio. Ad esempio, impostazione di un'istanza di Pingaccess, se è necessario l'accesso SSO basato su intestazione.

**Utilizzo di SSL tra Host del connettore e l'applicazione di destinazione**: La sicurezza è un requisito prioritario, in modo che TLS tra le applicazioni host e di destinazione di connector deve essere sempre utilizzata. In particolare se l'applicazione web è configurata per l'autenticazione basata su form (FBA), mentre le credenziali dell'utente vengono quindi trasmessi in modo efficace in testo non crittografato.

**Implementare in modo incrementale e testare ogni passaggio**. Eseguire il test funzionale base dopo la pubblicazione di un'applicazione per verificare che siano soddisfatti tutti i requisiti dell'utente finale e seguendo le istruzioni riportate di seguito:

1. Testare e convalidare l'accesso generale per l'applicazione web con la preautenticazione disabilitata.
2. Se ha esito positivo abilita la preautenticazione e assegnare utenti e gruppi. Testare e convalidare l'accesso.
3. Quindi aggiungere il metodo di accesso SSO per l'applicazione ed eseguire nuovamente il test per convalidare l'accesso.
4. Applicare l'accesso condizionale e i criteri di autenticazione a più fattori come richiesto. Testare e convalidare l'accesso.

**Risoluzione dei problemi degli strumenti**: Per risolvere il problema, sempre per iniziare, convalidare l'accesso all'applicazione pubblicata dal browser nell'host del connettore e verificare che l'applicazione funzioni come previsto. Quanto più semplice il programma di installazione, facile per determinare la causa radice, quindi, prendere in considerazione durante il tentativo di riprodurre i problemi con una configurazione minima, ad esempio usando solo un singolo connettore e SSO non è. In alcuni casi, può rivelarsi indispensabile risolvere i problemi di accesso o il contenuto in applicazioni a cui accede tramite un proxy web, ad esempio Fiddler di Telerik strumenti di debug. Fiddler può anche agire come proxy per consentire di traccia ed eseguire il debug del traffico per le piattaforme per dispositivi mobili come iOS e Android e praticamente qualsiasi cosa che può essere configurato per route tramite un proxy. Vedere le [Guida alla risoluzione dei](application-proxy-troubleshoot.md) per altre informazioni.

## <a name="implement-your-solution"></a>Implementare una soluzione

### <a name="deploy-application-proxy"></a>Distribuire il Proxy di applicazione

I passaggi per distribuire il Proxy di applicazione sono illustrati in questo [dell'esercitazione per l'aggiunta di un'applicazione in locale per l'accesso remoto](application-proxy-add-on-premises-application.md). Se l'installazione non è riuscito, selezionare **risolvere i problemi di Proxy di applicazione** nel portale o usare la Guida alla risoluzione dei problemi [i problemi di installazione del connettore dell'agente Proxy di applicazione](application-proxy-connector-installation-problem.md).

### <a name="publish-applications-via-application-proxy"></a>Pubblicare applicazioni tramite il Proxy di applicazione

Pubblicazione di applicazioni, si presuppone che si siano soddisfatti tutti i prerequisiti e di avere più connettori che mostra come registrato e attivo nella pagina del Proxy dell'applicazione.

È anche possibile pubblicare applicazioni mediante [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview).

Di seguito sono alcune procedure consigliate da seguire quando si pubblica un'applicazione:

* **Usare gruppi di connettori**: Assegnare un gruppo di connettori che è stato designato per la pubblicazione di ogni applicazione. È consigliabile che ogni gruppo di connettori abbia almeno due connettori per assicurare scalabilità e disponibilità elevata. Con tre connettori è ottimale nel caso in cui potrebbe essere necessario un computer in qualsiasi punto del servizio. Vedere inoltre [pubblicare applicazioni in reti e posizioni usando i gruppi di connettori separate](application-proxy-connector-groups.md) per vedere come è possibile usare i gruppi di connettori per i connettori dal percorso di rete o del segmento.

* **Impostare il Timeout applicazione back-end**: Questa impostazione è utile negli scenari in cui l'applicazione potrebbe richiedere più di 75 secondi per l'elaborazione di una transazione del client. Ad esempio quando un client invia una query a un'applicazione web che agisce come un front-end a un database. Il front-end invia la query al server di database back-end e attende una risposta, ma una volta che riceve una risposta, timeout lato client della conversazione. Impostare il timeout su tempo fornisce 180 secondi per le transazioni più tempo per il completamento.

* **Usare i tipi di Cookie appropriato**

   * **I Cookie HttpOnly**: Offre sicurezza aggiuntiva facendo in modo che il Proxy di applicazione di includere il flag HTTPOnly nelle intestazioni di risposta HTTP set-cookie. Questa impostazione consente di ridurre gli exploit, ad esempio il cross-site scripting (XSS). Lasciare questa opzione impostata su No per gli agenti client/utente che richiedono l'accesso per il cookie di sessione. Ad esempio client RDP/MTSC che si connette a un Gateway Desktop remoto pubblicata tramite il Proxy di App.

   * **Secure Cookie**: Quando un cookie è impostato con l'attributo Secure, l'agente utente (app sul lato Client) includerà solo i cookie nelle richieste HTTP se la richiesta viene trasmessa tramite un canale sicuro TLS. Ciò consente di ridurre il rischio di un cookie compromesso attraverso i canali di testo non crittografato, pertanto deve essere abilitata.

   * **Cookie persistente**: Consente il cookie di sessione del Proxy di applicazione rendere persistenti tra le chiusure di browser da rimangono validi fino a quando non scade o viene eliminato. Utilizzato per gli scenari in cui un'applicazione avanzata, ad esempio office accede a un documento all'interno di un'applicazione web pubblicata, senza che l'utente che venga richiesto nuovamente per l'autenticazione. Abilitare con cautela, tuttavia, come cookie permanenti può infine lasciare un servizio a rischio di accesso non autorizzato, se non utilizzato in combinazione con altri controlli di compensazione. Questa impostazione deve essere utilizzata solo per le applicazioni meno recenti che non possono condividere cookie tra processi. È preferibile aggiornare l'applicazione per gestire la condivisione dei cookie tra processi anziché usare questa impostazione.

* **Tradurre URL nelle intestazioni**: Abilitarlo per scenari in cui il DNS interno non può essere configurato in modo che corrispondano spazio dei nomi pubblico dell'organizzazione (dette anche Split DNS). A meno che l'applicazione richiede l'intestazione host originale nella richiesta del client, lasciare questo valore è impostato su Sì. L'alternativa consiste nell'avere il connettore Usa il nome di dominio completo nell'URL interno per il routing del traffico effettivo e il nome di dominio completo nell'URL esterno, come l'intestazione host. Nella maggior parte dei casi questa alternativa deve consentire il funzionamento dell'applicazione come di consueto, quando si accede in remoto, ma gli utenti perdono i vantaggi della presenza di un oggetto corrispondente all'interno e all'URL esterno.

* **Converti gli URL nel corpo dell’applicazione**: Attivare la conversione dei collegamenti del corpo dell'applicazione per un'app quando si desidera che i collegamenti dall'app da tradurre in risposte al client. Se abilitata, questa funzione fornisce un miglior tentativo possibile sforzo di conversione di tutti i collegamenti interni che consente di trovare il Proxy applicazione nelle risposte HTML e CSS restituite ai client. È utile quando la pubblicazione di App che contengono assoluto a livello di codice o collegamenti shortname NetBIOS nel contenuto o le app con il contenuto che include collegamenti ad altri applicazioni locali.

Per gli scenari in cui un'app pubblicata collegamenti ad altri le app pubblicate, abilitare la conversione dei collegamenti per ogni applicazione in modo da poter controllare l'esperienza dell'utente a livello di app.

Ad esempio, si supponga di avere tre applicazioni pubblicate mediante il proxy di applicazione e che tutte siano collegate tra loro: I vantaggi, le spese e viaggio, più una quarta app, commenti e suggerimenti, che non viene pubblicata tramite il Proxy di applicazione.

![Immagine 1](media/App-proxy-deployment-plan/link-translation.png)

Quando si abilita la conversione dei collegamenti per l'app Benefits, i collegamenti a Expenses e Travel sono reindirizzati agli URL esterni per tali App, in modo che gli utenti che accedono alle applicazioni di esterno alla rete aziendale possono accedervi. Collegamenti da Expenses e Travel a Benefits non funzionano perché conversione dei collegamenti non è ancora stata abilitata per queste due app. Il collegamento a Feedback non viene reindirizzato perché non esiste alcun URL esterno, in modo che gli utenti che usano l'app Benefits non sarà in grado di accedere all'app di commenti e suggerimenti dall'esterno della rete aziendale. Visualizzare informazioni dettagliate sul [collegare la traduzione e altre opzioni di reindirizzamento](application-proxy-configure-hard-coded-link-translation.md).

### <a name="access-your-application"></a>Accedere all'applicazione

Esistono diverse opzioni per la gestione dell'accesso al Proxy applicazione risorse pubblicate, quindi scegliere il più adatto alle proprie esigenze di scenario e la scalabilità specificate. Approcci comuni includono: Usa i gruppi locali siano sincronizzati con Azure AD Connect, Creazione gruppi dinamici in Azure AD basato sugli attributi dell'utente, uso dei gruppi self-servizi gestiti da un proprietario di risorse o una combinazione di tutti questi elementi. Vedere le risorse collegate per i vantaggi della ognuno.

Il modo più semplice assegnare agli utenti l'accesso a un'applicazione sta entrando nel **utenti e gruppi** opzioni nel riquadro a sinistra dell'applicazione pubblicata e assegnare direttamente singoli utenti o gruppi.

![Figura 24](media/App-proxy-deployment-plan/add-user.png)

Assegnazione di un gruppo che non sono attualmente un membro del e configurando le opzioni di Self-Service, è anche possibile consentire agli utenti di accesso self-service all'applicazione.

![Figura 25](media/App-proxy-deployment-plan/allow-access.png)

Se abilitata, gli utenti sarà in grado di accedere all'accesso di richiesta e portale App personali ed essere automaticamente approvata e aggiunto al gruppo self-service già consentita o richiedono l'approvazione dal responsabile approvazione designato.

Gli utenti guest possono rivelarsi [invitati ad accedere alle applicazioni interne pubblicate tramite Proxy di applicazione tramite Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/add-users-information-worker).

Per le applicazioni locali che sono in genere accessibili in modo anonimo, non richiedere alcuna autenticazione, potrebbe essere preferibile disattivare l'opzione nella casella dell'applicazione **proprietà**.

![Figura 26](media/App-proxy-deployment-plan/assignment-required.png)


Lasciando questa opzione è impostata su No, si consente agli utenti di accedere all'applicazione locale tramite Proxy App di Azure AD non dispongono di autorizzazioni, usarle con attenzione.

Una volta pubblicata l'applicazione, deve essere accessibile digitando l'URL esterno in un browser o dalla relativa icona nella [ https://myapps.microsoft.com ](https://myapps.microsoft.com/).

### <a name="enable-pre-authentication"></a>Abilita la preautenticazione

Verificare che l'applicazione è accessibile tramite il Proxy di applicazione l'accesso mediante l'URL esterno. 

1. Passare a **Azure Active Directory** > **Applicazioni aziendali** > **Tutte le applicazioni** e scegliere l'app da gestire.

2. Selezionare **Proxy dell'applicazione**.

3. Nel **preautenticazione** campo, utilizzare l'elenco a discesa per selezionare **Azure Active Directory**e selezionare **Salva**.

Con preautenticazione abilitata, Azure AD richiederà prima di tutto gli utenti per l'autenticazione e se l'accesso single sign-on è configued quindi l'applicazione back-end verrà inoltre verificato l'utente prima che venga concesso l'accesso all'applicazione. Modifica della modalità di pre-autenticazione da pass-through di Azure ad consente anche di configurare l'URL esterno con HTTPS, in modo che qualsiasi applicazione configurata inizialmente per il protocollo HTTP verrà ora protetti con HTTPS.

### <a name="enable-single-sign-on"></a>Abilitazione dell'accesso Single Sign-On

SSO fornisce la migliore esperienza utente possibile e la sicurezza perché gli utenti dovranno solo accedere una sola volta durante l'accesso AD Azure. Dopo che un utente ha già autenticato, l'accesso SSO viene eseguita mediante Application Proxy connector esegue l'autenticazione all'applicazione in locale, per conto dell'utente. L'applicazione back-end elabora l'account di accesso come se fosse l'utente se stessi. 

Scegliere il **pass-through** opzione consente agli utenti di accedere all'applicazione pubblicata senza mai dover autenticarsi con Azure AD.

Eseguire l'accesso SSO è possibile solo che se Azure AD può identificare l'utente che richiede l'accesso a una risorsa, in modo che l'applicazione deve essere configurato per pre-autenticare gli utenti con Azure AD durante l'accesso per l'accesso SSO a funzione, in caso contrario, le opzioni di SSO verranno disabilitate.

Lettura [Single sign-on alle applicazioni in Azure AD](what-is-single-sign-on.md) che consentono di scegliere il metodo SSO più appropriato durante la configurazione delle applicazioni.

###  <a name="working-with-other-types-of-applications"></a>Utilizzo di altri tipi di applicazioni

Proxy applicazione Azure AD può supportare anche applicazioni che sono state sviluppate per usare la libreria di autenticazione AD Azure ([ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)) o Microsoft Authentication Library ([MSAL](https://azure.microsoft.com/blog/start-writing-applications-today-with-the-new-microsoft-authentication-sdks/)). Supporta le app client native usando Azure AD ha ricevuti le informazioni di intestazione di richiesta del client di pre-autenticazione per conto di utenti di eseguire i token rilasciati.

Lettura [pubblicazione di App client native e per dispositivi mobili](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-native-client) e [applicazioni basate sulle attestazioni](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-claims-aware-apps) informazioni sulle configurazioni disponibili del Proxy dell'applicazione.

### <a name="use-conditional-access-to-strengthen-security"></a>Usare l'accesso condizionale per rafforzare la sicurezza

Sicurezza delle applicazioni necessita di un set avanzato di funzionalità di sicurezza che possono impedire e rispondere alle minacce complesse in locale e nel cloud. Gli utenti malintenzionati spesso accedono alla rete aziendale con "Weak", predefinito o le credenziali utente rubato.  Sicurezza basata sull'identità Microsoft riduce l'uso di furto delle credenziali tramite la gestione e la protezione delle identità con privilegi sia senza privilegi.

Le funzionalità seguenti possono essere utilizzate per supportare Proxy applicazione Azure AD:

* Utente e l'accesso condizionale basati sulla posizione: La protezione, limitando l'accesso utente basato su posizione geografica o un indirizzo IP con dati sensibili [criteri di accesso condizionale basati sulla posizione](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).

* Accesso condizionale basato su dispositivo: Assicurarsi che solo i dispositivi registrati, approvati e conformi possono accedere a dati aziendali con [accesso condizionale basato su dispositivo](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications).

* Accesso condizionale basato sull'applicazione: Lavoro non deve arrestare quando un utente non è nella rete aziendale. [Proteggere l'accesso alle App cloud e locali aziendali](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) e mantenere il controllo con l'accesso condizionale.

* Accesso condizionale basati sul rischio: Proteggere i dati dai pirati informatici malintenzionati con un [criteri di accesso condizionale basati sul rischio](https://www.microsoft.com/cloud-platform/conditional-access) che possono essere applicati a tutte le App e tutti gli utenti, se in locale o nel cloud.

* Pannello di accesso di Azure AD: Con il servizio Proxy di applicazione distribuita e le applicazioni pubblicate in modo sicuro, offrono agli utenti un semplice hub per individuare e accedere a tutte le applicazioni. Aumentare la produttività con funzionalità self-service, ad esempio la possibilità di richiedere l'accesso alle nuove app e i gruppi o gestire l'accesso a queste risorse per conto di altri, tramite il [Pannello di accesso](https://aka.ms/AccessPanelDPDownload).

## <a name="manage-your-implementation"></a>Gestire l'implementazione

### <a name="required-roles"></a>Ruoli necessari

Microsoft sostiene il principio di concedere il privilegio minimo per eseguire le attività necessarie con Azure AD. [Esaminare i diversi ruoli di Azure disponibili](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) e scegliere quello giusto per soddisfare le esigenze di ogni utente tipo. Alcuni ruoli debba essere applicata temporaneamente e rimossi dopo aver completato la distribuzione.

| Ruolo di Business| Attività di Business| Ruoli di Azure AD |
|---|---|---|
| Amministratore di help desk | In genere limitato alla qualificazione utente finale ha segnalato problemi ed effettuare le attività limitate, ad esempio la modifica delle password degli utenti, invalidando i token di aggiornamento e il monitoraggio dell'integrità del servizio. | Amministratore supporto tecnico |
| Amministratore di identità| Problemi relativi all'accesso AD Azure di lettura nei report e i log di controllo per eseguire il debug di App Proxy.| Ruolo con autorizzazioni di lettura per la sicurezza |
| Proprietario dell'applicazione| Creare e gestire tutti gli aspetti di applicazioni aziendali, registrazioni di applicazioni e impostazioni del proxy dell'applicazione.| Amministratore applicazione |
| Amministratore dell'infrastruttura | Proprietario del rollover dei certificati | Amministratore applicazione |

Riducendo al minimo il numero di persone che hanno accesso a informazioni o risorse protette consente di ridurre le probabilità di un attore malintenzionato ottenere accesso non autorizzato o un utente autorizzato abbia inavvertitamente effetto su una risorsa sensibile. 
 
Tuttavia, gli utenti devono comunque eseguire operazioni con privilegi di giorno in giorno, pertanto l'applicazione just-in-time (JIT) basato [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) i criteri per fornire su richiesta con privilegi di accesso alle risorse di Azure e Azure AD è la consiglia di approccio verso la gestione dell'accesso amministrativo e il controllo in modo efficace.

### <a name="reporting-and-monitoring"></a>Monitoraggio e creazione di report

Azure AD può fornire informazioni aggiuntive sull'utilizzo e integrità operativa mediante i log di controllo e report di provisioning dell'utente dell'organizzazione. 

#### <a name="application-audit-logs"></a>Log di controllo delle applicazioni

Questi log forniscono informazioni dettagliate sugli account di accesso alle applicazioni configurate con il Proxy di applicazione e il dispositivo e l'utente accede all'applicazione. I log di controllo si trovano nel portale di Azure e nell'API di controllo per l'esportazione.

#### <a name="windows-event-logs-and-performance-counters"></a>Contatori delle prestazioni e i registri eventi di Windows

Connettori hanno sia amministratore e sessione di log. I log di amministrazione includono gli eventi principali e i relativi errori. I log di sessione includono tutte le transazioni e i relativi dettagli di elaborazione. Registri contatori si trovano nei registri eventi di Windows e seguire questo [esercitazione per configurare le origini dati del registro eventi in Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events).

### <a name="troubleshooting-guide-and-steps"></a>Guida alla risoluzione dei problemi e passaggi

Altre informazioni sui problemi comuni e come risolverli con la Guida alla [risoluzione dei problemi](application-proxy-troubleshoot.md) i messaggi di errore. 

Gli articoli seguenti trattano scenari comuni che possono anche essere utilizzati per creare le guide alla risoluzione dei problemi per l'organizzazione di supporto. 

* [Problema durante la visualizzazione di una pagina dell'app](application-proxy-page-appearance-broken-problem.md)
* [Il caricamento dell'applicazione richiede troppo tempo](application-proxy-page-load-speed-problem.md)
* [I collegamenti nella pagina dell'applicazione non funzionano](application-proxy-page-links-broken-problem.md)
* [Porte da aprire per l'app](application-proxy-connectivity-ports-how-to.md)
* [Nessun connettore funzionante in un gruppo di connettori per le app](application-proxy-connectivity-no-working-connector.md)
* [Configurare nel portale di amministrazione](application-proxy-config-how-to.md)
* [Configurare l'accesso Single Sign-On all'app](application-proxy-config-sso-how-to.md)
* [Problema durante la creazione di un'app nel portale di amministrazione](application-proxy-config-problem.md)
* [Configurare la delega vincolata di Kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Configurare con PingAccess](application-proxy-back-end-ping-access-how-to.md)
* [Non è possibile accedere a questo errore all'applicazione aziendale](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [Problemi di installazione del connettore dell'agente proxy dell'applicazione](application-proxy-connector-installation-problem.md)
* [Problema di accesso](application-sign-in-problem-on-premises-application-proxy.md)
