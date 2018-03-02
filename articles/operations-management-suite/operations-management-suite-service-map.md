---
title: Uso del Mapping dei servizi in Azure | Microsoft Docs
description: "Service Map è una soluzione di Azure che rileva automaticamente i componenti delle applicazioni nei sistemi Windows e Linux e mappa la comunicazione tra i servizi. Questo articolo fornisce informazioni dettagliate su come distribuire Mapping dei servizi nell'ambiente e su come usarlo in svariati scenari."
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
ms.openlocfilehash: 84a43a4f04d7cd89d0d968acb436d196353eb81d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="using-service-map-solution-in-azure"></a>Uso del Mapping dei servizi in Azure
Mapping dei servizi individua automaticamente i componenti delle applicazioni nei sistemi Windows e Linux ed esegue la mappatura della comunicazione fra i servizi. Il Mapping dei servizi consente di visualizzare i server nel modo in cui si pensa a essi, ovvero come sistemi interconnessi che forniscono servizi critici. Il Mapping dei servizi mostra le connessioni fra i server, i processi e le porte di tutte le architetture connesse via TCP senza il bisogno di alcuna configurazione a parte l'installazione di un agente.

Questo articolo fornisce i dettagli su come usare Mapping dei servizi. Per informazioni sulla configurazione di Mapping dei servizi e sugli agenti di caricamento, vedere [Configurare la soluzione di elenco dei servizi in Azure](operations-management-suite-service-map-configure.md).


## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Casi di utilizzo: Riconoscimento delle dipendenze nei processi IT

### <a name="discovery"></a>Individuazione
Mapping dei servizi genera automaticamente una mappa di riferimento delle dipendenze tra server, processi e servizi di terze parti. La soluzione rileva e mappa tutte le dipendenze TCP, identificando le connessioni impreviste, i sistemi remoti di terze parti da cui si dipende e le dipendenze da aree tradizionali non note della rete, ad esempio Active Directory. Mapping dei servizi rileva i tentativi di connessione di rete non riusciti effettuati dai sistemi gestiti permettendo di identificare potenziali configurazioni del server errate, interruzioni dei servizi e problemi di rete.

### <a name="incident-management"></a>Gestione di eventi imprevisti
Mapping dei servizi consente di eliminare i tentativi di isolamento del problema visualizzando le connessioni tra i sistemi e la loro influenza reciproca. In aggiunta all'identificazione delle informazioni sulle connessioni non riuscite, consente di identificare i bilanciamenti del carico errati, il carico imprevisto o eccessivo sui servizi critici e i client non autorizzati, ad esempio i computer per lo sviluppo in comunicazione con i sistemi di produzione. L'uso dei flussi di lavoro integrati in Rilevamento modifiche consente anche di verificare se un evento di modifica in un computer o servizio back-end descrive la causa principale di un evento imprevisto.

### <a name="migration-assurance"></a>Garanzia di migrazione
L'uso di Mapping dei servizi consente di pianificare in modo efficace, accelerare e convalidare le migrazioni di Azure, garantendo che non venga tralasciato nulla e non si verifichino interruzioni impreviste. È possibile individuare tutti i sistemi interdipendenti di cui è necessario eseguire la migrazione insieme, valutare la configurazione e la capacità del sistema e determinare se un sistema in esecuzione è ancora utile agli utenti oppure è un candidato alla rimozione delle autorizzazioni anziché alla migrazione. Dopo aver eseguito lo spostamento, è possibile verificare il caricamento e l'identità dei client per assicurarsi che i sistemi di test e i clienti siano connessi. Se ci sono problemi nella pianificazione delle subnet e nelle definizioni dei firewall, le connessioni non riuscite nelle mappe di Mapping dei servizi indicano i sistemi che necessitano di connettività.

### <a name="business-continuity"></a>Continuità aziendale
Se si usa Azure Site Recovery e si necessita di aiuto per definire la sequenza di ripristino dell'ambiente delle applicazioni, Mapping dei servizi visualizza automaticamente le dipendenze reciproche tra i sistemi, consentendo di verificare l'affidabilità del proprio piano di ripristino. Scegliendo un server o un gruppo critico e visualizzandone i client è possibile identificare i sistemi front-end che devono essere ripristinati soltanto dopo aver ripristinato e reso disponibile il server critico. Al contrario, osservando le dipendenze back-end di un server critico è possibile identificare i sistemi che devono essere ripristinati prima di ripristinare il sistema critico.

