---
title: Come eseguire query sui log da Monitoraggio di Azure per le macchine virtuali (anteprima) | Microsoft Docs
description: Monitoraggio di Azure per le macchine virtuali soluzione raccoglie le metriche e i dati di log e questo articolo descrive i record e includono le query di esempio.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/10/2019
ms.author: magoedte
ms.openlocfilehash: 38979aa5cbb7eff0a949dfb77d6a29b2cdb5c67b
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65602088"
---
# <a name="how-to-query-logs-from-azure-monitor-for-vms-preview"></a>Come eseguire query sui log da Monitoraggio di Azure per le macchine virtuali (anteprima)
Monitoraggio di Azure per le macchine virtuali consente prestazioni e metriche relative alla connessione, computer e i dati di inventario di processo e le informazioni sullo stato di integrità e lo inoltra all'area di lavoro di Log Analitica in Monitoraggio di Azure.  Questi dati sono disponibili per [query](../../azure-monitor/log-query/log-query-overview.md) in Monitoraggio di Azure. Questi dati possono essere applicati a diversi scenari, tra cui la pianificazione della migrazione, l'analisi della capacità, l'individuazione e la risoluzione dei problemi di prestazioni on demand.

## <a name="map-records"></a>Record della mappa
Ogni ora viene generato un record per ogni computer e processo univoco, in aggiunta ai record generati all'avvio di un processo o computer o quando ne viene eseguito l'onboarding nella funzionalità di mappa di Monitoraggio di Azure per le macchine virtuali. I record hanno le proprietà descritte nelle tabelle seguenti. I campi e i valori negli eventi ServiceMapComputer_CL eseguono il mapping ai campi della risorsa del computer nell'API ServiceMap di Azure Resource Manager. I campi e i valori negli eventi ServiceMapProcess_CL eseguono il mapping ai campi della risorsa del computer nell'API ServiceMap di Azure Resource Manager. Il campo ResourceName_s coincide con il campo del nome nella risorsa di Resource Manager corrispondente. 

Sono disponibili proprietà generate internamente che è possibile usare per identificare processi e computer univoci:

- Computer: usare *ResourceId* o *ResourceName_s* per identificare in modo univoco un computer in un'area di lavoro Log Analytics.
- Processo: usare *ResourceId* per identificare in modo univoco un processo in un'area di lavoro Log Analytics. Il valore di *ResourceName_s* è univoco nel contesto del computer in cui viene eseguito il processo (MachineResourceName_s) 

Poiché possono essere presenti vari record per un determinato processo o computer in un intervallo di tempo specificato, le query possono restituire più di un record per lo stesso computer o processo. Per includere solo il record più recente, aggiungere "| dedup ResourceId" alla query.

### <a name="connections-and-ports"></a>Porte e le connessioni
La funzionalità metriche di connessione introduce due nuove tabelle in Azure Monitor log - VMConnection e VMBoundPort. Queste tabelle forniscono informazioni sulle connessioni per un computer (in ingresso e in uscita), nonché il server di porte open/attivo su di essi. ConnectionMetrics vengono inoltre esposte tramite le API che forniscono i mezzi per ottenere una metrica specifica durante un intervallo di tempo. Le connessioni TCP risultante dalla *accettazione* sono in ingresso, mentre quelli creati da un socket di ascolto *connessione* a un determinato IP e porta sono in uscita. La direzione di una connessione è rappresentata dalla proprietà Direction, che può essere impostata su **inbound** o **outbound**. 

I record in queste tabelle sono generati dai dati segnalati da Dependency Agent. Ogni record rappresenta un'osservazione in un intervallo di tempo di 1 minuto. La proprietà TimeGenerated indica l'inizio dell'intervallo di tempo. Ogni record contiene informazioni per identificare l'entità corrispondente, ovvero la connessione o la porta, oltre che le metriche associate a tale entità. Attualmente, viene segnalata solo l'attività di rete che si verifica tramite TCP su IPv4. 

#### <a name="common-fields-and-conventions"></a>I campi e le convenzioni comuni 
I campi e le convenzioni seguenti si applicano sia VMConnection VMBoundPort: 

