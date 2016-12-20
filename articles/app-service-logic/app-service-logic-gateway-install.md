---
title: Installare il gateway dati locale per App per la logica | Documentazione Microsoft
description: Informazioni su come installare il gateway dati locale per l&quot;uso in un&quot;app per la logica.
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: erikre
editor: 
ms.assetid: 47e3024e-88a0-4017-8484-8f392faec89d
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/05/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: ddd62d3112a2c93ea3c935ce89ff387049899c09


---
# <a name="install-the-on-premises-data-gateway-for-logic-apps"></a>Installare il gateway dati locale per App per la logica
## <a name="installation-and-configuration"></a>Installazione e configurazione
### <a name="prerequisites"></a>Prerequisiti
Minimo:

* .NET Framework 4.5
* Windows 7 versione a 64 bit o Windows Server 2008 R2 (o versione successiva)

Consigliato:

* 8 CPU core
* 8 GB di memoria
* Windows 2012 R2 versione a 64 bit (o versione successiva)

Considerazioni correlate:

* Non è possibile installare un gateway in un controller di dominio.
* Non installare il gateway su un computer, ad esempio un portatile, che può essere disattivato, in stato di sospensione o non connesso a Internet, perché il gateway non funziona in situazioni di questo tipo. Le prestazioni del gateway possono anche diminuire su una rete wireless.

### <a name="install-a-gateway"></a>Installare un gateway
È possibile scaricare il [programma di installazione del gateway dati locale qui](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409).

Specificare **Gateway dati locale** come modalità, accedere con l'account aziendale o dell'istituto di istruzione e quindi configurare un nuovo gateway oppure eseguire la migrazione, il ripristino o la sostituzione di un gateway esistente.

* Per configurare un gateway, digitare un **nome** e una **chiave di ripristino**, quindi fare clic o toccare **Configura**.
  
    Specificare una chiave di ripristino che contenga almeno otto caratteri e conservarla in un luogo sicuro. La chiave è necessaria per migrare, ripristinare o sostituire il gateway associato.
* Per migrare, ripristinare o sostituire un gateway esistente è necessario disporre della chiave di ripristino specificata durante la creazione.

### <a name="restart-the-gateway"></a>Riavviare il gateway
Il gateway viene eseguito come servizio Windows e, come qualsiasi altro servizio di questo tipo, può essere avviato e arrestato in diversi modi. Ad esempio, è possibile aprire un prompt dei comandi con autorizzazioni elevate nel computer dove è in esecuzione il gateway e quindi eseguire i comandi seguenti:

* Per arrestare il servizio, eseguire questo comando:
  
    `net stop PBIEgwService`
* Per avviare il servizio, eseguire questo comando:
  
    `net start PBIEgwService`