### <a name="patch-management"></a>Gestione delle patch
Mapping dei servizi ottimizza l'uso di System Update Assessment mostrando gli altri team e server che dipendono da un servizio, in modo che sia possibile inviare una notifica prima di arrestare i sistemi per l'applicazione di patch. Mapping dei servizi migliora anche la gestione delle patch indicando se i servizi sono disponibili e connessi correttamente dopo l'applicazione delle patch e il riavvio.


## <a name="mapping-overview"></a>Panoramica sui mapping
Gli agenti di Mapping dei servizi raccolgono informazioni su tutti i processi connessi tramite TCP nel server in cui sono installati e dettagli sulle connessioni in entrata e in uscita di ogni processo. Nell'elenco del riquadro a sinistra è possibile selezionare i computer o i gruppi con gli agenti di Mapping dei servizi per visualizzare le relative dipendenze in un intervallo di tempo specificato. Le mappe delle dipendenze dei computer sono specifiche di un computer e visualizzano tutti i computer che sono client TCP diretti o server del computer specifico.  Le mappe del gruppo di computer mostrano insiemi di server e le relative dipendenze.

![Panoramica di Mapping dei servizi](media/oms-service-map/service-map-overview.png)

È possibile espandere i computer nella mappa per visualizzare i gruppi di processi e i processi in esecuzione con connessioni di rete attive nell'intervallo di tempo selezionato. Quando si espande un computer remoto con un agente di Mapping dei servizi per visualizzare i dettagli dei processi, vengono visualizzati solo i processi che comunicano con il computer. Il numero di computer front-end senza agenti che si connettono al computer critico è indicato a sinistra dei processi a cui si connettono. Se il computer principale è in fase di connessione a computer back-end senza un agente, il server back-end è incluso in un gruppo di porte server con altre connessioni allo stesso numero di porta.

Per impostazione predefinita, Mapping dei servizi mostra le informazioni sulle dipendenze per gli ultimi 30 minuti. Usando i controlli temporali in alto a sinistra, è possibile cercare nelle mappe intervalli di tempo cronologici della durata massima di un'ora per visualizzare l'aspetto delle dipendenze nel passato, ad esempio durante un evento imprevisto o prima di una modifica. I dati di Mapping dei servizi vengono archiviati per 30 giorni nelle aree di lavoro a pagamento e per 7 giorni nelle aree di lavoro gratuite.

## <a name="status-badges-and-border-coloring"></a>Notifiche di stato e colorazione del bordo
Nella parte inferiore di ogni server nella mappa potrebbe essere presente un elenco di notifiche di stato con informazioni relative allo stato del server. Le notifiche indicano che sono presenti alcune informazioni rilevanti per il server da una delle integrazioni della soluzione. Facendo clic su una notifica, vengono visualizzati direttamente i dettagli dello stato nel riquadro di destra. Le notifiche di stato attualmente disponibili includono Avvisi, Service Desk, Modifiche, Sicurezza e Aggiornamenti.

In base alla gravità della notifica di stato, i bordi del nodo del computer possono essere colorati di rosso (critico), giallo (avviso) o blu (informativi). Il colore rappresenta lo stato di gravità di una notifica di stato. Un bordo grigio indica un nodo senza indicatori di stato.

![Notifiche di stato](media/oms-service-map/status-badges.png)

## <a name="process-groups"></a>Gruppi di processi
I gruppi di processi riuniscono i processi associati a un prodotto o servizio comune in un gruppo di processi.  Quando si espande un nodo del computer verranno visualizzati i processi autonomi oltre ai gruppi di processi.  In caso di errore per connessioni in ingresso e in uscita per un processo all'interno di un gruppo di processi, l'errore di connessione viene indicato per l'intero gruppo di processi.

## <a name="machine-groups"></a>Gruppi di computer
I gruppi di computer consentono di visualizzare le mappe incentrate su un insieme di server, non su un solo server in modo da visualizzare tutti i membri di un cluster di server o dell'applicazione a più livelli in una mappa.

Gli utenti possono selezionare tutti i server che appartengono a un gruppo e scegliere un nome per il gruppo.  È quindi possibile scegliere di visualizzare il gruppo con tutti i processi e le connessioni relativi o visualizzarlo solo con i processi e le connessioni direttamente correlati agli altri membri del gruppo.

