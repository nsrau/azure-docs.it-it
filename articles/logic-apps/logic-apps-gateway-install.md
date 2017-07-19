---
title: Installare il gateway dati locale - App per la logica di Azure | Microsoft Docs
description: Prima di accedere alle origini dei dati in locale, installare il gateway di dati locale per il trasferimento rapido dei dati e la crittografia tra le origini dati locale e le app per la logica
keywords: accesso ai dati, locale, trasferimento dei dati, crittografia, origini dei dati
services: logic-apps
documentationcenter: 
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 47e3024e-88a0-4017-8484-8f392faec89d
ms.service: logic-apps
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 06/9/2017
ms.author: LADocs; dimazaid; estfan
ms.translationtype: Human Translation
ms.sourcegitcommit: 5bbeb9d4516c2b1be4f5e076a7f63c35e4176b36
ms.openlocfilehash: 7122b970c2e4703df9771e8ace4e710399ca3e6c
ms.contentlocale: it-it
ms.lasthandoff: 06/13/2017


---
# <a name="install-the-on-premises-data-gateway-for-azure-logic-apps"></a>Installare il gateway dati locale per le app per la logica di Azure

Prima che le app per la logica possano accedere alle origini di dati in locale, è necessario installare e configurare il gateway dati locale. Il gateway funge da ponte che fornisce il trasferimento rapido dei dati e la crittografia tra sistemi locali e le app per la logica. Il gateway inoltra i dati da origini locali sui canali crittografati tramite il bus di servizio di Azure. Tutto il traffico ha origine come traffico sicuro in uscita dall'agente di gateway. Altre informazioni sul [funzionamento del gateway dati](#gateway-cloud-service).

Il gateway supporta le connessioni alle origine dati locali seguenti:

*   BizTalk Server
*   DB2  
*   File system
*   Informix
*   MQ
*   MySQL
*   Oracle Database
*   PostgreSQL
*   Server applicazioni SAP 
*   Server messaggi SAP
*   SharePoint solo per HTTP, non per HTTPS
*   SQL Server
*   Teradata

