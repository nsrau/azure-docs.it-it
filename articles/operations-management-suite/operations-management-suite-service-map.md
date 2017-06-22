---
title: Mapping dei servizi in Operations Management Suite (OMS) | Microsoft Docs
description: "Mapping dei servizi è una soluzione di Operations Management Suite (OMS) che individua automaticamente i componenti delle applicazioni nei sistemi Windows e Linux e mappa la comunicazione fra i servizi.  Questo articolo fornisce informazioni dettagliate su come distribuire Mapping dei servizi nell&quot;ambiente e su come usarlo in svariati scenari."
services: operations-management-suite
documentationcenter: 
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: 3ceb84cc-32d7-4a7a-a916-8858ef70c0bd
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/22/2016
ms.author: daseidma;bwren;dairwin
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: ce71f8cd842746e6e236f4a2034ba3ee3001586c
ms.contentlocale: it-it
ms.lasthandoff: 05/11/2017


---

# <a name="using-service-map-solution-in-operations-management-suite-oms"></a>Usare la soluzione Mapping dei servizi in Operations Management Suite (OMS)
Mapping dei servizi individua automaticamente i componenti delle applicazioni nei sistemi Windows e Linux ed esegue la mappatura della comunicazione fra i servizi. Consente di visualizzare i server nel modo in cui si pensa a essi, ovvero come sistemi interconnessi che forniscono servizi critici.  Mapping dei servizi mostra le connessioni fra i server, i processi e le porte di tutte le architetture connesse via TCP senza il bisogno di alcuna configurazione a parte l'installazione di un agente.

Questo articolo fornisce i dettagli su come usare Mapping dei servizi.  Per informazioni sulla configurazione di Mapping dei servizi e sugli agenti di caricamento, vedere [Configuring Service Map solution in Operations Management Suite (OMS)](operations-management-suite-service-map-configure.md) (Configurare la soluzione Mapping dei servizi in Operations Management Suite (OMS))


## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Casi di utilizzo: Riconoscimento delle dipendenze nei processi IT

### <a name="discovery"></a>Individuazione
Mapping dei servizi genera automaticamente una mappa di riferimento delle dipendenze tra server, processi e servizi di terze parti.  La soluzione rileva e mappa tutte le dipendenze TCP, identificando le connessioni impreviste, i sistemi remoti di terze parti da cui si dipende e le dipendenze da aree tradizionali non note della rete, ad esempio Active Directory.  Mapping dei servizi rileva i tentativi di connessione di rete non riusciti effettuati dai sistemi gestiti permettendo di identificare potenziali configurazioni del server errate, interruzioni dei servizi e problemi di rete.

### <a name="incident-management"></a>Gestione di eventi imprevisti
Mapping dei servizi consente di eliminare i tentativi di isolamento del problema visualizzando le connessioni tra i sistemi e la loro influenza reciproca.  In aggiunta alle informazioni sulle connessioni non riuscite, le informazioni sui client connessi permettono di identificare i bilanciamenti del carico errati, il carico imprevisto o eccessivo sui servizi critici e i client non autorizzati, ad esempio i computer per lo sviluppo in comunicazione con i sistemi di produzione.  I flussi di lavoro integrati in Rilevamento modifiche di OMS consentono inoltre di verificare se un evento di modifica in un computer o servizio back-end descrive la causa principale di un evento imprevisto.

### <a name="migration-assurance"></a>Garanzia di migrazione
Mapping dei servizi consente di pianificare in modo efficace, accelerare e convalidare le migrazioni di Azure, garantendo che non venga tralasciato nulla e non siano presenti interruzioni impreviste.  È possibile individuare tutti i sistemi interdipendenti di cui è necessario eseguire la migrazione insieme, valutare la configurazione e la capacità del sistema e determinare se un sistema in esecuzione è ancora utile agli utenti oppure è un candidato alla rimozione delle autorizzazioni anziché alla migrazione.  Dopo aver eseguito lo spostamento, è possibile verificare il caricamento e l'identità dei client per assicurarsi che i sistemi di test e i clienti siano connessi.  Se ci sono problemi nella pianificazione delle subnet e nelle definizioni dei firewall, le connessioni non riuscite nelle mappe di Mapping dei servizi indicano i sistemi che necessitano di connettività.