![Gruppo di computer](media/oms-service-map/machine-group.png)

### <a name="creating-a-machine-group"></a>Creazione di un gruppo di computer
Per creare un gruppo, selezionare il computer o i computer che si desidera aggiungere all'elenco dei computer e fare clic su **Add to group** (Aggiungi al gruppo).

![Creare un gruppo](media/oms-service-map/machine-groups-create.png)

È possibile quindi scegliere **Crea nuovo** e assegnare un nome al gruppo.

![Assegnare un nome al gruppo](media/oms-service-map/machine-groups-name.png)

>[!NOTE]
>I gruppi di computer sono attualmente limitati a 10 server, ma si prevede di aumentare questo limite il prima possibile.

### <a name="viewing-a-group"></a>Visualizzazione di un gruppo
Dopo avere creato i gruppi, è possibile visualizzarli scegliendo la scheda Gruppi.

![Scheda Gruppi](media/oms-service-map/machine-groups-tab.png)

Selezionare quindi il nome del Gruppo per visualizzare la mappa relativa al gruppo di computer.
![Gruppo di computer](media/oms-service-map/machine-group.png) I computer che appartengono al gruppo sono indicati con il bianco nella mappa.

Con l'espansione del gruppo verranno elencati i computer che fanno parte del gruppo di computer.

![Computer del gruppo di computer](media/oms-service-map/machine-groups-machines.png)

### <a name="filter-by-processes"></a>Filtrare in base ai processi
È possibile attivare o disattivare la visualizzazione della mappa e scegliere di visualizzare tutti i processi e le connessioni nel gruppo oppure solo quelli correlati direttamente al gruppo di computer.  La visualizzazione predefinita mostra tutti i processi.  È possibile modificare la visualizzazione facendo clic sull'icona del filtro sulla mappa.

![Gruppo di filtri](media/oms-service-map/machine-groups-filter.png)

Se si è selezionato **All processes** (Tutti i processi), la mappa includerà tutti i processi e le connessioni in ogni computer del gruppo.

![Tutti i processi del gruppo di computer](media/oms-service-map/machine-groups-all.png)

Se si modifica la visualizzazione per mostrare solo i **processi connessi al gruppo**, la mappa verrà ridotta solo ai processi e alle connessioni direttamente connessi ad altri computer nel gruppo, creando una visualizzazione semplificata.

![Processi filtrati del gruppo di computer](media/oms-service-map/machine-groups-filtered.png)
 
### <a name="adding-machines-to-a-group"></a>Aggiunta di computer a un gruppo
Per aggiungere computer a un gruppo esistente, selezionare le caselle accanto al computer desiderato e quindi fare clic su **Add to group** (Aggiungi al gruppo).  Quindi, scegliere il gruppo a cui si desidera aggiungere i computer.
 
### <a name="removing-machines-from-a-group"></a>Rimozione di computer da un gruppo
Nell'elenco gruppi espandere il nome del gruppo per vedere i computer nell'elenco del gruppo di computer.  Fare clic sui puntini di sospensione accanto al computer che si desidera rimuovere e scegliere **Rimuovi**.

![Rimuovere il computer dal gruppo](media/oms-service-map/machine-groups-remove.png)

### <a name="removing-or-renaming-a-group"></a>Rimozione o assegnazione di un nuovo nome al gruppo
Fare clic sui puntini di sospensione accanto al nome del gruppo nell'elenco del gruppo.

![Menu del gruppo di computer](media/oms-service-map/machine-groups-menu.png)


## <a name="role-icons"></a>Icone di ruolo
Alcuni processi svolgono ruoli particolari nei computer: server Web, server applicazioni, database e così via. Mapping dei servizi annota le caselle relative a processi e computer con icone di ruolo, per consentire di identificare rapidamente il ruolo di un processo o un server.

| Icona del ruolo | DESCRIZIONE |
|:--|:--|
| ![Server Web](media/oms-service-map/role-web-server.png) | Server Web |
| ![Server app](media/oms-service-map/role-application-server.png) | Server applicazioni |
| ![Server di database](media/oms-service-map/role-database.png) | Server di database |
| ![Server LDAP](media/oms-service-map/role-ldap.png) | Server LDAP |
| ![Server SMB](media/oms-service-map/role-smb.png) | Server SMB |