Questa procedura mostra come installare il gateway dati locale prima di [impostare una connessione tra il gateway e le app per la logica](./logic-apps-gateway-connection.md). Per altre informazioni sui connettori supportati, vedere [Connettori per le app per la logica di Azure](https://docs.microsoft.com/azure/connectors/apis-list). 

Per altre informazioni sui gateway dati per altri servizi Microsoft, vedere gli articoli seguenti:

*   [Gateway applicazione](https://azure.microsoft.com/services/application-gateway/): [Panoramica del gateway applicazione](../application-gateway/application-gateway-introduction.md)
*   [Gateway dati locale di Microsoft Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
*   [Gateway dati locale di Azure Analysis Services](../analysis-services/analysis-services-gateway.md)
*   [Gateway dati locale di Microsoft Flow](https://flow.microsoft.com/documentation/gateway-manage/)

<a name="requirements"></a>
## <a name="requirements"></a>Requisiti

**Minimo**:

* .NET Framework 4.5
* Windows 7 versione a 64 bit o Windows Server 2008 R2 (o versione successiva)

**Consigliato**:

* 8 CPU core
* 8 GB di memoria
* Windows 2012 R2 versione a 64 bit (o versione successiva)

**Considerazioni importanti**:

* Installare il gateway dati locale solo su un computer locale.
Non è possibile installare il gateway in un controller di dominio.

   > [!TIP]
   > Non è necessario installare il gateway nello stesso computer dell'origine dati. Per ridurre al minimo la latenza, è possibile installare il gateway il più vicino possibile all'origine dati o nello stesso computer, presupponendo che si disponga delle autorizzazioni.

* Non installare il gateway su un computer che si disattiva o che non può connettersi a Internet perché il gateway non può essere eseguito in tali circostanze. Le prestazioni del gateway possono anche diminuire su una rete wireless.

* Durante l'installazione, è necessario accedere con un [account aziendale o dell'istituto di istruzione](https://docs.microsoft.com/azure/active-directory/sign-up-organization) gestito da Azure Active Directory (Azure AD), non con un account Microsoft. 

  È necessario usare successivamente lo stesso account aziendale o dell'istituto di istruzione nel portale di Azure quando si crea e si associa una risorsa di gateway all'installazione del gateway. Si seleziona quindi la risorsa del gateway quando si crea la connessione tra l'app per la logica e l'origine dati locale. [Perché è necessario usare un account di Azure AD aziendale o dell'istituto di istruzione?](#why-azure-work-school-account)

  > [!TIP]
  > Se si è effettuata l'iscrizione a un'offerta di Office 365 senza fornire l'indirizzo di posta elettronica aziendale effettivo, l'indirizzo di accesso sarà simile a jeff@contoso.onmicrosoft.com. 

* Se si dispone di un gateway esistente impostato con un programma di installazione che è precedente alla versione 14.16.6317.4, non è possibile modificare il percorso del gateway eseguendo il programma di installazione più recente. Tuttavia, è possibile usare il programma di installazione più recente per configurare un nuovo gateway con il percorso che si desidera.
  
  Se si dispone di un programma di installazione di gateway che è precedente alla versione 14.16.6317.4, ma non è ancora stato installato il gateway, è possibile scaricare e usare il programma di installazione più recente.

<a name="install-gateway"></a>

## <a name="install-the-data-gateway"></a>Installare il gateway dati

1.  [Scaricare ed eseguire il programma di installazione di gateway in un computer locale](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409).

2. Leggere e accettare le condizioni per l'utilizzo e l'informativa sulla privacy.

3. Specificare il percorso nel computer locale in cui si desidera installare il gateway.

4. Quando richiesto, accedere con il proprio account aziendale o dell'istituzione di istruzione di Azure, non con un account Microsoft.

   ![Accedere con l'account aziendale o dell'istituto di istruzione di Azure](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

5. Ora registrare il gateway installato con il [servizio cloud gateway](#gateway-cloud-service). Scegliere l'opzione che **consente di registrare un nuovo gateway in questo computer**.

   Il servizio cloud gateway crittografa e archivia le credenziali dell'origine dati e i dettagli del gateway. 
   Il servizio instrada anche le query e i relativi risultati tra l'app per la logica, il gateway dati locale e l'origine dati in locale.

6. Specificare un nome per l'installazione del gateway. Creare una chiave di ripristino, quindi confermarla. 

   > [!IMPORTANT] 
   > La chiave di ripristino deve contenere almeno otto caratteri. Assicurarsi di salvare e conservare la chiave in un luogo sicuro. Questa chiave è necessaria anche per eseguire la migrazione, ripristinare o acquisire la proprietà di un gateway esistente.

   1. Per modificare l'area predefinita per il servizio cloud gateway e per il bus di servizio di Azure usato per l'installazione del gateway, scegliere **Cambia area**.

      ![Cambiare area](./media/logic-apps-gateway-install/change-region-gateway-install.png)

      L'area predefinita è l'area associata al tenant di Azure AD.

   2. Nel riquadro successivo aprire **Selezionare l'area** per scegliere un'area diversa.

      ![Selezionare un'altra area](./media/logic-apps-gateway-install/select-region-gateway-install.png)

      Si potrebbe ad esempio selezionare la stessa area come app per la logica o selezionare l'area più vicina all'origine dati in locale in modo da ridurre la latenza. Le risorse gateway e l'app per la logica possono avere posizioni diverse.

      > [!IMPORTANT]
      > Dopo l'installazione non è possibile modificare questa area. L'area determina e limita anche la posizione in cui è possibile creare la risorsa di Azure per il gateway. Pertanto, quando si crea la risorsa del gateway in Azure, assicurarsi che il percorso della risorsa corrisponda all'area selezionata durante l'installazione del gateway.
      > 
      > Se si desidera usare un'area diversa per il gateway in un secondo momento, è necessario configurare un nuovo gateway.

   3. Al termine, scegliere **Fine**.

7. Seguire ora la procedura seguente nel portale di Azure per [creare una risorsa di Azure per il gateway](../logic-apps/logic-apps-gateway-connection.md). 

Altre informazioni sul [funzionamento del gateway dati](#gateway-cloud-service).

## <a name="migrate-restore-or-take-over-an-existing-gateway"></a>Eseguire la migrazione, ripristinare o sostituire un gateway esistente

Per eseguire queste attività, è necessario disporre della chiave di ripristino che è stata specificata all'installazione del gateway.

1. Dal menu di avvio del computer, scegliere **Gateway dati locale**.

2. Quando viene aperto il programma di installazione accedere con lo stesso account di Azure aziendale o dell'istituto di istruzione usato in precedenza per installare il gateway.

3. Scegliere **Eseguire la migrazione, ripristinare o acquisire la proprietà di un gateway esistente**.

4. Specificare la chiave di ripristino per il gateway di cui si vuole eseguire la migrazione o il ripristino o acquisire la proprietà.

<a name="restart-gateway"></a>
## <a name="restart-the-gateway"></a>Riavviare il gateway

Il gateway viene eseguito come un servizio Windows. Come qualsiasi altro servizio Windows, può essere avviato e arrestato in diversi modi. Ad esempio, è possibile aprire un prompt dei comandi con autorizzazioni elevate nel computer in cui è in esecuzione il gateway e quindi eseguire uno dei comandi seguenti:

* Per arrestare il servizio, eseguire questo comando:
  
    `net stop PBIEgwService`

* Per avviare il servizio, eseguire questo comando:
  
    `net start PBIEgwService`

### <a name="windows-service-account"></a>Account del servizio Windows

Il gateway dati locale è configurato per usare `NT SERVICE\PBIEgwService` come credenziale di accesso al servizio Windows. Per impostazione predefinita, il gateway ha il diritto di "Accesso come servizio", per il computer in cui si installa il gateway.

> [!NOTE]
> Questo account del servizio Windows è diverso dall'account usato per la connessione a origini dati locali e dall'account di Azure aziendale o dell'istituto di istruzione usato per accedere ai servizi cloud.

## <a name="configure-a-firewall-or-proxy"></a>Configurare un firewall o proxy

Il gateway crea una connessione in uscita al [bus di servizio di Azure](https://azure.microsoft.com/services/service-bus/). Per fornire al gateway informazioni sul proxy, vedere [Configurazione delle impostazioni proxy](https://powerbi.microsoft.com/documentation/powerbi-gateway-proxy/).

Per verificare se il firewall, o proxy, potrebbe bloccare le connessioni, verificare che il computer riesca effettivamente a connettersi a Internet e al [bus di servizio di Azure](https://azure.microsoft.com/services/service-bus/). Da un prompt di PowerShell eseguire questo comando:

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

> [!NOTE]
> Questo comando verifica solo la connettività di rete e la connettività per il bus di servizio di Azure. Pertanto, il comando non ha nulla a che fare con il gateway o con il servizio cloud del gateway che crittografa e archivia le credenziali e i dettagli di gateway. 
>
> Questo comando è disponibile solo in Windows Server 2012 R2 o in una versione successiva e in Windows 8.1 o in una versione successiva. Nelle versioni precedenti del sistema operativo, è possibile usare Telnet per testare la connettività. Altre informazioni su [bus di servizio di Azure e soluzioni ibride](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

I risultati dovrebbero essere simili all'esempio seguente:

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

Se **TcpTestSucceeded** non è impostato su **True**, il firewall potrebbe essere la causa del blocco. Per la massima completezza sostituire i valori **ComputerName** e **Porta** con quelli elencati in [Configurare le porte](#configure-ports) in questo argomento.

Il firewall può anche bloccare le connessioni tra il bus di servizio e i data center di Azure. Se si verifica questo scenario, approvare (sbloccare) tutti gli indirizzi IP per i data center nell'area geografica. Per gli indirizzi IP, [ottenere l'elenco di indirizzi IP Azure qui](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="configure-ports"></a>Configurare le porte

Il gateway crea una connessione in uscita al [Bus di servizio di Azure](https://azure.microsoft.com/services/service-bus/) e comunica sulle porte in uscita: TCP 443 (predefinita), 5671, 5672, 9350 attraverso 9354. Non sono richieste porte in ingresso. Altre informazioni su [bus di servizio di Azure e soluzioni ibride](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

| NOMI DI DOMINIO | PORTE IN USCITA | Descrizione |
| --- | --- | --- |
| *. analysis.windows.net | 443 | HTTPS | 
| *.login.windows.net | 443 | HTTPS | 
| *.servicebus.windows.net | 5671-5672 | Advanced Message Queuing Protocol (AMQP) | 
| *.servicebus.windows.net | 443, 9350-9354 | Listener in Inoltro del Bus di servizio su TCP (richiede 443 per l'acquisizione del token di Controllo di accesso) | 
| *.frontend.clouddatahub.net | 443 | HTTPS | 
| *.core.windows.net | 443 | HTTPS | 
| login.microsoftonline.com | 443 | HTTPS | 
| *.msftncsi.com | 443 | Usato per testare la connettività a Internet se il gateway non è raggiungibile dal servizio Power BI. | 

Se è necessario approvare gli indirizzi IP anziché i domini, è possibile scaricare e usare l'[elenco di intervalli IP dei data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). In alcuni casi, le connessioni al Bus di servizio di Azure verranno stabilite con l'indirizzo IP anziché con i nomi di dominio completo.

<a name="gateway-cloud-service"></a>
## <a name="how-does-the-data-gateway-work"></a>Come funziona il gateway dati?

Il gateway dati facilita la comunicazione rapida e sicura tra l'app per la logica, il servizio cloud del gateway e l'origine dati locale. 

![diagram-for-on-premises-data-gateway-flow](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

Pertanto quando l'utente nel cloud interagisce con un elemento connesso a un'origine dati in locale:

1. Il servizio cloud del gateway crea una query, insieme alle credenziali crittografate per l'origine dati, e invia la query alla coda affinché venga elaborata dal gateway.

2. Il servizio cloud del gateway analizza la query e inserisce la richiesta nel bus di servizio di Azure.

3. Il gateway dati locale esegue il polling del bus di servizio per le richieste in sospeso.

4. Il gateway riceve la query, decrittografa le credenziali e le usa per la connessione alle origini dati.

5. Invia quindi la query all'origine dati per l'esecuzione.

6. I risultati vengono quindi inviati dall'origine dati al gateway e quindi al servizio cloud del gateway. Infine, il servizio cloud del gateway usa i risultati.

<a name="faq"></a>
## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="general"></a>Generale

**D**: È necessario un gateway per le origini dati nel cloud, ad esempio SQL Azure? <br/>
**R**: No. Il gateway si connette solo alle origini dati locali.

**D**: Il gateway deve essere installato nello stesso computer dell'origine dati? <br/>
**R**: No. Il gateway si connette all'origine dati tramite le informazioni di connessione fornite. In questo senso il gateway può essere paragonato a un'applicazione client. Il gateway deve solo potersi connettere al nome del server specificato.

<a name="why-azure-work-school-account"></a>

**D**: Perché è necessario usare un account di Azure aziendale o dell'istituto di istruzione per accedere? <br/>
**R**: È possibile usare solo un account di Azure aziendale o dell'istituto di istruzione quando si installa il gateway dati locale. L'account di accesso viene archiviato in un tenant gestito da Azure Active Directory (Azure AD). Il nome dell'entità utente (UPN) dell'account di Azure AD in genere corrisponde all'indirizzo di posta elettronica.

**D**: Dove sono archiviate le credenziali? <br/>
**R**: Le credenziali immesse per un'origine dati vengono crittografate e archiviate nel servizio cloud del gateway. Le credenziali vengono quindi decrittografate nel gateway dati locale.

**D**: Sono previsti requisiti per la larghezza di banda della rete? <br/>
**R**: È consigliabile che la connessione di rete abbia una buona velocità effettiva. Ogni ambiente è diverso e la quantità di dati inviati influisce sui risultati. L'uso di ExpressRoute può contribuire a garantire un livello di velocità effettiva tra i data center di Azure e quelli locali.
Lo strumenti di terze parti Azure Speed Test può aiutare a valutare la velocità effettiva.

**D**: Qual è la latenza per l'esecuzione di query a un'origine dati dal gateway? Qual è l'architettura ottimale? <br/>
**R**: Per ridurre la latenza di rete è consigliabile installare il gateway il più vicino possibile all'origine dati. Installando il gateway nell'origine dati effettiva, la latenza introdotta risulterà ridotta al minimo grazie a questa prossimità. Si considerino anche i data center. Se, ad esempio, il servizio usa il data center degli Stati Uniti occidentali e SQL Server è ospitato in una macchina virtuale di Azure, è opportuno che anche la macchina virtuale di Azure sia ubicata negli Stati Uniti occidentali. Grazie a questa prossimità la latenza è ridotta al minimo e si evitano addebiti relativi ai dati in uscita sulla macchina virtuale di Azure.

**D**: In che modo i risultati vengono inviati al cloud? <br/>
**R**: I risultati vengono inviati tramite il Bus di servizio di Azure.

**D**: Esistono connessioni in ingresso al gateway dal cloud? <br/>
**R**: No. Il gateway usa le connessioni in uscita al bus di servizio di Azure.

**D**: Cosa accade se si bloccano le connessioni in uscita? Cosa fare per sbloccarle? <br/>
**R**: Visualizzare le porte e gli host usati dal gateway.

**D**: Come viene chiamato il servizio Windows effettivo?<br/>
**R**: Nei servizi il gateway è denominato servizio Power BI Gateway Enterprise.

**D**: Il servizio gateway di Windows può essere eseguito con un account Azure Active Directory? <br/>
**R**: No. Il servizio Windows deve disporre di un account Windows valido. Per impostazione predefinita, il sevizio viene eseguito con il SID servizio NT SERVICE\PBIEgwService.

### <a name="high-availability-and-disaster-recovery"></a>Disponibilità elevata e ripristino di emergenza

**D**: Quali opzioni sono disponibili per il ripristino di emergenza? <br/>
**R**: È possibile usare la chiave di ripristino per ripristinare o spostare un gateway. La chiave di ripristino viene specificata al momento dell'installazione del gateway.

**D**: Qual è il vantaggio della chiave di ripristino? <br/>
**R**: La chiave di ripristino consente di eseguire la migrazione o di ripristinare le impostazioni del gateway in caso di emergenza.

**D**: Esistono piani per l'abilitazione di scenari a disponibilità elevata con il gateway? <br/>
**R**: Questi scenari verranno implementati in futuro, ma non è ancora stato definito quando.

## <a name="troubleshooting"></a>Risoluzione dei problemi

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

**D**: Come è possibile visualizzare le query inviate all'origine dati locale? <br/>
**R**: È possibile abilitare la funzione di tracciamento delle query, che include le query inviate. Dopo aver risolto il problema, ripristinare il valore originale per il tracciamento delle query. Se il tracciamento delle query non viene disabilitato, si creeranno dei log più grandi.

È anche possibile usare gli strumenti per il tracciamento delle query di cui è dotata l'origine dati. Ad esempio, è possibile usare Eventi estesi o SQL Profiler per SQL Server e Analysis Services.

**D**: Dove si trovano i log del gateway? <br/>
**R**: Vedere la sezione Strumenti più avanti in questo argomento.

### <a name="update-to-the-latest-version"></a>Aggiornare alla versione più recente

Quando la versione del gateway non è aggiornata, possono emergere numerosi problemi. Come buona pratica, assicurarsi di usare la versione più recente. Se il gateway non è stato aggiornato per un mese o più è consigliabile installarne la versione più recente e verificare se è possibile riprodurre il problema.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Errore: Impossibile aggiungere l'utente al gruppo. (Utenti log delle prestazioni -2147463168 PBIEgwService)

Questo errore viene visualizzato se si sta tentando di installare il gateway in un controller di dominio, un'operazione non consentita. Assicurarsi di distribuire il gateway in un computer che non sia un controller di dominio.

## <a name="tools"></a>Strumenti

### <a name="collect-logs-from-the-gateway-configurer"></a>Raccolta di log dallo strumento di configurazione del gateway

È possibile raccogliere numerosi log per il gateway. La raccolta dei log è sempre la prima operazione da eseguire.

#### <a name="installer-logs"></a>Log dei programmi di installazione

`%localappdata%\Temp\Power_BI_Gateway_–Enterprise.log`

#### <a name="configuration-logs"></a>Log di configurazione

`%localappdata%\Microsoft\Power BI Enterprise Gateway\GatewayConfigurator.log`

#### <a name="enterprise-gateway-service-logs"></a>Log del servizio gateway Enterprise

`C:\Users\PBIEgwService\AppData\Local\Microsoft\Power BI Enterprise Gateway\EnterpriseGateway.log`

#### <a name="event-logs"></a>Log eventi

I log di Gateway di gestione dati e PowerBIGateway sono reperibili in **Registri applicazioni e servizi**.

### <a name="fiddler-trace"></a>Fiddler Trace

[Fiddler](http://www.telerik.com/fiddler) è uno strumento gratuito di Telerik che consente di monitorare il traffico HTTP. Dal computer client è possibile visualizzare il traffico con il sevizio Power BI e il computer client. Questo servizio consente di visualizzare errori e altre informazioni correlate.

## <a name="next-steps"></a>Passaggi successivi
    
* [Connessione al gateway dati locale per le app per la logica](../logic-apps/logic-apps-gateway-connection.md)
* [Funzionalità di Enterprise Integration](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Connettori per App per la logica di Azure](../connectors/apis-list.md)