### <a name="business-continuity"></a>Continuità aziendale
Se si usa Azure Site Recovery e si necessita di aiuto per definire la sequenza di ripristino dell'ambiente delle applicazioni, Mapping dei servizi visualizza automaticamente le dipendenze reciproche tra i sistemi, consentendo di verificare l'affidabilità del proprio piano di ripristino.  Scegliendo un server critico e visualizzandone i client è possibile identificare i sistemi front-end che devono essere ripristinati soltanto dopo che il server critico è stato ripristinato ed è disponibile.  Allo stesso modo, osservando le dipendenze back-end di un server critico è possibile identificare i sistemi che devono essere ripristinati prima del sistema critico.

### <a name="patch-management"></a>Gestione delle patch
Mapping dei servizi ottimizza l'uso di System Update Assessment di OMS mostrando gli altri team e server che dipendono da un servizio, in modo che sia possibile inviare una notifica prima di arrestare i sistemi per l'applicazione di patch.  Mapping dei servizi migliora anche la gestione delle patch in OMS indicando se i servizi sono disponibili e connessi correttamente dopo l'applicazione delle patch e il riavvio.


## <a name="mapping-overview"></a>Panoramica sui mapping
Gli agenti di Mapping dei servizi raccolgono informazioni su tutti i processi connessi tramite TCP nel server in cui sono installati e dettagli sulle connessioni in entrata e in uscita di ogni processo.  Usando l'elenco di computer a sinistra della soluzione Mapping dei servizi è possibile selezionare i computer con agenti di Mapping dei servizi per visualizzarne le dipendenze in un determinato intervallo di tempo.  Le mappe delle dipendenze dei computer sono specifiche di un computer e visualizzano tutti i computer che sono client TCP diretti o server del computer specifico.

![Panoramica di Mapping dei servizi](media/oms-service-map/service-map-overview.png)

È possibile espandere i computer nella mappa per visualizzare i processi in esecuzione con connessioni di rete attive nell'intervallo di tempo selezionato.  Quando si espande un computer remoto con un agente di Mapping dei servizi per visualizzare i dettagli dei processi, vengono visualizzati solo i processi che comunicano con il computer.  Il numero di computer front-end senza agenti che si connettono al computer è indicato a sinistra dei processi a cui si connettono.  Se il computer attivo è in fase di connessione a computer back-end senza un agente, il server back-end è incluso in un gruppo di porte server con altre connessioni allo stesso numero di porta.

Per impostazione predefinita, Mapping dei servizi mostra le informazioni sulle dipendenze per gli ultimi 30 minuti.  Usando i controlli temporali in alto a sinistra, è possibile cercare nelle mappe intervalli di tempo cronologici della durata massima di un'ora per visualizzare l'aspetto delle dipendenze nel passato, ad esempio durante un evento imprevisto o prima di una modifica.    I dati di Mapping dei servizi vengono archiviati per 30 giorni nelle aree di lavoro a pagamento e per 7 giorni nelle aree di lavoro gratuite.

## <a name="status-badges-and-border-coloring"></a>Notifiche di stato e colorazione del bordo
Nella parte inferiore di ogni server nella mappa potrebbe essere presente un elenco di notifiche di stato con informazioni relative allo stato del server.  Le notifiche indicano che sono presenti alcune informazioni rilevanti per il server da una delle integrazioni della soluzione OMS.  Facendo clic su una notifica, vengono visualizzati direttamente i dettagli dello stato nel pannello di destra.  Le notifiche di stato attualmente disponibili includono Avvisi, Modifiche, Sicurezza e Aggiornamenti.

