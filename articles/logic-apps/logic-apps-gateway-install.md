---
title: Installare il gateway dati locale-app per la logica di Azure
description: Prima di poter accedere ai dati in locale delle App per la logica di Azure, è necessario scaricare e installare il gateway dati locale
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 07/01/2019
ms.openlocfilehash: 657bc704e33e89b1646dffa6123a27169e6c317a
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860668"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Installare il gateway dati locale per App per la logica di Azure

Prima di poter connettere le origini dati locali dalle App per la logica di Azure, è necessario scaricare e installare il gateway dati locale in un computer locale. Il gateway opera come un ponte che fornisce il trasferimento rapido dei dati e la crittografia tra le origini di dati locali (non nel cloud) e le app per la logica. È possibile usare la stessa installazione del gateway con altri servizi cloud, ad esempio Power BI, Microsoft Flow, PowerApps e Azure Analysis Services. Per informazioni su come usare il gateway con questi servizi, vedere i seguenti articoli:

* [Gateway dati locale di Microsoft Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Gateway dati locale di Microsoft PowerApps](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Gateway dati locale di Microsoft Flow](https://flow.microsoft.com/documentation/gateway-manage/)
* [Gateway dati locale di Azure Analysis Services](../analysis-services/analysis-services-gateway.md)

Questo articolo illustra come scaricare, installare e configurare il gateway dati locale in modo che sia possibile accedere alle origini dati locali da app per la logica di Azure. È anche possibile ottenere altre informazioni sul [funzionamento del gateway dati](#gateway-cloud-service) più avanti in questo argomento.

<a name="supported-connections"></a>

Il gateway supporta [connettori locali](../connectors/apis-list.md#on-premises-connectors) nelle app per la logica di Azure per queste origini dati:

* BizTalk Server 2016
* File system
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Oracle Database
* PostgreSQL
* SAP
* Server SharePoint
* SQL Server
* Teradata

Sebbene il gateway da solo non comporti costi aggiuntivi, il [modello di prezzi di app](../logic-apps/logic-apps-pricing.md) per la logica si applica a questi connettori e ad altre operazioni in app per la logica di Azure

<a name="requirements"></a>

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

  * È necessario usare lo stesso account Azure per installare e per amministrare il gateway. Durante l'installazione, usare questo account Azure per associare il gateway nel computer a una sottoscrizione di Azure. In seguito, si userà lo stesso account di Azure quando si crea una risorsa di Azure nel portale di Azure per l'installazione del gateway. 

  * È necessario accedere con un account aziendale o dell'Istituto di istruzione, noto anche come account *aziendale* , simile `username@contoso.com`a. Non è possibile usare account di @hotmail.com Azure B2B (Guest) o account Microsoft personali, ad esempio o. @outlook.com

    > [!TIP]
    > Se è stata effettuata l'iscrizione per un'offerta di Office 365 e non è stato fornito l'indirizzo di posta elettronica `username@domain.onmicrosoft.com`aziendale, l'indirizzo potrebbe essere simile a. L'account viene archiviato all'interno di un tenant in un Azure Active Directory (Azure AD). Nella maggior parte dei casi, il nome dell'entità utente (UPN) per l'account Azure AD è uguale a quello dell'indirizzo di posta elettronica.
    >
    > Per usare una [sottoscrizione standard di Visual Studio](https://visualstudio.microsoft.com/vs/pricing/) associata a un account Microsoft, creare prima [un tenant in Azure ad](../active-directory/develop/quickstart-create-new-tenant.md)oppure usare la directory predefinita. Aggiungere un utente con una password per la directory e quindi concedere all'utente l'accesso alla sottoscrizione. 
    > Sarà quindi possibile accedere durante l'installazione del gateway con questo nome utente e questa password.

* Di seguito sono riportati i requisiti per il computer locale:

  **Requisiti minimi**

  * .NET Framework 4.6
  * Windows 7 versione a 64 bit o Windows Server 2008 R2 (o versione successiva)

  **Requisiti consigliati**

  * CPU 8 core
  * 8 GB di memoria
  * versione a 64 bit di Windows Server 2012 R2 o versione successiva
  * Archiviazione SSD (Solid-State Drive) per lo spooling

  > [!NOTE]
  > Il gateway non supporta Windows Server 2016 core.

* **Considerazioni correlate**

  * È possibile installare il gateway dati locale solo in un computer locale, non in un controller di dominio. Non è tuttavia necessario installare il gateway nello stesso computer dell'origine dati. È necessario un solo gateway per tutte le origini dati, pertanto non è necessario installare il gateway per ogni origine dati.

    > [!TIP]
    > Per ridurre al minimo la latenza, è possibile installare il gateway il più vicino possibile all'origine dati o nello stesso computer, presupponendo che si disponga delle autorizzazioni.

  * Installare il gateway in un computer che si trova su una rete cablata, connessa a Internet, sempre acceso e non passa alla modalità sospensione. In caso contrario, il gateway non può essere eseguito e le prestazioni potrebbero subire su una rete wireless.

  * Se si prevede di usare l'autenticazione di Windows, assicurarsi di installare il gateway in un computer membro dello stesso ambiente di Active Directory delle origini dati.

  * L'area selezionata per l'installazione del gateway è la stessa che è necessario selezionare quando in seguito si crea la risorsa del gateway di Azure per l'app per la logica. Per impostazione predefinita, questa area è la stessa in cui si trova il tenant di Azure AD che gestisce l'account Azure. Tuttavia, è possibile modificare il percorso durante l'installazione del gateway.

  * Il gateway dispone di due modalità: la modalità standard e la modalità personale, che si applicano solo ai Power BI. Non è possibile avere più di un gateway in esecuzione nella stessa modalità nello stesso computer.

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Installare il gateway dati

1. [Scaricare ed eseguire il programma di installazione di gateway in un computer locale](https://aka.ms/on-premises-data-gateway-installer).

1. Dopo l'apertura del programma di installazione, fare clic su **Avanti**.

   ![Introduzione del programma di installazione](./media/logic-apps-gateway-install/gateway-intro-screen.png)

1. Selezionare **gateway dati locale (scelta consigliata)** , che è la modalità standard, quindi selezionare **Avanti**.

   ![Selezionare la modalità gateway](./media/logic-apps-gateway-install/select-gateway-mode.png)

1. Verificare i requisiti minimi, tenere il percorso di installazione predefinito, accettare le condizioni per l'utilizzo e quindi selezionare **Installa**.

   ![Esaminare i requisiti e accettare le condizioni per l'utilizzo](./media/logic-apps-gateway-install/accept-terms.png)

1. Al termine dell'installazione del gateway, fornire l'indirizzo di posta elettronica per l'account dell'organizzazione e quindi selezionare **Accedi**, ad esempio:

   ![Accedere con l'account aziendale o dell'istituto di istruzione](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   A questo punto è stato effettuato l'accesso al proprio account.

1. Selezionare **registra un nuovo gateway in questo computer** > **Avanti**. Questo passaggio registra l'installazione del gateway con il [servizio cloud gateway](#gateway-cloud-service).

   ![Registrare il gateway](./media/logic-apps-gateway-install/register-gateway.png)

1. Specificare le informazioni seguenti per l'installazione del gateway:

   * Nome del gateway univoco nel tenant di Azure AD
   * Chiave di ripristino, che deve contenere almeno otto caratteri che si desidera utilizzare
   * Conferma per la chiave di ripristino

   ![Configurare il gateway](./media/logic-apps-gateway-install/set-up-gateway.png)

   > [!IMPORTANT]
   > Salvare e conservare la chiave di ripristino in un luogo sicuro. Questa chiave è necessaria se si vuole modificare il percorso, spostare, ripristinare o sostituire un'installazione del gateway.

   Si noti l'opzione da **aggiungere a un cluster di gateway esistente**, che è possibile selezionare quando si installano gateway aggiuntivi per gli [scenari a disponibilità elevata](#high-availability).

1. Controllare l'area per il servizio cloud gateway e il [bus di servizio di Azure](https://azure.microsoft.com/services/service-bus/) usato dall'installazione del gateway. Per impostazione predefinita, quest'area è la stessa località del tenant di Azure AD per l'account Azure.

   ![Controllare l'area](./media/logic-apps-gateway-install/check-region.png)

1. Per accettare l'area predefinita, selezionare **Configura**. Tuttavia, se l'area predefinita non è quella più vicina, è possibile modificare l'area.

   *Perché modificare l'area per l'installazione del gateway?*

   Ad esempio, per ridurre la latenza, è possibile modificare l'area del gateway nella stessa area dell'app per la logica. In alternativa, è possibile selezionare l'area più vicina all'origine dati locale. La *risorsa per il gateway in Azure* e l'app per la logica possono avere posizioni diverse.

   1. Accanto all'area corrente, selezionare **Cambia area**.

      ![Cambiare area](./media/logic-apps-gateway-install/change-region.png)

   1. Nella pagina successiva aprire l'elenco **Seleziona area** , selezionare l'area desiderata, quindi fare clic su **fine**.

      ![Selezionare un'altra area](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. Esaminare le informazioni nella finestra di conferma finale. Questo esempio usa lo stesso account per app per la logica, Power BI, PowerApps e Microsoft Flow, in modo che il gateway sia disponibile per tutti questi servizi. Quando si è pronti, selezionare **Chiudi**.

   ![Gateway completato](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. [Creare ora la risorsa di Azure per l'installazione del gateway](../logic-apps/logic-apps-gateway-connection.md).

<a name="high-availability"></a>

## <a name="high-availability-support"></a>Supporto per la disponibilità elevata

Per evitare singoli punti di errore per l'accesso ai dati locali, è possibile avere più installazioni del gateway (solo modalità standard) con ognuna in un computer diverso e impostarle come cluster o gruppo. In questo modo, se il gateway primario non è disponibile, le richieste di dati vengono instradate al secondo gateway e così via. Poiché è possibile installare un solo gateway standard in un computer, è necessario installare ogni gateway aggiuntivo presente nel cluster in un computer diverso. Tutti i connettori che funzionano con il gateway dati locale supportano la disponibilità elevata. 

* È necessario disporre già di almeno un'installazione del gateway nella stessa sottoscrizione di Azure del gateway primario e della chiave di ripristino per tale installazione.

* Il gateway primario deve eseguire l'aggiornamento del gateway di novembre 2017 o versione successiva.

Dopo aver configurato il gateway principale, quando si installa un altro gateway, selezionare **Aggiungi a un cluster di gateway esistente**, selezionare il gateway principale, ovvero il primo gateway installato e fornire la chiave di ripristino per il gateway. Per altre informazioni, vedere [Cluster a disponibilità elevata per il gateway dati locale](https://docs.microsoft.com/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster).

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Modificare la posizione, eseguire la migrazione, ripristinare o sostituire un gateway esistente

Per modificare la posizione del gateway, spostare l'installazione del gateway in un nuovo computer, ripristinare un gateway danneggiato o assumere la proprietà di un gateway esistente, è necessaria la chiave di ripristino che è stata specificata durante l'installazione del gateway.

1. Eseguire il programma di installazione del gateway nel computer che dispone del gateway esistente. Se non si dispone del programma di installazione del gateway più recente, [scaricare la versione più recente del gateway](https://aka.ms/on-premises-data-gateway-installer).

   > [!NOTE]
   > Prima di ripristinare il gateway nel computer in cui è presente l'installazione del gateway originale, è necessario prima disinstallare il gateway nel computer. Questa azione disconnette il gateway originale.
   > Se si rimuove o si elimina un cluster di gateway per qualsiasi servizio cloud, non è possibile ripristinare tale cluster.

1. Dopo l'apertura del programma di installazione, accedere con lo stesso account Azure usato per installare il gateway.

1. Selezionare **Esegui migrazione, ripristino o acquisizione di un gateway** > esistente**Avanti**, ad esempio:

   ![Selezionare "Eseguire la migrazione, ripristinare o acquisire la proprietà di un gateway esistente"](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. Selezionare tra i cluster e i gateway disponibili e immettere la chiave di ripristino per il gateway selezionato, ad esempio:

   ![Selezionare il gateway](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. Per modificare l'area, selezionare **Cambia area**e selezionare la nuova area.

1. Quando si è pronti, selezionare **Configura** per poter completare l'attività.

## <a name="configure-proxy-or-firewall"></a>Configurare il proxy o il firewall

Se l'ambiente di lavoro richiede che il traffico attraversi un proxy per accedere a Internet, questa restrizione potrebbe impedire al gateway dati locale di connettersi al servizio cloud gateway e al [bus di servizio di Azure](../service-bus-messaging/service-bus-messaging-overview.md). Per altre informazioni, vedere [configurare le impostazioni proxy per il gateway dati locale](https://docs.microsoft.com/power-bi/service-gateway-proxy).

Per verificare se il proxy o il firewall potrebbe bloccare le connessioni, verificare che il computer sia in grado di connettersi a Internet e al bus di servizio di Azure. Da un prompt di PowerShell eseguire questo comando:

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

> [!NOTE]
> Questo comando verifica solo la connettività di rete e la connettività per il bus di servizio di Azure. Il comando non ha nulla a che fare con il gateway o con il servizio cloud del gateway che crittografa e archivia le credenziali e i dettagli del gateway. 
>
> Questo comando è disponibile solo in Windows Server 2012 R2 o in una versione successiva e in Windows 8.1 o in una versione successiva. Nelle versioni precedenti del sistema operativo, è possibile usare Telnet per testare la connettività. Altre informazioni su [bus di servizio di Azure e soluzioni ibride](../service-bus-messaging/service-bus-messaging-overview.md).

I risultati dovrebbero essere simili all'esempio seguente, con **TcpTestSucceeded** impostato su **True**:

```text
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

Se **TcpTestSucceeded** non è impostato su **True**, il gateway potrebbe essere bloccato da un firewall. Per la massima completezza, sostituire i valori di **ComputerName** e **Port** con quelli elencati nella sezione [Configurare le porte](#configure-ports) in questo articolo.

Il firewall può anche bloccare le connessioni tra il bus di servizio e i data center di Azure. Se si verifica questo scenario, approvare (sbloccare) tutti gli indirizzi IP per i data center nell'area geografica. Per gli indirizzi IP, [ottenere l'elenco di indirizzi IP Azure qui](https://www.microsoft.com/download/details.aspx?id=41653).

<a name="configure-ports"></a>

## <a name="configure-ports"></a>Configurare le porte

Il gateway crea una connessione in uscita al bus di servizio di Azure e comunica sulle porte in uscita: TCP 443 (predefinita), 5671, 5672 e dalla 9350 alla 9354. Non sono richieste porte in ingresso. Altre informazioni su [bus di servizio di Azure e soluzioni ibride](../service-bus-messaging/service-bus-messaging-overview.md).

Il gateway usa i nomi di dominio completi seguenti:

| Nomi di dominio | Porte in uscita | Descrizione |
| ------------ | -------------- | ----------- |
| *. analysis.windows.net | 443 | HTTPS |
| *.core.windows.net | 443 | HTTPS |
| *.frontend.clouddatahub.net | 443 | HTTPS |
| *.login.windows.net | 443 | HTTPS |
| *.microsoftonline-p.com | 443 | Usato per l'autenticazione, a seconda della configurazione. |
| *.msftncsi.com | 443 | Usato per testare la connettività a Internet se il gateway non è raggiungibile dal servizio Power BI. |
| *.servicebus.windows.net | 443, 9350-9354 | Listener in Inoltro del Bus di servizio su TCP (richiede 443 per l'acquisizione del token di Controllo di accesso) |
| *.servicebus.windows.net | 5671-5672 | Advanced Message Queuing Protocol (AMQP) |
| login.microsoftonline.com | 443 | HTTPS |
||||

In alcuni casi, le connessioni al Bus di servizio di Azure verranno stabilite con gli indirizzi IP anziché con i nomi di dominio completi. Quindi, potrebbe essere necessario sbloccare gli indirizzi IP per l'area dati nel firewall. Per consentire l'accesso per gli indirizzi IP anziché per i domini, è possibile scaricare e usare l' [elenco di intervalli IP del data center Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Gli indirizzi IP in questo elenco sono nella notazione [CIDR (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

### <a name="force-https-communication-with-azure-service-bus"></a>Forzare la comunicazione HTTPS con il bus di servizio di Azure

Alcuni proxy consentono il passaggio di traffico solo alle porte 80 e 443. Per impostazione predefinita, la comunicazione con il bus di servizio di Azure avviene su porte diverse dalla 443. È possibile forzare il gateway a comunicare con il bus di servizio di Azure usando HTTPS anziché TCP diretto. Questa operazione, tuttavia, può ridurre le prestazioni in misura significativa. Per altre informazioni, vedere [forzare la comunicazione HTTPS con il bus di servizio di Azure](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication#force-https-communication-with-azure-service-bus).

<a name="windows-service-account"></a>

## <a name="windows-service-account"></a>Account del servizio Windows

Per impostazione predefinita, l'installazione del gateway nel computer locale viene eseguita come account di servizio di Windows denominato "servizio gateway dati locale". Tuttavia, l'installazione del gateway usa `NT SERVICE\PBIEgwService` il nome per le credenziali dell'account "Accedi come" e ha le autorizzazioni di accesso come servizio.

> [!NOTE]
> L'account del servizio Windows è diverso dall'account usato per la connessione alle origini dati locali e dall'account Azure usato quando si accede ai servizi cloud.

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Riavviare il gateway

Il gateway dati viene eseguito come servizio Windows e, come qualsiasi altro servizio di questo tipo, può essere avviato e arrestato in vari modi. Per altre informazioni, vedere [riavviare un gateway dati locale](https://docs.microsoft.com/data-integration/gateway/service-gateway-restart).

## <a name="tenant-level-administration"></a>Amministrazione a livello di tenant

Per ottenere la visibilità di tutti i gateway dati locali in un tenant di Azure AD, gli amministratori globali in tale tenant possono accedere all'interfaccia di [amministrazione di Power Platform](https://powerplatform.microsoft.com) come amministratore tenant e selezionare l'opzione **gateway dati** . Per altre informazioni, vedere [amministrazione a livello di tenant per il gateway dati locale](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin).

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>Funzionamento del gateway

Il gateway dati facilita la comunicazione rapida e sicura tra l'app per la logica, il servizio cloud del gateway e l'origine dati locale. Il servizio cloud gateway crittografa e archivia le credenziali dell'origine dati e i dettagli del gateway. Il servizio instrada anche le query e i relativi risultati tra l'app per la logica, il gateway dati locale e l'origine dati in locale.

Il gateway funziona con i firewall e usa solo connessioni in uscita. Tutto il traffico ha origine come traffico sicuro in uscita dall'agente di gateway. Il gateway inoltra i dati dalle origini locali nei canali crittografati tramite il bus di servizio di Azure. Il bus di servizio crea un canale tra il gateway e il servizio chiamante, ma non archivia alcun dato. Tutti i dati che attraversano il gateway sono crittografati.

![Architettura per il gateway dati locale](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

I passaggi seguenti descrivono quello che accade quando un utente nel cloud interagisce con un elemento connesso a un'origine dati in locale:

1. Il servizio cloud del gateway crea una query, insieme alle credenziali crittografate per l'origine dati, e invia la query alla coda affinché venga elaborata dal gateway.

1. Il servizio cloud del gateway analizza la query e inserisce la richiesta nel bus di servizio di Azure.

1. Il gateway dati locale esegue il polling del bus di servizio per le richieste in sospeso.

1. Il gateway riceve la query, decrittografa le credenziali e le usa per la connessione alle origini dati.

1. Invia quindi la query all'origine dati per l'esecuzione.

1. I risultati vengono quindi inviati dall'origine dati al gateway e quindi al servizio cloud del gateway. Infine, il servizio cloud del gateway usa i risultati.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="general"></a>Generale

**D**: È necessario un gateway per le origini dati nel cloud, ad esempio il database SQL di Azure? <br/>
**R**: No, il gateway si connette solo alle origini dati locali.

**D**: Il gateway deve essere installato nello stesso computer dell'origine dati? <br/>
**R**: No, il gateway si connette all'origine dati tramite le informazioni di connessione fornite. In questo senso il gateway può essere paragonato a un'applicazione client. Il gateway deve solo potersi connettere al nome del server specificato.

<a name="why-azure-work-school-account"></a>

**D**: Perché è necessario usare un account aziendale o dell'istituto di istruzione per accedere? <br/>
**R**: Quando si installa il gateway dati locale, è possibile usare solo un account aziendale o dell'istituto di istruzione. L'account di accesso viene archiviato in un tenant gestito da Azure Active Directory (Azure AD). Il nome dell'entità utente (UPN) dell'account di Azure AD in genere corrisponde all'indirizzo di posta elettronica.

**D**: Dove sono archiviate le credenziali? <br/>
**R**: Le credenziali immesse per un'origine dati vengono crittografate e archiviate nel servizio cloud gateway. Le credenziali vengono quindi decrittografate nel gateway dati locale.

**D**: Sono previsti requisiti per la larghezza di banda della rete? <br/>
**R**: Controllare che la connessione di rete abbia una buona velocità effettiva. Ogni ambiente è diverso e sui risultati può influire anche la quantità di dati inviati. Per garantire un livello di velocità effettiva tra l'origine dati in locale e il data center di Azure, provare [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/). Per misurare la velocità effettiva, provare uno strumento esterno come Azure Speed Test.

**D**: Qual è la latenza per l'esecuzione di query a un'origine dati dal gateway? Qual è l'architettura ottimale? <br/>
**R**: Per ridurre la latenza di rete, installare il gateway il più vicino possibile all'origine dati. Installando il gateway nell'origine dati effettiva, la latenza introdotta risulterà ridotta al minimo grazie a questa prossimità. Prendere inoltre in considerazione la vicinanza ai data center di Azure. Se, ad esempio, il servizio usa il data center degli Stati Uniti occidentali e SQL Server è ospitato in una macchina virtuale di Azure, può essere preferibile posizionare anche la macchina virtuale di Azure negli Stati Uniti occidentali. Grazie a questa prossimità la latenza è ridotta al minimo e si evitano addebiti relativi ai dati in uscita sulla macchina virtuale di Azure.

**D**: In che modo i risultati vengono inviati al cloud? <br/>
**R**: I risultati vengono inviati tramite il bus di servizio di Azure.

**D**: Esistono connessioni in ingresso al gateway dal cloud? <br/>
**R**: No, il gateway usa le connessioni in uscita al bus di servizio di Azure.

**D**: Cosa accade se si bloccano le connessioni in uscita? Cosa fare per sbloccarle? <br/>
**R**: Vedere le porte e gli host usati dal gateway.

**D**: Come viene chiamato il servizio Windows effettivo? <br/>
**R**: Nella scheda Servizi in Gestione attività, il nome del servizio è "PBIEgwService" o servizio Power BI Gateway Enterprise. Nella console servizi, il nome del servizio è "On-premises data gateway service" (Servizio gateway dati locale). Il servizio di Windows usa "NT SERVICE\PBIEgwService" come il SID del servizio (SSID).

**D**: Il servizio gateway di Windows può essere eseguito con un account Azure Active Directory? <br/>
**R**: No, il servizio di Windows deve avere un account Windows valido.

### <a name="disaster-recovery"></a>Ripristino di emergenza

**D**: Quali opzioni sono disponibili per il ripristino di emergenza? <br/>
**R**: È possibile usare la chiave di ripristino per ripristinare o spostare un gateway. La chiave di ripristino viene specificata al momento dell'installazione del gateway.

**D**: Quale vantaggio offre la chiave di ripristino? <br/>
**R**: La chiave di ripristino consente di eseguire la migrazione o di ripristinare le impostazioni del gateway in caso di emergenza.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Questa sezione affronta alcuni dei problemi comuni che possono verificarsi durante la configurazione e l'uso del gateway dati locale.

**D**: Perché l'installazione del gateway non è riuscita? <br/>
**R**: Questo problema può verificarsi se il software antivirus nel computer di destinazione non è aggiornato. È possibile aggiornare il software antivirus oppure disabilitarlo temporaneamente durante l'installazione del gateway e quindi abilitarlo nuovamente.

**D**: Perché non viene visualizzata l'installazione del gateway durante la creazione della risorsa gateway in Azure? <br/>
**R**: Questo problema può verificarsi per i motivi seguenti:

* L'installazione del gateway è già registrata e richiesta da un'altra risorsa del gateway in Azure. Le installazioni del gateway non vengono visualizzate nell'elenco delle istanze dopo che le risorse del gateway vengono create per loro. Per controllare le registrazioni del gateway nel portale di Azure, esaminare tutte le risorse di Azure di tipo **Gateway dati locali** per *tutte* le sottoscrizioni di Azure.

* L'identità di Azure AD per la persona che ha installato il gateway è diversa da quella della persona che ha effettuato l'accesso al portale di Azure. Verificare di aver eseguito l'accesso con la stessa identità usata per l'installazione del gateway.

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

**D**: Dove si trovano i log del gateway? <br/>
**R**: Vedere la [sezione **Log**](#logs) più avanti in questo articolo.

**D**: Come è possibile visualizzare le query inviate all'origine dati locale? <br/>
**R**: È possibile abilitare la funzione di tracciamento delle query, che include le query inviate. Dopo aver risolto il problema, ripristinare il valore originale per il tracciamento delle query. Se il tracciamento delle query non viene disabilitato, si creeranno dei log più grandi.

È anche possibile usare gli strumenti per il tracciamento delle query di cui è dotata l'origine dati. Ad esempio, è possibile usare Eventi estesi o SQL Profiler per SQL Server e Analysis Services.

### <a name="outdated-gateway-version"></a>Versione del gateway non aggiornata

Quando la versione del gateway non è aggiornata, possono emergere numerosi problemi. Come procedura consigliata, verificare di disporre della versione più recente. Se il gateway non è stato aggiornato per un mese o più è consigliabile installarne la versione più recente e verificare se è possibile riprodurre il problema.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Errore: Impossibile aggiungere l'utente al gruppo. (Utenti log delle prestazioni -2147463168 PBIEgwService)

Questo errore viene visualizzato se si sta tentando di installare il gateway in un controller di dominio, un'operazione non consentita. Assicurarsi di distribuire il gateway in un computer che non sia un controller di dominio.

<a name="logs"></a>

### <a name="logs"></a>Log

Per la risoluzione dei problemi, iniziare sempre raccogliendo ed esaminando i log del gateway. Esistono diversi modi per raccogliere i log, ma l'opzione più semplice, dopo l'installazione del gateway, è tramite l'interfaccia utente del programma di installazione del gateway.

1. Nel computer, aprire il programma di installazione del gateway dati locale.

1. Nel menu a sinistra selezionare **Diagnostica**.

1. In **Log del gateway** selezionare **Esporta log**.

   ![Esportare i log dal programma di installazione del gateway](./media/logic-apps-gateway-install/export-logs.png)

Di seguito sono riportate altre posizioni in cui è possibile trovare vari log:

| Tipo di log | Location |
|----------|----------|
| **Log del programma di installazione** | %localappdata%\Temp\On-premises_data_gateway_<*aaaammgg*>.<*numero*>.log |
| **Log di configurazione** | C:\Users\<*nomeutente*>\AppData\Local\Microsoft\On-premises data gateway\GatewayConfigurator<*aaaammgg*>.<*numero*>.log |
| **Log del servizio gateway Enterprise** | C:\Users\PBIEgwService\AppData\Local\Microsoft\On-premises data gateway\Gateway<*aaaammgg*>.<*numero*>.log |
|||

**Log eventi**

Per trovare i log eventi per il gateway, seguire questa procedura:

1. Nel computer con l'installazione del gateway aprire il **Visualizzatore eventi**.

1. Espandere **Visualizzatore eventi (locale)**  > **Registri applicazioni e servizi**.

1. Selezionare **On-premises data gateway service** (Servizio gateway dati locale).

   ![Visualizzare i log eventi per il gateway](./media/logic-apps-gateway-install/event-viewer.png)

### <a name="review-slow-query-performance"></a>Esaminare le prestazioni delle query lente

Se le query vengono eseguite lentamente tramite il gateway, è possibile attivare una registrazione aggiuntiva che restituisce le query e la relativa durata. Questi log potrebbero essere utili per individuare le query lente o a esecuzione prolungata. Per ottimizzare le prestazioni delle query, potrebbe essere necessario modificare l'origine dati, ad esempio regolando gli indici per le query di SQL Server.

Per determinare la durata di una query, seguire questa procedura:

1. Passare allo stesso percorso del client del gateway, che in genere è: ```C:\Program Files\On-premises data gateway```

   In alternativa, per trovare il percorso del client, aprire la console dei servizi nello stesso computer, eseguire una ricerca di **On-premises data gateway service** (Servizio gateway dati locale) e visualizzare la proprietà **Path to executable** (Percorso del file eseguibile).

1. Aprire e modificare questi file di configurazione come descritto:

   * **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

     In questo file, modificare il valore di **EmitQueryTraces** da **false** a **true** in modo che il gateway possa registrare le query inviate dal gateway a un'origine dati:

     ```html
     <setting name="EmitQueryTraces" serializeAs="String">
        <value>true</value>
     </setting>
     ```

     > [!IMPORTANT]
     > L'attivazione dell'impostazione EmitQueryTraces potrebbe aumentare notevolmente le dimensioni del log in base all'uso del gateway. Dopo aver esaminato i log, assicurarsi di impostare nuovamente EmitQueryTraces su **false**, invece di lasciare attivata questa impostazione a lungo termine.

   * **Microsoft.PowerBI.DataMovement.Pipeline.Diagnostics.dll.config**

     Per consentire al gateway di registrare voci dettagliate, incluse le voci che indicano la durata, modificare il valore **TracingVerbosity** dal **4** a **5** mediante l'esecuzione di uno dei seguenti passaggi:

     * In questo file di configurazione, modificare il valore **TracingVerbosity** da **4** a **5**

       ```html
       <setting name="TracingVerbosity" serializeAs="String">
          <value>5</value>
       </setting>
       ```

     * Aprire il programma di installazione del gateway, selezionare **Diagnostica**, attivare **Registrazione aggiuntiva** e quindi scegliere **Applica**:

       ![Attivare la registrazione aggiuntiva](./media/logic-apps-gateway-install/turn-on-additional-logging.png)

     > [!IMPORTANT]
     > L'attivazione dell'impostazione TracingVerbosity potrebbe aumentare notevolmente le dimensioni del log in base all'uso del gateway. Dopo aver esaminato i log, assicurarsi di disattivare **Registrazione aggiuntiva** nel programma di installazione del gateway o di impostare nuovamente TracingVerbosity su **4** nel file di configurazione, invece di lasciare attivata questa impostazione a lungo termine.

1. Per individuare la durata di una query, seguire questa procedura:

   1. [Esportare](#logs) e aprire il log del gateway.

   1. Per trovare una query, cercare un tipo di attività, ad esempio:

      | Tipo di attività | Descrizione |
      |---------------|-------------|
      | MGEQ | Query eseguite su ADO.NET |
      | MGEO | Query eseguite su OLEDB |
      | MGEM | Query eseguite dal motore mashup |
      |||

   1. Si noti il secondo GUID, ovvero l'ID richiesta.

   1. Continuare la ricerca del tipo di attività finché non si trova una voce denominata "FireActivityCompletedSuccessfullyEvent", che ha una durata in millisecondi. Verificare che la voce abbia lo stesso ID richiesta, ad esempio:

      ```text
      DM.EnterpriseGateway Verbose: 0 : 2016-09-26T23:08:56.7940067Z DM.EnterpriseGateway    baf40f21-2eb4-4af1-9c59-0950ef11ec4a    5f99f566-106d-c8ac-c864-c0808c41a606    MGEQ    21f96cc4-7496-bfdd-748c-b4915cb4b70c    B8DFCF12 [DM.Pipeline.Common.TracingTelemetryService] Event: FireActivityCompletedSuccessfullyEvent (duration=5004)
      ```

      > [!NOTE]
      > La voce "FireActivityCompletedSuccessfullyEvent" è una voce dettagliata e non viene registrata, a meno che l'impostazione "TracingVerbosity" non sia al livello 5.

### <a name="trace-traffic-with-fiddler"></a>Tracciare il traffico con Fiddler

[Fiddler](https://www.telerik.com/fiddler) è uno strumento gratuito di Telerik che consente di monitorare il traffico HTTP. È possibile esaminare il traffico con il sevizio Power BI dal computer client. Questo servizio consente di visualizzare errori e altre informazioni correlate.

## <a name="next-steps"></a>Passaggi successivi

* [Connessione al gateway dati locale per le app per la logica](../logic-apps/logic-apps-gateway-connection.md)
* [Funzionalità di Enterprise Integration](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Connettori per App per la logica di Azure](../connectors/apis-list.md)
