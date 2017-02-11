---
title: Panoramica dell&quot;array virtuale StorSimple di Microsoft Azure | Microsoft Docs
description: "Fornisce una descrizione dell&quot;array virtuale StorSimple, una soluzione di archiviazione integrata che gestisce le attività di archiviazione tra un array virtuale locale e la risorsa di archiviazione cloud di Microsoft Azure."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 169c639b-1124-46a5-ae69-ba9695525b77
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 12/09/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 6ba37cbfc8c92d00cf0ec02dd7b1ea9795946bcd
ms.openlocfilehash: 100eed4694d2017333ef25eca86034d17cce78d1

---
# <a name="introduction-to-the-storsimple-virtual-array"></a>Introduzione a StorSimple Virtual Array
## <a name="overview"></a>Panoramica
Microsoft Azure StorSimple Virtual Array è una soluzione di archiviazione integrata che consente di gestire le attività di archiviazione tra un array virtuale locale eseguito in un hypervisor e la risorsa di archiviazione cloud di Microsoft Azure. L'array virtuale è un file server o una soluzione server iSCSI efficiente, dai costi contenuti e facilmente gestibile che elimina molti problemi e spese associati agli archivi dell'organizzazione e alla protezione dei dati. L'array virtuale è particolarmente adatto per scenari di sedi remote/succursali.

Il presente argomento fornisce una panoramica dell'array virtuale. Di seguito sono riportate altre risorse:

