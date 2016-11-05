---
title: Application Dependency Monitor (ADM) in Operations Management Suite (OMS) | Microsoft Docs
description: Application Dependency Monitor (ADM) è una soluzione Operations Management Suite (OMS) che individua automaticamente i componenti delle applicazioni nei sistemi Windows e Linux e mappa la comunicazione fra i servizi.  Questo articolo fornisce informazioni dettagliate per la distribuzione di ADM nell'ambiente e il suo utilizzo in un'ampia gamma di scenari.
services: operations-management-suite
documentationcenter: ''
author: daseidma
manager: jwhit
editor: tysonn

ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2016
ms.author: daseidma;bwren

---
# <a name="using-application-dependency-monitor-solution-in-operations-management-suite-(oms)"></a>Uso della soluzione Application Dependency Monitor in Operations Management Suite (OMS)
![Icona di Alert Management](media/operations-management-suite-application-dependency-monitor/icon.png) Application Dependency Monitor (ADM) individua automaticamente i componenti delle applicazioni nei sistemi Windows e Linux e mappa la comunicazione fra i servizi. Consente di visualizzare i server nel modo in cui si pensa a essi, ovvero come sistemi interconnessi che forniscono servizi critici.  Application Dependency Monitor mostra le connessioni fra i server, i processi e le porte di tutte le architetture connesse via TCP senza il bisogno di alcuna configurazione a parte l'installazione di un agente.

Questo articolo offre informazioni dettagliate sull'uso di Application Dependency Monitor.  Per informazioni sulla configurazione di ADM e sugli agenti di caricamento, vedere [Configuring Application Dependency Monitor solution in Operations Management Suite (OMS)](operations-management-suite-application-dependency-monitor-configure.md) (Configurazione della soluzione Application Dependency Monitor in Operations Management Suite (OMS))

> [!NOTE]
> Application Dependency Monitor si trova attualmente in fase di anteprima privata.  È possibile richiedere l'accesso all'anteprima privata di ADM all'indirizzo [https://aka.ms/getadm](https://aka.ms/getadm).
> 
> Durante la fase di anteprima privata tutti gli account OMS hanno accesso illimitato ad ADM.  I nodi ADM sono gratuiti ma i dati di Log Analytics per i tipi AdmComputer_CL e AdmProcess_CL saranno misurati come in qualsiasi altra soluzione.
> 
> Quando ADM entra nello stato di anteprima pubblica, è disponibile solo per i clienti che usano Insight & Analytics gratuitamente o a pagamento nel piano tariffario di OMS.  Gli account di livello gratuito saranno limitati a 5 nodi ADM.  Se si partecipa all'anteprima privata e non si è registrati nel piano tariffario di OMS quando ADM entra nello stato di anteprima pubblica, ADM verrà disabilitato in quel momento. 
> 
> 

## <a name="use-cases:-make-your-it-processes-dependency-aware"></a>Casi di utilizzo: Riconoscimento delle dipendenze nei processi IT
### <a name="discovery"></a>Individuazione
ADM genera automaticamente una mappa di riferimento delle dipendenze tra server, processi e servizi di terze parti.  La soluzione rileva e mappa tutte le dipendenze TCP, identificando le connessioni impreviste, i sistemi remoti di terze parti da cui si è dipendenti e le dipendenze da aree tradizionali non note della rete, ad esempio DNS e AD.  ADM rileva i tentativi di connessione di rete non riusciti effettuati dai sistemi gestiti permettendo di identificare potenziali configurazioni del server errate, interruzioni dei servizi e problemi di rete.

### <a name="incident-management"></a>Gestione dell'evento imprevisto
ADM consente di eliminare i tentativi di isolamento del problema visualizzando le connessioni tra i sistemi e la loro influenza reciproca.  In aggiunta alle informazioni sulle connessioni non riuscite, le informazioni sui client connessi permettono di identificare i bilanciamenti del carico errati, il carico imprevisto o eccessivo sui servizi critici e i client non autorizzati, ad esempio i computer per lo sviluppo in comunicazione con i sistemi di produzione.  I flussi di lavoro integrati in Rilevamento modifiche di OMS consentono inoltre di verificare se un evento di modifica in un computer o servizio back-end descrive la causa principale di un evento imprevisto.