### <a name="configure-a-firewall-or-proxy"></a>Configurare un firewall o proxy
Per informazioni su come fornire al gateway informazioni sul proxy, vedere [Configurare le impostazioni proxy](https://powerbi.microsoft.com/en-us/documentation/powerbi-gateway-proxy/).

Per verificare se il firewall, o proxy, blocca le connessioni, eseguire il comando seguente da un prompt dei comandi di PowerShell. Il comando verifica la connettività al bus di servizio di Azure. Il comando verifica esclusivamente la connettività di rete e non ha niente a che vedere con il servizio server cloud o il gateway. Consente di determinare se il computer può accedere a Internet.

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

I risultati dovrebbero essere simili all'esempio seguente: Se **TcpTestSucceeded** non è true, il firewall potrebbe essere la causa del blocco.

```
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

Per la massima completezza sostituire i valori **NomeComputer** e **Porta** con quelli elencati in [Configurare le porte](#configure-ports) più avanti in questo argomento.

Il firewall può anche bloccare le connessioni tra il bus di servizio e i data center di Azure. In questo caso è opportuno consentire (sbloccare) tutti gli indirizzi IP dell'area relativi ai data center. È possibile ottenere un elenco di [indirizzi IP di Azure qui](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="configure-ports"></a>Configurare le porte
Il gateway crea una connessione in uscita al bus di servizio di Azure. La comunicazione avviene sulle porte in uscita: TCP 443 (impostazione predefinita), 5671, 5672, da 9350 a 9354. Non sono richieste porte in ingresso.

Altre informazioni sulle [soluzioni ibride](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

| NOMI DI DOMINIO | PORTE IN USCITA | Descrizione |
| --- | --- | --- |
| *. analysis.windows.net |443 |HTTPS |
| *.login.windows.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Advanced Message Queuing Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |Listener in Inoltro del Bus di servizio su TCP (richiede 443 per l'acquisizione del token di Controllo di accesso) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |Usato per testare la connettività a Internet se il gateway non è raggiungibile dal servizio Power BI. |

Se è necessario consentire gli indirizzi IP anziché i domini, è possibile scaricare e usare l' [elenco di intervalli IP dei data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). In alcuni casi, le connessioni al bus di servizio di Azure verranno stabilite con l'indirizzo IP anziché con i nomi di dominio completo.

### <a name="sign-in-account"></a>Account di accesso
Gli utenti accedono con l'account aziendale o dell'istituto di istruzione. Questo è l'account dell'organizzazione. Se ci si è registrati per un'offerta di Office 365 senza fornire l'indirizzo di posta elettronica di lavoro effettivo, l'account sarà simile a jeff@contoso.onmicrosoft.com. Nell'ambito di un servizio cloud, l'account è archiviato in un tenant in Azure Active Directory (AAD). Nella maggior parte dei casi, l'UPN dell'account AAD corrisponde all'indirizzo di posta elettronica.

### <a name="windows-service-account"></a>Account del servizio Windows
Il gateway dati locale è configurato per usare NT SERVICE\PBIEgwService come credenziali di accesso al servizio Windows. Per impostazione predefinita, tale account dispone del diritto di accesso come servizio al computer in cui si sta installando il gateway.

Non si tratta dell'account usato per la connessione a origini dati locali o l'account aziendale o dell'istituto di istruzione con cui si accede ai servizi cloud.

## <a name="frequently-asked-questions"></a>Domande frequenti
### <a name="general"></a>Generale
**Domanda**: Quali origini dati sono supportate dal gateway?<br/>
**Risposta**: Al momento della stesura di questo articolo è supportato SQL Server.

**Domanda**: È necessario un gateway per le origini dati nel cloud, ad esempio SQL Azure? <br/>
**Risposta**: No. Il gateway si connette solo alle origini dati locali.

**Domanda**: Come viene chiamato il servizio Windows effettivo?<br/>
**Risposta**: Nei servizi il gateway è denominato servizio Power BI Gateway - Enterprise.

**Domanda**: Esistono connessioni in ingresso al gateway dal cloud? <br/>
**Risposta**: No. Il gateway usa le connessioni in uscita al bus di servizio di Azure.

**Domanda**: Cosa accade se si bloccano le connessioni in uscita? Cosa fare per sbloccarle? <br/>
**Risposta**: Visualizzare le porte e gli host utilizzati dal gateway.

**Domanda**: Il gateway deve essere installato nello stesso computer dell'origine dati? <br/>
**Risposta**: No. Il gateway si connette all'origine dati tramite le informazioni di connessione fornite. Il gateway può infatti essere paragonato a un'applicazione client. Deve solo potersi connettere al nome del server specificato.

**Domanda**: Qual è la latenza per l'esecuzione di query a un'origine dati dal gateway? Qual è l'architettura ottimale? <br/>
**Risposta**: Per ridurre la latenza di rete è consigliabile installare il gateway il più vicino possibile all'origine dati. Installando il gateway nell'origine dati effettiva, la latenza introdotta risulterà ridotta al minimo. Si considerino i data center. Se, ad esempio, il servizio usa il data center degli Stati Uniti occidentali e SQL Server è ospitato in una macchina virtuale di Azure, è opportuno che anche la macchina virtuale di Azure sia ubicata negli Stati Uniti occidentali. In questo modo la latenza è ridotta al minimo e si evitano addebiti relativi ai dati in uscita sulla macchina virtuale di Azure.

**Domanda**: Sono previsti requisiti di larghezza di banda di rete? <br/>
**Risposta**: È consigliabile disporre di una buona velocità effettiva per la connessione di rete. Ogni ambiente è diverso e sui risultati influisce anche la quantità di dati inviati. L'uso di ExpressRoute può contribuire a garantire un livello di velocità effettiva tra i data center di Azure e quelli locali.

Lo strumenti di terze parti Azure Speed Test può aiutare a valutare la velocità effettiva.

**Domanda**: Il servizio gateway di Windows può essere eseguito con un account Azure Active Directory? <br/>
**Risposta**: No. Il servizio Windows deve disporre di un account Windows valido. Per impostazione predefinita, viene eseguito con il SID servizio NT SERVICE\PBIEgwService.

**Domanda**: In che modo i risultati vengono inviati al cloud? <br/>
**Risposta**: L'operazione viene eseguita tramite il bus di servizio di Azure. Per altre informazioni, vedere la sezione Funzionamento del gateway.

**Domanda**: Dove sono archiviate le credenziali? <br/>
**Risposta**: Le credenziali immesse per un'origine dati vengono crittografate e archiviate nel servizio cloud del gateway. Vengono quindi decrittografate nel gateway locale.

### <a name="high-availabilitydisaster-recovery"></a>Disponibilità elevata e ripristino di emergenza
**Domanda**: Esistono piani per l'abilitazione di scenari a disponibilità elevata con il gateway? <br/>
**Risposta**: Questa funzionalità verrà implementata in futuro, ma non è ancora stato definito quando.

**Domanda**: Quali opzioni sono disponibili per il ripristino di emergenza? <br/>
**Risposta**: È possibile usare la chiave di ripristino per ripristinare o spostare un gateway. La chiave di ripristino viene specificata al momento dell'installazione del gateway.

**Domanda**: Qual è il vantaggio della chiave di ripristino? <br/>
**Risposta**: Consente di eseguire la migrazione o di ripristinare le impostazioni del gateway in caso di emergenza.

### <a name="troubleshooting"></a>Risoluzione dei problemi
**Domanda**: Dove si trovano i log del gateway? <br/>
**Risposta**: Vedere la sezione Strumenti più avanti in questo argomento.

**Domanda**: Come è possibile visualizzare le query inviate all'origine dati locale? <br/>
**Risposta**: È possibile abilitare la funzione di tracciamento delle query, che include le query inviate. Dopo aver risolto il problema, ripristinare il valore originale. Se la funzione di tracciamento delle query resta attiva, le dimensioni dei log saranno maggiori.

È anche possibile usare gli strumenti per il tracciamento delle query di cui è dotata l'origine dati. Ad esempio, è possibile usare Eventi estesi o SQL Profiler per SQL Server e Analysis Services.

## <a name="how-the-gateway-works"></a>Funzionamento del gateway
Quando un utente interagisce con un elemento connesso a un'origine dati locale:

1. Il servizio cloud crea una query, insieme alle credenziali crittografate per l'origine dati, e invia la query alla coda affinché venga elaborata dal gateway.
2. Il servizio analizza la query e invia la richiesta al bus di servizio di Azure.
3. Il gateway dati locale esegue il polling del bus di servizio per le richieste in sospeso.
4. Il gateway riceve la query, decrittografa le credenziali e le usa per la connessione alle origini dati.
5. Invia quindi la query all'origine dati per l'esecuzione.
6. I risultati vengono quindi inviati dall'origine dati al gateway e quindi al servizio cloud. Infine, il servizio usa i risultati.

## <a name="troubleshooting"></a>Risoluzione dei problemi
### <a name="update-to-the-latest-version"></a>Aggiornare alla versione più recente
Quando la versione del gateway non è aggiornata, possono emergere numerosi problemi.  È buona norma verificare di disporre sempre della versione più recente.  Se il gateway non è stato aggiornato per un mese o più è consigliabile installarne la versione più recente e verificare se il problema può essere riprodotto.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users-"></a>Errore: Impossibile aggiungere l'utente al gruppo. (-2147463168 PBIEgwService Performance Log Users)
Questo errore viene visualizzato se si sta tentando di installare il gateway in un controller di dominio, un'operazione non consentita. È necessario distribuire il gateway in un computer che non sia un controller di dominio.

## <a name="tools"></a>Strumenti
### <a name="collecting-logs-from-the-gateway-configurator"></a>Raccolta di log dallo strumento di configurazione del gateway
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
[Fiddler](http://www.telerik.com/fiddler) è uno strumento gratuito di Telerik che consente di monitorare il traffico HTTP.  Dal computer client è possibile visualizzare gli spostamenti tra il servizio Power BI e il computer client. Ciò consente di visualizzare errori e altre informazioni correlate.

## <a name="next-steps"></a>Passaggi successivi
* [Creare una connessione locale ad App per la logica](app-service-logic-gateway-connection.md)
* [Funzionalità di Enterprise Integration](app-service-logic-enterprise-integration-overview.md)
* [Connettori di App per la logica](../connectors/apis-list.md)




<!--HONumber=Nov16_HO3-->