- Computer: Nome di dominio completo del report di computer 
- Valore di AgentID: L'identificatore univoco per un computer con l'agente di Log Analitica  
- Computer: Nome della risorsa di Azure Resource Manager per la macchina esposta da ServiceMap. È nel formato *milioni-{GUID}*, dove *GUID* è lo stesso GUID come valore di AgentID  
- Processo: Nome della risorsa di Azure Resource Manager per il processo esposto da ServiceMap. È nel formato *p: {stringa esadecimale}*. Processo è univoco all'interno di un ambito di computer e per generare un ID univoco del processo tra più computer, combina i campi di computer e processo. 
- ProcessName: Nome dell'eseguibile del processo di creazione di report.
- Tutti gli indirizzi IP sono stringhe in formato canonico IPv4, ad esempio *13.107.3.160* 

Per gestire i costi e la complessità, i record di connessione non rappresentano singole connessioni di rete fisiche. Più connessioni di rete fisiche vengono raggruppate in una connessione logica, che viene quindi riflessa nella rispettiva tabella.  Ciò significa che i record nella tabella *VMConnection* rappresentano un raggruppamento logico e non le singole connessioni fisiche osservate. Le connessioni di rete fisiche che condividono lo stesso valore per gli attributi seguenti durante uno specifico intervallo di un minuto vengono aggregate in un singolo record logico in *VMConnection*. 

| Proprietà | Description |
|:--|:--|
|Direction |Direzione della connessione. Il valore è *inbound* o *outbound* |
|Machine |FQDN del computer |
|Process |Identità del processo o dei gruppi di processi che avviano/accettano la connessione |
|SourceIp |Indirizzo IP dell'origine |
|DestinationIp |Indirizzo IP della destinazione |
|DestinationPort |Numero di porta della destinazione |
|Protocol |Protocollo usato per la connessione.  Il valore è *tcp*. |

Per rendere conto dell'impatto del raggruppamento, nelle proprietà del record seguenti vengono fornite informazioni sul numero di connessioni fisiche raggruppate:

| Proprietà | Description |
|:--|:--|
|LinksEstablished |Numero di connessioni di rete fisiche che sono state stabilite durante l'intervallo di tempo di creazione del report |
|LinksTerminated |Numero di connessioni di rete fisiche che sono state terminate durante l'intervallo di tempo di creazione del report |
|LinksFailed |Numero di connessioni di rete fisiche che hanno avuto esito negativo durante l'intervallo di tempo di creazione del report. Queste informazioni sono attualmente disponibili solo per le connessioni in uscita. |
|LinksLive |Numero di connessioni di rete fisiche aperte alla fine dell'intervallo di tempo di creazione del report|

#### <a name="metrics"></a>Metriche

Oltre alle metriche relative al numero di connessioni, nelle proprietà del record seguenti vengono fornite anche informazioni sul volume dei dati inviati e ricevuti in una determinata connessione logica o porta di rete:

| Proprietà | Description |
|:--|:--|
|BytesSent |Numero totale di byte che sono stati inviati durante l'intervallo di tempo di creazione del report |
|BytesReceived |Numero totale di byte che sono stati ricevuti durante l'intervallo di tempo di creazione del report |
|Responses |Numero totale di risposte osservate durante l'intervallo di tempo di creazione del report. 
|ResponseTimeMax |Tempo di risposta più lungo (millisecondi) osservato durante l'intervallo di tempo di creazione del report. In assenza di valore, la proprietà è vuota.|
|ResponseTimeMin |Tempo di risposta più breve (millisecondi) osservato durante l'intervallo di tempo di creazione del report. In assenza di valore, la proprietà è vuota.|
|ResponseTimeSum |Somma di tutti i tempi di risposta (millisecondi) osservati durante l'intervallo di tempo di creazione del report. In assenza di valore, la proprietà è vuota.|

Il terzo tipo di dati segnalati è il tempo di risposta, ovvero il tempo di attesa, da parre di un chiamante, affinché una richiesta inviata tramite una connessione venga elaborata dall'endpoint remoto e venga fornita una risposta. Il tempo di risposta segnalato è una stima del tempo di risposta effettivo del protocollo dell'applicazione sottostante. Viene calcolato usando l'euristica in base all'osservazione del flusso di dati tra l'origine e la destinazione di una connessione di rete fisica. Concettualmente, corrisponde alla differenza tra l'ora in cui l'ultimo byte di una richiesta lascia il mittente e l'ora in cui l'ultimo byte della risposta torna al mittente. Questi due timestamp vengono usati per delineare gli eventi di richiesta e di risposta in una determinata connessione fisica. La differenza tra di essi rappresenta il tempo di risposta di una singola richiesta. 