In base alla gravità della notifica di stato, i bordi del nodo del computer possono essere colorati di rosso (critico), giallo (avviso) o blu (informativi).  Il colore rappresenta lo stato di gravità di una notifica di stato.  Un bordo grigio indica un nodo senza indicatori di stato correnti.

![Notifiche di stato](media/oms-service-map/status-badges.png)

## <a name="role-icons"></a>Icone di ruolo
Alcuni processi svolgono ruoli particolari nei computer: server web, server applicazioni, database e così via.  Mapping dei servizi annota le caselle relative a processi e computer con icone di ruolo, per consentire di identificare rapidamente il ruolo di un processo o un server.

| Icona del ruolo | Descrizione |
|:--|:--|
| ![Server Web](media/oms-service-map/role-web-server.png) | Server web |
| ![Server app](media/oms-service-map/role-application-server.png) | Server applicazioni |
| ![Server di database](media/oms-service-map/role-database.png) | Server di database |
| ![Server LDAP](media/oms-service-map/role-ldap.png) | Server LDAP |
| ![Server SMB](media/oms-service-map/role-smb.png) | Server SMB |

![Icone di ruolo](media/oms-service-map/role-icons.png)


## <a name="failed-connections"></a>Connessioni non riuscite
Le connessioni a processi e computer non riuscite sono visualizzate nelle mappe di Mapping dei servizi con una linea rossa tratteggiata, che indica se un sistema client non riesce a raggiungere un processo o una porta.  Vengono visualizzate le connessioni non riuscite di qualsiasi sistema con un agente di Mapping dei servizi distribuito, a condizione che il sistema corrisponda a quello che esegue la connessione non riuscita.  Mapping dei servizi esegue questo calcolo osservando i socket TCP che non riescono a stabilire una connessione.  Ciò potrebbe essere causato da un firewall, da un errore di configurazione del client o server oppure da un servizio remoto non disponibile.

![Connessioni non riuscite](media/oms-service-map/failed-connections.png)

Le informazioni sulle connessioni non riuscite facilitano la risoluzione dei problemi, la convalida della migrazione, l'analisi di sicurezza e la comprensione dell'intera architettura.  A volte le connessioni non riuscite non offrono informazioni utili, spesso tuttavia indicano direttamente un problema, ad esempio un ambiente di failover che diventa improvvisamente non raggiungibile oppure due livelli applicazione che non comunicano dopo una migrazione cloud.

## <a name="client-groups"></a>Gruppi di client
I gruppi di client sono costituiti da caselle sulla mappa che rappresentano i computer client privi di agenti di dipendenza.  Un singolo gruppo di client rappresenta i client per un unico processo.

![Gruppi di client](media/oms-service-map/client-groups.png)

Per visualizzare gli indirizzi IP dei server in un gruppo di client, selezionare il gruppo.  Il contenuto del gruppo è elencato nel pannello Proprietà.

![Proprietà del gruppo di client](media/oms-service-map/client-group-properties.png)

## <a name="server-port-groups"></a>Gruppi di porte di server
I gruppi di porte di server sono costituiti da caselle che rappresentano le porte sui server privi di agenti di dipendenza.  La casella elenca la porta del server con il numero di server con connessioni a tale porta.  Espandere la casella per visualizzare i singoli server e le connessioni.  Se nella casella c'è un solo server, viene indicato il relativo nome o indirizzo IP.

![Gruppi di porte di server](media/oms-service-map/server-port-groups.png)

## <a name="context-menu"></a>Menu di scelta rapida
Facendo clic sui tre punti in alto a destra di un server, verrà visualizzato il menu di scelta rapida per quel server.

![Connessioni non riuscite](media/oms-service-map/context-menu.png)

### <a name="load-server-map"></a>Carica mappa del server
Carica mappa del server consente di passare a una nuova mappa con il server selezionato come nuovo computer attivo.

