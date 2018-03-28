---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 1f4bd0d81d16997a9d627f918d77cb8e770a5b53
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>Informazioni sui riavvii delle VM: manutenzione e tempo di inattività
Sono tre gli scenari che possono interessare la macchina virtuale in Azure: manutenzione dell'hardware non pianificata, tempo di inattività imprevisto e manutenzione pianificata.

* **Un evento di manutenzione hardware non pianificata** si verifica quando la piattaforma Azure prevede che stanno per verificarsi problemi all'hardware o a un componente della piattaforma associato a un computer fisico. Quando la piattaforma prevede un errore, genera un evento di manutenzione hardware non pianificata per ridurre l'impatto sulle macchine virtuali ospitate in tale hardware. Azure usa la tecnologia Live Migration per eseguire la migrazione delle macchine virtuali dall'hardware non funzionante a un computer fisico integro. Live Migration è un'operazione di mantenimento della VM che sospende la macchina virtuale solo per un breve periodo. Memoria, file aperti e connessioni di rete vengono conservati, ma le prestazioni potrebbero subire un rallentamento prima e/o dopo l'evento. Nei casi in cui non è possibile usare Live Migration, si verificheranno problemi di tempi di inattività imprevisti nella VM, come descritto sotto.


* **Un tempo di inattività imprevisto** ha luogo raramente quando si verifica un guasto dell'hardware o dell'infrastruttura fisica sottostante la macchina virtuale. Può trattarsi, ad esempio, di errori della rete locale, guasti di un disco locale o altri errori a livello di rack. Quando viene rilevato un errore di questo tipo, la piattaforma Azure esegue automaticamente la migrazione (riparazione) della macchina virtuale a un computer fisico integro nello stesso data center. Durante la procedura di riparazione, nelle macchine virtuali si verificano tempi di inattività (riavvio) e in alcuni casi la perdita dell'unità temporanea. Il sistema operativo e i dischi dati collegati vengono sempre conservati. 

  Le macchine virtuali possono anche subire tempi di inattività nell'improbabile eventualità di un'interruzione o emergenza che interessa un intero data center o anche un'intera area. Per questi scenari, Azure offre opzioni di protezione che includono [zone di disponibilità](../articles/availability-zones/az-overview.md) e [aree abbinate](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions).