![Icone di ruolo](media/oms-service-map/role-icons.png)


## <a name="failed-connections"></a>Connessioni non riuscite
Le connessioni a processi e computer non riuscite sono visualizzate nelle mappe di Mapping dei servizi con una linea rossa tratteggiata, che indica se un sistema client non riesce a raggiungere un processo o una porta. Vengono visualizzate le connessioni non riuscite di qualsiasi sistema con un agente di Mapping dei servizi distribuito, a condizione che il sistema corrisponda a quello che esegue la connessione non riuscita. Mapping dei servizi esegue il calcolo di questo processo osservando i socket TCP che non riescono a stabilire una connessione. Questo errore potrebbe essere causato da un firewall, da un errore di configurazione del client o server oppure da un servizio remoto non disponibile.

![Connessioni non riuscite](media/oms-service-map/failed-connections.png)

Le informazioni sulle connessioni non riuscite facilitano la risoluzione dei problemi, la convalida della migrazione, l'analisi di sicurezza e la comprensione dell'intera architettura. A volte le connessioni non riuscite non offrono informazioni utili, spesso tuttavia indicano direttamente un problema, ad esempio un ambiente di failover che diventa improvvisamente non raggiungibile oppure due livelli applicazione che non comunicano dopo una migrazione cloud.

## <a name="client-groups"></a>Gruppi di client
I gruppi di client sono costituiti da caselle sulla mappa che rappresentano i computer client privi di agenti di dipendenza. Un singolo gruppo di client rappresenta i client per un unico processo o computer.

![Gruppi di client](media/oms-service-map/client-groups.png)

Per visualizzare gli indirizzi IP dei server in un gruppo di client, selezionare il gruppo. Il contenuto del gruppo verrà elencato nel riquadro **Client Group Properties** (Proprietà del gruppo di client).

![Proprietà del gruppo di client](media/oms-service-map/client-group-properties.png)

## <a name="server-port-groups"></a>Gruppi di porte di server
I gruppi di porte di server sono costituiti da caselle che rappresentano le porte sui server privi di agenti di dipendenza. La casella contiene la porta del server con il numero di server con connessioni a tale porta. Espandere la casella per visualizzare i singoli server e le connessioni. Se nella casella c'è un solo server, viene indicato il relativo nome o indirizzo IP.

![Gruppi di porte di server](media/oms-service-map/server-port-groups.png)

## <a name="context-menu"></a>Menu di scelta rapida
Facendo clic sui puntini di sospensione (...) in alto a destra di un server, verrà visualizzato il menu a comparsa per quel server.

![Connessioni non riuscite](media/oms-service-map/context-menu.png)

### <a name="load-server-map"></a>Caricare la mappa del server
Facendo clic su **Carica mappa del server** è possibile passare a una nuova mappa con il server selezionato come nuovo computer attivo.

### <a name="show-self-links"></a>Mostra collegamenti automatici
Facendo clic su **Mostra collegamenti automatici** il nodo del server viene ridisegnato, inclusi eventuali collegamenti automatici, ovvero connessioni TCP che si avviano e terminano con i processi nel server. Se i collegamenti automatici sono visibili, il comando di menu viene modificato in **Nascondi collegamenti automatici**, in modo che sia possibile disattivarli.

## <a name="computer-summary"></a>Riepilogo del computer
Il riquadro di **riepilogo del computer** include una panoramica del sistema operativo di un server, i conteggi di dipendenza e i dati delle soluzioni. Tali dati includono le metriche delle prestazioni, i ticket del Service Desk, il rilevamento delle modifiche, la sicurezza e gli aggiornamenti.

![Riquadro di riepilogo del computer](media/oms-service-map/machine-summary.png)

## <a name="computer-and-process-properties"></a>Proprietà dei computer e dei processi
In una mappa di Mapping dei servizi è possibile selezionare computer e processi per visualizzare ulteriori informazioni sulle loro proprietà. I computer visualizzano informazioni sul nome DNS, gli indirizzi IPv4, la capacità di CPU e di memoria, il tipo di macchina virtuale, la versione del sistema operativo, l'ora dell'ultimo riavvio e gli ID degli agenti OMS e di Mapping dei servizi.

