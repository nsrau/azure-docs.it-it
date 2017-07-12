---
title: Eseguire la replica di macchine virtuali VMware e server fisici in Azure (legacy classico) | Documentazione Microsoft
description: Illustra come eseguire la replica di macchine virtuali locali e server fisici Windows o Linux in Azure usando Azure Site Recovery in una distribuzione legacy nel portale classico.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: f980fb52-8ec2-4dd9-85e2-8e52e449f1ba
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: d686d411b0877d2e4aef992e6b28da2a6f03b66e
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017


---
<a id="replicate-vmware-virtual-machines-and-physical-servers-to-azure-with-azure-site-recovery-using-the-classic-portal-legacy" class="xliff"></a>

# Eseguire la replica di macchine virtuali VMware e server fisici in Azure con Azure Site Recovery tramite il portale classico (legacy)
> [!div class="op_single_selector"]
> * [Portale di Azure](site-recovery-vmware-to-azure.md)
> * [Portale classico](site-recovery-vmware-to-azure-classic.md)
> * [Portale classico (legacy)](site-recovery-vmware-to-azure-classic-legacy.md)
>
>

Benvenuti in Azure Site Recovery. Questo articolo illustra una distribuzione legacy per eseguire la replica di macchine virtuali VMware locali o server fisici Windows o Linux in Azure usando Azure Site Recovery nel portale classico.

<a id="overview" class="xliff"></a>

## Overview
Le organizzazioni necessitano di una strategia di continuità aziendale e ripristino di emergenza per determinare come app, carichi di lavoro e dati possano rimanere in esecuzione e disponibili durante i periodi di inattività, pianificati o meno, e come ripristinare le normali condizioni di lavoro il prima possibile. La strategia di continuità aziendale e ripristino di emergenza deve garantire la sicurezza dei dati aziendali e la possibilità di recuperarli, oltre alla disponibilità costante dei carichi di lavoro in caso di emergenza.

