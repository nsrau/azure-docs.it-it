---
title: Proteggere le macchine virtuali distribuite in Azure Stack | Microsoft Docs
description: Linee guida su come proteggere le macchine virtuali distribuite in Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 4e5833cf-4790-4146-82d6-737975fb06ba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/10/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: 759ea6b8e4981b3ea198077cabf9df7966d6e883
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242961"
---
# <a name="protect-virtual-machines-deployed-on-azure-stack"></a>Proteggere le macchine virtuali distribuite in Azure Stack

Usare questo articolo come guida per lo sviluppo di un piano per la protezione di macchine virtuali (VM) che gli utenti distribuiscono in Azure Stack.

Per proteggersi dalla perdita di dati e i tempi di inattività non pianificato, è necessario implementare un piano di ripristino di backup o ripristino di emergenza per le applicazioni utente e i relativi dati. Questo piano potrebbe essere univoco per ogni applicazione, ma segue un modello definito dalla strategia BC/ripristino di emergenza di emergenza e continuità aziendale completa della tua organizzazione. È un buon punto di partenza [progettazione di applicazioni resilienti per Azure](https://docs.microsoft.com/azure/architecture/resiliency), che fornisce modelli generali e procedure consigliate per la disponibilità dell'applicazione e la resilienza.

>[!IMPORTANT]
> Testare i piani di ripristino di backup e ripristino di emergenza in modo continuativo. È necessario per questa opzione per assicurarsi che:
> * I piani di lavoro
> * I piani comunque soddisfano le esigenze che per cui sono stati progettati.

## <a name="azure-stack-infrastructure-recovery"></a>Ripristino dell'infrastruttura di Azure Stack

Gli utenti sono responsabili per proteggere le proprie macchine virtuali separatamente da servizi di infrastruttura di Azure Stack.

Il piano di ripristino per i servizi di infrastruttura di Azure Stack **non** includono il ripristino di macchine virtuali degli utenti, account di archiviazione o database. Il proprietario dell'applicazione, si è responsabili dell'implementazione di un piano di ripristino per le applicazioni e dati.

Se il cloud di Azure Stack è offline per un periodo di tempo prolungato o perde, è necessario disporre di un ripristino del piano posizionare che:

* Garantisce tempi di inattività minimi
* Mantiene le macchine virtuali critiche, ad esempio i server di database, in esecuzione
* Consente alle applicazioni di mantenere le richieste degli utenti di manutenzione

L'operatore del cloud Azure Stack è responsabile per la creazione di un piano di ripristino per i servizi e infrastruttura di Azure Stack sottostante. Per altre informazioni, leggere l'articolo [risarcimento grave perdita dei dati](https://docs.microsoft.com/azure/azure-stack/azure-stack-backup-recover-data).

## <a name="sourcetarget-combinations"></a>Combinazioni di origine/destinazione

Ogni cloud Azure Stack viene distribuito in un Data Center. È possibile ripristinare le applicazioni è necessario un ambiente distinto. L'ambiente di ripristino può essere un altro cloud di Azure Stack in un altro Data Center o cloud pubblico di Azure. La titolarità dei dati e i requisiti sulla privacy dei dati determina l'ambiente di ripristino per l'applicazione. Come abilitare la protezione per ogni applicazione, è necessario scegliere un'opzione di ripristino specifici per ciascuna di esse. È possibile avere applicazioni in una sottoscrizione di backup dei dati a un altro Data Center. In un'altra sottoscrizione, è possibile replicare i dati al cloud pubblico di Azure.

Pianificare la strategia di ripristino di backup e ripristino di emergenza per ogni applicazione determinare la destinazione per ogni applicazione. Un piano di ripristino consentiranno alla propria organizzazione correttamente le dimensioni di archiviazione capacità necessaria in locale e nel cloud pubblico di consumo del progetto.

|  | Azure globale | Azure Stack sia distribuito nel Data Center CSP e gestito dal CSP | Azure Stack sia distribuito nel Data Center dei clienti e gestito dal cliente |
|------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------|
| **Azure Stack sia distribuito nel Data Center CSP e gestito dal CSP** | Macchine virtuali degli utenti vengono distribuite allo Stack di Azure CSP gestito.<br><br>Macchine virtuali degli utenti sono ripristinate dal backup o del failover direttamente in Azure. | CSP opera le istanze principale e secondarie di Azure Stack nei propri datacenter.<br><br>Macchine virtuali degli utenti sono ripristinate o state eseguito il failover tra le due istanze di Azure Stack. | CSP opera Azure Stack in un sito primario.<br><br>Data Center del cliente è la destinazione di ripristino o il failover. |
| **Azure Stack sia distribuito nel Data Center dei clienti e gestito dal cliente** | Macchine virtuali degli utenti vengono distribuite al cliente gestito di Azure Stack.<br><br>Macchine virtuali degli utenti sono ripristinate dal backup o del failover direttamente in Azure. | Cliente opera Azure Stack in un sito primario.<br><br>Data Center di CSP è la destinazione di ripristino o il failover. | Cliente opera le istanze principale e secondarie di Azure Stack nei propri datacenter.<br><br>Macchine virtuali degli utenti sono ripristinate o state eseguito il failover tra le due istanze di Azure Stack. |

![Combinazioni di origine-destinazione](media/azure-stack-manage-vm-backup/vm_backupdataflow_01.png)

## <a name="application-recovery-objectives"></a>Obiettivi del ripristino dell'applicazione

È necessario determinare la quantità di tempo di inattività e perdita di dati che per ogni applicazione può tollerare la propria organizzazione. Identificando i tempi di inattività e perdita di dati è possibile creare un piano di ripristino che riduce al minimo l'impatto di una situazione di emergenza per l'organizzazione. Per ogni applicazione, prendere in considerazione:

 - **Obiettivo del tempo di ripristino (RTO)**  
RTO è il tempo massimo accettabile che un'applicazione può essere non disponibile dopo un evento imprevisto. Ad esempio, un obiettivo del tempo di 90 minuti significa che è necessario essere in grado di ripristinare l'applicazione a uno stato di esecuzione entro 90 minuti dall'inizio di una situazione di emergenza. Se si dispone di un RTO basso, si potrebbe tenere una seconda distribuzione continuamente eseguita in standby per la protezione da un'interruzione di area.
 - **Obiettivo del punto di ripristino (RPO)**  
RPO è la durata massima di perdita di dati che è accettabile durante un'emergenza. Per esempio, se si archiviano i dati in un singolo database, senza replica in altri database, e si eseguono backup orari, si potrebbero perdere fino a un'ora di dati.

Gli obiettivi RTO e RPO sono requisiti aziendali. Condurre una valutazione dei rischi per definire gli obiettivi RTO e RPO dell'applicazione.

È un'altra metrica **tempo medio per il recupero** (MTTR), ovvero il tempo medio impiegato per ripristinare l'applicazione dopo un errore. MTTR è un valore empirical per un sistema. Se questo tempo supera l'obiettivo RTO, di conseguenza un errore di sistema causerà un'interruzione delle attività inaccettabile, poiché non sarà possibile ripristinare il sistema entro l'obiettivo RTO definito.

### <a name="backup-restore"></a>Backup-ripristino

Lo schema di protezione più comune per le applicazioni basate su macchina virtuale consiste nell'usare software di backup. Backup di una macchina virtuale in genere include il sistema operativo, configurazione del sistema operativo, file binari dell'applicazione e i dati dell'applicazione. I backup vengono creati dall'esecuzione di uno snapshot dei volumi, dischi o dell'intera macchina virtuale. Con Azure Stack, si dispone della flessibilità di backup all'interno del contesto del sistema operativo guest o dalla risorsa di archiviazione di Azure Stack e le API di calcolo. Azure Stack non supporta effettuare backup a livello di hypervisor.
 
![Backup-restor](media/azure-stack-manage-vm-backup/vm_backupdataflow_03.png)

Ripristino dopo l'applicazione richiede il ripristino di uno o più macchine virtuali nel cloud stesso o a un nuovo cloud. È possibile scegliere un cloud nel tuo Data Center o nel cloud pubblico. Il cloud che scelto è completamente all'interno del controllo e si basa sui requisiti di privacy e la sovranità dei dati.
 
 - RTO: Tempo di inattività misurato in ore
 - RPO: Perdita di dati della variabile (in base alla frequenza di backup)
 - Topologia di distribuzione: Attiva/passiva

#### <a name="planning-your-backup-strategy"></a>Pianificazione della strategia di backup

Pianificazione della strategia di backup e definendo i requisiti di scalabilità inizia con quantificare il numero di istanze di macchine Virtuali che devono essere protetti. Backup di tutte le macchine virtuali in tutti i server in un ambiente è una strategia comune. Con Azure Stack, esistono tuttavia alcune macchine virtuali che sono necessario eseguire il backup. Ad esempio, le macchine virtuali in un set di scalabilità sono considerate risorse temporanee che possono essere aggiunti o rimossi, a volte senza preavviso. Tutti i dati durevoli devono essere protetti vengono archiviati in un archivio diverso, ad esempio un archivio di database o un oggetto.

Considerazioni importanti per il backup di macchine virtuali in Azure Stack:

 - **Categorizzazione**
    - Si consideri un modello in cui gli utenti acconsentire esplicitamente a backup di macchine Virtuali.
    - Definire un ripristino contratto di servizio (SLA) basato sulla priorità delle applicazioni o l'impatto sull'azienda.
 - **Ridimensionare**
    - Prende in considerazione i backup sfalsati di onboarding un numero elevato di nuove macchine virtuali (se è necessario un backup).
    - Valutare i prodotti di backup che è possano acquisire e trasmettere i dati di backup per ridurre al minimo il contenuto di risorse della soluzione in modo efficiente.
    - Valutare i prodotti di backup che archiviano in modo efficace i dati di backup tramite backup incrementale o differenziale per ridurre al minimo la necessità di backup completi di tutte le macchine virtuali nell'ambiente.
 - **Restore**
    - Prodotti per il backup possono ripristinare i dati dell'applicazione all'interno di una macchina virtuale esistente, o l'intera risorsa di macchina virtuale i dischi virtuali e dischi virtuali associati. Lo schema di ripristino che è necessario dipende dal modo in cui si prevede di ripristinare l'applicazione e può rallentare l'applicazione il tempo di ripristino. Ad esempio, potrebbe essere più semplice ridistribuire SQL server da un modello e quindi ripristinare i database anziché ripristinare l'intera macchina virtuale o il set di macchine virtuali.

### <a name="replicationmanual-failover"></a>Failover della replica/manuale

Un approccio alternativo per supportare la disponibilità elevata è replicare le macchine virtuali dell'applicazione a un altro cloud e si basano su un failover manuale. La replica del sistema operativo, file binari dell'applicazione e i dati dell'applicazione può essere eseguita a livello di macchina virtuale oppure a livello del sistema operativo guest. Il failover viene gestito usando software aggiuntivi che non fa parte dell'applicazione.

Con questo approccio, l'applicazione viene distribuita in un cloud e le VM vengono replicate in altri cloud. Se viene attivato un failover, il database secondario macchine virtuali dovranno essere acceso nel cloud secondo. In alcuni scenari, il failover consente di creare i dischi di macchine virtuali e collega ad essi. Questo processo può richiedere molto tempo per il completamento, in particolare con un'applicazione multilivello che richiede una sequenza di avvio specifico. È anche possibile passaggi che devono essere eseguiti prima che l'applicazione è pronta per iniziare a soddisfare le richieste.

![Failover manuale di replica](media/azure-stack-manage-vm-backup/vm_backupdataflow_02.png)

 - RTO: Tempo di inattività espresso in minuti
 - RPO: Perdita di dati della variabile (in base alla frequenza di replica)
 - Topologia di distribuzione: Attiva/passiva in stand-by
 
### <a name="high-availabilityautomatic-failover"></a>Failover a disponibilità elevata/automatico

Per le applicazioni in cui l'azienda in grado di tollerare alcuni secondi o minuti di inattività e perdita di dati minima, è necessario prendere in considerazione una configurazione a disponibilità elevata. Applicazioni a disponibilità elevata sono progettate per rapidamente e automaticamente il ripristino da errori. Per gli errori hardware locale, dell'infrastruttura di Azure Stack implementa la disponibilità elevata nella rete fisica usando due cima commutatori rack. Per gli errori a livello di calcolo, Azure Stack utilizzi più nodi in un'unità di scala. A livello di macchina virtuale, è possibile utilizzare set di scalabilità in combinazione con domini di errore per assicurarsi che gli errori dei nodi non rendere inattive all'applicazione.

In combinazione con i set di scalabilità, l'applicazione sarà necessario supportare la disponibilità elevata in modo nativo o supporta l'uso di software di clustering. Ad esempio, Microsoft SQL Server supporta in modo nativo la disponibilità elevata per i database utilizzando la modalità commit sincrono. Tuttavia, se è possibile supportare la replica asincrona, vi sarà perdita di dati. Le applicazioni possono inoltre essere distribuite in un cluster di failover in cui il software di clustering gestisce il failover automatico dell'applicazione.

Con questo approccio, l'applicazione è attiva solo in un cloud, ma il software viene distribuito su più cloud. Altri cloud sono in modalità standby pronte avviare l'applicazione quando viene attivato il failover.

 - RTO: Tempo di inattività espresso in secondi
 - RPO: Perdita di dati minima
 - Topologia di distribuzione: Attiva/attiva in stand-by

### <a name="fault-tolerance"></a>Tolleranza di errore

Azure Stack fisica disponibilità ridondanza e l'infrastruttura del servizio di proteggere solo per l'hardware di livello gli errori e gli errori tale disco, alimentazione elettrica, porta di rete o nodo. Tuttavia, se l'applicazione deve essere sempre disponibile e non comportano la perdita di tutti i dati, è necessario implementare la tolleranza di errore in modo nativo nell'applicazione oppure utilizzare software aggiuntivo per abilitare la tolleranza di errore.

In primo luogo, è necessario assicurarsi che l'applicazione che vengono distribuite macchine virtuali tramite scala imposta per proteggerti da errori a livello di nodo. Per proteggere il cloud passerà offline, l'applicazione stessa deve essere distribuita già a un cloud diverso, in modo che può continuare a gestire le richieste senza interruzioni. Questo modello viene in genere definito per una distribuzione attiva / attiva.

Tenere presente che ogni cloud Azure Stack è indipendenti tra loro, in modo che i cloud vengono sempre considerati attivi da un punto di vista dell'infrastruttura. In questo caso, vengono distribuite più istanze attive dell'applicazione per uno o più cloud attivo.

 - RTO: Senza tempi di inattività
 - RPO: Nessuna perdita di dati
 - Topologia di distribuzione: Attiva/attiva

### <a name="no-recovery"></a>Nessun recupero

Alcune applicazioni nell'ambiente in uso potrebbero non essere necessario protezione contro la perdita di dati o tempi di inattività non pianificato. Ad esempio, le macchine virtuali usate per lo sviluppo e test in genere non è necessario essere ripristinati. È la decisione di eseguire senza protezione per un'applicazione o di una macchina virtuale specifica. Stack di Azure non offre il backup o la replica di macchine virtuali dall'infrastruttura sottostante. Simile ad Azure, è necessario acconsentire esplicitamente alla protezione per ogni macchina virtuale in ogni sottoscrizione.

 - RTO: Errore irreversibile
 - RPO: Perdita dei dati completa

## <a name="recommended-topologies"></a>Topologie consigliate

Considerazioni importanti per la distribuzione di Azure Stack:

|     | Recommendation | Commenti |
|-------------------------------------------------------------------------------------------------|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Backup e ripristino di macchine virtuali a una destinazione di backup esterno già distribuita nel Data Center | Consigliato | Sfruttare i vantaggi dell'infrastruttura di backup esistente e competenze operative. Assicurarsi di ridimensionare l'infrastruttura di backup in modo è possibile proteggere le istanze di macchine Virtuali aggiuntive. Assicurarsi che l'infrastruttura di backup non è presente in stretta vicinanza all'origine. È possibile ripristinare le macchine virtuali in Azure Stack, l'origine per un'istanza secondaria di Azure Stack o Azure. |
| Backup e ripristino di macchine virtuali a una destinazione di backup esterna dedicato ad Azure Stack | Consigliato | È possibile acquistare una nuova infrastruttura di backup o effettuare il provisioning infrastrutture backup dedicata per Azure Stack. Assicurarsi che l'infrastruttura di backup non è presente in stretta vicinanza all'origine. È possibile ripristinare le macchine virtuali in Azure Stack, l'origine per un'istanza secondaria di Azure Stack o Azure. |
| Backup e ripristino di macchine virtuali direttamente su Azure globale o un provider di servizi affidabili | Consigliato | Come è possibile soddisfare i requisiti normativi e la privacy dei dati, è possibile archiviare i backup in Azure globale o un provider di servizi attendibili. In teoria il provider di servizi esegue anche Azure Stack è ottenere coerenza nell'esperienza operativa quando esegue il ripristino. |
| Macchine virtuali replicate/failover a un'istanza separata di Azure Stack | Consigliato | In caso di failover, è necessario disporre di un cloud di Azure Stack secondo completamente operativa, è possibile evitare i tempi di inattività dell'applicazione estese. |
| Macchine virtuali replicate/failover direttamente in Azure o in un provider di servizi affidabili | Consigliato | Come è possibile soddisfare i requisiti normativi e la privacy dei dati, è possibile replicare i dati in Azure globale o un provider di servizi attendibili. In teoria il provider di servizi esegue anche Azure Stack è ottenere coerenza nell'esperienza operativa dopo il failover. |
| Distribuzione di destinazione di backup nello stesso cloud Azure Stack con i dati delle applicazioni | Non è consigliata | Evitare di archiviare i backup appartenenti alla stessa area di Azure Stack. Tempo di inattività non pianificato del cloud è possibile evitare i dati primari e i dati di backup. Se si sceglie di distribuire una destinazione di backup come un'appliance virtuale (per scopi di ottimizzazione per il backup e ripristino), è necessario assicurarsi di tutti i dati in modo continuo viene copiato in un percorso di backup esterno. |
| Distribuire le appliance di backup fisica in stesso rack in cui è installata la soluzione di Azure Stack | Non supportate | In questo momento, non è possibile connettersi tutti gli altri dispositivi nella parte superiore di opzioni di rack che non fanno parte della soluzione originale. |

## <a name="next-steps"></a>Passaggi successivi

Questo articolo contiene le linee guida generali per la protezione di macchine virtuali degli utenti distribuite in Azure Stack. Per informazioni sull'uso di servizi di Azure per proteggere macchine virtuali degli utenti, vedere:

 - [Usare Backup di Azure per eseguire il backup di file e le applicazioni in Azure Stack](https://docs.microsoft.com/azure/backup/backup-mabs-files-applications-azure-stack)
 - [Supporto di Server di Backup di Azure per Azure Stack](https://docs.microsoft.com/azure/backup/ ) 
 - [Supporto tecnico di Azure Site Recovery per Azure Stack](https://docs.microsoft.com/azure/site-recovery/)  

Per altre informazioni sui prodotti partner che offrono protezione delle macchine Virtuali in Azure Stack, fare riferimento a "[la protezione di applicazioni e i dati in Azure Stack](https://azure.microsoft.com/blog/protecting-applications-and-data-on-azure-stack/)."