![Riquadro Proprietà del computer](media/oms-service-map/machine-properties.png)

È possibile raccogliere i dettagli dei processi dai metadati del sistema operativo relativi ai processi in esecuzione che includono nome e descrizione del processo, nome utente e dominio, in Windows, nome dell'azienda, nome e versione del prodotto, directory di lavoro, riga dei comandi e ora di inizio del processo.

![Riquadro Proprietà del processo](media/oms-service-map/process-properties.png)

Il riquadro di **riepilogo del processo** visualizza informazioni aggiuntive sulla connettività del processo, incluse le porte associate, le connessioni in entrata e in uscita e le connessioni non riuscite.

![Riquadro del riepilogo del processo](media/oms-service-map/process-summary.png)

## <a name="alerts-integration"></a>Integrazione degli avvisi
Gli avvisi integrati in Log Analytics di Mapping dei servizi consentono di visualizzare gli avvisi attivati per un determinato server nell'intervallo di tempo selezionato. Se ci sono avvisi correnti, viene visualizzata un'icona per il server e nel riquadro degli **avvisi del computer** vengono elencati gli avvisi.

![Riquadro degli avvisi del computer](media/oms-service-map/machine-alerts.png)

Per abilitare Mapping dei servizi al fine di visualizzare i relativi avvisi, creare una regola di avviso attivata per un computer specifico. Per creare gli avvisi appropriati:
- Includere una clausola che consenta di raggruppare per computer, ad esempio **per intervallo computer 1 minuto**.
- Scegliere di impostare un avviso in base alle metriche misurate.

![Configurazione degli avvisi](media/oms-service-map/alert-configuration.png)


## <a name="log-events-integration"></a>Integrazione eventi log
Mapping dei servizi si integra con ricerca di log per visualizzare un conteggio di tutti gli eventi di log disponibili per il server selezionato durante l'intervallo di tempo selezionato. È possibile fare clic su una riga qualsiasi nell'elenco di conteggi degli eventi per passare alla ricerca log e visualizzare i singoli eventi di log.

![Riquadro degli eventi del registro del computer](media/oms-service-map/log-events.png)

## <a name="service-desk-integration"></a>Integrazione di Service Desk
L'integrazione di Mapping dei servizi con il connettore di gestione dei servizi IT è automatica quando entrambe le soluzioni sono abilitate e configurate nell'area di lavoro di Log Analytics. L'integrazione in Mapping dei servizi è indicata come "Service Desk". Per informazioni vedere [Gestire centralmente gli elementi di lavoro ITSM con IT Service Management Connector](https://docs.microsoft.com/azure/log-analytics/log-analytics-itsmc-overview).

Il riquadro del **Service Desk del computer** elenca tutti gli eventi di gestione dei servizi IT per il server selezionato nell'intervallo di tempo selezionato. Se ci sono elementi correnti, viene visualizzata un'icona per il server e il riquadro del Service Desk del computer elenca gli elementi.

![Riquadro del Service Desk del computer](media/oms-service-map/service-desk.png)

Per aprire l'elemento nella soluzione ITSM connessa fare clic su **Visualizza elemento di lavoro**.

Fare clic su **Mostra in Ricerca log** per visualizzare i dettagli dell'elemento nella ricerca log.


## <a name="change-tracking-integration"></a>Integrazione con Rilevamento modifiche
L'integrazione del rilevamento delle modifiche in Mapping dei servizi è automatica quando entrambe le soluzioni sono abilitate e configurate nell'area di lavoro di Log Analytics.

Il riquadro di **rilevamento modifiche del computer** elenca tutte le modifiche a partire dalla più recente e un collegamento per eseguire una ricerca nel log per ulteriori dettagli.

![Riquadro di rilevamento modifiche del computer](media/oms-service-map/change-tracking.png)

L'immagine seguente è una vista dettagliata di un evento ConfigurationChange che potrebbe essere visualizzata dopo aver selezionato **Mostra in Ricerca log**.

![Evento ConfigurationChange](media/oms-service-map/configuration-change-event.png)


## <a name="performance-integration"></a>Integrazione delle prestazioni
Il riquadro relativo alle **prestazioni del computer** mostra le metriche di prestazioni standard relative al server selezionato. Le metriche includono l'uso della CPU, l'uso della memoria, i byte di rete inviati e ricevuti e un elenco dei processi principali per byte di rete inviati e ricevuti.