Il servizio Azure Site Recovery contribuisce alla strategia BCDR orchestrando la replica dei server fisici locali e delle macchine virtuali sul cloud (Azure) o in un data center secondario. In caso di interruzioni nella località primaria, verrà eseguito il failover alla località secondaria per mantenere disponibili app e carichi di lavoro. Quando la località primaria sarà di nuovo operativa, si tornerà a tale località. Per altre informazioni, vedere [Che cos'è Site Recovery?](site-recovery-overview.md)

> [!WARNING]
> Questo articolo contiene **istruzioni legacy**. Non usare queste informazioni per nuove distribuzioni. Per distribuire Site Recovery nel portale di Azure, seguire invece [queste istruzioni](site-recovery-vmware-to-azure.md). Per configurare la distribuzione avanzata nel portale classico, [usare queste istruzioni](site-recovery-vmware-to-azure-classic.md). Se è già stata eseguita la distribuzione usando il metodo descritto in questo articolo, è consigliabile eseguire la migrazione alla distribuzione avanzata nel portale classico.
>
>

<a id="migrate-to-the-enhanced-deployment" class="xliff"></a>

## Eseguire la migrazione alla distribuzione avanzata
Questa sezione è applicabile solo se è già stato distribuito Site Recovery seguendo le istruzioni contenute in questo articolo.

Per eseguire la migrazione alla distribuzione esistente, sarà necessario:

1. Distribuire nuovi componenti di Site Recovery nel sito locale.
2. Configurare le credenziali per l'individuazione automatica di macchine virtuali VMware nel nuovo server di configurazione.
3. Trovare i server VMware con il nuovo server di configurazione.
4. Creare un nuovo gruppo protezione dati con il nuovo server di configurazione.

Prima di iniziare:

* È consigliabile impostare una finestra di manutenzione per la migrazione.
* L'opzione **Esegui la migrazione delle macchine virtuali** è disponibile solo se sono presenti gruppi di protezione esistenti creati durante una distribuzione legacy.
* Dopo aver completato i passaggi della migrazione, possono essere necessari 15 minuti o più per aggiornare le credenziali e per trovare e aggiornare le macchine virtuali da aggiungere a un gruppo di protezione. È possibile aggiornare manualmente anziché attendere.

Eseguire la migrazione nel modo seguente:

1. Vedere la sezione relativa alla [distribuzione avanzata nel portale classico](site-recovery-vmware-to-azure-classic.md#enhanced-deployment). Vedere le sezioni relative all'[architettura](site-recovery-vmware-to-azure-classic.md#scenario-architecture) avanzata e ai [prerequisiti](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment).
2. Disinstallare il servizio Mobility dai computer di cui si sta eseguendo la replica. Una nuova versione del servizio verrà installata nei computer nel momento in cui verranno aggiunti al nuovo gruppo di protezione.
3. Scaricare una [chiave di registrazione dell'insieme di credenziali](site-recovery-vmware-to-azure-classic.md#step-4-download-a-vault-registration-key) ed eseguire la [procedura guidata per la configurazione unificata](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server) per installare i componenti server di configurazione, server di elaborazione e server di destinazione master. Altre informazioni sulla [pianificazione della capacità](site-recovery-vmware-to-azure-classic.md#capacity-planning).
4. [Impostare le credenziali](site-recovery-vmware-to-azure-classic.md#step-6-set-up-credentials-for-the-vcenter-server) che Site Recovery può usare per accedere al server VMware e rilevare automaticamente le macchine virtuali VMware. Vedere la sezione relativa alle [autorizzazioni necessarie](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
5. Aggiungere [server vCenter o host vSphere](site-recovery-vmware-to-azure-classic.md#step-7-add-vcenter-servers-and-esxi-hosts). Possono essere necessari 15 minuti o più perché i server vengano visualizzati nel portale di Site Recovery.
6. Creare un [nuovo gruppo protezione dati](site-recovery-vmware-to-azure-classic.md#step-8-create-a-protection-group). Possono essere necessari fino a 15 minuti per l'aggiornamento del portale in modo che le macchine virtuali vengano individuate e visualizzate. Se non si vuole attendere, è possibile evidenziare il nome del server di gestione, senza farvi clic sopra, e fare clic su **Aggiorna**.
7. Nel nuovo gruppo protezione dati fare clic su **Esegui la migrazione delle macchine virtuali**.

    ![Aggiungi account](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration1.png)
8. In **Selezionare le macchine virtuali** selezionare il gruppo protezione dati dal quale eseguire la migrazione e le macchine di cui eseguire la migrazione.

    ![Aggiungi account](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration2.png)
9. In **Configurare le impostazioni di destinazione** specificare se si vogliono usare le stesse impostazioni per tutte le macchine e selezionare il server di elaborazione e l'account di archiviazione di Azure. Se non è disponibile un server di elaborazione separato, sarà l'indirizzo IP del server di configurazione.

    ![Aggiungi account](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration3.png)

    > [AZURE.NOTE] [La migrazione degli account di archiviazione](../resource-group-move-resources.md) all'interno dei gruppi di risorse con la stessa sottoscrizione o all'interno delle sottoscrizioni non è supportata per gli account di archiviazione usati per la distribuzione di Site Recovery.

1. In **Specificare gli account**selezionare l'account creato per consentire al server di elaborazione di accedere al computer ed effettuare il push della nuova versione del servizio Mobility.

   ![Aggiungi account](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration4.png)
2. Site Recovery eseguirà la migrazione dei dati replicati all'account di archiviazione di Azure fornito dall'utente. Facoltativamente è possibile usare nuovamente l'account di archiviazione usato nella distribuzione legacy.
3. Al termine del processo, le macchine virtuali verranno sincronizzate automaticamente. Al termine della sincronizzazione, eliminare le macchine virtuali dal gruppo protezione dati legacy.
4. Dopo che è stata eseguita la migrazione di tutti i computer, eliminare il gruppo protezione dati legacy.
5. Ricordarsi di specificare le proprietà di failover per i computer e le impostazioni di rete di Azure al termine della sincronizzazione.
6. Se sono presenti piani di ripristino esistenti, è possibile eseguirne la migrazione alla distribuzione avanzata con l'opzione **Esegui la migrazione del piano di ripristino** . Questa operazione deve essere eseguita solo dopo la migrazione di tutti i computer protetti.

   ![Aggiungi account](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration5.png)

> [!NOTE]
> Dopo aver completato la migrazione, proseguire con l' [articolo avanzato](site-recovery-vmware-to-azure-classic.md). La parte restante di questo articolo non è più rilevante e non è quindi necessario seguire le altre procedure descritte**.
>
>

<a id="what-do-i-need" class="xliff"></a>

## Che cosa occorre?
Questo diagramma illustra i componenti della distribuzione.

![Nuovo insieme di credenziali](./media/site-recovery-vmware-to-azure-classic-legacy/architecture.png)

Sono necessari gli elementi seguenti:

| **Componente** | **Distribuzione** | **Dettagli** |
| --- | --- | --- |
| **Server di configurazione** |Una macchina virtuale A3 standard di Azure nella stessa sottoscrizione di Site Recovery. |Il server di configurazione coordina le comunicazioni tra i computer protetti, il server di elaborazione e i server di destinazione master in Azure. Configura la replica e coordina il ripristino in Azure quando si verifica il failover. |
| **Server master di destinazione** |Una macchina virtuale di Azure: come server Windows basato su un'immagine della raccolta di Windows Server 2012 R2, per proteggere i computer Windows, o come server Linux basato su un'immagine della raccolta di OpenLogic CentOS 6.6, per proteggere i computer Linux.<br/><br/> Per le dimensioni sono disponibili tre opzioni: A4 Standard, D14 Standard e DS4 Standard.<br/><br/> Il server è connesso alla stessa rete di Azure in cui risiede il server di configurazione.<br/><br/> Viene configurato nel portale di Site Recovery. |Riceve e mantiene i dati replicati dai computer protetti tramite VHD collegati creati nell'archiviazione BLOB dell'account di archiviazione di Azure.<br/><br/> Selezionare DS4 Standard in modo specifico per la configurazione della protezione per carichi di lavoro che richiedono prestazioni elevate omogenee e a bassa latenza con l'account di archiviazione Premium. |
| **Server di elaborazione** |Un server virtuale o fisico locale che esegue Windows Server 2012 R2.<br/><br/> È consigliabile collocarlo nella stessa rete e nello stesso segmento di LAN in cui risiedono i computer da proteggere, ma è possibile eseguirlo in una rete diversa, a condizione che i computer protetti possano avere la visibilità di rete L3 per tale server.<br/><br/> Viene impostato e registrato nel server di configurazione nel portale di Site Recovery. |I computer protetti inviano dati di replica al server di elaborazione locale. Dispone di una cache basata su disco per la memorizzazione dei dati di replica ricevuti. Esegue una serie di azioni su tali dati.<br/><br/> Ottimizza i dati attraverso la memorizzazione nella cache, la compressione e la crittografia prima di inviarli al server master di destinazione.<br/><br/> Gestisce l'installazione push del servizio Mobility.<br/><br/> Esegue l'individuazione automatica delle macchine virtuali VMware. |
| **Computer locali** |Macchine virtuali VMware locali oppure server fisici che eseguono Windows o Linux. |Vengono configurate le impostazioni di replica applicabili a uno o più computer. È possibile eseguire il failover di un singolo computer o, più comunemente, di più computer raccolti in un piano di ripristino. |
| **Servizio Mobility** |Viene installato in ogni macchina virtuale o server fisico da proteggere.<br/><br/> È possibile installarlo manualmente oppure effettuarne il push e l'installazione automatica tramite il server di elaborazione quando si abilita la replica per un computer. |Il servizio Mobility invia dati al server di elaborazione durante la replica iniziale (risincronizzazione). Quando lo stato del computer è protetto, al termine della risincronizzazione, il servizio Mobility acquisisce le scritture su disco in memoria e le invia al server di elaborazione. La coerenza delle applicazioni per i server Windows si ottiene tramite VSS. |
| **Insieme di credenziali di Azure Site Recovery** |Viene creato un insieme di credenziali di Site Recovery con una sottoscrizione di Azure e al suo interno vengono registrati i server. |L'insieme di credenziali coordina e orchestra la replica, il failover e il ripristino dei dati tra il sito locale e Azure. |
| **Meccanismo di replica** |**Tramite Internet**: comunica e replica i dati dai server locali protetti in Azure usando un canale SSL/TLS protetto tramite Internet. Questa è l'opzione predefinita.<br/><br/> **VPN/ExpressRoute**: comunica e replica i dati tra i server locali e Azure tramite una connessione VPN. È necessario configurare una VPN da sito a sito o una connessione ExpressRoute tra la rete di Azure e il sito locale.<br/><br/> Viene selezionata la modalità di replica durante la distribuzione di Site Recovery. Dopo aver configurato il meccanismo, non è possibile modificarlo senza influire sulla replica dei computer esistenti. |Nessuna delle due opzioni richiede l'apertura delle porte di rete in ingresso nei computer protetti. Tutte le comunicazioni di rete vengono avviate dal sito locale. |

<a id="capacity-planning" class="xliff"></a>

## pianificazione della capacità
Di seguito sono elencate le aree principali da prendere in considerazione:

* **Ambiente di origine**- Infrastruttura VMware, impostazioni dei computer di origine e requisiti.
* **Server dei componenti**- Server di elaborazione, server di configurazione e server di destinazione master

<a id="considerations-for-the-source-environment" class="xliff"></a>

### Considerazioni per l'ambiente di origine
* **Dimensione massima del disco**- La dimensione massima del disco che attualmente può essere collegato a una macchina virtuale è 1 TB. Pertanto, anche la dimensione massima di un disco di origine che può essere replicato è limitata a 1 TB.
* **Dimensione massima per ogni origine**- La dimensione massima di un singolo computer di origine è 31 TB (con 31 dischi) e con il provisioning di un'istanza D14 per il server di destinazione master.
* **Numero di origini per ogni server di destinazione master** - È possibile proteggere più computer di origine con un singolo server di destinazione master. Tuttavia, non è possibile proteggere un singolo computer di origine in più server di destinazione master. Infatti, quando i dischi vengono replicati, nell'archivio BLOB di Azure viene creato un VHD che rispecchia le dimensioni del disco e che viene collegato come disco dati al server di destinazione master.  
* **Frequenza di modifica giornaliera massima per ogni origine**- Esistono tre fattori da considerare quando si valuta la frequenza di modifica consigliata per ogni origine. Per le considerazioni relative alla destinazione, sono necessarie due operazioni IOPS sul disco di destinazione per ogni operazione nell'origine. Questo avviene perché vengono eseguite un'operazione di lettura dei dati precedenti e un'operazione di scrittura dei nuovi dati sul disco di destinazione.
  * **Frequenza di modifica giornaliera supportata dal server di elaborazione**- Un computer di origine non può estendersi su più server di elaborazione. Un singolo server di elaborazione può supportare fino a 1 TB di frequenza di modifica giornaliera. Pertanto 1 TB è la frequenza massima di modifica giornaliera dei dati supportata per una macchina di origine.
  * **Velocità effettiva massima supportata dal disco di destinazione**- La varianza massima per ogni disco di origine non può essere superiore a 144 GB al giorno (con una dimensione di scrittura di 8 K). Vedere la tabella nella sezione relativa al server di destinazione master per la velocità effettiva e gli IOPS della destinazione per le varie dimensioni di scrittura. Questo numero deve essere diviso per due, perché ogni operazione IOP di origine genera 2 IOPS sul disco di destinazione. Fare riferimento a [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](../storage/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks) durante la configurazione della destinazione per gli account di archiviazione Premium.
  * **Velocità effettiva massima supportata dall'account di archiviazione**- Un'origine non può estendersi su più account di archiviazione. Dato che un account di archiviazione accetta al massimo 20.000 richieste al secondo e che ogni IOP di origine genera 2 IOPS nel server di destinazione master, è consigliabile mantenere il numero di IOPS nell'origine su 10.000. Fare riferimento a [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](../storage/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks) durante la configurazione dell'origine per gli account di archiviazione Premium.

<a id="considerations-for-component-servers" class="xliff"></a>

### Considerazioni relative ai server dei componenti
Nella tabella 1 sono riepilogate le dimensioni delle macchine virtuali per i server di configurazione e i server di destinazione master.

| **Componente** | **Istanze di Azure distribuite** | **Core** | **Memoria** | **Numero massimo di dischi** | **Dimensioni disco** |
| --- | --- | --- | --- | --- | --- |
| Server di configurazione |A3 standard |4 |7 GB |8 |1023 GB |
| Server master di destinazione |A4 standard |8 |14 GB |16 |1023 GB |
| D14 standard |16 |112 GB |32 |1023 GB | |
| DS4 standard |8 |28 GB |16 |1023 GB | |

**Tabella 1**

<a id="process-server-considerations" class="xliff"></a>

#### Considerazioni sui server di elaborazione
In genere il dimensionamento del server di elaborazione dipende dalla frequenza di modifica giornaliera per tutti i carichi di lavoro protetti.

* È necessaria una potenza di calcolo sufficiente per eseguire operazioni quali la crittografia e la compressione inline.
* Il server di elaborazione usa una cache basata su disco. Verificare che siano disponibili lo spazio della cache e la velocità effettiva del disco consigliate per facilitare le modifiche dei dati archiviate in caso di interruzione o collo di bottiglia della rete.
* Verificare che sia disponibile larghezza di banda sufficiente, in modo che il server di elaborazione possa caricare i dati nel server di destinazione master per garantire la protezione dei dati continua.

Nella tabella 2 sono riepilogate le linee guida per il server di elaborazione.

| **Frequenza di modifica dei dati** | **CPU** | **Memoria** | **Dimensione disco cache** | **Velocità effettiva disco cache** | **Ingresso/uscita larghezza di banda** |
| --- | --- | --- | --- | --- | --- |
| Meno di 300 GB |4 vCPU (2 socket * 2 core a 2,5 GHz) |4 GB |600 GB |Da 7 a 10 MB al secondo |30 Mbps/21 Mbps |
| Da 300 a 600 GB |8 vCPU (2 socket * 4 core a 2,5 GHz) |6 GB |600 GB |Da 11 a 15 MB al secondo |60 Mbps/42 Mbps |
| Da 600 GB a 1 TB |12 vCPU (2 socket * 6 core a 2,5 GHz) |8 GB |600 GB |Da 16 a 20 MB al secondo |100 Mbps/70 Mbps |
| Più di 1 TB |Distribuire un altro server di elaborazione | | | | |

**Tabella 2**

Dove:

* Per ingresso si intende la larghezza di banda in download (rete Intranet tra l'origine e il server di elaborazione).
* Per uscita si intende la larghezza di banda in caricamento (rete Internet tra il server di elaborazione e il server di destinazione master). I valori relativi all'uscita presuppongono una compressione media del server di elaborazione del 30%.
* Per il disco cache è consigliabile un disco separato dal sistema operativo di almeno 128 GB per tutti i server di elaborazione.
* Per la velocità effettiva del disco cache è stata usata l'archiviazione seguente a scopo di benchmark: 8 unità SAS a 10.000 RPM con configurazione RAID 10.

<a id="configuration-server-considerations" class="xliff"></a>

#### Considerazioni relative ai server di configurazione
Ogni server di configurazione può supportare fino a 100 computer di origine con 3 o 4 volumi. Se la distribuzione ha dimensioni maggiori, è consigliabile distribuire un altro server di configurazione. Per le proprietà della macchina virtuale predefinita del server di configurazione, vedere la tabella 1.

<a id="master-target-server-and-storage-account-considerations" class="xliff"></a>

#### Considerazioni relative al server di destinazione master e all'account di archiviazione
Per ogni server di destinazione master l'archiviazione include un disco del sistema operativo, un volume di conservazione e dischi dati. L'unità di conservazione mantiene il journal delle modifiche dei dischi per la durata dell'intervallo di conservazione definito nel portale di Site Recovery.  Per le proprietà della macchina virtuale del server di destinazione master, fare riferimento alla tabella 1. Nella tabella 3 viene illustrato l'uso dei dischi per A4.

| **Istanza** | **Disco del sistema operativo** | **Conservazione** | **Dischi dati** |
| --- | --- | --- | --- |
| **Conservazione** |**Dischi dati** | | |
| A4 standard |1 disco (1 * 1023 GB) |1 disco (1 * 1023 GB) |15 dischi (15 * 1023 GB) |
| D14 standard |1 disco (1 * 1023 GB) |1 disco (1 * 1023 GB) |31 dischi (15 * 1023 GB) |
| DS4 standard |1 disco (1 * 1023 GB) |1 disco (1 * 1023 GB) |15 dischi (15 * 1023 GB) |

**Tabella 3**

La pianificazione della capacità per il server di destinazione master dipende da:

* Limitazioni e prestazioni dell'archiviazione di Azure
  * Il numero massimo di dischi a utilizzo elevato per una VM di livello Standard è di circa 40 (20.000/500 IOPS per disco) in un singolo account di archiviazione. Informazioni sugli [obiettivi di scalabilità per account di archiviazione Standard](../storage/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks) e [account di archiviazione Premium](../storage/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks).
* Frequenza di modifica giornaliera
* Archiviazione del volume di conservazione.

Si noti che:

* Un'origine non può estendersi su più account di archiviazione. Questo vale per il disco dati assegnato agli account di archiviazione selezionati quando si configura la protezione. I dischi del sistema operativo e di conservazione in genere sono assegnati all'account di archiviazione distribuito automaticamente.
* Il volume di archiviazione di conservazione richiesto dipende dalla frequenza di modifica giornaliera e dal numero di giorni di conservazione. L'archiviazione di conservazione richiesta per ogni server di destinazione master è uguale alla varianza totale dall'origine al giorno per il numero di giorni di conservazione.
* Ogni server di destinazione master ha un solo volume di conservazione. Il volume di conservazione è condiviso tra i dischi collegati al server di destinazione master. ad esempio:
  * Se è presente una macchina di origine con 5 dischi e ogni disco genera 120 IOPS (dimensioni di 8K) nell'origine, questo si traduce in 240 IOPS per disco (2 operazioni sul disco di destinazione per ogni IO di origine). 240 IOPS è entro il limite di IOPS per disco di Azure, pari a 500.
  * Nel volume di conservazione questo diventa 120 * 5 = 600 IOPS e ciò può rappresentare un collo di bottiglia. In questo scenario, una strategia valida potrebbe essere aggiungere ulteriori dischi al volume di conservazione ed estenderlo, come una configurazione di striping RAID. Ciò consente di migliorare le prestazioni, poiché le operazioni di IOPS vengono distribuite in più unità. Il numero di unità da aggiungere al volume di conservazione sarà come segue:
    * IOPS totali dall'ambiente di origine / 500
    * Varianza totale al giorno dall'ambiente di origine (senza compressione) / 287 GB. 287 GB è la velocità effettiva massima supportata da un disco di destinazione al giorno. Questa metrica varia in base alla dimensione di scrittura con un fattore di 8K, poiché in questo caso 8K è la dimensione di scrittura presupposta. Ad esempio, se la dimensione di scrittura è 4K, la velocità effettiva sarà 287/2. Se la dimensione di scrittura è 16K, la velocità effettiva sarà 287*2.
* Il numero di account di archiviazione richiesto = IOPS di origine totali/10000.

<a id="before-you-start" class="xliff"></a>

## Prima di iniziare
| **Componente** | **Requisiti** | **Dettagli** |
| --- | --- | --- |
| **Account di Azure** |È necessario un account [Microsoft Azure](https://azure.microsoft.com/) . È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/). | |
| **Archiviazione di Azure** |Per archiviare i dati replicati è necessario un account di archiviazione di Azure.<br/><br/> Deve trattarsi di un [account di archiviazione con ridondanza geografica Standard](../storage/storage-redundancy.md#geo-redundant-storage) oppure di un [account di archiviazione Premium](../storage/storage-premium-storage.md).<br/><br/> L'account deve trovarsi nella stessa area geografica del servizio Azure Site Recovery e deve essere associato alla stessa sottoscrizione. Non è supportato lo spostamento degli account di archiviazione creati con il [nuovo portale di Azure](../storage/storage-create-storage-account.md) tra gruppi di risorse.<br/><br/> Per altre informazioni, vedere [Introduzione ad Archiviazione di Microsoft Azure](../storage/storage-introduction.md). | |
| **Rete virtuale di Azure** |Sarà necessaria una rete virtuale di Azure in cui saranno distribuiti il server di configurazione e il server di destinazione master. Dovrà trovarsi nella stessa sottoscrizione e nella stessa area dell'insieme di credenziali di Azure Site Recovery. Se si desidera replicare i dati tramite una connessione VPN o ExpressRoute, la rete virtuale di Azure deve essere connessa alla rete locale tramite una connessione ExpressRoute o una VPN da sito a sito. | |
| **Risorse di Azure** |Accertarsi di avere risorse di Azure sufficienti per distribuire tutti i componenti. Per altre informazioni, vedere [Limiti relativi alle sottoscrizioni di Azure](../azure-subscription-service-limits.md). | |
| **Macchine virtuali di Azure** |Le macchine virtuali da proteggere devono essere conformi ai [prerequisiti di Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).<br/><br/> **Numero di dischi**: in un singolo server protetto è possibile supportare un massimo di 31 dischi.<br/><br/> **Dimensioni del disco**: la capacità dei singoli dischi non deve superare 1023 GB.<br/><br/> **Clustering**: i server di cluster non sono supportati.<br/><br/> **Avvio**: l'avvio UEFI (Unified Extensible Firmware Interface)/EFI (Extensible Firmware Interface) non è supportato.<br/><br/> **Volumi**: i volumi crittografati con Bitlocker non sono supportati.<br/><br/> **Nomi dei server**: i nomi devono contenere da 1 a 63 caratteri (lettere, numeri e trattini). Il nome deve iniziare e terminare con una lettera o un numero. Dopo aver protetto un computer, è possibile modificare il nome di Azure. | |
| **Server di configurazione** |Nella sottoscrizione per il server di configurazione viene creata una macchina virtuale A3 Standard basata su un'immagine della raccolta di Windows Server 2012 R2 con Azure Site Recovery. Viene creata come prima istanza in un nuovo servizio cloud. Se si seleziona Internet pubblico come tipo di connettività per il server di configurazione, il servizio cloud viene creato con un indirizzo IP pubblico riservato.<br/><br/> Il percorso di installazione può contenere solo caratteri inglesi. | |
| **Server master di destinazione** |Macchina virtuale di Azure, A4 Standard, D14 Standard o DS4 Standard.<br/><br/> Il percorso di installazione può contenere solo caratteri inglesi. Ad esempio, il percorso per un server di destinazione master che esegue Linux deve essere **/usr/local/ASR** . | |
| **Server di elaborazione** |È possibile distribuire il server di elaborazione in macchine virtuali o computer fisici che eseguono Windows Server 2012 R2 con gli aggiornamenti più recenti. Eseguire l'installazione in C:/.<br/><br/> Collocare il server nella stessa rete e subnet dei computer da proteggere.<br/><br/> Installare VMware vSphere CLI 5.5.0 nel server di elaborazione. Il componente VMware vSphere dell'interfaccia della riga di comando è necessario nel server di elaborazione per trovare le macchine virtuali gestite da un server vCenter o le macchine virtuali in esecuzione in un host ESXi.<br/><br/> Il percorso di installazione può contenere solo caratteri inglesi.<br/><br/> Il file system ReFS non è supportato. | |
| **VMware** |Server VMware vCenter per la gestione degli hypervisor VMware vSphere. Deve eseguire vCenter versione 5.1 o 5.5 con gli aggiornamenti più recenti.<br/><br/> Uno o più hypervisor vSphere contenenti le macchine virtuali VMWare da proteggere. L'hypervisor deve eseguire ESX/ESXi versione 5.1 o 5.5 con gli aggiornamenti più recenti.<br/><br/> Nelle macchine virtuali VMware devono essere installati e in esecuzione gli strumenti VMware. | |
| **Computer Windows** |Per i server fisici o le macchine virtuali VMWare protette che eseguono Windows esistono diversi requisiti.<br/><br/> Sistema operativo a 64 bit supportato: **Windows Server 2012 R2**, **Windows Server 2012** o **Windows Server 2008 R2 con SP1 o versioni successive**.<br/><br/> Il nome host, i punti di montaggio, i nomi dei dispositivi e il percorso del sistema Windows, ad esempio C:\Windows, possono contenere solo caratteri inglesi.<br/><br/> Il sistema operativo dovrà essere installato nell'unità C:\.<br/><br/> Sono supportati solo i dischi di base. I dischi dinamici non sono supportati.<br/><br/> Le regole del firewall nei computer protetti devono consentire a tali computer di raggiungere i server di configurazione e di destinazione master in Azure.<p>È necessario specificare un account amministratore, che deve essere un amministratore locale nel computer Windows, per l'installazione push del servizio Mobility nei server Windows. Se l'account specificato non è un account di dominio, si dovrà disabilitare il Controllo dell'accesso utente remoto nel computer locale. A tale scopo, aggiungere la voce del Registro di sistema DWORD LocalAccountTokenFilterPolicy con un valore di 1 in HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System. Per aggiungere la voce del Registro di sistema da un'interfaccia della riga di comando, aprire il prompt dei comandi o PowerShell e digitare **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**. [Altre informazioni](https://msdn.microsoft.com/library/aa826699.aspx) sul controllo di accesso.<br/><br/> Dopo il failover, per connettersi alle macchine virtuali Windows in Azure con Desktop remoto, accertarsi che per il computer locale sia abilitato Desktop remoto. Se non ci si connette tramite VPN, le regole firewall devono consentire le connessioni Desktop remoto tramite Internet. | |
| **Computer Linux** |Sistema operativo a 64 bit supportato: **Centos 6.4, 6.5 o 6.6**, **Oracle Enterprise Linux 6.4 o 6.5 che esegue il kernel compatibile Red Hat o Unbreakable Enterprise Kernel versione 3 (UEK3)**, **SUSE Linux Enterprise Server 11 SP3**.<br/><br/> Le regole del firewall nei computer protetti devono consentire a tali computer di raggiungere i server di configurazione e di destinazione master in Azure.<br/><br/> I file /etc/hosts nei computer protetti devono contenere le voci che eseguono il mapping del nome host locale agli indirizzi IP associati a tutte le schede NIC. <br/><br/> Per connettersi a una macchina virtuale di Azure che esegue Linux dopo il failover usando un client Secure Shell (SSH), accertarsi che il servizio Secure Shell nel computer protetto sia impostato per l'avvio automatico all'avvio del sistema e che le regole del firewall permettano una connessione SSH a tale computer.<br/><br/> Nome host, punti di montaggio, nomi dispositivo e percorsi di sistema di Linux e nomi file (ad esempio /etc/, /usr) dovranno essere specificati solo con caratteri dell'alfabeto latino.<br/><br/> La protezione può essere abilitata per i computer locali con le risorse di archiviazione indicate di seguito:<br>File system: EXT3, ETX4, ReiserFS, XFS<br>Software con percorsi multipli: mapper dispositivi (con percorsi multipli)<br>Gestore volumi: LVM2<br>I server fisici con archiviazione del controller HP CCISS non sono supportati. | |
| **Terze parti** |Alcuni componenti della distribuzione in questo scenario dipendono da software di terze parti per funzionare correttamente. Per un elenco completo, vedere [Informazioni e comunicazioni sul software di terze parti](#third-party) | |

<a id="network-connectivity" class="xliff"></a>

### Connettività di rete
Sono disponibili due opzioni quando si configura la connettività di rete tra il sito locale e la rete virtuale di Azure in cui vengono distribuiti i componenti dell'infrastruttura (server di configurazione, server di destinazione master). È necessario decidere quale opzione di connettività di rete usare prima di distribuire il server di configurazione. È necessario scegliere questa impostazione in fase di distribuzione. L'impostazione non può essere modificata in seguito.

**Internet:** le comunicazioni e la replica dei dati tra i server locali, come il server di elaborazione e i computer protetti, e i server dei componenti dell'infrastruttura di Azure, come il server di configurazione e il server di destinazione master, avvengono tramite una connessione SSL/TLS protetta dal sito locale agli endpoint pubblici nel server di configurazione e nel server di destinazione master. L'unica eccezione è la connessione tra il server di elaborazione e il server di destinazione master sulla porta TCP 9080 che è non crittografata. Solo le informazioni di controllo relative al protocollo di replica per la configurazione della replica vengono scambiate tramite questa connessione.

![Diagramma di distribuzione Internet](./media/site-recovery-vmware-to-azure-classic-legacy/internet-deployment.png)

**VPN:**le comunicazioni e la replica dei dati tra i server locali, come il server di elaborazione e i computer protetti, e i server dei componenti dell'infrastruttura di Azure, come il server di configurazione e il server di destinazione master, avvengono tramite una connessione VPN tra la rete locale e la rete virtuale di Azure in cui vengono distribuiti il server di configurazione e i server di destinazione master. Assicurarsi che la rete locale sia connessa alla rete virtuale di Azure tramite una connessione ExpressRoute o una connessione VPN da sito a sito.

![Diagramma di distribuzione VPN](./media/site-recovery-vmware-to-azure-classic-legacy/vpn-deployment.png)

<a id="step-1-create-a-vault" class="xliff"></a>

## Passaggio 1: Creare un insieme di credenziali
1. Accedere al [portale di gestione](https://portal.azure.com).
2. Espandere **Servizi dati** > **Servizi di ripristino** e fare clic su **Insieme di credenziali di Site Recovery**.
3. Fare clic su **Creare nuovo** > **Creazione rapida**.
4. In **Nome**immettere un nome descrittivo per identificare l'insieme di credenziali.
5. In **Region**selezionare l'area geografica per l'insieme di credenziali. Per informazioni sulla disponibilità a livello geografico e sulle aree supportate, vedere la pagina relativa ai [dettagli sui prezzi per Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/)
6. Fare clic su **Create vault**.

    ![Nuovo insieme di credenziali](./media/site-recovery-vmware-to-azure-classic-legacy/quick-start-create-vault.png)

Controllare la barra di stato per verificare che l'insieme di credenziali sia stato creato correttamente. L'insieme di credenziali verrà elencato come **Attivo** nella pagina principale di **Servizi di ripristino**.

<a id="step-2-deploy-a-configuration-server" class="xliff"></a>

## Passaggio 2: Distribuire un server di configurazione
<a id="configure-server-settings" class="xliff"></a>

### Configurare le impostazioni del server
1. Nella pagina **Servizi di ripristino** fare clic sull'insieme di credenziali per aprire la pagina Avvio rapido. La pagina Avvio rapido può anche essere aperta in qualsiasi momento tramite l'icona.

    ![Quick Start Icon](./media/site-recovery-vmware-to-azure-classic-legacy/quick-start-icon.png)
2. Nell'elenco a discesa selezionare **Tra un sito locale con server VMware/fisici e Azure**.
3. In **Preparare le risorse (Azure) di destinazione** fare clic su **Distribuisci server di configurazione**.

    ![Distribuisci server di configurazione](./media/site-recovery-vmware-to-azure-classic-legacy/deploy-cs2.png)
4. In **Dettagli del nuovo server di configurazione** specificare:

   * Un nome per il server di configurazione e le credenziali per la connessione.
   * Nell'elenco a discesa del tipo di connettività di rete selezionare **Internet pubblico** o **VPN**. Si noti che non è possibile modificare questa impostazione dopo averla applicata.
   * Selezionare la rete di Azure in cui dovrà essere collocato il server. Se si usa una VPN, assicurarsi che la rete di Azure sia connessa alla rete locale come previsto.
   * Specificare l'indirizzo IP interno e la subnet da assegnare al server. Si noti che i primi quattro indirizzi IP in qualsiasi subnet sono riservati per l'uso interno in Azure. Usare gli altri indirizzi IP disponibili.

     ![Distribuisci server di configurazione](./media/site-recovery-vmware-to-azure-classic-legacy/cs-details.png)
5. Quando si fa clic su **OK** , nella sottoscrizione del server di configurazione verrà creata una macchina virtuale A3 standard basata su un'immagine della raccolta di Windows Server 2012 R2 con Azure Site Recovery. Viene creata come prima istanza in un nuovo servizio cloud. Se si sceglie di connettersi tramite Internet, il servizio cloud viene creato con un indirizzo IP pubblico riservato. Lo stato di avanzamento può essere monitorato nella scheda **Processi** .

    ![Monitorare lo stato](./media/site-recovery-vmware-to-azure-classic-legacy/monitor-cs.png)
6. Se ci si connette tramite Internet, dopo la distribuzione del server di configurazione annotare l'indirizzo IP pubblico assegnato al server nella pagina **Macchine virtuali** nel portale di Azure. Nella scheda **Endpoint** annotare quindi la porta HTTPS pubblica mappata alla porta privata 443. Queste informazioni saranno necessarie in seguito per la registrazione dei server di destinazione master e di elaborazione con il server di configurazione. Il server di configurazione viene distribuito con gli endpoint indicati di seguito.

   * HTTPS: viene usata una porta pubblica per coordinare le comunicazioni tra i server dei componenti e Azure tramite Internet. La porta privata 443 viene usata per coordinare la comunicazione tra i server dei componenti e Azure tramite VPN.
   * Personalizzato: viene usata una porta pubblica per le comunicazioni con lo strumento di failback tramite Internet. La porta privata 9443 viene usata per la comunicazione con lo strumento di failback tramite VPN.
   * PowerShell: porta privata 5986
   * Desktop remoto: porta privata 3389

   ![Endpoint VM](./media/site-recovery-vmware-to-azure-classic-legacy/vm-endpoints.png)

   > [!WARNING]
   > Non eliminare o modificare il numero della porta pubblica o privata di qualsiasi endpoint creato durante la distribuzione del server di configurazione.
   >
   >

Il server di configurazione viene distribuito in un servizio cloud di Azure creato automaticamente con un indirizzo IP riservato. L'indirizzo riservato è necessario per garantire che l'indirizzo IP del servizio cloud del server di configurazione rimanga invariato dopo i riavvii delle macchine virtuali, incluso il server di configurazione, nel servizio cloud. La prenotazione dell'indirizzo IP pubblico riservato dovrà essere annullata manualmente al momento della rimozione del server di configurazione. In caso contrario, l'indirizzo IP resterà riservato. Esiste un limite predefinito di 20 indirizzi IP pubblici riservati per ogni sottoscrizione. [Altre informazioni](../virtual-network/virtual-networks-reserved-private-ip.md) sugli indirizzi IP riservati.

<a id="register-the-configuration-server-in-the-vault" class="xliff"></a>

### Registrare il server di configurazione nell'insieme di credenziali
1. Nella pagina **Avvio rapido** fare clic su **Preparare le risorse (Azure) di destinazione** > **Scaricare una chiave di registrazione**. Il file di chiave viene generato automaticamente. È valido 5 giorni dopo essere stato generato. Copiare il file nel server di configurazione.
2. In **Macchine virtuali** selezionare il server di configurazione nell'elenco delle macchine virtuali. Aprire la scheda **Dashboard** e fare clic su **Connetti**. **aprire** il file RDP scaricato. Se si usa una VPN, usare l'indirizzo IP interno, ovvero l'indirizzo specificato durante la distribuzione del server di configurazione, per una connessione Desktop remoto dal sito locale. Quando si accede per la prima volta, viene eseguita automaticamente la procedura guidata Azure Site Recovery Configuration Server Setup Wizard.

    ![Registrazione](./media/site-recovery-vmware-to-azure-classic-legacy/splash.png)
3. In **Third-Party Software Installation** (Installazione software di terze parti) fare clic su **I Accept** (Accetto) per scaricare e installare MySQL.

    ![Installazione di MySQL](./media/site-recovery-vmware-to-azure-classic-legacy/sql-eula.png)
4. In **MySQL Server Details** creare le credenziali per l'accesso all'istanza del server MySQL.

    ![Credenziali di MySQL](./media/site-recovery-vmware-to-azure-classic-legacy/sql-password.png)
5. In **Internet Settings** specificare la modalità di connessione a Internet del server di configurazione. Si noti che:

   * Se si vuole usare un server proxy personalizzato, configurarlo prima di installare il provider.
   * Facendo clic su **Avanti** , verrà eseguito un test per verificare la connessione al proxy.
   * Se si usa un proxy personalizzato oppure se il proxy predefinito richiede l'autenticazione, sarà necessario immettere i dettagli del proxy, tra cui l'indirizzo, la porta e le credenziali.
   * Gli URL seguenti dovranno essere accessibili tramite il proxy:
     * *.hypervrecoverymanager.windowsazure.com
     * *.accesscontrol.windows.net
     * *.backup.windowsazure.com
     * *.blob.core.windows.net
     * *. store.core.windows.net
   * Se sono presenti regole firewall basate sull'indirizzo IP, verificare che siano impostate per autorizzare la comunicazione tra il server di configurazione e gli indirizzi IP descritti in [Intervalli IP dei data center di Azure](https://msdn.microsoft.com/library/azure/dn175718.aspx) e per il protocollo HTTPS (443). È necessario aggiungere all'elenco di indirizzi consentiti gli IP dell'area Azure che si prevede di utilizzare e quello degli Stati Uniti occidentali.

     ![Registrazione del proxy](./media/site-recovery-vmware-to-azure-classic-legacy/register-proxy.png)
6. In **Provider Error Message Localization Settings** specificare in quale lingua si desidera visualizzare i messaggi di errore.

    ![Registrazione di messaggi di errore](./media/site-recovery-vmware-to-azure-classic-legacy/register-locale.png)
7. In **Azure Site Recovery Registration** individuare e selezionare il file di chiave copiato nel server.

    ![Registrazione del file di chiave](./media/site-recovery-vmware-to-azure-classic-legacy/register-vault.png)
8. Nella pagina di completamento della procedura guidata selezionare le opzioni seguenti:

   * Selezionare **Avviare la finestra di gestione dell’account** per specificare che deve essere aperta la finestra Gestisci account dopo il completamento della procedura guidata.
   * Selezionare **Crea un'icona desktop per Cspsconfigtool** per aggiungere un collegamento sul desktop nel server di configurazione, in modo da poter aprire la finestra di dialogo **Gestisci account** in qualsiasi momento senza dover ripetere la procedura guidata.

     ![Completare la registrazione](./media/site-recovery-vmware-to-azure-classic-legacy/register-final.png)
9. Fare clic su **Finish** per completare la procedura guidata. Verrà generata una passphrase. Copiarla in una posizione sicura. Sarà necessaria per autenticare e registrare i server di elaborazione e di destinazione master con il server di configurazione. Verrà usata anche per assicurare l'integrità del canale nelle comunicazioni del server di configurazione. La passphrase può essere rigenerata, ma si dovrà registrare di nuovo i server di elaborazione e di destinazione master con la nuova passphrase.

    ![Passphrase](./media/site-recovery-vmware-to-azure-classic-legacy/passphrase.png)

Dopo la registrazione, il server di configurazione verrà elencato nella pagina **Server di configurazione** nell'insieme di credenziali.

<a id="set-up-and-manage-accounts" class="xliff"></a>

### Impostare e gestire gli account
Durante la distribuzione, Site Recovery richiede le credenziali per le azioni seguenti:

* Un account di VMware, per permettere a Site Recovery di rilevare automaticamente le macchine virtuali nei server vCenter o negli host vSphere.
* Quando si aggiungono computer per la protezione, in modo che Site Recovery possa installare il servizio Mobility su di essi.

Dopo aver registrato il server di configurazione, è possibile aprire la finestra di dialogo **Gestisci account** per aggiungere e gestire gli account che devono essere usati per queste azioni. Esistono due modi per eseguire questa operazione:

* Aprire il collegamento che si è scelto di creare per la finestra di dialogo nell'ultima pagina dell'installazione per il server di configurazione (cspsconfigtool).
* Aprire la finestra di dialogo al termine della dell'installazione del server di configurazione.

1. In **Gestisci account** click **Aggiungi account**. È inoltre possibile modificare ed eliminare account esistenti.

    ![Gestisci account](./media/site-recovery-vmware-to-azure-classic-legacy/manage-account.png)
2. In **Dettagli account** specificare un nome di account da usare in Azure e le credenziali (dominio/nome utente).

    ![Gestisci account](./media/site-recovery-vmware-to-azure-classic-legacy/account-details.png)

<a id="connect-to-the-configuration-server" class="xliff"></a>

### Connettersi al server di configurazione
Esistono due modi per connettersi al server di configurazione:

* Tramite una connessione VPN da sito a sito o ExpressRoute
* Tramite Internet

Si noti che:

* Una connessione Internet usa gli endpoint della macchina virtuale insieme all'indirizzo IP virtuale pubblico del server.
* Una connessione VPN usa l'indirizzo IP interno del server insieme alle porte private dell'endpoint.
* È necessario decidere se eseguire la connessione (dati di controllo e replica) dai server locali ai vari server dei componenti (server di configurazione, server di destinazione master) in esecuzione in Azure tramite una connessione VPN o via Internet. Non è possibile modificare questa impostazione in un secondo momento. Se la si modifica, sarà necessario ridistribuire lo scenario e proteggere nuovamente i computer.  

<a id="step-3-deploy-the-master-target-server" class="xliff"></a>

## Passaggio 3: Distribuire il server di destinazione master
1. Fare clic su **Preparare le risorse (Azure) di destinazione** > **Distribuisci server di destinazione master**.
2. Specificare i dettagli e le credenziali del server di destinazione master. Il server verrà distribuito nella stessa rete di Azure in cui risiede il server di configurazione. Quando si fa clic per confermare, verrà creata una macchina virtuale di Azure con un'immagine della raccolta di Windows o Linux.

    ![Impostazioni del server di destinazione](./media/site-recovery-vmware-to-azure-classic-legacy/target-details.png)

Si noti che i primi quattro indirizzi IP in qualsiasi subnet sono riservati per l'uso interno in Azure. Specificare gli altri indirizzi IP disponibili.

> [!NOTE]
> Selezionare DS4 Standard durante la configurazione della protezione dei carichi di lavoro che richiedono prestazioni di I/O elevate e omogenee e bassa latenza per ospitare carichi di lavoro con numerose operazioni di I/O con un [account di archiviazione Premium](../storage/storage-premium-storage.md).
>
>

1. Viene creata una macchina virtuale del server di destinazione master Windows con gli endpoint indicati di seguito. Si noti che gli endpoint pubblici vengono creati solo se ci si connette tramite Internet.

   * Personalizzato: porta pubblica usata dal server di elaborazione per inviare dati di replica tramite Internet. La porta privata 9443 viene usata dal server di elaborazione per inviare dati di replica al server di destinazione master tramite VPN.
   * Personalizzato1: porta pubblica usata dal server di elaborazione per inviare metadati tramite Internet. La porta privata 9080 viene usata dal server di elaborazione per inviare metadati al server di destinazione master tramite VPN.
   * PowerShell: porta privata 5986
   * Desktop remoto: porta privata 3389
2. Viene creata una macchina virtuale del server di destinazione master Linux con gli endpoint indicati di seguito. Si noti che gli endpoint pubblici vengono creati solo se ci si connette tramite Internet.

   * Personalizzato: porta pubblica usata dal server di elaborazione per inviare dati di replica tramite Internet. La porta privata 9443 viene usata dal server di elaborazione per inviare dati di replica al server di destinazione master tramite VPN.
   * Personalizzato1: porta pubblica usata dal server di elaborazione per inviare metadati tramite Internet. La porta privata 9080 viene usata dal server di elaborazione per inviare metadati al server di destinazione master tramite VPN.
   * SSH: porta privata 22

     > [!WARNING]
     > Non eliminare o modificare il numero della porta pubblica o privata di qualsiasi endpoint creato durante la distribuzione del server di destinazione master.
     >
     >
3. In **Macchine virtuali** attendere l'avvio della macchina virtuale.

   * Se si tratta di un server Windows, annotare le informazioni sul desktop remoto.
   * Se si tratta di un server Linux e ci si connette tramite VPN, annotare l'indirizzo IP interno della macchina virtuale. Se si stabilisce la connessione tramite Internet, annotare l'indirizzo IP pubblico.
4. Accedere al server per completare l'installazione ed eseguirne la registrazione con il server di configurazione.
5. Se si usa Windows:

   1. Avviare una connessione Desktop remoto alla macchina virtuale. La prima volta che si accede, verrà eseguito uno script nella finestra di PowerShell. Non chiuderla. Al termine, viene aperto automaticamente lo strumento di configurazione dell'agente host (Host Agent Config) per registrare il server.
   2. Nella finestra **Host Agent Config** specificare l'indirizzo IP interno del server di configurazione e la porta 443. È possibile usare l'indirizzo interno e la porta privata 443 anche se non si usa la modalità VPN per la connessione, perché la macchina virtuale è collegata alla stessa rete di Azure in cui risiede il server di configurazione. Lasciare abilitata l'opzione **Use HTTPS** . Immettere la passphrase per il server di configurazione annotata in precedenza. Fare clic su **OK** per registrare il server. È possibile ignorare le opzioni NAT. in quanto non vengono usate.
   3. Se l'unità di conservazione stimata richiede più di 1 TB, è possibile configurare il volume di conservazione (R:) usando un disco virtuale e [spazi di archiviazione](http://blogs.technet.com/b/askpfeplat/archive/2013/10/21/storage-spaces-how-to-configure-storage-tiers-with-windows-server-2012-r2.aspx)

   ![Server di destinazione master Windows](./media/site-recovery-vmware-to-azure-classic-legacy/target-register.png)
6. Se si usa Linux:

   1. Assicurarsi di aver installato il LIS (Linux Integration Services) più recente prima di installare il server di destinazione master. È possibile trovare la versione più recente di LIS insieme alle istruzioni su come installarlo [qui](https://www.microsoft.com/download/details.aspx?id=46842). Riavviare il computer dopo l’installazione di LIS.
   2. In **Preparare le risorse (Azure) di destinazione** fare clic su **Scarica e installa il software aggiuntivo (solo per il server di destinazione master Linux)**. Copiare il file TAR scaricato nella macchina virtuale usando un client SFTP. In alternativa, è possibile accedere al server di destinazione master Linux distribuito e usare *wget http://go.microsoft.com/fwlink/?LinkID=529757&clcid=0x409* per scaricare il file.
   3. Accedere al server con un client Secure Shell. Se si è connessi alla rete di Azure tramite VPN, usare l'indirizzo IP interno. In caso contrario, usare l'indirizzo IP esterno e l'endpoint pubblico SSH.
   4. Estrarre i file dal programma di installazione compresso con gzip eseguendo: **tar –xvzf Microsoft-ASR_UA_8.4.0.0_RHEL6-64***
      ![Server di destinazione master Linux](./media/site-recovery-vmware-to-azure-classic-legacy/linux-tar.png)
   5. Accertarsi di essere nella directory nella quale è stato estratto il contenuto del file TAR.
   6. Copiare la passphrase del server di configurazione in un file locale usando il comando ***echo `<passphrase>`* >passphrase.txt**
   7. Eseguire il comando "***sudo ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i `<Configuration server internal IP address>`* -p 443 -s y -c https -P passphrase.txt**".

      ![Registrare il server di destinazione](./media/site-recovery-vmware-to-azure-classic-legacy/linux-mt-install.png)
7. Attendere da 10 a 15 minuti, quindi verificare nella pagina che il server di destinazione master sia elencato come registrato nella scheda **Server** > **Server di configurazione** **Dettagli server**. Se si usa Linux e il server non è stato registrato, eseguire di nuovo lo strumento di configurazione dell'host da /usr/local/ASR/Vx/bin/hostconfigcli. Sarà necessario impostare le autorizzazioni di accesso eseguendo chmod come utente ROOT.

    ![Verificare il server di destinazione](./media/site-recovery-vmware-to-azure-classic-legacy/target-server-list.png)

> [!NOTE]
> Al termine della registrazione, possono essere necessari fino a 15 minuti perché il server di destinazione master venga elencato nel portale. Per aggiornarlo immediatamente, fare clic su **Aggiorna** on the **Server di configurazione** .
>
>

<a id="step-4-deploy-the-on-premises-process-server" class="xliff"></a>

## Passaggio 4: Distribuire il server di elaborazione locale
Prima di iniziare è consigliabile configurare un indirizzo IP statico nel server di elaborazione per fare in modo che sia persistente dopo i riavvii.

1. Fare clic su Avvio rapido > **Installare il server di elaborazione locale** > **Scaricare e installare il server di elaborazione**.

    ![Installare il server di elaborazione](./media/site-recovery-vmware-to-azure-classic-legacy/ps-deploy.png)
2. Copiare il file ZIP scaricato nel server in cui si installerà il server di elaborazione. Il file ZIP contiene due file di installazione:

   * Microsoft-ASR_CX_TP_8.4.0.0_Windows*
   * Microsoft-ASR_CX_8.4.0.0_Windows*
3. Decomprimere il file compresso e copiare i file di installazione in un percorso del server.
4. Eseguire il file di installazione **Microsoft-ASR_CX_TP_8.4.0.0_Windows*** e seguire le istruzioni. Verranno installati i componenti di terze parti necessari per la distribuzione.
5. Eseguire quindi **Microsoft-ASR_CX_8.4.0.0_Windows***.
6. Nella pagina **Modalità server** selezionare **Server di elaborazione**.
7. Nella pagina **Dettagli ambiente** eseguire le operazioni seguenti:

    - Se si desidera proteggere macchine virtuali VMware, fare clic su **Sì**
    - Se si vuole solo proteggere server fisici e quindi non è necessario installare VMware vCLI nel server di elaborazione, fare clic su **No** e continuare.

1. Quando si installa VMware vCLI, tenere presente quanto segue:

   * **È supportato solo VMware vSphere CLI 5.5.0**. Il server di elaborazione non funziona con altre versioni o aggiornamenti di vSphere CLI.
   * Scaricare vSphere CLI 5.5.0 da [qui.](https://my.vmware.com/web/vmware/details?downloadGroup=VCLI550&productId=352)
   * Se vSphere CLI è stato installato subito prima di avviare l'installazione del server di elaborazione e non viene rilevato dal programma di installazione, attendere fino a cinque minuti prima di riprovare a eseguire l'installazione. Questo garantisce che tutte le variabili di ambiente necessarie per il rilevamento di vSphere CLI siano state inizializzate correttamente.
2. In **Selezione della NIC per il server di elaborazione** selezionare la scheda di rete che deve essere usata dal server di elaborazione.

   ![Selezionare la scheda](./media/site-recovery-vmware-to-azure-classic-legacy/ps-nic.png)
3. In **Dettagli del server di configurazione**:

   * Per l'indirizzo IP e la porta, se si stabilisce la connessione tramite VPN, specificare l'indirizzo IP interno del server di configurazione e la porta 443. In caso contrario, specificare l'indirizzo IP virtuale e l'endpoint HTTP pubblico mappato.
   * Digitare la passphrase del server di configurazione.
   * Per disabilitare la verifica quando si usa il push automatico per installare il servizio, deselezionare **Verifica firma del software del servizio Mobility** . La verifica della firma richiede la connettività Internet dal server di elaborazione.
   * Fare clic su **Next**.

   ![Registrare il server di configurazione](./media/site-recovery-vmware-to-azure-classic-legacy/ps-cs.png)
4. In **Selezionare l'unità di installazione** selezionare un'unità cache. Il server di elaborazione richiede un'unità cache con almeno 600 GB di spazio libero. Fare clic su **Installa**.

   ![Registrare il server di configurazione](./media/site-recovery-vmware-to-azure-classic-legacy/ps-cache.png)
5. Si noti che potrebbe essere necessario riavviare il server per completare l'installazione. In **Configuration Server** > **Dettagli server** verificare che il server di elaborazione sia visualizzato e risulti registrato correttamente nell'insieme di credenziali.

> [!NOTE]
> Possono essere necessari fino a 15 minuti dopo la registrazione perché il server di elaborazione venga elencato nel server di configurazione. Per aggiornare immediatamente, aggiornare il server di configurazione facendo clic sul pulsante Aggiorna nella parte inferiore della pagina Server di configurazione.
>
>

![Convalidare il server di elaborazione](./media/site-recovery-vmware-to-azure-classic-legacy/ps-register.png)

Se la verifica della firma per il servizio Mobility non è stata disabilitata al momento della registrazione del server di elaborazione, è possibile farlo in un secondo momento come descritto di seguito:

1. Accedere al server di elaborazione come amministratore e aprire il file C:\pushinstallsvc\pushinstaller.conf per la modifica. Nella sezione **[PushInstaller.transport]** aggiungere la riga seguente: **SignatureVerificationChecks="0"**. Salvare e chiudere il file.
2. Riavviare il servizio InMage PushInstall.

<a id="step-5-update-site-recovery-components" class="xliff"></a>

## Passaggio 5: Aggiornare i componenti di Site Recovery
I componenti di Site Recovery vengono aggiornati periodicamente. Quando sono disponibili nuovi aggiornamenti è necessario installarli nell'ordine seguente:

1. Server di configurazione
2. Server di elaborazione
3. Server master di destinazione
4. Strumento di failback (vContinuum)

<a id="obtain-and-install-the-updates" class="xliff"></a>

### Ottenere e installare gli aggiornamenti
1. Gli aggiornamenti per i server di configurazione, di elaborazione e di destinazione master sono disponibili nel **dashboard**di Site Recovery. Per l'installazione in Linux, estrarre i file dal programma di installazione in formato GZIP ed eseguire il comando "sudo ./install" per installare l'aggiornamento.
2. [Scaricare](http://go.microsoft.com/fwlink/?LinkID=533813) l'aggiornamento più recente per lo strumento di failback (vContinuum).
3. Se si eseguono macchine virtuali o server fisici in cui è già installato il servizio Mobility, è possibile ottenere gli aggiornamenti per il servizio come segue:

   * **Opzione 1**: scaricare gli aggiornamenti seguenti.
     * [Windows Server (solo 64 bit)](http://download.microsoft.com/download/8/4/8/8487F25A-E7D9-4810-99E4-6C18DF13A6D3/Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe)
     * [CentOS 6.4,6.5,6.6 (solo 64 bit)](http://download.microsoft.com/download/7/E/D/7ED50614-1FE1-41F8-B4D2-25D73F623E9B/Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz)
     * [Oracle Enterprise Linux 6.4,6.5 (solo 64 bit)](http://download.microsoft.com/download/5/2/6/526AFE4B-7280-4DC6-B10B-BA3FD18B8091/Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz)
     * [SUSE Linux Enterprise Server SP3 (solo 64 bit)](http://download.microsoft.com/download/B/4/2/B4229162-C25C-4DB2-AD40-D0AE90F92305/Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz)
     * Dopo l'aggiornamento del server di elaborazione, la versione aggiornata del servizio Mobility è disponibile nella cartella C:\pushinstallsvc\repository nel server di elaborazione.
   * **Opzione 2**: se è disponibile un computer su cui è installata una versione precedente del servizio Mobility, è possibile aggiornare automaticamente il servizio Mobility nel computer dal portale di gestione.

     1. Assicurarsi che il server di elaborazione sia aggiornato.
     2. Assicurarsi che il computer protetto sia conforme ai [prerequisiti](#install-the-mobility-service-automatically) per il push automatico del servizio Mobility, in modo che l'aggiornamento funzioni come previsto.
     3. Selezionare il gruppo di protezione, evidenziare il computer protetto e fare clic su **Aggiorna servizio Mobility**. Questo pulsante è disponibile solo se è presente una versione più recente del servizio Mobility.

         ![Selezionare il server vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/update-mobility.png)

In Seleziona account specificare l'account amministratore da utilizzare per aggiornare il servizio Mobility sul server protetto. Fare clic su OK e attendere il completamento del processo attivato.

<a id="step-6-add-vcenter-servers-or-vsphere-hosts" class="xliff"></a>

## Passaggio 6: Aggiungere server vCenter o host vSphere
1. Fare clic su **Server** > **Server di configurazione**. Selezionare il server di configurazione e fare clic su **Aggiungi server vCenter** per aggiungere un server vCenter o un host vSphere.

    ![Selezionare il server vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter.png)
2. Specificare i dettagli del server o dell'host e selezionare il server di elaborazione da usare per trovarlo.

   * Se il server vCenter non è in esecuzione sulla porta 443 predefinita, specificare il numero di porta su cui è in esecuzione il server vCenter.
   * Il server di elaborazione deve trovarsi nella stessa rete del server vCenter o dell'host vSphere e deve esservi installato VMware vSphere CLI 5.5.0.

     ![Impostazioni del server vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter4.png)
3. Al termine dell'individuazione, il server vCenter sarà elencato nei dettagli del server di configurazione.

    ![Impostazioni del server vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter2.png)
4. Se si usa un account senza privilegi di amministratore per aggiungere il server o l'host, assicurarsi che l'account abbia i privilegi seguenti:

   * Per gli account vCenter devono essere abilitati i privilegi Datacenter, Datastore, Folder, Host, Network, Resource, Storage views, Virtual machine e vSphere Distributed Switch abilitati.
   * Per gli account host vSphere devono essere abilitati i privilegi Datacenter, Datastore, Folder, Host, Network, Resource, Virtual machine e vSphere Distributed Switch.

<a id="step-7-create-a-protection-group" class="xliff"></a>

## Passaggio 7: Creare un gruppo di protezione
1. Aprire **Elementi protetti** > **Gruppo di protezione** > **Crea gruppo di protezione**.

    ![Crea gruppo di protezione](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg1.png)
2. Nella pagina **Specificare le impostazioni del gruppo di protezione** immettere un nome per il gruppo e selezionare il server di configurazione in cui creare il gruppo.

    ![Impostazioni del gruppo di protezione](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg2.png)
3. Nella pagina **Specifica le impostazioni di replica** configurare le impostazioni di replica da usare per tutti i computer nel gruppo.

    ![Replica del gruppo di protezione](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg3.png)
4. Impostazioni:

   * **Coerenza di più macchine virtuali**: se si attiva questa opzione, vengono creati punti di ripristino coerenti con l'applicazione condivisi tra i computer nel gruppo di protezione. Questa impostazione è importante quando tutti i computer nel gruppo di protezione eseguono lo stesso carico di lavoro. Tutti i computer saranno ripristinati nello stesso punto dati. Disponibile solo per i server Windows.
   * **Soglia RPO**: se il valore RPO della replica per la protezione dati continua supera la soglia RPO configurata, vengono generati avvisi.
   * **Conservazione del punto di ripristino**: specifica l'intervallo di conservazione. I computer protetti possono essere ripristinati in qualsiasi punto all'interno di questo intervallo.
   * **Frequenza di snapshot coerenti con l'applicazione**: specifica con quale frequenza verranno creati i punti di ripristino contenenti snapshot coerenti con l'applicazione.

Dopo averlo creato, è possibile monitorare il gruppo di protezione nella pagina **Elementi protetti** .

<a id="step-8-set-up-machines-you-want-to-protect" class="xliff"></a>

## Passaggio 8: Configurare i computer da proteggere
È necessario installare il servizio Mobility nelle macchine virtuali e nei server fisici che si desidera proteggere. Questa operazione può essere eseguita in due modi:

* Eseguire il push e installare automaticamente il servizio in ogni computer dal server di elaborazione.
* Installare manualmente il servizio.

<a id="install-the-mobility-service-automatically" class="xliff"></a>

### Installare automaticamente il servizio Mobility
Quando si aggiungono computer a un gruppo di protezione, il server di elaborazione effettua automaticamente il push e l'installazione del servizio Mobility in ogni computer.

**Eseguire automaticamente l'installazione push del servizio Mobility nei server Windows:**

1. Installare gli aggiornamenti più recenti per il server di elaborazione, come descritto in [Passaggio 5: Installare gli aggiornamenti più recenti](#step-5-install-latest-updates)e assicurarsi che il server di elaborazione sia disponibile.
2. Verificare che sia presente la connettività di rete tra il computer di origine e il server di elaborazione e che il computer di origine sia accessibile dal server di elaborazione.  
3. Configurare Windows Firewall per abilitare **Condivisione di file e stampanti** e **Strumentazione gestione Windows**. Nelle impostazioni di Windows Firewall selezionare l'opzione "Consenti app o funzionalità attraverso Windows Firewall" e selezionare le applicazioni, come illustrato nella figura seguente. Per i computer appartenenti a un dominio, è possibile configurare i criteri del firewall con un oggetto Criteri di gruppo.

    ![Impostazioni del firewall](./media/site-recovery-vmware-to-azure-classic-legacy/push-firewall.png)
4. L'account usato per eseguire l'installazione push deve appartenere al gruppo Administrators nel computer da proteggere. Queste credenziali vengono usate solo per l'installazione push del servizio Mobility e verranno specificate quando si aggiunge un computer a un gruppo di protezione.
5. Se l'account specificato non è un account di dominio, si dovrà disabilitare il Controllo dell'accesso utente remoto nel computer locale. A tale scopo, aggiungere la voce del Registro di sistema DWORD LocalAccountTokenFilterPolicy con un valore di 1 in HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System. Per aggiungere la voce del Registro di sistema da un'interfaccia della riga di comando, aprire il prompt dei comandi o PowerShell e digitare **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.

**Eseguire automaticamente l'installazione push del servizio Mobility nei server Linux:**

1. Installare gli aggiornamenti più recenti per il server di elaborazione, come descritto in [Passaggio 5: Installare gli aggiornamenti più recenti](#step-5-install-latest-updates)e assicurarsi che il server di elaborazione sia disponibile.
2. Verificare che sia presente la connettività di rete tra il computer di origine e il server di elaborazione e che il computer di origine sia accessibile dal server di elaborazione.  
3. Verificare che l'account sia un utente ROOT nel server Linux di origine.
4. Assicurarsi che il file /etc/hosts nel server Linux di origine contenga le voci che eseguono il mapping del nome host locale agli indirizzi IP associati a tutte le schede NIC.
5. Installare i pacchetti openssh, openssh-server, openssl più recenti nel computer da proteggere.
6. Assicurarsi che SSH sia abilitato e in esecuzione sulla porta 22.
7. Abilitare il sottosistema SFTP e l'autenticazione della password nel file sshd_config, come segue:

   * a) Accedere come utente ROOT.
   * b) Nel file /etc/ssh/sshd_config trovare la riga che inizia con **PasswordAuthentication**.
   * c) Rimuovere il commento dalla riga e modificare il valore da "no" in "yes".

       ![Mobility di Linux](./media/site-recovery-vmware-to-azure-classic-legacy/linux-push.png)
   * d) Trovare la riga che inizia con Subsystem e rimuovere il commento.

       ![Mobility push Linux](./media/site-recovery-vmware-to-azure-classic-legacy/linux-push2.png)    
8. Verificare che la variante Linux del computer di origine sia supportata.

<a id="install-the-mobility-service-manually" class="xliff"></a>

### Installare manualmente il servizio Mobility
I pacchetti software usati per installare il servizio Mobility sono nel server di elaborazione, in C:\pushinstallsvc\repository. Accedere al server di elaborazione e copiare il pacchetto di installazione appropriato per il computer di origine in base alla tabella seguente:

| Sistema operativo di origine | Pacchetto del servizio Mobility nel server di elaborazione |
| --- | --- |
| Windows Server (solo 64 bit) |`C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe` |
| CentOS 6.4, 6.5, 6.6 (solo 64 bit) |`C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz` |
| SUSE Linux Enterprise Server 11 SP3 (solo 64 bit) |`C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz` |
| Oracle Enterprise Linux 6.4, 6.5 (solo 64 bit) |`C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz` |

**Per installare manualmente il servizio Mobility in un server Windows**, seguire questa procedura:

1. Copiare il pacchetto **Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe** dal percorso della directory del server di elaborazione elencato nella tabella precedente nel computer di origine.
2. Installare il servizio Mobility eseguendo il file eseguibile nel computer di origine.
3. Seguire le istruzioni del programma di installazione.
4. Selezionare **Servizio Mobility** come ruolo e fare clic su **Avanti**.

    ![Installare il servizio Mobility](./media/site-recovery-vmware-to-azure-classic-legacy/ms-install.png)
5. Mantenere la directory di installazione come percorso di installazione predefinito e fare clic su **Installa**.
6. Nella finestra **Host Agent Config** specificare l'indirizzo IP e la porta HTTPS del server di configurazione.

   * Se si sta eseguendo la connessione via Internet, specificare l'indirizzo IP virtuale pubblico e l'endpoint HTTPS pubblico come porta.
   * Se si sta eseguendo la connessione tramite VPN, specificare l'indirizzo IP interno e 443 per la porta. Lasciare selezionata l'opzione **Use HTTPS** .

     ![Installare il servizio Mobility](./media/site-recovery-vmware-to-azure-classic-legacy/ms-install2.png)
7. Specificare la passphrase del server di configurazione e fare clic su **OK** per registrare il servizio Mobility con il server di configurazione.

**Per eseguire dalla riga di comando:**

1. Copiare la passphrase da CX nel file "C:\connection.passphrase" sul server ed eseguire questo comando. Nel nostro esempio CX è 104.40.75.37 e la porta HTTPS è 62519:

    `C:\Microsoft-ASR_UA_8.2.0.0_Windows_PREVIEW_20Mar2015_Release.exe" -ip 104.40.75.37 -port 62519 -mode UA /LOG="C:\stdout.txt" /DIR="C:\Program Files (x86)\Microsoft Azure Site Recovery" /VERYSILENT  /SUPPRESSMSGBOXES /norestart  -usesysvolumes  /CommunicationMode https /PassphrasePath "C:\connection.passphrase"`

**Installare manualmente il servizio Mobility in un server Linux**:

1. Copiare l'archivio TAR appropriato in base alla tabella precedente dal server di elaborazione nel computer di origine.
2. Aprire un programma shell ed eseguire `tar -xvzf Microsoft-ASR_UA_8.2.0.0*`
3. Immettere *`echo <passphrase> >passphrase.txt`* dalla shell per creare un file passphrase.txt nella directory locale in cui è stato estratto il contenuto dell'archivio TAR.
4. Immettere *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*per installare il servizio Mobility.
5. Specificare l'indirizzo IP e la porta:

   * Se si sta eseguendo la connessione al server di configurazione via Internet, specificare indirizzo IP pubblico virtuale e l'endpoint HTTPS pubblico del server di configurazione in `<IP address>` e `<port>`.
   * Se si sta eseguendo la connessione tramite VPN, specificare l'indirizzo IP interno e 443.

**Per eseguire dalla riga di comando**:

1. Copiare la passphrase da CX nel file "passphrase.txt" sul server ed eseguire questi comandi. Nel nostro esempio CX è 104.40.75.37 e la porta HTTPS è 62519:

Per eseguire l'installazione in un server di produzione:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt

Per eseguire l'installazione nel server di destinazione:

    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt

> [!NOTE]
> Quando si aggiungono computer a un gruppo di protezione in cui è già in esecuzione una versione appropriata del servizio Mobility, l'installazione push viene ignorata.
>
>

<a id="step-9-enable-protection" class="xliff"></a>

## Passaggio 9: Abilitare la protezione
Per abilitare la protezione, aggiungere macchine virtuali e server fisici a un gruppo di protezione. Prima di iniziare, tenere presente quanto segue:

* Le macchine virtuali vengono individuate ogni 15 minuti e possono essere necessari fino a 15 minuti perché vengano visualizzate in Azure Site Recovery dopo l'individuazione.
* Anche le modifiche dell'ambiente nella macchina virtuale (ad esempio, l'installazione degli strumenti VMware) possono richiedere fino a 15 minuti per l'aggiornamento in Site Recovery.
* È possibile controllare l'ora dell'ultima individuazione nel campo **ORA ULTIMO CONTATTO** per il server vCenter o l'host ESXi nella pagina **Server di configurazione**.
* Se è già stato creato un gruppo di protezione e successivamente si aggiunge un server vCenter o un host ESXi, sono necessari 15 minuti per l'aggiornamento del portale di Azure Site Recovery e per la visualizzazione delle macchine virtuali nella finestra di dialogo **Aggiungi macchine virtuali a un gruppo di protezione** .
* Se si desidera procedere immediatamente con l'aggiunta di computer al gruppo di protezione senza attendere l'individuazione pianificata, evidenziare il server di configurazione (senza fare clic su di esso) e fare clic sul pulsante **Aggiorna** .
* Quando si aggiungono macchine virtuali o computer fisici a un gruppo di protezione, il server di elaborazione esegue automaticamente il push e l'installazione del servizio Mobility nel server di origine, se non è già installato.
* Per il funzionamento del meccanismo di push automatico, accertarsi di avere configurato i computer protetti come descritto nel passaggio precedente.

Aggiungere i computer come segue:

1. Fare clic su **Elementi protetti** > **Gruppo di protezione** > **Computer** > **Aggiungi computer**. Come procedura consigliata, i gruppi di protezione dovranno riflettere i carichi di lavoro, in modo da aggiungere allo stesso gruppo i computer che eseguono un'applicazione specifica.
2. Se si attiva la protezione per server fisici, nella pagina **Seleziona macchine virtuali** della procedura guidata **Aggiungi computer fisici** specificare l'indirizzo IP e il nome descrittivo. Selezionare quindi la famiglia di sistemi operativi.

    ![Aggiungere un server V-Center](./media/site-recovery-vmware-to-azure-classic-legacy/physical-protect.png)
3. Se si attiva la protezione per macchine virtuali VMware, nella pagina **Seleziona macchine virtuali** selezionare il server vCenter che gestisce le macchine virtuali (o l'host EXSi in cui sono in esecuzione) e quindi selezionare le macchine.

    ![Aggiungere un server V-Center](./media/site-recovery-vmware-to-azure-classic-legacy/select-vms.png)    
4. In **Specificare le risorse di destinazione** selezionare i server di destinazione master e l'archiviazione da usare per la replica e indicare se le impostazioni devono essere usate per tutti i carichi di lavoro. Selezionare [Account di archiviazione Premium](../storage/storage-premium-storage.md) durante la configurazione della protezione dei carichi di lavoro che richiedono prestazioni di I/O elevate e coerenti e bassa latenza per ospitare i carichi di lavoro con numerose operazioni di I/O. Se si desidera utilizzare un account di archiviazione Premium per i dischi dei carichi di lavoro, è necessario usare destinazioni master della serie DS. Non è possibile usare dischi di archiviazione Premium su destinazioni master diverse dalla serie DS.

   > [!NOTE]
   > Non è supportato lo spostamento degli account di archiviazione creati con il [nuovo portale di Azure](../storage/storage-create-storage-account.md) tra gruppi di risorse.
   >
   >

    ![Server vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/machine-resources.png)
5. In **Specificare gli account** selezionare l'account da usare per l'installazione del servizio Mobility nei computer protetti. Sono necessarie le credenziali dell'account per l'installazione automatica del servizio Mobility. Se non è possibile selezionare un account, assicurarsi di configurare uno come descritto nel passaggio 2. Si noti che questo account non è accessibile da Azure. Per i server Windows è richiesto un account con privilegi di amministratore nel server di origine. Per Linux l'account deve essere l'utente ROOT.

    ![Credenziali Linux](./media/site-recovery-vmware-to-azure-classic-legacy/mobility-account.png)
6. Fare clic sul segno di spunta per completare l'aggiunta dei computer al gruppo di protezione e avviare la replica iniziale per ogni computer. Lo stato può essere monitorato nella pagina **Processi** .

    ![Aggiungere un server V-Center](./media/site-recovery-vmware-to-azure-classic-legacy/pg-jobs2.png)
7. Per monitorare lo stato della protezione, fare clic su **Elementi protetti** > nome del gruppo di protezione > **Macchine virtuali**. Dopo il completamento della replica iniziale e la sincronizzazione dei dati nei computer, verrà visualizzato lo stato **Protetto** .

    ![Processi di macchine virtuali](./media/site-recovery-vmware-to-azure-classic-legacy/pg-jobs.png)

<a id="set-protected-machine-properties" class="xliff"></a>

### Impostare le proprietà dei computer protetti
1. Quando lo stato della macchina virtuale è **Protetto** , sarà possibile configurarne le proprietà di failover. Nei dettagli del gruppo di protezione selezionare il computer e aprire la scheda **Configura** .
2. È possibile modificare il nome assegnato al computer in Azure dopo il failover e le dimensioni della macchina virtuale di Azure. È anche possibile selezionare la rete di Azure alla quale verrà connesso il computer dopo il failover.

   > [!NOTE]
   > La [migrazione di reti](../resource-group-move-resources.md) all'interno dei gruppi di risorse con la stessa sottoscrizione o all'interno delle sottoscrizioni non è supportata per le reti usate per la distribuzione di Site Recovery.
   >
   >

    ![Impostare le proprietà di una macchina virtuale](./media/site-recovery-vmware-to-azure-classic-legacy/vm-props.png)

Si noti che:

* Il nome del computer in Azure deve rispettare i requisiti di Azure.
* Per impostazione predefinita, le macchine virtuali replicate in Azure non sono connesse a una rete di Azure. Per permettere la comunicazione delle macchine virtuali, accertarsi di impostare la stessa rete di Azure.
* Se si ridimensiona un volume in una macchina virtuale VMware o un server fisico, questo entra in uno stato critico. Se è necessario modificare la dimensione, eseguire le operazioni seguenti:

  * a) Modificare l'impostazione della dimensione.
  * b) Nella scheda **Macchine virtuali** selezionare la macchina virtuale e fare clic su **Rimuovi**.
  * c) In **Rimuovi macchina virtuale** selezionare l'opzione **Disabilita protezione (Usare per analisi ripristino e ridimensionamento volume)**. Questa opzione disabilita la protezione ma mantiene i punti di ripristino in Azure.

      ![Impostare le proprietà di una macchina virtuale](./media/site-recovery-vmware-to-azure-classic-legacy/remove-vm.png)
  * d) Abilitare nuovamente la protezione per la macchina virtuale. Quando si abilita nuovamente la protezione, i dati per il volume ridimensionato verranno trasferiti in Azure.

<a id="step-10-run-a-failover" class="xliff"></a>

## Passaggio 10: Eseguire un failover
Attualmente è possibile eseguire solo failover non pianificati per le macchine virtuali VMware e i server fisici protetti. Tenere presente quanto segue:

* Prima di avviare un failover, assicurarsi che i server di configurazione e di destinazione master siano in esecuzione e integri. In caso contrario, il failover avrà esito negativo.
* I computer di origine non vengono arrestati nell'ambito di un failover non pianificato. L'esecuzione di un failover non pianificato interrompe la replica dei dati per i server protetti. È necessario eliminare i computer dal gruppo di protezione e aggiungerli nuovamente per iniziare a proteggerli di nuovo dopo il failover non pianificato.
* Se si desidera eseguire il failover senza perdite di dati, assicurarsi che le macchine virtuali del sito primario siano disattivate prima di avviare il failover.

1. Nella pagina **Piani di ripristino** aggiungere un piano di ripristino. Specificare i dettagli per il piano e selezionare **Azure** come destinazione.

    ![Configurare un piano di ripristino](./media/site-recovery-vmware-to-azure-classic-legacy/rplan1.png)
2. In **Seleziona macchine virtuali** selezionare un gruppo di protezione e quindi i computer nel gruppo da aggiungere al piano di ripristino. [Ulteriori informazioni](site-recovery-create-recovery-plans.md) sui piani di ripristino.

    ![Aggiungi macchine virtuali.](./media/site-recovery-vmware-to-azure-classic-legacy/rplan2.png)
3. Se necessario, è possibile personalizzare il piano per creare gruppi e una sequenza dell'ordine in cui verrà eseguito il failover dei computer nel piano di ripristino. È anche possibile aggiungere istruzioni per azioni manuali e script. Gli script durante il ripristino in Azure possono essere aggiunti tramite i [runbook di automazione di Azure](site-recovery-runbook-automation.md).
4. Nella pagina **Piani di ripristino** selezionare il piano e fare clic su **Failover non pianificato**.
5. In **Conferma failover** verificare la direzione del failover (in Azure) e selezionare il punto di ripristino in cui effettuare il failover.
6. Attendere il completamento del processo di failover e quindi verificare che sia stato eseguito nel modo previsto e che le macchine virtuali replicate vengano avviate in Azure.

<a id="step-11-fail-back-failed-over-machines-from-azure" class="xliff"></a>

## Passaggio 11: Eseguire il failback dei computer sottoposti a failover da Azure
[Altre informazioni](site-recovery-failback-azure-to-vmware-classic-legacy.md) su come rendere nuovamente disponibili nell'ambiente locale i computer sottoposti a failover in esecuzione in Azure.

<a id="manage-your-process-servers" class="xliff"></a>

## Gestire i server di elaborazione
Il server di elaborazione invia i dati di replica al server di destinazione master in Azure e individua le nuove macchine virtuali VMware aggiunte a un server vCenter. Nelle seguenti circostanze può essere necessario cambiare il server di elaborazione nella distribuzione:

* Se il server di elaborazione corrente non è disponibile
* Se l'obiettivo del punto di ripristino (RPO) raggiunge un livello non accettabile per l'organizzazione.

Se necessario, è possibile spostare in un server di elaborazione diverso la replica di alcuni o tutti i server fisici e le macchine virtuali VMware in locale. ad esempio:

* **Errore**: se si verifica un errore in un server di elaborazione o questo risulta non disponibile, è possibile spostare la replica delle macchine virtuali protette in un altro server di elaborazione. I metadati del computer di origine e del computer di replica vengono spostati nel nuovo server di elaborazione e i dati vengono sincronizzati di nuovo. Il nuovo server di elaborazione si connetterà automaticamente al server vCenter per eseguire l'individuazione automatica. È possibile monitorare lo stato dei server di elaborazione nel dashboard di Site Recovery.
* **Bilanciamento del carico per regolare l'obiettivo del punto di ripristino**: per migliorare il bilanciamento del carico, è possibile selezionare un server di elaborazione diverso nel portale di Azure Site Recovery e spostare in tale server la replica di una o più macchine virtuali per il bilanciamento del carico manuale. In questo caso, i metadati dei computer di origine e di replica selezionati vengono spostati nel nuovo server di elaborazione. Il server di elaborazione originale rimane connesso al server vCenter.

<a id="monitor-the-process-server" class="xliff"></a>

### Monitorare il server di elaborazione
Se un server di elaborazione è in stato critico, nel dashboard di Site Recovery verrà visualizzato un avviso di stato. È possibile fare clic sullo stato per aprire la scheda Eventi e quindi eseguire il drill-down a processi specifici nella scheda Processi.

<a id="modify-the-process-server-used-for-replication" class="xliff"></a>

### Modificare il server di elaborazione usato per la replica
1. Aprire **Server** > **Server di configurazione**. Selezionare il server di configurazione e aprire **Dettagli server**.
2. Fare clic su **Server di elaborazione** > **Cambia server di elaborazione** accanto al server da modificare.

    ![Modificare il server di elaborazione 1](./media/site-recovery-vmware-to-azure-classic-legacy/change-ps1.png)
3. In **Cambia server di elaborazione** > **Server di elaborazione di destinazione** selezionare il nuovo server da usare e quindi selezionare le macchine virtuali da replicare nel nuovo server. Fare clic sull'icona informazioni accanto al nome del server per informazioni dettagliate sullo spazio disponibile e la memoria usata. Per consentire di prendere le decisioni relative al carico, viene visualizzato lo spazio medio che sarà necessario per replicare ogni macchina virtuale selezionata nel nuovo server di elaborazione.

    ![Modificare il server di elaborazione 2](./media/site-recovery-vmware-to-azure-classic-legacy/change-ps2.png)
4. Fare clic sul segno di spunta per avviare la replica nel nuovo server di elaborazione. Si noti che se si rimuovono tutte le macchine virtuali da un server di elaborazione in stato critico, per tale server non dovrebbe più essere visualizzato un avviso critico nel dashboard.

<a id="third-party-software-notices-and-information" class="xliff"></a>

## Informazioni e comunicazioni sul software di terze parti
Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”).  Microsoft is the not original author of the Third Party Code.  The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only.  This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.  

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.

