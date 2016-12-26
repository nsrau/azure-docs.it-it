---
title: Elenco dei servizi in Operations Management Suite (OMS) | Microsoft Docs
description: "L&quot;elenco dei servizi è una soluzione di Operations Management Suite (OMS) che individua automaticamente i componenti delle applicazioni nei sistemi Windows e Linux e mappa la comunicazione fra i servizi.  Questo articolo fornisce informazioni dettagliate su come distribuire l&quot;elenco dei servizi nell&quot;ambiente e su come usarlo in svariati scenari."
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
ms.date: 11/18/2016
ms.author: daseidma;bwren;dairwin
translationtype: Human Translation
ms.sourcegitcommit: cbed591d15daf8060f13d0e9b009d65c85d256aa
ms.openlocfilehash: c5cd77dc53c36c4ad6c41eb876e24b4077d2007c


---

# <a name="using-service-map-solution-in-operations-management-suite-oms"></a>Usare la soluzione di elenco dei servizi in Operations Management Suite (OMS)
![Icona di Alert Management](media/oms-service-map/icon.png) L'elenco dei servizi individua automaticamente i componenti delle applicazioni nei sistemi Windows e Linux ed esegue la mappatura della comunicazione fra i servizi. Consente di visualizzare i server nel modo in cui si pensa a essi, ovvero come sistemi interconnessi che forniscono servizi critici.  L'elenco dei servizi mostra le connessioni fra i server, i processi e le porte di tutte le architetture connesse via TCP senza il bisogno di alcuna configurazione a parte l'installazione di un agente.

Questo articolo fornisce i dettagli su come usare l'elenco dei servizi.  Per informazioni sulla configurazione dell'elenco dei servizi e sugli agenti di caricamento, vedere [Configuring Service Map solution in Operations Management Suite (OMS)](operations-management-suite-service-map-configure.md) (Configurare la soluzione di elenco dei servizi in Operations Management Suite (OMS))


## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Casi di utilizzo: Riconoscimento delle dipendenze nei processi IT

### <a name="discovery"></a>Individuazione
L'elenco dei servizi genera automaticamente una mappa di riferimento delle dipendenze tra server, processi e servizi di terze parti.  La soluzione rileva e mappa tutte le dipendenze TCP, identificando le connessioni impreviste, i sistemi remoti di terze parti da cui si è dipendenti e le dipendenze da aree tradizionali non note della rete, ad esempio DNS e AD.  L'elenco dei servizi rileva i tentativi di connessione di rete non riusciti effettuati dai sistemi gestiti permettendo di identificare potenziali configurazioni del server errate, interruzioni dei servizi e problemi di rete.

### <a name="incident-management"></a>Gestione dell'evento imprevisto
L'elenco dei servizi consente di eliminare i tentativi di isolamento del problema visualizzando le connessioni tra i sistemi e la loro influenza reciproca.  In aggiunta alle informazioni sulle connessioni non riuscite, le informazioni sui client connessi permettono di identificare i bilanciamenti del carico errati, il carico imprevisto o eccessivo sui servizi critici e i client non autorizzati, ad esempio i computer per lo sviluppo in comunicazione con i sistemi di produzione.  I flussi di lavoro integrati in Rilevamento modifiche di OMS consentono inoltre di verificare se un evento di modifica in un computer o servizio back-end descrive la causa principale di un evento imprevisto.

### <a name="migration-assurance"></a>Garanzia di migrazione
L'elenco dei servizi consente di pianificare in modo efficace, accelerare e convalidare le migrazioni di Azure, garantendo che non venga tralasciato nulla e non siano presenti interruzioni impreviste.  È possibile individuare tutti i sistemi interdipendenti di cui è necessario eseguire la migrazione insieme, valutare la configurazione e la capacità del sistema e determinare se un sistema in esecuzione è ancora utile agli utenti oppure è un candidato alla rimozione delle autorizzazioni anziché alla migrazione.  Dopo aver eseguito lo spostamento, è possibile verificare il caricamento e l'identità dei client per assicurarsi che i sistemi di test e i clienti siano connessi.  Se sono presenti problemi nella pianificazione delle subnet e nelle definizioni dei firewall, le connessioni non riuscite nelle mappe dell'elenco dei servizi indicheranno i sistemi che necessitano di connettività.