### <a name="migration-assurance"></a>Garanzia di migrazione
ADM consente di pianificare in modo efficace, accelerare e convalidare le migrazioni di Azure, garantendo che non venga tralasciato nulla e non siano presenti interruzioni impreviste.  È possibile individuare tutti i sistemi interdipendenti di cui è necessario eseguire la migrazione insieme, valutare la configurazione e la capacità del sistema e determinare se un sistema in esecuzione è ancora utile agli utenti oppure è un candidato alla rimozione delle autorizzazioni anziché alla migrazione.  Dopo aver eseguito lo spostamento, è possibile verificare il caricamento e l'identità dei client per assicurarsi che i sistemi di test e i clienti siano connessi.  Se sono presenti problemi nella pianificazione delle subnet e nelle definizioni dei firewall, le connessioni non riuscite nelle mappe di ADM indicheranno i sistemi che necessitano di connettività.

### <a name="business-continuity"></a>Continuità aziendale
Se si usa Azure Site Recovery e si necessita di aiuto per definire la sequenza di ripristino dell'ambiente delle applicazioni, ADM visualizza automaticamente la dipendenza reciproca tra i sistemi consentendo di verificare l'affidabilità del proprio piano di ripristino.  Scegliendo un server critico e visualizzandone i client è possibile identificare i sistemi front-end che devono essere ripristinati soltanto dopo che il server critico è stato ripristinato ed è disponibile.  Allo stesso modo, osservando le dipendenze back-end di un server critico è possibile identificare i sistemi che devono essere ripristinati prima del sistema critico.

### <a name="patch-management"></a>Gestione delle patch
ADM ottimizza l'uso di System Update Assessment di OMS mostrando gli altri team e server che dipendono dal proprio servizio consentendo di inviare loro una notifica prima di arrestare i sistemi per l'applicazione di patch.  ADM migliora anche la gestione delle patch in OMS indicando se i servizi sono disponibili e connessi correttamente dopo l'applicazione delle patch e il riavvio. 

## <a name="mapping-overview"></a>Panoramica sui mapping
Gli agenti ADM raccolgono informazioni su tutti i processi connessi tramite TCP presenti sul server in cui sono installati e dettagli sulle connessioni in entrata e in uscita di ogni processo.  Usando l'elenco di computer a sinistra della soluzione ADM è possibile selezionare i computer con agenti ADM per visualizzarne le dipendenze in un determinato intervallo di tempo.  Le mappe delle dipendenze dei computer sono specifiche di un computer e visualizzano tutti i computer che sono client TCP diretti o server del computer specifico.

![Panoramica di ADM](media/operations-management-suite-application-dependency-monitor/adm-overview.png)

È possibile espandere i computer nella mappa per visualizzare i processi in esecuzione con connessioni di rete attive nell'intervallo di tempo selezionato.  Quando un computer remoto con un agente ADM viene espanso per visualizzare i dettagli dei processi, vengono visualizzati solo i processi che comunicano con il computer.  Il numero di computer front-end senza agenti che si connettono al computer è indicato a sinistra dei processi a cui si connettono.  Se il computer si connette a un computer back-end senza un agente, il computer back-end è rappresentato nella mappa da un nodo che mostra l'indirizzo IPv4 e che può essere espanso per visualizzare le singole porte e i servizi che comunicano con il computer.

Per impostazione predefinita, le mappe ADM mostrano le informazioni sulle dipendenze degli ultimi 10 minuti.  Usando i controlli di tempo in alto a sinistra, è possibile cercare nelle mappe intervalli di tempo cronologici della durata massima di un'ora per visualizzare l'aspetto delle dipendenze nel passato, ad esempio durante un evento imprevisto o prima di una modifica.    I dati ADM vengono archiviati per 30 giorni nelle aree di lavoro a pagamento e per 7 giorni nelle aree di lavoro gratuite.