### <a name="showhide-self-links"></a>Mostra collegamenti automatici/Nascondi collegamenti automatici
Mostra collegamenti automatici ridisegna il nodo del server, inclusi eventuali collegamenti automatici, ovvero connessioni TCP che si avviano e terminano con i processi nel server.  Se i collegamenti automatici sono visualizzati, il menu cambia in Nascondi collegamenti automatici, per consentire agli utenti di attivare/disattivare il disegno dei collegamenti automatici.

## <a name="computer-summary"></a>Riepilogo del computer
Il pannello con il riepilogo del computer include una panoramica del sistema operativo del server e del numero di dipendenze, insieme a dati di altre soluzioni OMS, tra cui quelle relative a metriche delle prestazioni, rilevamento delle modifiche, sicurezza, aggiornamenti e così via.

![Riepilogo del computer](media/oms-service-map/machine-summary.png)

## <a name="computer-and-process-properties"></a>Proprietà dei computer e dei processi
In una mappa di Mapping dei servizi è possibile selezionare computer e processi per visualizzare ulteriori informazioni sulle loro proprietà.  I computer visualizzano informazioni sul nome DNS, gli indirizzi IPv4, la capacità di CPU e di memoria, il tipo di macchina virtuale, la versione del sistema operativo, l'ora dell'ultimo riavvio e gli ID degli agenti OMS e di Mapping dei servizi.

![Proprietà del computer](media/oms-service-map/machine-properties.png)

I dettagli dei processi vengono raccolti dai metadati del sistema operativo relativi ai processi in esecuzione che includono nome e descrizione del processo, nome utente e dominio (in Windows), nome dell'azienda, nome e versione del prodotto, directory di lavoro, riga dei comandi e ora di inizio del processo.

![Proprietà del processo](media/oms-service-map/process-properties.png)

Il pannello di riepilogo del processo visualizza informazioni aggiuntive sulla connettività del processo, incluse le porte, le connessioni in entrata e in uscita e le connessioni non riuscite.

![Riepilogo del processo](media/oms-service-map/process-summary.png)

## <a name="oms-alerts-integration"></a>Integrazione degli avvisi OMS
Gli avvisi OMS integrati in Mapping dei servizii consentono di visualizzare gli avvisi attivati per un determinato server nell'intervallo di tempo selezionato.  Se ci sono avvisi correnti, viene visualizzata un'icona per il server e nel pannello degli avvisi del computer vengono elencati gli avvisi

![Pannello degli avvisi del computer](media/oms-service-map/machine-alerts.png)

Tenere presente che, per poter visualizzare gli avvisi rilevanti in Mapping dei servizi, è necessario creare una regola specifica che si attiva per il computer interessato.  Per creare gli avvisi appropriati:
- Includere una clausola che consenta di raggruppare i computer: "per intervallo computer 1 minuto"
- Scegliere di impostare un avviso in base alle metriche misurate

![Configurazione degli avvisi](media/oms-service-map/alert-configuration.png)


## <a name="oms-log-events-integration"></a>Integrazione eventi log OMS
Mapping dei servizi si integra con ricerca di log per visualizzare un conteggio di tutti gli eventi di log disponibili per il server selezionato durante l'intervallo di tempo selezionato.  È possibile fare clic su una riga qualsiasi nell'elenco di conteggi degli eventi per passare alla ricerca log e visualizzare i singoli eventi di log.

![Eventi log](media/oms-service-map/log-events.png)

## <a name="oms-service-desk-integration"></a>Integrazione di OMS Service Desk
L'integrazione di Mapping dei servizi con il connettore di gestione dei servizi IT è automatica quando entrambe le soluzioni sono abilitate e configurate nell'area di lavoro di OMS.  L'integrazione in Mapping dei servizi è indicata come "Service Desk".  [Altre informazioni su come abilitare e configurare il connettore ITSM.](https://docs.microsoft.com/azure/log-analytics/log-analytics-itsmc-overview)