### <a name="business-continuity"></a>Continuità aziendale
Se si usa Azure Site Recovery e si necessita di aiuto per definire la sequenza di ripristino dell'ambiente delle applicazioni, l'elenco dei servizi visualizza automaticamente le dipendenze reciproche tra i sistemi, consentendo di verificare l'affidabilità del proprio piano di ripristino.  Scegliendo un server critico e visualizzandone i client è possibile identificare i sistemi front-end che devono essere ripristinati soltanto dopo che il server critico è stato ripristinato ed è disponibile.  Allo stesso modo, osservando le dipendenze back-end di un server critico è possibile identificare i sistemi che devono essere ripristinati prima del sistema critico.

### <a name="patch-management"></a>Gestione delle patch
L'elenco dei servizi ottimizza l'uso di System Update Assessment di OMS mostrando gli altri team e server che dipendono dal proprio servizio consentendo di inviare loro una notifica prima di arrestare i sistemi per l'applicazione di patch.  L'elenco dei servizi migliora anche la gestione delle patch in OMS indicando se i servizi sono disponibili e connessi correttamente dopo l'applicazione delle patch e il riavvio.


## <a name="mapping-overview"></a>Panoramica sui mapping
Gli agenti dell'elenco dei servizi raccolgono informazioni su tutti i processi connessi tramite TCP presenti sul server in cui sono installati e dettagli sulle connessioni in entrata e in uscita di ogni processo.  Usando l'elenco di computer a sinistra della soluzione relativa all'elenco dei servizi è possibile selezionare i computer con agenti dell'elenco dei servizi per visualizzarne le dipendenze in un determinato intervallo di tempo.  Le mappe delle dipendenze dei computer sono specifiche di un computer e visualizzano tutti i computer che sono client TCP diretti o server del computer specifico.