![Mappa di computer con le proprietà del computer selezionato](media/operations-management-suite-application-dependency-monitor/machine-map.png)

## <a name="failed-connections"></a>Connessioni non riuscite
Le connessioni non riuscite sono visualizzate nelle mappe ADM di processi e computer con una linea rossa tratteggiata che indica se un sistema client non riesce a raggiungere un processo o una porta.  Vengono visualizzate le connessioni non riuscite di qualsiasi sistema con un agente ADM distribuito, a condizione che il sistema corrisponda a quello che esegue la connessione non riuscita.  ADM esegue questo calcolo osservando i socket TCP che non riescono a stabilire una connessione.  Ciò potrebbe essere causato da un firewall, da un errore di configurazione del client o server oppure da un servizio remoto non disponibile. 

![Connessioni non riuscite](media/operations-management-suite-application-dependency-monitor/failed-connections.png)

Le informazioni sulle connessioni non riuscite facilitano la risoluzione dei problemi, la convalida della migrazione, l'analisi di sicurezza e la comprensione dell'intera architettura.  A volte le connessioni non riuscite non offrono informazioni utili, spesso tuttavia indicano direttamente un problema, ad esempio un ambiente di failover che diventa improvvisamente non raggiungibile oppure due livelli applicazione che non comunicano dopo una migrazione cloud.  Nell'immagine precedente, IIS e WebSphere sono entrambi in esecuzione ma non possono connettersi. 

## <a name="computer-and-process-properties"></a>Proprietà dei computer e dei processi
In una mappa ADM è possibile selezionare computer e processi per visualizzare ulteriori informazioni sulle loro proprietà.  I computer visualizzano informazioni sul nome DNS, gli indirizzi IPv4, la capacità di CPU e di memoria, il tipo di macchina virtuale, la versione del sistema operativo, l'ora dell'ultimo riavvio e gli ID degli agenti OMS e ADM.

I dettagli sui processi vengono raccolti dai metadati del sistema operativo relativi ai processi in esecuzione che includono nome e descrizione del processo, nome utente e dominio (in Windows), nome della società, nome e versione del prodotto, directory di lavoro, riga dei comandi e ora di inizio del processo.

![Proprietà del processo](media/operations-management-suite-application-dependency-monitor/process-properties.png)

Il pannello di riepilogo del processo visualizza informazioni aggiuntive sulla connettività del processo, incluse le porte, le connessioni in entrata e in uscita e le connessioni non riuscite. 

![Riepilogo del processo](media/operations-management-suite-application-dependency-monitor/process-summary.png)

## <a name="oms-change-tracking-integration"></a>Integrazione con Rilevamento modifiche di OMS
L'integrazione di ADM con Rilevamento modifiche è automatica quando entrambe le soluzioni vengono abilitate e configurate nell'area di lavoro OMS.

Il pannello di riepilogo del computer indica se si sono verificati eventi di rilevamento delle modifiche nel computer selezionato nell'intervallo di tempo selezionato.

![Pannello di riepilogo del computer](media/operations-management-suite-application-dependency-monitor/machine-summary.png)

Il pannello di rilevamento modifiche del computer visualizza un elenco di tutte le modifiche a partire dalla più recente e un collegamento per eseguire una ricerca nel log per ulteriori dettagli.
![Pannello di rilevamento modifiche del computer](media/operations-management-suite-application-dependency-monitor/machine-change-tracking.png)

Di seguito è riportata una visualizzazione drill-down dell'evento di modifica configurazione dopo aver selezionato **Mostra in Log Analytics**.
![Evento di modifica configurazione](media/operations-management-suite-application-dependency-monitor/configuration-change-event.png)

## <a name="log-analytics-records"></a>Record di Log Analytics
I dati di inventario di computer e processi di ADM sono disponibili per la [ricerca](../log-analytics/log-analytics-log-searches.md) in Log Analytics.  Ciò può essere applicato a scenari che includono la pianificazione della migrazione, l'analisi della capacità, l'individuazione e la risoluzione personalizzata dei problemi di prestazioni. 