Il pannello Service Desk mostra un elenco di tutti gli eventi di gestione dei servizi IT per il server selezionato nell'intervallo di tempo selezionato.  Se ci sono elementi correnti, viene visualizzata un'icona per il server e il pannello Service Desk elenca gli elementi.
![Pannello Service Desk](media/oms-service-map/service-desk.png)

Fare clic su "View Work Item" (Visualizza elemento di lavoro) per aprire l'elemento nella soluzione ITSM connessa.

Fare clic su "Mostra in Ricerca log" per visualizzare i dettagli dell'elemento nella ricerca log.


## <a name="oms-change-tracking-integration"></a>Integrazione con Rilevamento modifiche di OMS
L'integrazione del rilevamento delle modifiche in Mapping dei servizi è automatica quando entrambe le soluzioni sono abilitate e configurate nell'area di lavoro OMS.

Il pannello di rilevamento modifiche del computer visualizza un elenco di tutte le modifiche a partire dalla più recente e un collegamento per eseguire una ricerca nel log per ulteriori dettagli.
![Pannello di rilevamento modifiche del computer](media/oms-service-map/change-tracking.png)

Di seguito è riportata una visualizzazione drill-down dell'evento di modifica configurazione dopo aver selezionato **Show in Log Analytics** (Mostra in Log Analytics).
![Evento di modifica configurazione](media/oms-service-map/configuration-change-event.png)


## <a name="oms-performance-integration"></a>Integrazione delle prestazioni OMS
Il pannello relativo alle prestazioni del computer mostra le metriche di prestazioni standard relative al server selezionato.  Le metriche includono l'uso della CPU, l'uso della memoria, i byte di rete inviati e ricevuti e un elenco dei processi principali per byte di rete inviati e ricevuti.  Si noti che per ottenere i dati sulle prestazioni di rete è necessario anche aver abilitato la soluzione Wire Data 2.0 in OMS.
![Pannello di rilevamento modifiche del computer](media/oms-service-map/machine-performance.png)


## <a name="oms-security-integration"></a>Integrazione della sicurezza OMS
L'integrazione di sicurezza e controllo in Mapping dei servizi è automatica quando entrambe le soluzioni sono abilitate e configurate nell'area di lavoro OMS.

Il pannello relativo alla sicurezza del computer mostra i dati provenienti dalla soluzione di sicurezza e controllo di OMS relativi al server selezionato.  Il pannello elenca un riepilogo dei problemi di sicurezza del server non risolti durante l'intervallo di tempo selezionato.  Facendo clic su uno dei problemi di sicurezza, verrà eseguito il drill-down in una ricerca log per ottenere i dettagli sui problemi di sicurezza.
![Pannello di rilevamento modifiche del computer](media/oms-service-map/machine-security.png)


## <a name="oms-updates-integration"></a>Integrazione degli aggiornamenti OMS
La gestione degli aggiornamenti di Mapping dei servizi è automatica quando entrambe le soluzioni sono abilitate e configurate nell'area di lavoro OMS.

Il pannello relativo agli aggiornamenti del computer mostra i dati provenienti dalla soluzione di gestione degli aggiornamenti di OMS relativi al server selezionato.  Il pannello elenca un riepilogo degli aggiornamenti mancanti per il server durante l'intervallo di tempo selezionato.
![Pannello di rilevamento modifiche del computer](media/oms-service-map/machine-updates.png)

## <a name="log-analytics-records"></a>Record di Log Analytics
I dati di inventario di computer e processi di Mapping dei servizi sono disponibili per la [ricerca](../log-analytics/log-analytics-log-searches.md) in Log Analytics.  Questi dati possono essere applicati a diversi scenari, tra cui la pianificazione della migrazione, l'analisi della capacità, l'individuazione e la risoluzione dei problemi di prestazioni on demand.