* Per le procedure consigliate, vedere [Procedure consigliate per l'array virtuale StorSimple](storsimple-ova-best-practices.md).
* Per una panoramica dei dispositivi StorSimple serie 8000, consultare [Serie 8000 StorSimple: una soluzione di archiviazione cloud ibrida](storsimple-overview.md). 
* Per informazioni sui dispositivi StorSimple serie 5000/7000, vedere la [guida di StorSimple](http://onlinehelp.storsimple.com/).

L'array virtuale supporta il protocollo iSCSI o Server Message Block (SMB). Viene eseguito nell'infrastruttura hypervisor esistente e fornisce più livelli per il cloud, il backup nel cloud, il ripristino rapido, il ripristino a livello di elemento e le funzionalità di ripristino di emergenza.

La tabella seguente riepiloga le funzionalità principali dell'array virtuale StorSimple.

| Funzionalità | Array virtuale StorSimple |
| --- | --- |
| Requisiti di installazione |Viene usata un'infrastruttura di virtualizzazione (Hyper-V o VMware) |
| Disponibilità |Nodo singolo |
| Capacità totale (incluso il cloud) |Fino a 64 TB di capacità utilizzabile per array virtuale |
| Capacità locale |Capacità utilizzabile da 390 GB a 6,4 TB per array virtuale (necessità di provisioning da 500 GB a 8 TB di spazio su disco) |
| Protocolli nativi |iSCSI o SMB |
| Obiettivo del tempo di ripristino (RTO) |iSCSI: meno di 2 minuti indipendentemente dalle dimensioni |
| Obiettivo del punto di ripristino (RPO) |Backup giornalieri e backup su richiesta |
| Suddivisione in livelli di archiviazione |Uso di mappe termiche per determinare in che modo suddividere in livelli i dati |
| Supporto |Infrastruttura di virtualizzazione supportata dal fornitore |
| Prestazioni |Variabile in base all'infrastruttura sottostante |
| Mobilità dei dati |Consente di ripristinare lo stesso dispositivo o eseguire il ripristino a livello di elemento (file server) |
| Livelli di archiviazione |Archiviazione hypervisor locale e cloud |
| Dimensione della condivisione |A livelli: fino a 20 TB; aggiunti in locale: fino a 2 TB |
| Dimensioni del volume |A livelli: da 500 GB a 5 TB; aggiunti in locale: da 50 GB a 500 GB |
| Dimensioni del volume |A livelli: fino a 5 TB; aggiunti in locale: fino a 500 GB |
| Snapshot |Coerenza in caso di arresto anomalo |
| Ripristino a livello di elemento |Sì. Gli utenti possono ripristinare dalle condivisioni |

## <a name="why-use-storsimple"></a>Vantaggi dell'uso di StorSimple
StorSimple connette gli utenti e i server ad Archiviazione di Azure in pochi minuti, senza alcuna modifica dell'applicazione.

La tabella seguente illustra alcuni dei vantaggi principali forniti dalla soluzione di array virtuale StorSimple.

| Funzionalità | Vantaggi |
| --- | --- |
| Integrazione trasparente |L'array virtuale supporta il protocollo iSCSI o SMB. Lo spostamento dei dati tra il livello locale e il livello cloud è semplice e trasparente per l'utente. |
| Riduzione dei costi di archiviazione |Con StorSimple, è possibile eseguire il provisioning di archiviazione locale sufficiente per soddisfare le richieste correnti per i dati sensibili più utilizzati. Mentre le necessità di archiviazione crescono, StorSimple suddivide i dati meno utilizzati in livelli in un'archiviazione cloud a costi contenuti. Per ridurre ulteriormente i costi e i requisiti di archiviazione, i dati vengono deduplicati e compressi prima di essere inviati al cloud. |
| Gestione dell'archiviazione semplificata |StorSimple fornisce la gestione centralizzata nel cloud tramite Gestione dispositivi StorSimple per la gestione di più dispositivi. |
| Miglioramento del ripristino di emergenza e della conformità |StorSimple accelera il ripristino di emergenza con il ripristino immediato di dati e metadati in base alle esigenze. Le normali operazioni possono quindi continuare con un'interruzione minima. |
| Mobilità dei dati |È possibile accedere ai dati caricati nel cloud da altri siti con finalità di ripristino e migrazione. Tenere presente la possibilità di ripristinare i dati solo sull'array virtuale originale. È tuttavia possibile usare le funzionalità di ripristino di emergenza per ripristinare l'intero array virtuale su un altro array virtuale. |

## <a name="storsimple-workload-summary"></a>Riepilogo dei carichi di lavoro di StorSimple

Di seguito è riportato un riepilogo dei carichi di lavoro StorSimple supportati.

|Scenario     |Carico di lavoro     |Supportato      |Restrizioni               |
|-------------|-------------|---------------|---------------------------|
|Collaborazione ROBO |Condivisione di file     |Sì      |Vedere [limiti massimi per file server](storsimple-ova-limits.md).<br></br>Vedere [requisiti di sistema per le versioni supportate di SMB](storsimple-ova-system-requirements.md).| Tutte le versioni     |

## <a name="workflows"></a>Flussi di lavoro
StorSimple Virtual Array è particolarmente adatto per i flussi di lavoro seguenti:

* [Gestione dell'archiviazione basata su cloud](#cloud-based-storage-management)
* [Backup indipendente dalla posizione](#location-independent-backup)
* [Protezione dati e ripristino di emergenza](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>Gestione dell'archiviazione basata su cloud
È possibile usare il servizio Gestione dispositivi StorSimple in esecuzione nel portale di Azure per gestire i dati archiviati su più dispositivi e in più posizioni. Questo è particolarmente utile in scenari di succursali distribuite. Tenere presente che è necessario creare istanze separate del servizio Gestione dispositivi StorSimple per gestire array virtuali e dispositivi StorSimple fisici. Inoltre, la matrice virtuale utilizza ora il nuovo portale di Azure anziché quello classico.

![Gestione dell'archiviazione basata su cloud](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>Backup indipendente dalla posizione
Con l'array virtuale, gli snapshot cloud forniscono una copia indipendente dalla posizione e temporizzata di un volume o di una condivisione. Gli snapshot cloud sono abilitati per impostazione predefinita e non possono essere disabilitati. Tutti i volumi e tutte le condivisioni vengono sottoposti a backup contemporaneamente tramite un singolo criterio di backup giornaliero ed è possibile eseguire sempre dei backup ad hoc aggiuntivi.

### <a name="data-protection-and-disaster-recovery"></a>Protezione dati e ripristino di emergenza
L'array virtuale supporta i seguenti scenari di protezione dei dati e ripristino di emergenza:

* **Ripristino volume o condivisione** : usare il ripristino come nuovo flusso di lavoro per ripristinare un volume o una condivisione. Usare questo approccio per ripristinare l'intero volume o l'intera condivisione.
* **Ripristino a livello di elemento** : le condivisioni consentono l'accesso semplificato ai backup recenti. È possibile ripristinare facilmente un singolo file da una cartella speciale con estensione *backup* disponibile nel cloud. Questa funzionalità di ripristino è gestita dall'utente e non è richiesto alcun intervento amministrativo.
* **Ripristino di emergenza**: usare la funzionalità di failover per ripristinare tutti i volumi o tutte le condivisioni in un nuovo array virtuale. Creare il nuovo array virtuale e registrarlo con il servizio Gestione dispositivi StorSimple, quindi eseguire il failover dell'array virtuale originale. Il nuovo array virtuale presuppone quindi le risorse sottoposte a provisioning. 

## <a name="storsimple-virtual-array-components"></a>Componenti dell'array virtuale StorSimple
L'array virtuale include i componenti seguenti:

* [Array virtuale.](#virtual-array) Un dispositivo di archiviazione cloud ibrido basato su una macchina virtuale sottoposta a provisioning nell'ambiente virtualizzato o in hypervisor.  
* [Servizio Gestione dispositivi StorSimple.](#storsimple-device-manager-service) Un'estensione del portale di Azure che consente di gestire uno o più dispositivi StorSimple da una singola interfaccia Web accessibile da diverse posizioni geografiche. È possibile usare il servizio Gestione dispositivi StorSimple per creare e gestire i servizi, visualizzare e gestire i dispositivi e gli avvisi, oltre a gestire volumi, condivisioni e snapshot esistenti.
* [Interfaccia utente Web locale.](#local-web-user-interface) Un'interfaccia utente basata sul Web usata per configurare il dispositivo in modo che possa connettersi alla rete locale e quindi registrare il dispositivo con il servizio Gestione dispositivi StorSimple. 
* [Interfaccia della riga di comando.](#command-line-interface) Un'interfaccia di Windows PowerShell da usare per avviare una sessione di supporto nell'array virtuale.
  Le sezioni seguenti descrivono dettagliatamente ciascuno dei componenti e illustrano il modo in cui la soluzione organizza i dati, alloca le risorse di archiviazione e facilita la gestione dell'archiviazione e la protezione dei dati.

### <a name="virtual-array"></a>Array virtuale
L'array virtuale è una soluzione di archiviazione a nodo singolo in grado di offrire archiviazione primaria, gestire la comunicazione con l'archiviazione cloud e favorire la sicurezza e la riservatezza di tutti i dati archiviati nel dispositivo.

L'array virtuale è disponibile in un modello scaricabile. L'array virtuale ha una capacità massima di 6,4 TB nel dispositivo (con un requisito di archiviazione sottostante di 8 TB) e di 64 TB includendo l'archiviazione cloud. 

L'array virtuale presenta le seguenti caratteristiche:

* È conveniente. Usa l'infrastruttura di virtualizzazione esistente e può essere distribuito in un  hypervisor Hyper-V o VMware esistente.
* Si trova nel data center e può essere configurato come un server iSCSI o un file server. 
* È integrato con il cloud.
* I backup vengono archiviati nel cloud, in grado di facilitare il ripristino di emergenza e semplificare il ripristino a livello di elemento (ILR). 
* È possibile applicare gli aggiornamenti all'array virtuale nello stesso modo in cui si applicano a un dispositivo fisico.

> [!NOTE]
> Non è possibile espandere un array virtuale. Pertanto, quando si crea l'array virtuale, è importante eseguire il provisioning di un'archiviazione adeguata. 
> 
> 

### <a name="storsimple-device-manager-service"></a>Servizio Gestione dispositivi StorSimple
Microsoft Azure StorSimple fornisce l'accesso a un'interfaccia utente basata sul Web, il servizio Gestione dispositivi StorSimple, che consente di gestire in modo centralizzato l'archiviazione StorSimple. È possibile usare il servizio Gestione dispositivi StorSimple per eseguire le attività seguenti:

* Gestire più StorSimple Virtual Array da un singolo servizio. 
* Configurare e gestire le impostazioni di sicurezza per gli array virtuali StorSimple. (La crittografia nel cloud dipende da API Microsoft Azure.)
* Configurare le credenziali dell'account di archiviazione e le proprietà.
* Configurare e gestire volumi o condivisioni.
* Eseguire il backup e il ripristino dei dati in volumi o condivisioni.
* Monitorare le prestazioni.
* Rivedere le impostazioni di sistema e identificare possibili problemi.

Usare il servizio Gestione dispositivi StorSimple per eseguire l'amministrazione giornaliera dell'array virtuale.

Per altre informazioni, vedere l'articolo [Use the StorSimple Device Manager service to administer your StorSimple device](storsimple-virtual-array-manager-service-administration.md) (Uso del servizio Gestione dispositivi StorSimple per gestire il dispositivo StorSimple).

### <a name="local-web-user-interface"></a>Interfaccia utente Web locale
L'array virtuale include un'interfaccia utente basata sul Web per configurazione e registrazione singole del dispositivo con il servizio Gestione dispositivi StorSimple. È possibile usarla per arrestare e riavviare l'array virtuale, eseguire i test diagnostici, aggiornare il software, modificare la password amministratore del dispositivo, visualizzare i registri di sistema e contattare il supporto tecnico Microsoft per inviare una richiesta di assistenza. 

Per informazioni sull'uso di un'interfaccia utente basata sul Web, vedere [Usare l'interfaccia utente Web per amministrare StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

### <a name="command-line-interface"></a>Interfaccia della riga di comando
L'interfaccia di Windows PowerShell inclusa consente di avviare una sessione di supporto con il supporto tecnico Microsoft per ricevere assistenza nella risoluzione dei problemi e risolvere quelli che possono verificarsi nell'array virtuale.

## <a name="storage-management-technologies"></a>Tecnologie di gestione dell'archiviazione
Oltre all'array virtuale e ad altri componenti, la soluzione StorSimple usa le seguenti tecnologie software per fornire accesso rapido ai dati importanti, ridurre il consumo di memoria e proteggere i dati archiviati nell'array virtuale:

* [Suddivisione automatica in livelli dell'archiviazione](#automatic-storage-tiering) 
* [Condivisioni e volumi aggiunti in locale](#locally-pinned-shares-and-volumes)
* [Deduplicazione e compressione dei dati a livelli o sottoposti a backup nel cloud](#deduplication-and-compression-for-data-tiered/backed-up-to-the-cloud) 
* [Backup pianificati e su richiesta](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>Suddivisione automatica in livelli dell'archiviazione
L'array virtuale si serve di un meccanismo di suddivisione in livelli nuovo per gestire i dati archiviati nell'array virtuale e nel cloud. Sono disponibili solo due livelli: l'array virtuale locale e l'archiviazione cloud di Azure. StorSimple Virtual Array organizza automaticamente i dati in livelli in base a una mappa termica che tiene traccia dell'uso corrente, dell'età e delle relazioni con altri dati. I dati più attivi (i più caldi) vengono archiviati in locale, mentre quelli meno attivi e non attivi vengono automaticamente migrati nel cloud. (Tutti i backup vengono archiviati nel cloud.) StorSimple ordina e riorganizza i dati e le assegnazioni delle risorse di archiviazione in base ai cambiamenti dei modelli di utilizzo. Alcune informazioni, ad esempio, possono diventare meno attive nel corso del tempo. Man mano che diminuisce la loro attività, vengono distribuite nel cloud. Se gli stessi dati diventano nuovamente attivi, vengono reinseriti nell'array di archiviazione.

Viene garantito uno spazio a livello locale per i dati di una particolare condivisione a livelli o un volume specifico (circa il 10% dello spazio totale con provisioning per la condivisione o il volume). Anche se in questo modo si riduce la memoria disponibile nell'array virtuale per una data condivisione o un dato volume, si garantisce che la suddivisione in livelli di una condivisione o di un volume non sia interessata dalle esigenze di suddivisione in livelli di altre condivisioni o altri volumi. Pertanto, un carico di lavoro molto pesante in una condivisione o in un volume non può forzare tutti gli altri carichi di lavoro nel cloud. 

![Suddivisione automatica in livelli dell'archiviazione](./media/storsimple-ova-overview/automatic-storage-tiering.png)

> [!NOTE]
> È possibile specificare un volume come aggiunto in locale. In questo caso i dati rimangono nell'array virtuale e non vengono mai suddivisi in livelli nel cloud. Per altre informazioni, vedere [Condivisioni e volumi aggiunti in locale](#locally-pinned-shares-and-volumes).
> 
> 

### <a name="locally-pinned-shares-and-volumes"></a>Condivisioni e volumi aggiunti in locale
È possibile creare condivisioni e volumi appropriati come aggiunti in locale. Questa funzionalità garantisce che i dati richiesti dalle applicazioni critiche rimangano nell'array virtuale e non vengano mai suddivisi in livelli nel cloud. Condivisioni e volumi aggiunti in locale presentano le funzionalità seguenti: 

* Non sono soggetti a latenze cloud o a problemi di connettività.
* Traggono ugualmente vantaggio dalle funzionalità di backup cloud e ripristino di emergenza di StorSimple.

È possibile ripristinare una condivisione o un volume aggiunti in locale come fossero a livelli o una condivisione o un volume a livelli come aggiunti in locale. 

Per altre informazioni sui volumi aggiunti in locale, vedere [Usare il servizio Gestione dispositivi StorSimple per visualizzare i volumi sull'array virtuale StorSimple ](storsimple-virtual-array-manage-volumes.md).

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>Deduplicazione e compressione dei dati a livelli o sottoposti a backup nel cloud
StorSimple usa deduplicazione e compressione dei dati per ridurre ulteriormente i requisiti di archiviazione nel cloud. La deduplicazione riduce la quantità complessiva di dati archiviati eliminando la ridondanza nel set di dati archiviato. Se i dati subiscono delle variazioni, StorSimple ignora quelli non modificati e acquisisce soltanto le differenze. Inoltre, StorSimple riduce la quantità di dati archiviati identificando e rimuovendo le informazioni duplicate. 

> [!NOTE]
> I dati archiviati nell'array virtuale non sono deduplicati o compressi. Deduplicazione e compressione si verificano un attimo prima dell'invio dei dati al cloud.
> 
> 

### <a name="scheduled-and-on-demand-backups"></a>Backup pianificati e su richiesta
Le funzionalità di protezione dei dati di StorSimple consentono di creare backup su richiesta. Inoltre, una pianificazione di backup predefinita garantisce il backup dei dati ogni giorno. Si eseguono backup sotto forma di snapshot incrementali che vengono archiviati nel cloud. Gli snapshot, che registrano solo le modifiche apportate dall'ultimo backup, possono essere creati e ripristinati rapidamente. Gli snapshot possono essere fondamentali in scenari di ripristino di emergenza perché sostituiscono i sistemi di archiviazione secondaria (come il backup su nastro) e consentono di ripristinare i dati nel data center o in siti alternativi, se necessario.

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come [preparare il portale dell'array virtuale](storsimple-virtual-array-deploy1-portal-prep.md).




<!--HONumber=Dec16_HO2-->