Ogni ora viene generato un record per ogni computer e processo univoco che si aggiunge ai record generati quando il processo o il computer viene avviato o caricato in ADM.  I record hanno le proprietà descritte nelle tabelle seguenti. 

Sono disponibili proprietà generate internamente che è possibile usare per identificare processi e computer univoci:

* PersistentKey_s viene definita in modo univoco dalla configurazione del processo, ad esempio dalla riga di comando e dall'ID utente.  Si tratta di una proprietà univoca di un computer specifico ma può essere ripetuta in più computer.
* ProcessId_s e ComputerId_s sono proprietà univoche globali nel modello ADM.

### <a name="admcomputer_cl-records"></a>Record AdmComputer_CL
I record di tipo **AdmComputer_CL** includono i dati di inventario di server con agenti ADM.  Questi record includono le proprietà elencate nella tabella seguente.  

| Proprietà | Descrizione |
|:--- |:--- |
| Tipo |*AdmComputer_CL* |
| SourceSystem |*OpsManager* |
| ComputerName_s |Nome del computer Windows o Linux |
| CPUSpeed_d |Velocità della CPU in MHz |
| DnsNames_s |Elenco di tutti i nomi DNS per il computer |
| IPv4s_s |Elenco di tutti gli indirizzi IPv4 usati dal computer |
| IPv6s_s |Elenco di tutti gli indirizzi IPv6 usati dal computer.  (ADM identifica gli indirizzi IPv6 ma non individua le dipendenze di IPv6). |
| Is64Bit_b |true o false in base al tipo di sistema operativo |
| MachineId_s |GUID interno univoco in un'area di lavoro OMS |
| OperatingSystemFamily_s |Windows o Linux |
| OperatingSystemVersion_s |Stringa di versione del sistema operativo lunga |
| TimeGenerated |Data e ora di creazione del record. |
| TotalCPUs_d |Numero di core CPU |
| TotalPhysicalMemory_d |Capacità di memoria in MB |
| VirtualMachine_b |true o false se il sistema operativo è guest della macchina virtuale o meno |
| VirtualMachineID_g |ID della macchina virtuale Hyper-V |
| VirtualMachineName_g |Nome della macchina virtuale Hyper-V |
| VirtualMachineType_s |Hyperv, Vmware, Xen, Kvm, Ldom, Lpar, Virtualpc |

### <a name="admprocess_cl-type-records"></a>Record AdmProcess_CL
I record di tipo **AdmProcess_CL** includono i dati di inventario di processi con connessione TCP su server con agenti ADM.  Questi record includono le proprietà elencate nella tabella seguente.

| Proprietà | Descrizione |
|:--- |:--- |
| Tipo |*AdmProcess_CL* |
| SourceSystem |*OpsManager* |
| CommandLine_s |Riga di comando completa del processo |
| CompanyName_s |Nome della società (da Windows PE o Linux RPM) |
| Description_s |Descrizione del processo estesa (da Windows PE o Linux RPM) |
| FileVersion_s |Versione del file eseguibile (da Windows PE, solo Windows) |
| FirstPid_d |ID processo del sistema operativo |
| InternalName_s |Nome interno del file eseguibile (da Windows PE, solo Windows) |
| MachineId_s |GUID interno univoco in un'area di lavoro OMS |
| Name_s |Nome dell'eseguibile del processo |
| Path_s |Percorso del file system dell'eseguibile del processo |
| PersistentKey_s |GUID interno univoco all'interno del computer |
| PoolId_d |ID interno per i processi di aggregazione basati su righe di comando simili. |
| ProcessId_s |GUID interno univoco in un'area di lavoro OMS |
| ProductName_s |Stringa del nome del prodotto (da Windows PE o Linux RPM) |
| ProductVersion_s |Stringa della versione del prodotto (da Windows PE o Linux RPM) |
| StartTime_t |Ora di inizio del processo dell'orologio del computer locale |
| TimeGenerated |Data e ora di creazione del record. |
| UserDomain_s |Dominio del proprietario del processo (solo Windows) |
| UserName_s |Nome del proprietario del processo (solo Windows) |
| WorkingDirectory_s |Directory di lavoro del processo |