Ogni ora viene generato un record per ogni computer e processo univoco che si aggiunge ai record generati quando un processo o computer viene avviato o caricato in Mapping dei servizi.  I record hanno le proprietà descritte nelle tabelle seguenti.  I campi e i valori negli eventi ServiceMapComputer_CL eseguono il mapping ai campi della risorsa del computer nell'API ServiceMap ARM.  I campi e i valori negli eventi ServiceMapProcess_CL eseguono il mapping ai campi della risorsa del processo nell'API ServiceMap ARM.  Il campo ResourceName_s coincide con il campo del nome nella risorsa ARM corrispondente. Nota: con l'aumentare delle funzionalità di Mapping dei servizi, questi campi sono soggetti a modifica.


Sono disponibili proprietà generate internamente che è possibile usare per identificare processi e computer univoci:

- Computer: utilizzare ResourceId o ResourceName_s per identificare in modo univoco un computer all'interno di un'area di lavoro di OMS.
- Processo: utilizzare ResourceId per identificare in modo univoco un computer all'interno di un'area di lavoro di OMS. ResourceName_s è univoco all'interno del contesto del computer in cui viene eseguito il processo (MachineResourceName_s) 

Poiché possono essere presenti vari record per un determinato processo o computer in un intervallo di tempo specificato, le query possono restituire più di un record per lo stesso computer o processo. Per includere solo il record più recente, aggiungere "| dedup ResourceId" alla query.

### <a name="servicemapcomputercl-records"></a>Record ServiceMapComputer_CL
I record che contengono il tipo **ServiceMapComputer_CL** includono dati di inventario relativi ai server con agenti del modello dei servizi.  Questi record includono le proprietà elencate nella tabella seguente:

| Proprietà | Descrizione |
|:--|:--|
| Tipo | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | identificatore univoco per il computer nell'area di lavoro |
| ResourceName_s | identificatore univoco per il computer nell'area di lavoro |
| ComputerName_s | FQDN del computer |
| Ipv4Addresses_s | un elenco degli indirizzi IPv4 del server |
| Ipv6Addresses_s | un elenco degli indirizzi IPv6 del server |
| DnsNames_s | matrice di nomi DNS |
| OperatingSystemFamily_s | Windows o Linux |
| OperatingSystemFullName_s | nome completo del sistema operativo  |
| Bitness_s | numero di bit del computer (32 bit) o (64 bit) |
| PhysicalMemory_d | memoria fisica in MB |
| Cpus_d | numero di CPU |
| CPUSpeed_d | velocità della CPU in MHz|
| VirtualizationState_s | "sconosciuto", "fisico", "virtuale", "hypervisor" |
| VirtualMachineType_s | "hyperv", "vmware" e così via |
| VirtualMachineNativeMachineId_g | ID VM assegnato dal relativo hypervisor |
| VirtualMachineName_s | Nome della VM. |
| BootTime_t | tempo di avvio |



### <a name="servicemapprocesscl-type-records"></a>Record con tipo ServiceMapProcess_CL
I record con tipo **ServiceMapProcess_CL** includono dati di inventario relativi ai processi con connessione TCP eseguiti sui server con agenti del modello dei servizi.  Questi record includono le proprietà elencate nella tabella seguente:

| Proprietà | Descrizione |
|:--|:--|
| Tipo | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | identificatore univoco per il processo nell'area di lavoro |
| ResourceName_s | identificatore univoco per il processo nel computer in cui è in esecuzione|
| MachineResourceName_s | nome della risorsa del computer |
| ExecutableName_s | nome dell'eseguibile del processo |
| StartTime_t | ora di inizio del pool del processo |
| FirstPid_d | primo pid nel pool del processo |
| Description_s | descrizione del processo |
| CompanyName_s | nome della società |
| InternalName_s | nome interno |
| ProductName_s | nome del prodotto |
| ProductVersion_s | versione del prodotto |
| FileVersion_s | versione del file |
| CommandLine_s | riga di comando |
| ExecutablePath _s | percorso del file eseguibile |
| WorkingDirectory_s | directory di lavoro |
| UserName | account con cui è in esecuzione il processo |
| UserDomain | dominio in cui è in esecuzione il processo |