![Panoramica dell'elenco dei servizi](media/oms-service-map/service-map-overview.png)

È possibile espandere i computer nella mappa per visualizzare i processi in esecuzione con connessioni di rete attive nell'intervallo di tempo selezionato.  Quando si espande un computer remoto con un agente dell'elenco dei servizi per visualizzare i dettagli dei processi, vengono visualizzati solo i processi che comunicano con il computer.  Il numero di computer front-end senza agenti che si connettono al computer è indicato a sinistra dei processi a cui si connettono.  Se il computer si connette a un computer back-end senza un agente, nella mappa il computer back-end è rappresentato da un nodo espandibile da cui è possibile visualizzare le singole porte e i servizi che comunicano con il computer.

Per impostazione predefinita, le mappe dell'elenco dei servizi mostrano le informazioni sulle dipendenze degli ultimi 10 minuti.  Usando i controlli di tempo in alto a sinistra, è possibile cercare nelle mappe intervalli di tempo cronologici della durata massima di un'ora per visualizzare l'aspetto delle dipendenze nel passato, ad esempio durante un evento imprevisto o prima di una modifica.    I dati dell'elenco dei servizi vengono archiviati per 30 giorni nelle aree di lavoro a pagamento e per 7 giorni nelle aree di lavoro gratuite.

![Mappa di computer con le proprietà del computer selezionato](media/oms-service-map/machine-map.png)

## <a name="failed-connections"></a>Connessioni non riuscite
Le connessioni a processi e computer non riuscite sono visualizzate nelle mappe dell'elenco dei servizi con una linea rossa tratteggiata, che indica se un sistema client non riesce a raggiungere un processo o una porta.  Vengono visualizzate le connessioni non riuscite di qualsiasi sistema con un agente dell'elenco dei servizi distribuito, a condizione che il sistema corrisponda a quello che esegue la connessione non riuscita.  L'elenco dei servizi esegue questo calcolo osservando i socket TCP che non riescono a stabilire una connessione.  Ciò potrebbe essere causato da un firewall, da un errore di configurazione del client o server oppure da un servizio remoto non disponibile.

![Connessioni non riuscite](media/oms-service-map/failed-connections.png)

Le informazioni sulle connessioni non riuscite facilitano la risoluzione dei problemi, la convalida della migrazione, l'analisi di sicurezza e la comprensione dell'intera architettura.  A volte le connessioni non riuscite non offrono informazioni utili, spesso tuttavia indicano direttamente un problema, ad esempio un ambiente di failover che diventa improvvisamente non raggiungibile oppure due livelli applicazione che non comunicano dopo una migrazione cloud.

## <a name="computer-and-process-properties"></a>Proprietà dei computer e dei processi
In una mappa dell'elenco dei servizi è possibile selezionare computer e processi per visualizzare ulteriori informazioni sulle loro proprietà.  I computer visualizzano informazioni sul nome DNS, gli indirizzi IPv4, la capacità di CPU e di memoria, il tipo di macchina virtuale, la versione del sistema operativo, l'ora dell'ultimo riavvio e gli ID degli agenti OMS e dell'elenco dei servizi.

![Proprietà del computer](media/oms-service-map/machine-properties.png)

I dettagli dei processi vengono raccolti dai metadati del sistema operativo relativi ai processi in esecuzione che includono nome e descrizione del processo, nome utente e dominio (in Windows), nome dell'azienda, nome e versione del prodotto, directory di lavoro, riga dei comandi e ora di inizio del processo.

![Proprietà del processo](media/oms-service-map/process-properties.png)

Il pannello di riepilogo del processo visualizza informazioni aggiuntive sulla connettività del processo, incluse le porte, le connessioni in entrata e in uscita e le connessioni non riuscite.

![Riepilogo del processo](media/oms-service-map/process-summary.png)

## <a name="computer-summary"></a>Riepilogo del computer
Il riquadro con il riepilogo del computer include una panoramica del sistema operativo del server e il numero di dipendenza, insieme a una serie di dati provenienti da altre soluzioni OMS, tra cui metriche delle prestazioni, rilevamento delle modifiche, sicurezza, aggiornamenti e così via.

![Riepilogo del computer](media/oms-service-map/machine-summary.png)

## <a name="oms-change-tracking-integration"></a>Integrazione con Rilevamento modifiche di OMS
L'integrazione del rilevamento delle modifiche nell'elenco dei servizi è automatica quando entrambe le soluzioni sono abilitate e configurate nell'area di lavoro OMS.

Il pannello di rilevamento modifiche del computer visualizza un elenco di tutte le modifiche a partire dalla più recente e un collegamento per eseguire una ricerca nel log per ulteriori dettagli.
![Pannello di rilevamento modifiche del computer](media/oms-service-map/change-tracking.png)

Di seguito è riportata una visualizzazione drill-down dell'evento di modifica configurazione dopo aver selezionato **Show in Log Analytics** (Mostra in Log Analytics).
![Evento di modifica configurazione](media/oms-service-map/configuration-change-event.png)


## <a name="oms-performance-integration"></a>Integrazione delle prestazioni OMS
Il pannello relativo alle prestazioni del computer mostra le metriche di prestazioni standard relative al server selezionato.  Le metriche includono l'uso della CPU, l'uso della memoria, i byte di rete inviati e ricevuti e un elenco dei processi principali per byte di rete inviati e ricevuti.
![Pannello di rilevamento modifiche del computer](media/oms-service-map/machine-performance.png)


## <a name="oms-security-integration"></a>Integrazione della sicurezza OMS
L'integrazione di sicurezza e controllo nell'elenco dei servizi è automatica quando entrambe le soluzioni sono abilitate e configurate nell'area di lavoro OMS.

Il pannello relativo alla sicurezza del computer mostra i dati provenienti dalla soluzione di sicurezza e controllo di OMS relativi al server selezionato.  Nel pannello sarà incluso un riepilogo dei problemi di sicurezza del server in sospeso durante l'intervallo di tempo selezionato.  Facendo clic su uno dei problemi di sicurezza, nel log verranno ricercate le relative informazioni.
![Pannello di rilevamento modifiche del computer](media/oms-service-map/machine-security.png)


## <a name="oms-updates-integration"></a>Integrazione degli aggiornamenti OMS
La gestione degli aggiornamenti dell'elenco dei servizi è automatica quando entrambe le soluzioni sono abilitate e configurate nell'area di lavoro OMS.

Il pannello relativo agli aggiornamenti del computer mostra i dati provenienti dalla soluzione di gestione degli aggiornamenti di OMS relativi al server selezionato.  Nel pannello sarà incluso un riepilogo degli aggiornamenti mancanti durante l'intervallo di tempo selezionato.
![Pannello di rilevamento modifiche del computer](media/oms-service-map/machine-updates.png)


## <a name="oms-alert-integration"></a>Integrazione degli avvisi OMS
Gli avvisi OMS integrati nell'elenco dei servizi consentono di visualizzare gli avvisi attivati per un determinato server nell'intervallo di tempo selezionato.  Se sono presenti avvisi correnti, sul server verrà visualizzata un'icona e nel pannello degli avvisi del computer verranno elencati gli avvisi in questione.

![Pannello degli avvisi del computer](media/oms-service-map/machine-alerts.png)

Tenere presente che, per poter visualizzare gli avvisi rilevanti nell'elenco dei servizi, è necessario creare una regola specifica che si attiva per il computer interessato.  Per creare gli avvisi appropriati:
- Includere una clausola che consenta di raggruppare i computer: "per intervallo computer 1 minuto"
- Scegliere di impostare un avviso in base alle metriche misurate

![Configurazione degli avvisi](media/oms-service-map/alert-configuration.png)


## <a name="log-analytics-records"></a>Record di Log Analytics
I dati di inventario di computer e processi dell'elenco dei servizi sono disponibili per la [ricerca](../log-analytics/log-analytics-log-searches.md) in Log Analytics.  Ciò può essere applicato a scenari che includono la pianificazione della migrazione, l'analisi della capacità, l'individuazione e la risoluzione personalizzata dei problemi di prestazioni.

Ogni ora viene generato un record per ogni computer e processo univoco che si aggiunge ai record generati quando il processo o il computer viene avviato o caricato nell'elenco dei servizi.  I record hanno le proprietà descritte nelle tabelle seguenti.

Sono disponibili proprietà generate internamente che è possibile usare per identificare processi e computer univoci:

- PersistentKey_s viene definita in modo univoco dalla configurazione del processo, ad esempio dalla riga di comando e dall'ID utente.  Si tratta di una proprietà univoca di un computer specifico ma può essere ripetuta in più computer.
- ProcessId_s e ComputerId_s sono proprietà univoche globali nel modello dell'elenco dei servizi.



### <a name="servicemapcomputercl-records"></a>Record ServiceMapComputer_CL
I record che contengono il tipo **ServiceMapComputer_CL** includono dati di inventario relativi ai server con agenti del modello dei servizi.  Questi record includono le proprietà elencate nella tabella seguente:

| Proprietà | Descrizione |
|:--|:--|
| Tipo | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ComputerName_s | Nome del computer Windows o Linux |
| CPUSpeed_d | Velocità della CPU in MHz |
| DnsNames_s | Elenco di tutti i nomi DNS per il computer |
| IPv4s_s | Elenco di tutti gli indirizzi IPv4 usati dal computer |
| IPv6s_s | Elenco di tutti gli indirizzi IPv6 usati dal computer.  (L'elenco dei servizi individua gli indirizzi IPv6, ma non le dipendenze.) |
| Is64Bit_b | true o false in base al tipo di sistema operativo |
| MachineId_s | GUID interno univoco in un'area di lavoro OMS  |
| OperatingSystemFamily_s | Windows o Linux |
| OperatingSystemVersion_s | Stringa di versione del sistema operativo lunga |
| TimeGenerated | Data e ora di creazione del record. |
| TotalCPUs_d | Numero di core CPU |
| TotalPhysicalMemory_d | Capacità di memoria in MB |
| VirtualMachine_b | true o false se il sistema operativo è guest della macchina virtuale o meno |
| VirtualMachineID_g | ID della macchina virtuale Hyper-V |
| VirtualMachineName_g | Nome della macchina virtuale Hyper-V |
| VirtualMachineType_s | Hyperv, Vmware, Xen, Kvm, Ldom, Lpar, Virtualpc |


### <a name="servicemapprocesscl-type-records"></a>Record con tipo ServiceMapProcess_CL
I record con tipo **ServiceMapProcess_CL** includono dati di inventario relativi ai processi con connessione TCP eseguiti sui server con agenti del modello dei servizi.  Questi record includono le proprietà elencate nella tabella seguente:

| Proprietà | Descrizione |
|:--|:--|
| Tipo | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| CommandLine_s | Riga di comando completa del processo |
| CompanyName_s | Nome della società (da Windows PE o Linux RPM) |
| Description_s | Descrizione del processo estesa (da Windows PE o Linux RPM) |
| FileVersion_s | Versione del file eseguibile (da Windows PE, solo Windows) |
| FirstPid_d | ID processo del sistema operativo |
| InternalName_s | Nome interno del file eseguibile (da Windows PE, solo Windows) |
| MachineId_s | GUID interno univoco in un'area di lavoro OMS  |
| Name_s | Nome dell'eseguibile del processo |
| Path_s | Percorso del file system dell'eseguibile del processo |
| PersistentKey_s | GUID interno univoco all'interno del computer |
| PoolId_d | ID interno per i processi di aggregazione basati su righe di comando simili. |
| ProcessId_s | GUID interno univoco in un'area di lavoro OMS  |
| ProductName_s | Stringa del nome del prodotto (da Windows PE o Linux RPM) |
| ProductVersion_s | Stringa della versione del prodotto (da Windows PE o Linux RPM) |
| StartTime_t | Ora di inizio del processo dell'orologio del computer locale |
| TimeGenerated | Data e ora di creazione del record. |
| UserDomain_s | Dominio del proprietario del processo (solo Windows) |
| UserName_s | Nome del proprietario del processo (solo Windows) |
| WorkingDirectory_s | Directory di lavoro del processo |


## <a name="sample-log-searches"></a>Ricerche di log di esempio

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Visualizzare la capacità di memoria fisica di tutti i computer gestiti.
Type=ServiceMapComputer_CL | select TotalPhysicalMemory_d, ComputerName_s | Dedup ComputerName_s

### <a name="list-computer-name-dns-ip-and-os-version"></a>Visualizzare nome del computer, DNS, IP e versione del sistema operativo.
Type=ServiceMapComputer_CL | select ComputerName_s, OperatingSystemVersion_s, DnsNames_s, IPv4s_s  | dedup ComputerName_s

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Trovare tutti i processi con "sql" nella riga di comando
Type=ServiceMapProcess_CL CommandLine_s = \*sql\* | dedup ProcessId_s

### <a name="after-viewing-event-data-for-given-process-use-its-machine-id-to-retrieve-the-computers-name"></a>Dopo aver visualizzato i dati degli eventi per un processo specifico, usare l'ID computer per recuperare il nome del computer
Type=ServiceMapComputer_CL "m!m-9bb187fa-e522-5f73-66d2-211164dc4e2b" | Distinct ComputerName_s

### <a name="list-all-computers-running-sql"></a>Visualizzare tutti i computer che eseguono SQL
Type=ServiceMapComputer_CL MachineId_s IN {Type=ServiceMapProcess_CL \*sql\* | Distinct MachineId_s} | Distinct ComputerName_s

### <a name="list-of-all-unique-product-versions-of-curl-in-my-datacenter"></a>Visualizzare tutte le versioni di prodotto univoche di curl nel data center
Type=ServiceMapProcess_CL Name_s=curl | Distinct ProductVersion_s

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Creare un gruppo di tutti i computer che eseguono CentOS
Type=ServiceMapComputer_CL OperatingSystemVersion_s = "CentOS" | Distinct ComputerName_s



## <a name="diagnostic-and-usage-data"></a>Dati di diagnostica e di utilizzo
Microsoft raccoglie automaticamente i dati di utilizzo e prestazioni tramite l'uso del servizio relativo all'elenco dei servizi da parte dell'utente. Microsoft usa questi dati per fornire e migliorare la qualità, la sicurezza e l'integrità del servizio relativo all'elenco dei servizi. I dati includono informazioni sulla configurazione del software, come il sistema operativo e la versione, e comprendono anche l'indirizzo IP, il nome DNS e il nome della workstation, al fine di fornire capacità di risoluzione dei problemi accurate ed efficienti. Non vengono raccolti nomi, indirizzi o altre informazioni di contatto.

Per altre informazioni sulla raccolta dei dati e sull'utilizzo, vedere l'[Informativa sulla privacy di Microsoft Online Services](hhttps://go.microsoft.com/fwlink/?LinkId=512132).



## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [ricerche nei log](../log-analytics/log-analytics-log-searches.md) in Log Analytics per recuperare i dati raccolti dall'elenco dei servizi.



<!--HONumber=Nov16_HO4-->