## <a name="sample-log-searches"></a>Ricerche di log di esempio
### <a name="list-the-physical-memory-capacity-of-all-managed-computers."></a>Visualizzare la capacità di memoria fisica di tutti i computer gestiti.
Type=AdmComputer_CL | select TotalPhysicalMemory_d, ComputerName_s | Dedup ComputerName_s

![Esempio di query ADM](media/operations-management-suite-application-dependency-monitor/adm-example-01.png)

### <a name="list-computer-name,-dns,-ip,-and-os-version."></a>Visualizzare nome del computer, DNS, IP e versione del sistema operativo.
Type=AdmComputer_CL | select ComputerName_s, OperatingSystemVersion_s, DnsNames_s, IPv4s_s  | dedup ComputerName_s

![Esempio di query ADM](media/operations-management-suite-application-dependency-monitor/adm-example-02.png)

### <a name="find-all-processes-with-"sql"-in-the-command-line"></a>Trovare tutti i processi con "sql" nella riga di comando
Type=AdmProcess_CL CommandLine_s = \*sql\* | dedup ProcessId_s

![Esempio di query ADM](media/operations-management-suite-application-dependency-monitor/adm-example-03.png)

### <a name="after-viewing-event-data-for-given-process,-use-its-machine-id-to-retrieve-the-computer’s-name"></a>Dopo aver visualizzato i dati degli eventi per un processo specifico, usare l'ID computer per recuperare il nome del computer
Type=AdmComputer_CL "m!m-9bb187fa-e522-5f73-66d2-211164dc4e2b" | Distinct ComputerName_s

![Esempio di query ADM](media/operations-management-suite-application-dependency-monitor/adm-example-04.png)

### <a name="list-all-computers-running-sql"></a>Visualizzare tutti i computer che eseguono SQL
Type=AdmComputer_CL MachineId_s IN {Type=AdmProcess_CL \*sql\* | Distinct MachineId_s} | Distinct ComputerName_s

![Esempio di query ADM](media/operations-management-suite-application-dependency-monitor/adm-example-05.png)

### <a name="list-of-all-unique-product-versions-of-curl-in-my-datacenter"></a>Visualizzare tutte le versioni di prodotto univoche di curl nel data center
Type=AdmProcess_CL Name_s=curl | Distinct ProductVersion_s

![Esempio di query ADM](media/operations-management-suite-application-dependency-monitor/adm-example-06.png)

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Creare un gruppo di tutti i computer che eseguono CentOS
![Esempio di query ADM](media/operations-management-suite-application-dependency-monitor/adm-example-07.png)

## <a name="diagnostic-and-usage-data"></a>Dati di diagnostica e di utilizzo
Microsoft raccoglie automaticamente i dati di utilizzo e prestazioni tramite l'utilizzo del servizio Application Dependency Monitor da parte dell'utente. Microsoft usa questi dati per fornire e migliorare la qualità, la sicurezza e l'integrità del servizio Application Dependency Monitor. I dati includono informazioni sulla configurazione del software, come il sistema operativo e la versione, e comprendono anche l'indirizzo IP, il nome DNS e il nome della workstation, al fine di fornire capacità di risoluzione dei problemi accurate ed efficienti. Non vengono raccolti nomi, indirizzi o altre informazioni di contatto.

Per altre informazioni sulla raccolta dei dati e sull'utilizzo, vedere l'[Informativa sulla privacy di Microsoft Online Services](hhttps://go.microsoft.com/fwlink/?LinkId=512132).

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulle [ricerche nei log](../log-analytics/log-analytics-log-searches.md\] in Log Analytics to retrieve data collected by Application Dependency Monitor..md)

<!--HONumber=Oct16_HO2-->