## <a name="sample-log-searches"></a>Ricerche di log di esempio

### <a name="list-all-known-machines"></a>Visualizzare tutti i computer noti
Type=ServiceMapComputer_CL | dedup ResourceId

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Visualizzare la capacità di memoria fisica di tutti i computer gestiti.
Type=ServiceMapComputer_CL | select PhysicalMemory_d, ComputerName_s | Dedup ResourceId

### <a name="list-computer-name-dns-ip-and-os"></a>Visualizzare nome del computer, DNS, IP e sistema operativo.
Type=ServiceMapComputer_CL | select ComputerName_s, OperatingSystemFullName_s, DnsNames_s, IPv4Addresses_s  | dedup ResourceId

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Trovare tutti i processi con "sql" nella riga di comando
Type=ServiceMapProcess_CL CommandLine_s = \*sql\* | dedup ResourceId

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Trovare un computer (record più recente) in base al nome di risorsa
Type=ServiceMapComputer_CL "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | dedup ResourceId

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Trovare un computer (record più recente) in base all’indirizzo IP
Type=ServiceMapComputer_CL "10.229.243.232" | dedup ResourceId

### <a name="list-all-known-processes-on-a-given-machine"></a>Visualizzare tutti i processi noti su un computer specifico
Type=ServiceMapProcess_CL MachineResourceName_s="m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | dedup ResourceId

### <a name="list-all-computers-running-sql"></a>Visualizzare tutti i computer che eseguono SQL
Type=ServiceMapComputer_CL ResourceName_s IN {Type=ServiceMapProcess_CL \*sql\* | Distinct MachineResourceName_s} | dedup ResourceId | Distinct ComputerName_s

### <a name="list-of-all-unique-product-versions-of-curl-in-my-datacenter"></a>Visualizzare tutte le versioni di prodotto univoche di curl nel data center
Type=ServiceMapProcess_CL ExecutableName_s=curl | Distinct ProductVersion_s

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Creare un gruppo di tutti i computer che eseguono CentOS
Type=ServiceMapComputer_CL OperatingSystemFullName_s = \*CentOS\* | Distinct ComputerName_s


## <a name="rest-api"></a>API REST
Tutti i dati relativi a server, processi e dipendenze in Mapping dei servizi sono disponibili tramite l'[API REST di Mapping dei servizi](https://docs.microsoft.com/rest/api/servicemap/).


## <a name="diagnostic-and-usage-data"></a>Dati di diagnostica e di utilizzo
Microsoft raccoglie automaticamente i dati di utilizzo e prestazioni tramite l'uso del servizio Mapping dei servizi da parte dell'utente. Microsoft usa questi dati per fornire e migliorare la qualità, la sicurezza e l'integrità del servizio Mapping dei servizi. I dati includono informazioni sulla configurazione del software, come il sistema operativo e la versione, e comprendono anche l'indirizzo IP, il nome DNS e il nome della workstation, al fine di fornire capacità di risoluzione dei problemi accurate ed efficienti. Non vengono raccolti nomi, indirizzi o altre informazioni di contatto.

Per altre informazioni sulla raccolta dei dati e sull'utilizzo, vedere l'[Informativa sulla privacy di Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).


## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [ricerche nei log](../log-analytics/log-analytics-log-searches.md) in Log Analytics per recuperare i dati raccolti da Mapping dei servizi.


## <a name="troubleshooting"></a>Risoluzione dei problemi
- Vedere la [sezione Risoluzione dei problemi del documento relativo alla configurazione di Mapping dei servizi](operations-management-suite-service-map-configure.md#troubleshooting).


## <a name="feedback"></a>Commenti e suggerimenti
Per inviare commenti su Mapping dei servizi e sulla relativa documentazione,  visitare la [pagina per i suggerimenti degli utenti](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), in cui è possibile suggerire funzionalità o votare i suggerimenti esistenti.