In questa prima versione di questa funzionalità, l'algoritmo è un'approssimazione che potrebbe funzionare con un diverso livello di precisione a seconda del protocollo dell'applicazione effettivo usato per una connessione di rete specifica. L'attuale approccio, ad esempio, funziona bene per protocolli basati su richiesta-risposta, come HTTP(S), ma non con protocolli unidirezionali o protocolli basati sulla coda di messaggi.

Ecco alcuni punti importanti da considerare:

1. Se un processo accetta le connessioni sullo stesso indirizzo IP ma su più interfacce di rete, verrà segnalato un record distinto per ogni interfaccia. 
2. I record con IP con caratteri jolly non contengono attività. Sono inclusi per rappresentare il fatto che una porta nel computer è aperta per il traffico in ingresso.
3. Per ridurre il livello di dettaglio e il volume di dati, i record con IP con caratteri jolly vengono omessi quando è presente un record corrispondente (per processo, porta e protocollo uguali) con un indirizzo IP specifico. Quando un record di IP con caratteri jolly viene omesso, la proprietà IsWildcardBind del record con l'indirizzo IP specifico viene impostata su "True" per indicare che la porta è esposta in ogni interfaccia del computer che esegue la segnalazione.
4. Le porte che sono associate solo su una specifica interfaccia avere IsWildcardBind impostata su *False*.

#### <a name="naming-and-classification"></a>Denominazione e classificazione
Per praticità, l'indirizzo IP dell'estremità remota di una connessione è incluso nella proprietà RemoteIp. Per le connessioni in ingresso, RemoteIp corrisponde a SourceIp, mentre per le connessioni in uscita corrisponde a DestinationIp. La proprietà RemoteDnsCanonicalNames rappresenta i nomi canonici DNS segnalati dal computer per RemoteIp. Le proprietà RemoteDnsQuestions e RemoteClassification sono riservate per l'uso futuro. 

#### <a name="geolocation"></a>Georilevazione
*VMConnection* include anche informazioni di georilevazione per l'estremità remota di ogni record di connessione nelle proprietà del record seguenti: 

| Proprietà | Description |
|:--|:--|
|RemoteCountry |Nome del paese/area geografica che ospita RemoteIp.  Ad esempio, *Stati Uniti* |
|RemoteLatitude |Latitudine della georilevazione. Ad esempio, *47.68* |
|RemoteLongitude |Longitudine della georilevazione. Ad esempio, *-122.12* |