![Riquadro delle prestazioni del computer](media/oms-service-map/machine-performance.png)

Per visualizzare i dati sulle prestazioni, potrebbe essere necessario [abilitare i contatori delle prestazioni di Log Analytics appropriati](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters).  Contatori da attivare:

Windows:
- Processore(*)\\% di tempo processore
- Memoria\\% di byte vincolati in uso
- Scheda di rete(*)\\Byte inviati/sec
- Scheda di rete(*)\\Byte ricevuti/sec

Linux:
- Processore(*)\\% di tempo processore
- Memoria(*)\\% di memoria utilizzata
- Scheda di rete(*)\\Byte inviati/sec
- Scheda di rete(*)\\Byte ricevuti/sec

Per ottenere i dati sulle prestazioni di rete è necessario anche aver abilitato la soluzione Wire Data 2.0 nell'area di lavoro.
 
## <a name="security-integration"></a>Integrazione della sicurezza
L'integrazione di sicurezza e controllo in Mapping dei servizi è automatica quando entrambe le soluzioni sono abilitate e configurate nell'area di lavoro di Log Analytics.

Il pannello relativo alla **sicurezza del computer** mostra i dati provenienti dalla soluzione di sicurezza e controllo relativi al server selezionato. Il riquadro elenca un riepilogo dei problemi di sicurezza del server non risolti durante l'intervallo di tempo selezionato. Facendo clic su uno dei problemi di sicurezza, verrà eseguito il drill-down in una ricerca log per ottenerne i dettagli.

![Riquadro relativo alla sicurezza dei computer](media/oms-service-map/machine-security.png)


## <a name="updates-integration"></a>Integrazione degli aggiornamenti
La gestione degli aggiornamenti di Mapping dei servizi è automatica quando entrambe le soluzioni sono abilitate e configurate nell'area di lavoro di Log Anlaytics.

Il pannello relativo agli **aggiornamenti del computer** mostra i dati provenienti dalla soluzione di gestione degli aggiornamenti relativi al server selezionato. Il riquadro elenca un riepilogo degli aggiornamenti mancanti per il server durante l'intervallo di tempo selezionato.

![Riquadro di rilevamento modifiche del computer](media/oms-service-map/machine-updates.png)

## <a name="log-analytics-records"></a>Record di Log Analytics
I dati di inventario di computer e processi di Mapping dei servizi sono disponibili per la [ricerca](../log-analytics/log-analytics-log-searches.md) in Log Analytics. Questi dati possono essere applicati a diversi scenari, tra cui la pianificazione della migrazione, l'analisi della capacità, l'individuazione e la risoluzione dei problemi di prestazioni on demand.

Ogni ora viene generato un record per ogni computer e processo univoco che si aggiunge ai record generati quando un processo o computer viene avviato o caricato in Mapping dei servizi. I record hanno le proprietà descritte nelle tabelle seguenti. I campi e i valori negli eventi ServiceMapComputer_CL eseguono il mapping ai campi della risorsa del computer nell'API ServiceMap di Azure Resource Manager. I campi e i valori negli eventi ServiceMapProcess_CL eseguono il mapping ai campi della risorsa del computer nell'API ServiceMap di Azure Resource Manager. Il campo ResourceName_s coincide con il campo del nome nella risorsa di Resource Manager corrispondente. 

>[!NOTE]
>Con l'aumentare delle funzionalità di Mapping dei servizi, questi campi sono soggetti a modifica.

Sono disponibili proprietà generate internamente che è possibile usare per identificare processi e computer univoci:

- Computer: usare ResourceId o ResourceName_s per identificare in modo univoco un computer all'interno di un'area di lavoro di Log Analytics.
- Processo: usare ResourceId per identificare in modo univoco un computer all'interno di un'area di lavoro di Log Analytics. ResourceName_s è univoco all'interno del contesto del computer in cui viene eseguito il processo (MachineResourceName_s) 

Poiché possono essere presenti vari record per un determinato processo o computer in un intervallo di tempo specificato, le query possono restituire più di un record per lo stesso computer o processo. Per includere solo il record più recente, aggiungere "| dedup ResourceId" alla query.