* Gli **eventi di manutenzione pianificata** sono aggiornamenti periodici effettuati da Microsoft sulla piattaforma Azure sottostante per migliorare l'affidabilità, la sicurezza e le prestazioni complessive dell'infrastruttura della piattaforma su cui sono in esecuzione le macchine virtuali. La maggior parte di questi aggiornamenti viene eseguita senza alcuna conseguenza per le macchine virtuali o i servizi cloud. Vedere [Manutenzione con mantenimento delle macchine virtuali](https://docs.microsoft.com/azure/virtual-machines/windows/preserving-maintenance). Anche se la piattaforma Azure cerca di usare la manutenzione con mantenimento delle VM in tutte le occasioni possibili, in alcuni rari casi questi aggiornamenti richiedono un riavvio della macchina virtuale per applicare gli aggiornamenti necessari all'infrastruttura sottostante. In tal caso, è possibile eseguire la manutenzione pianificata di Azure con l'operazione di ridistribuzione della manutenzione avviando la manutenzione per le VM nell'intervallo di tempo adatto. Per altre informazioni, vedere [Manutenzione pianificata per le macchine virtuali](https://docs.microsoft.com/azure/virtual-machines/windows/planned-maintenance/).


Per ridurre le conseguenze dei tempi di inattività causati da uno o più di questi eventi, è possibile attuare le seguenti procedure consigliate per aumentare la disponibilità delle macchine virtuali:

* [Configurare più macchine virtuali in un set di disponibilità per la ridondanza]
* [Usare Managed Disks per le macchine virtuali nel set di disponibilità]
* [Usare eventi pianificati per rispondere in modo proattivo agli eventi che hanno impatto sulle macchine virtuali] (https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events)
* [Configurare ogni livello dell'applicazione in set di disponibilità separati]
* [Combinare il bilanciamento del carico con set di disponibilità]
* [Usare le zone di disponibilità per la protezione dagli errori a livello di data center]

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Configurare più macchine virtuali in un set di disponibilità per la ridondanza
Per garantire la ridondanza dell'applicazione, è consigliabile raggruppare due o più macchine virtuali in un set di disponibilità. Questa configurazione in un data center assicura infatti che, nel corso di un evento di manutenzione pianificata o non pianificata, almeno una delle macchine virtuali sia sempre disponibile e soddisfi per almeno il 99,95% i requisiti del contratto di servizio di Azure. Per altre informazioni, vedere [Contratto di Servizio per Macchine virtuali](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

> [!IMPORTANT]
> Evitare di lasciare un'unica istanza di macchina virtuale in un set di disponibilità. Le macchine virtuali in questa configurazione non si qualificano per la garanzia del contratto di servizio e sono soggette a tempi di inattività durante gli eventi di manutenzione pianificata di Azure, se non quando una macchina virtuale singola usa [Archiviazione Premium di Azure](../articles/virtual-machines/windows/premium-storage.md). Il contratto di servizio di Azure si applica alle macchine virtuali singole che usano Archiviazione Premium.

A ciascuna macchina virtuale nel set di disponibilità viene assegnato un **dominio di aggiornamento** e un **dominio di errore** dalla piattaforma Azure sottostante. Per ogni set di disponibilità vengono assegnati cinque domini di aggiornamento non configurabili dall'utente per impostazione predefinita, è possibile quindi aumentare le distribuzioni Resource Manager per fornire fino a venti domini di aggiornamento, per indicare i gruppi di macchine virtuali con il relativo hardware fisico sottostante che è possibile riavviare contemporaneamente. Se in un set di disponibilità vengono configurate più di cinque macchine virtuali, la sesta macchina viene inserita nello stesso dominio di aggiornamento della prima, la settima nel dominio di aggiornamento della seconda e così via. I domini di aggiornamento non vengono necessariamente riavviati in ordine sequenziale durante la manutenzione pianificata, ma ne viene riavviato uno solo alla volta. Un dominio di aggiornamento riavviato ha 30 minuti di tempo per eseguire il ripristino prima che venga avviata la manutenzione in un altro dominio di aggiornamento.

I domini di errore definiscono il gruppo di macchine virtuali che condividono una fonte di alimentazione e uno switch di rete comuni. Per impostazione predefinita, le macchine virtuali configurate in un set di disponibilità vengono suddivise tra un massimo di tre domini di errore per le distribuzioni di Resource Manager e in due domini di errore per la distribuzione classica. Il raggruppamento di più macchine virtuali in un set di disponibilità non garantisce la protezione dell'applicazione da eventuali errori del sistema operativo o di singole applicazioni, ma limita le conseguenze prodotte da potenziali guasti dell'hardware fisico e interruzioni di rete o di alimentazione.

<!--Image reference-->
   ![Rappresentazione concettuale della configurazione di domini di aggiornamento e di errore](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>Usare Managed Disks per le macchine virtuali nel set di disponibilità
Se si usano macchine virtuali con dischi non gestiti, è fortemente consigliabile [convertire le macchine virtuali nel set di disponibilità per l'uso di Managed Disks](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md).

[Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md) offre una maggiore affidabilità per i set di disponibilità, perché fa in modo che i dischi delle macchine virtuali in un set di disponibilità siano sufficientemente isolati gli uni dagli altri per evitare singoli punti di errore. Ciò avviene inserendo automaticamente i dischi in domini di errore di archiviazione diversi (cluster di archiviazione) e allineandoli al dominio di errore della macchina virtuale. Se un dominio di errore di archiviazione non riesce a causa di un errore hardware o software, risulterà in errore solo l'istanza della macchina virtuale con dischi nel dominio di errore di archiviazione.
![Domini di errore di Managed Disks](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

> [!IMPORTANT]
> Il numero di domini di errore per i set di disponibilità gestiti dipende dall'area: due o tre per area. La tabella seguente illustra il numero per area

[!INCLUDE [managed-disks-common-fault-domain-region-list](managed-disks-common-fault-domain-region-list.md)]

Se si intende usare macchine virtuali con [dischi non gestiti](../articles/virtual-machines/windows/about-disks-and-vhds.md#types-of-disks), seguire queste procedure consigliate per gli account di archiviazione in cui i dischi rigidi virtuali (VHD) delle macchine virtuali vengono archiviati come [BLOB di pagine](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs).

1. **Mantenere tutti i dischi (sistema operativo e dati) associati a una macchina virtuale nello stesso account di archiviazione**
2. **Esaminare i [limiti](../articles/storage/common/storage-scalability-targets.md) al numero di dischi non gestiti in un account di archiviazione** prima di aggiungere altri dischi rigidi virtuali a un account di archiviazione
3. **Usare un account di archiviazione separato per ogni macchina virtuale di un set di disponibilità.** Non condividere gli account di archiviazione con più macchine virtuali in uno stesso set di disponibilità. Le macchine virtuali di set di disponibilità diversi possono condividere gli account di archiviazione, purché vengano seguite le procedure consigliate indicate in precedenza in ![Domini di errore dei dischi non gestiti](./media/virtual-machines-common-manage-availability/umd-updated.png)

## <a name="configure-each-application-tier-into-separate-availability-sets"></a>Configurare ogni livello dell'applicazione in set di disponibilità separati
Se le macchine virtuali sono quasi identiche e svolgono tutte la stessa funzione per l'applicazione, è consigliabile configurare un set di disponibilità per ogni livello dell'applicazione.  Se in un set di disponibilità si definiscono due livelli diversi, è possibile riavviare contemporaneamente tutte le macchine virtuali dello stesso livello applicazione. Configurando almeno due macchine virtuali per ogni livello di un set di disponibilità, si garantisce la disponibilità di almeno una macchina virtuale in ogni livello.

Ad esempio, è possibile inserire in un unico set di disponibilità tutte le macchine virtuali che si trovano nel front-end dell'applicazione che esegue IIS, Apache e Nginx. Accertarsi che in uno stesso set di disponibilità siano inserite solo le macchine virtuali del front-end e, analogamente, che in uno specifico set di disponibilità siano inserite solo le macchine virtuali livello dati, ad esempio le macchine virtuali di SQL Server replicate o MySQL.

<!--Image reference-->
   ![Livelli di applicazione](./media/virtual-machines-common-manage-availability/application-tiers.png)

## <a name="combine-a-load-balancer-with-availability-sets"></a>Combinare il bilanciamento del carico con set di disponibilità
Per ottenere una resilienza elevata dell'applicazione, è possibile combinare [Azure Load Balancer](../articles/load-balancer/load-balancer-overview.md) con un set di disponibilità. Il servizio di bilanciamento del carico distribuisce il traffico tra più macchine virtuali ed è incluso nelle macchine virtuali di livello Standard. Non tutti i livelli delle macchine virtuali includono Azure Load Balancer. Per altre informazioni sul bilanciamento del carico delle macchine virtuali, vedere [Load Balancing virtual machines](../articles/virtual-machines/virtual-machines-linux-load-balance.md) (Bilanciamento del carico delle macchine virtuali).

Se il bilanciamento del carico non è configurato in modo da bilanciare il traffico tra più macchine virtuali, qualsiasi evento di manutenzione pianificata influirà sull'unica macchina virtuale di gestione del traffico, determinando un'interruzione del livello di applicazione. Associando più macchine virtuali dello stesso livello a un unico servizio di bilanciamento del carico e a uno stesso set di disponibilità, si garantisce che il traffico sia sempre gestito da almeno un'istanza.

## <a name="use-availability-zones-to-protect-from-datacenter-level-failures"></a>Usare le zone di disponibilità per la protezione dagli errori a livello di data center

[Zone di disponibilità](../articles/availability-zones/az-overview.md) (anteprima), un'alternativa ai set di disponibilità, espande il livello di controllo per mantenere la disponibilità delle applicazioni e dei dati nelle macchine virtuali. Una zona di disponibilità è una zona fisicamente separata in un'area di Azure. Esistono tre zone di disponibilità per ogni area di Azure supportata. Ogni zona di disponibilità ha alimentazione, rete e raffreddamento a sé ed è separata in modo logico da altre zone di disponibilità nell'area di Azure. Progettando le soluzioni in modo che usino VM replicate nelle zone, è possibile proteggere app e dati dalla perdita di un data center. Se una zona è compromessa, le app e i dati replicati diventano immediatamente disponibili in un'altra zona. 

![Zone di disponibilità](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

[!INCLUDE [availability-zones-preview-statement.md](availability-zones-preview-statement.md)]

Altre informazioni sulla distribuzione di una macchina virtuale [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) o [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) in una zona di disponibilità.


<!-- Link references -->
[Configurare più macchine virtuali in un set di disponibilità per la ridondanza]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Configurare ogni livello dell'applicazione in set di disponibilità separati]: #configure-each-application-tier-into-separate-availability-sets
[Combinare il bilanciamento del carico con set di disponibilità]: #combine-a-load-balancer-with-availability-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[Usare Managed Disks per le macchine virtuali nel set di disponibilità]: #use-managed-disks-for-vms-in-an-availability-set
[Usare le zone di disponibilità per la protezione dagli errori a livello di data center]: #use-availability-zones-to-protect-from-datacenter-level-failures