#### <a name="malicious-ip"></a>Indirizzi IP dannosi
Ogni proprietà RemoteIp nella tabella *VMConnection* viene confrontata con un set di indirizzi IP con attività dannosa nota. Se la proprietà RemoteIp viene identificata come dannosa, le proprietà del record seguenti (vuote quando l'indirizzo IP non è considerato dannoso) vengono popolate:

| Proprietà | Description |
|:--|:--|
|MaliciousIp |Indirizzo RemoteIp |
|IndicatorThreadType |L'indicatore di minaccia rilevato è uno dei valori seguenti, *Botnet*, *C2*, *CryptoMining*, *Darknet*, *DDos* , *MaliciousUrl*, *Malware*, *Phishing*, *Proxy*, *PUA*, *Watchlist*.   |
|Description |Descrizione della minaccia osservata. |
|TLPLevel |Il livello del protocollo di segnalazione del traffico è uno dei valori definiti: *Bianco*, *Verde*, *Ambra*, *Rosso*. |
|Confidence |I valori sono *0 - 100*. |
|Severity |I valori sono *0-5*, dove *5* è il più grave e *0* non è grave. Il valore predefinito è *3*.  |
|FirstReportedDateTime |La prima volta che il provider ha segnalato l'indicatore. |
|LastReportedDateTime |L'ultima volta in cui l'indicatore è stato visualizzato da Interflow. |
|IsActive |Indica che gli indicatori vengono disattivati con il valore *True* o *False*. |
|ReportReferenceLink |Offre collegamenti ai report relativi a un oggetto osservabile specificato. |
|AdditionalInformation |Offre informazioni aggiuntive, se disponibili, sulla minaccia osservata. |

### <a name="ports"></a>Porte 
Porte in un computer che attivamente accettano il traffico in ingresso o potenzialmente può accettare il traffico, ma sono inattive durante l'intervallo di tempo di creazione di report, vengono scritte nella tabella VMBoundPort.  

>[!NOTE]
>Monitoraggio di Azure per le macchine virtuali non supporta la raccolta e la registrazione di trasferire i dati in un'area di lavoro di Log Analitica nelle aree seguenti:  
>- Stati Uniti orientali  
>- Europa occidentale
>
> Raccolta dei dati è abilitata in altro [aree supportate](vminsights-enable-overview.md#log-analytics) per monitoraggio di Azure per le macchine virtuali. 

Ogni record presente nel VMBoundPort è identificato dai campi seguenti: 

| Proprietà | Description |
|:--|:--|
|Process | Identità del processo (o gruppi di processi) a cui è associata con la porta.|
|Ip | Porte indirizzo IP (indirizzo IP con caratteri jolly, è possibile *0.0.0.0*) |
|Port |Il numero di porta |
|Protocol | Il protocollo.  Esempio *tcp* oppure *udp* (solo *tcp* è attualmente supportata).|
 
L'identità una porta è derivata da cinque campi sopra elencati e viene archiviata nella proprietà ID porta. Questa proprietà è utilizzabile per trovare rapidamente i record per una porta specifica nel tempo. 

#### <a name="metrics"></a>Metriche 
Porta record includono le metriche che rappresentano le connessioni associate. Attualmente, vengono segnalate le metriche seguenti (i dettagli per ogni metrica sono descritti nella sezione precedente): 

- BytesSent e BytesReceived 
- LinksEstablished, LinksTerminated, LinksLive 
- ResposeTime, ResponseTimeMin, ResponseTimeMax, ResponseTimeSum 

Ecco alcuni punti importanti da considerare:

- Se un processo accetta le connessioni sullo stesso indirizzo IP ma su più interfacce di rete, verrà segnalato un record distinto per ogni interfaccia.  
- I record con IP con caratteri jolly non contengono attività. Sono inclusi per rappresentare il fatto che una porta nel computer è aperta per il traffico in ingresso. 
- Per ridurre il livello di dettaglio e il volume di dati, i record con IP con caratteri jolly vengono omessi quando è presente un record corrispondente (per processo, porta e protocollo uguali) con un indirizzo IP specifico. Quando un record IP con caratteri jolly viene omesso, il *IsWildcardBind* per il record con l'indirizzo IP specifico, verrà impostata su *True*.  Ciò indica che la porta viene esposto attraverso ogni interfaccia del computer di creazione di report. 
- Le porte che sono associate solo su una specifica interfaccia avere IsWildcardBind impostata su *False*. 

### <a name="servicemapcomputercl-records"></a>Record ServiceMapComputer_CL
I record di tipo *ServiceMapComputer_CL* includono dati di inventario per i server con Dependency Agent. Questi record includono le proprietà elencate nella tabella seguente:

| Proprietà | Description |
|:--|:--|
| Type | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | Identificatore univoco per il computer nell'area di lavoro |
| ResourceName_s | Identificatore univoco per il computer nell'area di lavoro |
| ComputerName_s | FQDN del computer |
| Ipv4Addresses_s | Un elenco degli indirizzi IPv4 del server |
| Ipv6Addresses_s | Un elenco degli indirizzi IPv6 del server |
| DnsNames_s | Una matrice di nomi DNS |
| OperatingSystemFamily_s | Windows o Linux |
| OperatingSystemFullName_s | Nome completo del sistema operativo  |
| Bitness_s | Numero di bit del computer, a 32 bit o a 64 bit  |
| PhysicalMemory_d | Memoria fisica in MB |
| Cpus_d | Numero di CPU |
| CPUSpeed_d | Velocità della CPU in MHz|
| VirtualizationState_s | *sconosciuto*, *fisico*, *virtuale*, *hypervisor* |
| VirtualMachineType_s | *hyperv*, *vmware* e così via |
| VirtualMachineNativeMachineId_g | ID della macchina virtuale assegnato dal relativo hypervisor |
| VirtualMachineName_s | Nome della macchina virtuale |
| BootTime_t | Tempo di avvio |

### <a name="servicemapprocesscl-type-records"></a>Record con tipo ServiceMapProcess_CL
I record di tipo *ServiceMapProcess_CL* includono dati di inventario per i processi connessi tramite TCP nei server con Dependency Agent. Questi record includono le proprietà elencate nella tabella seguente:

| Proprietà | Description |
|:--|:--|
| Type | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | Identificatore univoco per il processo nell'area di lavoro |
| ResourceName_s | Identificatore univoco per il processo nel computer in cui è in esecuzione|
| MachineResourceName_s | Nome della risorsa del computer |
| ExecutableName_s | Nome dell'eseguibile del processo |
| StartTime_t | Ora di inizio del pool del processo |
| FirstPid_d | Primo PID nel pool del processo |
| Description_s | Descrizione del processo |
| CompanyName_s | Nome dell'azienda |
| InternalName_s | Nome interno |
| ProductName_s | Nome del prodotto |
| ProductVersion_s | Versione del prodotto |
| FileVersion_s | Versione del file |
| CommandLine_s | Riga di comando |
| ExecutablePath_s | Percorso del file eseguibile |
| WorkingDirectory_s | La directory di lavoro |
| UserName | Account con cui è in esecuzione il processo |
| UserDomain | Dominio in cui è in esecuzione il processo |

## <a name="sample-log-searches"></a>Ricerche di log di esempio

### <a name="list-all-known-machines"></a>Visualizzare tutti i computer noti
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="when-was-the-vm-last-rebooted"></a>Data dell'ultimo riavvio della macchina virtuale
```kusto
let Today = now(); ServiceMapComputer_CL | extend DaysSinceBoot = Today - BootTime_t | summarize by Computer, DaysSinceBoot, BootTime_t | sort by BootTime_t asc`
```

### <a name="summary-of-azure-vms-by-image-location-and-sku"></a>Riepilogo delle macchine virtuali di Azure per immagine, posizione e SKU
```kusto
ServiceMapComputer_CL | where AzureLocation_s != "" | summarize by ComputerName_s, AzureImageOffering_s, AzureLocation_s, AzureImageSku_s`
```

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Visualizzare la capacità di memoria fisica di tutti i computer gestiti.
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project PhysicalMemory_d, ComputerName_s`
```

### <a name="list-computer-name-dns-ip-and-os"></a>Visualizzare nome del computer, DNS, IP e sistema operativo.
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s`
```

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Trovare tutti i processi con "sql" nella riga di comando
```kusto
ServiceMapProcess_CL | where CommandLine_s contains_cs "sql" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Trovare un computer (record più recente) in base al nome di risorsa
```kusto
search in (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Trovare un computer, ovvero il record più recente, in base all’indirizzo IP
```kusto
search in (ServiceMapComputer_CL) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="list-all-known-processes-on-a-specified-machine"></a>Elencare tutti i processi noti su un computer specifico
```kusto
ServiceMapProcess_CL | where MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="list-all-computers-running-sql-server"></a>Elencare tutti i computer che eseguono SQL Server
```kusto
ServiceMapComputer_CL | where ResourceName_s in ((search in (ServiceMapProcess_CL) "\*sql\*" | distinct MachineResourceName_s)) | distinct ComputerName_s`
```

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Elencare tutte le versioni di prodotto univoche di curl nel data center
```kusto
ServiceMapProcess_CL | where ExecutableName_s == "curl" | distinct ProductVersion_s`
```

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Creare un gruppo di tutti i computer che eseguono CentOS
```kusto
ServiceMapComputer_CL | where OperatingSystemFullName_s contains_cs "CentOS" | distinct ComputerName_s`
```

### <a name="bytes-sent-and-received-trends"></a>Tendenze di byte inviati e ricevuti
```kusto
VMConnection | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer | order by Computer desc | render timechart`
```

### <a name="which-azure-vms-are-transmitting-the-most-bytes"></a>Macchine virtuali di Azure che trasmettono il maggior numero di byte
```kusto
VMConnection | join kind=fullouter(ServiceMapComputer_CL) on $left.Computer == $right.ComputerName_s | summarize count(BytesSent) by Computer, AzureVMSize_s | sort by count_BytesSent desc`
```

### <a name="link-status-trends"></a>Tendenze dello stato del collegamento
```kusto
VMConnection | where TimeGenerated >= ago(24hr) | where Computer == "acme-demo" | summarize  dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h) | render timechart`
```

### <a name="connection-failures-trend"></a>Tendenza degli errori di connessione
```kusto
VMConnection | where Computer == "acme-demo" | extend bythehour = datetime_part("hour", TimeGenerated) | project bythehour, LinksFailed | summarize failCount = count() by bythehour | sort by bythehour asc | render timechart`
```

### <a name="bound-ports"></a>Le porte con binding
```kusto
VMBoundPort
| where TimeGenerated >= ago(24hr)
| where Computer == 'admdemo-appsvr'
| distinct Port, ProcessName
```

### <a name="number-of-open-ports-across-machines"></a>Numero di aprire le porte tra più computer
```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by Computer, Machine, Port, Protocol
| summarize OpenPorts=count() by Computer, Machine
| order by OpenPorts desc
```

### <a name="score-processes-in-your-workspace-by-the-number-of-ports-they-have-open"></a>Assegnare punteggi di processi nell'area di lavoro per il numero di porte che hanno open
```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by ProcessName, Port, Protocol
| summarize OpenPorts=count() by ProcessName
| order by OpenPorts desc
```

### <a name="aggregate-behavior-for-each-port"></a>Comportamento di aggregazione per ogni porta
Questa query è quindi utilizzabile per assegnare un punteggio porte dall'attività, ad esempio, le porte con il maggior traffico in ingresso/uscita, le porte con la maggior parte delle connessioni
```kusto
// 
VMBoundPort
| where Ip != "127.0.0.1"
| summarize BytesSent=sum(BytesSent), BytesReceived=sum(BytesReceived), LinksEstablished=sum(LinksEstablished), LinksTerminated=sum(LinksTerminated), arg_max(TimeGenerated, LinksLive) by Machine, Computer, ProcessName, Ip, Port, IsWildcardBind
| project-away TimeGenerated
| order by Machine, Computer, Port, Ip, ProcessName
```

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>Riepilogare le connessioni in uscita da un gruppo di computer
```kusto
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
// all connections to/from the machines of interest
let out=materialize(VMConnection
| where Machine in (machines)
| summarize arg_max(TimeGenerated, *) by ConnectionId);
// connections to localhost augmented with RemoteMachine
let local=out
| where RemoteIp startswith "127."
| project ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=Machine;
// connections not to localhost augmented with RemoteMachine
let remote=materialize(out
| where RemoteIp !startswith "127."
| join kind=leftouter (ips) on $left.RemoteIp == $right.ips
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=MonitoredMachine);
// the remote machines to/from which we have connections
let remoteMachines = remote | summarize by RemoteMachine;
// all augmented connections
(local)
| union (remote)
//Take all outbound records but only inbound records that come from either //unmonitored machines or monitored machines not in the set for which we are computing dependencies.
| where Direction == 'outbound' or (Direction == 'inbound' and RemoteMachine !in (machines))
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine
// identify the remote port
| extend RemotePort=iff(Direction == 'outbound', DestinationPort, 0)
// construct the join key we'll use to find a matching port
| extend JoinKey=strcat_delim(':', RemoteMachine, RemoteIp, RemotePort, Protocol)
// find a matching port
| join kind=leftouter (VMBoundPort 
| where Machine in (remoteMachines) 
| summarize arg_max(TimeGenerated, *) by PortId 
| extend JoinKey=strcat_delim(':', Machine, Ip, Port, Protocol)) on JoinKey
// aggregate the remote information
| summarize Remote=makeset(iff(isempty(RemoteMachine), todynamic('{}'), pack('Machine', RemoteMachine, 'Process', Process1, 'ProcessName', ProcessName1))) by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol
```

## <a name="next-steps"></a>Passaggi successivi
* Se si ha familiarità con la scrittura di query di log in Monitoraggio di Azure, consultare [come usare Log Analitica](../../azure-monitor/log-query/get-started-portal.md) nel portale di Azure per scrivere le query di log.
* Leggere le informazioni su come [scrivere query di ricerca](../../azure-monitor/log-query/search-queries.md).