### <a name="servicemapcomputercl-records"></a>Record ServiceMapComputer_CL
I record che contengono il tipo *ServiceMapComputer_CL* includono dati di inventario relativi ai server con agenti del modello dei servizi. Questi record includono le proprietà elencate nella tabella seguente:

| Proprietà | DESCRIZIONE |
|:--|:--|
| type | *ServiceMapComputer_CL* |
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
I record con tipo *ServiceMapProcess_CL* includono dati di inventario relativi ai processi con connessione TCP eseguiti sui server con agenti del modello dei servizi. Questi record includono le proprietà elencate nella tabella seguente:

| Proprietà | DESCRIZIONE |
|:--|:--|
| type | *ServiceMapProcess_CL* |
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
| ExecutablePath _s | Percorso del file eseguibile |
| WorkingDirectory_s | La directory di lavoro |
| UserName | Account con cui è in esecuzione il processo |
| UserDomain | Dominio in cui è in esecuzione il processo |


## <a name="sample-log-searches"></a>Ricerche di log di esempio

### <a name="list-all-known-machines"></a>Visualizzare tutti i computer noti
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Visualizzare la capacità di memoria fisica di tutti i computer gestiti.
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project PhysicalMemory_d, ComputerName_s

### <a name="list-computer-name-dns-ip-and-os"></a>Visualizzare nome del computer, DNS, IP e sistema operativo.
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Trovare tutti i processi con "sql" nella riga di comando
ServiceMapProcess_CL | where CommandLine_s contains_cs "sql" | summarize arg_max(TimeGenerated, *) by ResourceId

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Trovare un computer (record più recente) in base al nome di risorsa
search in (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by ResourceId

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Trovare un computer, ovvero il record più recente, in base all'indirizzo IP
search in (ServiceMapComputer_CL) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by ResourceId

### <a name="list-all-known-processes-on-a-specified-machine"></a>Elencare tutti i processi noti su un computer specifico
ServiceMapProcess_CL | where MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by ResourceId

### <a name="list-all-computers-running-sql"></a>Visualizzare tutti i computer che eseguono SQL
ServiceMapComputer_CL | where ResourceName_s in ((search in (ServiceMapProcess_CL) "\*sql\*" | distinct MachineResourceName_s)) | distinct ComputerName_s

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Elencare tutte le versioni di prodotto univoche di curl nel data center
ServiceMapProcess_CL | where ExecutableName_s == "curl" | distinct ProductVersion_s

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Creare un gruppo di tutti i computer che eseguono CentOS
ServiceMapComputer_CL | where OperatingSystemFullName_s contains_cs "CentOS" | distinct ComputerName_s


## <a name="rest-api"></a>API REST
Tutti i dati relativi a server, processi e dipendenze in Mapping dei servizi sono disponibili tramite l'[API REST di Mapping dei servizi](https://docs.microsoft.com/rest/api/servicemap/).


## <a name="diagnostic-and-usage-data"></a>Dati di diagnostica e di utilizzo
Microsoft raccoglie automaticamente i dati di utilizzo e prestazioni tramite l'uso del servizio Mapping dei servizi da parte dell'utente. Microsoft usa questi dati per offrire e migliorare la qualità, la sicurezza e l'integrità del servizio Mapping dei servizi. Per offrire capacità di risoluzione dei problemi accurate ed efficienti, i dati includono informazioni sulla configurazione del software, come il sistema operativo e la versione, l'indirizzo IP, il nome DNS e il nome della workstation. Microsoft non raccoglie nomi, indirizzi o altre informazioni di contatto.

Per altre informazioni sulla raccolta e sull'uso dei dati , vedere l'[Informativa sulla privacy Servizi online Microsoft ](https://go.microsoft.com/fwlink/?LinkId=512132).


## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sulle [ricerche nei log](../log-analytics/log-analytics-log-searches.md) in Log Analytics per recuperare i dati raccolti da Mapping dei servizi.


## <a name="troubleshooting"></a>risoluzione dei problemi
Vedere la [sezione Risoluzione dei problemi del documento relativo alla configurazione di Mapping dei servizi](operations-management-suite-service-map-configure.md#troubleshooting).


## <a name="feedback"></a>Commenti e suggerimenti
Per inviare commenti su Mapping dei servizi e sulla relativa documentazione,  Visitare la [pagina per i suggerimenti degli utenti](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), in cui è possibile suggerire funzionalità o votare i suggerimenti esistenti.
