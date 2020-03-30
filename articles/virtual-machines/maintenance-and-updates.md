---
title: Manutenzione e aggiornamenti
description: Panoramica della manutenzione e degli aggiornamenti per le macchine virtuali in esecuzione in Azure.Overview of maintenance and updates for virtual machines running in Azure.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/18/2019
ms.author: shants
ms.openlocfilehash: eaf7616b3bd69828829342b4dca9247c009d3475
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250231"
---
# <a name="maintenance-for-virtual-machines-in-azure"></a>Manutenzione per macchine virtuali in Azure

Azure aggiorna periodicamente la propria piattaforma per migliorare l'affidabilità, le prestazioni e la sicurezza dell'infrastruttura host per le macchine virtuali. Lo scopo di questi aggiornamenti include l'applicazione di patch ai componenti software nell'ambiente host, l'aggiornamento dei componenti di rete e la rimozione delle autorizzazioni per l'hardware. 

Gli aggiornamenti raramente influiscono sulle macchine virtuali ospitate. Quando gli aggiornamenti hanno effetto, Azure sceglie il metodo meno efficace per gli aggiornamenti:When updates do have an effect, Azure chooses the least impactful method for updates:

- Se l'aggiornamento non richiede un riavvio, la macchina virtuale viene sospesa durante l'aggiornamento dell'host o la migrazione in tempo reale della macchina virtuale in un host già aggiornato. 
- Se la manutenzione richiede un riavvio, si riceve una notifica della manutenzione pianificata. Azure offre anche un intervallo di tempo in cui è possibile avviare la manutenzione manualmente, in un momento adatto alle esigenze. La finestra di auto-manutenzione è in genere di 30 giorni a meno che la manutenzione sia urgente. Azure sta investendo nelle tecnologie per ridurre il numero di casi in cui la manutenzione pianificata della piattaforma richiede il riavvio delle macchine virtuali. Per istruzioni sulla gestione della manutenzione pianificata, vedere Gestione delle notifiche di manutenzione pianificata tramite [l'interfaccia della riga di comando di](maintenance-notifications-cli.md)Azure, [PowerShell](maintenance-notifications-powershell.md) o il [portale.](maintenance-notifications-portal.md)

Questa pagina descrive come Azure esegue entrambi i tipi di manutenzione. Per altre informazioni sugli eventi non pianificati (interruzioni), vedere [Gestire la disponibilità delle macchine virtuali per Windows](./windows/manage-availability.md) o l'articolo corrispondente per [Linux.](./linux/manage-availability.md)

All'interno di una macchina virtuale, è possibile ricevere notifiche sulla manutenzione imminente [usando eventi pianificati per Windows](./windows/scheduled-events.md) o per [Linux.](./linux/scheduled-events.md)



## <a name="maintenance-that-doesnt-require-a-reboot"></a>Manutenzione che non richiede un riavvio

La maggior parte degli aggiornamenti della piattaforma non influisce sulle macchine virtuali dei clienti. Quando un aggiornamento senza impatto non è possibile, Azure sceglie il meccanismo di aggiornamento meno impattante per le macchine virtuali dei clienti. 

La maggior parte della manutenzione a impatto diverso da zero sospende la macchina virtuale per meno di 10 secondi. In alcuni casi, Azure usa meccanismi di manutenzione per la conservazione della memoria. Questi meccanismi sospendono la macchina virtuale per un massimo di 30 secondi e conservano la memoria nella RAM. La macchina virtuale viene quindi ripresa e l'orologio viene sincronizzato automaticamente. 

La manutenzione di conservazione della memoria funziona per oltre il 90% delle macchine virtuali di Azure.Memory-preserving maintenance works for more than 90 percent of Azure VMs. Non funziona per le serie G, M, N e H. Azure usa sempre più tecnologie di migrazione in tempo reale e migliora i meccanismi di manutenzione per la conservazione della memoria per ridurre la durata della pausa.  

Queste operazioni di manutenzione che non richiedono un riavvio vengono applicate un dominio di errore alla volta. Si fermano se ricevono segnali di salute di avvertimento. 

Questi tipi di aggiornamenti possono influire su alcune applicazioni. Quando la macchina virtuale viene migrata in tempo reale in un host diverso, alcuni carichi di lavoro sensibili potrebbero mostrare una leggera riduzione delle prestazioni nei pochi minuti che precedono la sospensione della macchina virtuale. Per preparare la manutenzione delle macchine virtuali e ridurre l'impatto durante la manutenzione di Azure, provare a [usare Eventi pianificati per Windows](./windows/scheduled-events.md) o [Linux](./linux/scheduled-events.md) per tali applicazioni. 

C'è anche una funzione, controllo di manutenzione, in anteprima pubblica che può aiutare a gestire la manutenzione che non richiede un riavvio. È necessario usare host dedicati di Azure o [una macchina virtuale isolata.](../security/fundamentals/isolation-choices.md) [Azure Dedicated Hosts](./linux/dedicated-hosts.md) Il controllo della manutenzione offre la possibilità di ignorare gli aggiornamenti della piattaforma e applicare gli aggiornamenti a scelta del tempo all'interno di una finestra attiva di 35 giorni. Per altre informazioni, vedere [Controllare gli aggiornamenti con Il controllo di manutenzione e l'interfaccia della riga di comando](maintenance-control-cli.md)di Azure.For more information, see Control updates with Maintenance Control and the Azure CLI .


### <a name="live-migration"></a>Migrazione in tempo reale

La migrazione in tempo reale è un'operazione che non richiede un riavvio e che mantiene la memoria per la macchina virtuale. Provoca una pausa o un congelamento, in genere non dura più di 5 secondi. Ad eccezione delle serie G, M, N e H, tutte le macchine virtuali dell'infrastruttura come servizio (IaaS) sono idonee per la migrazione in tempo reale. Le macchine virtuali idonee rappresentano più del 90% delle macchine virtuali IaaS distribuite nella flotta di Azure.Eligible VMs represent more than 90 percent of the IaaS VMs that are deployed to the Azure fleet. 

La piattaforma Azure avvia la migrazione in tempo reale negli scenari seguenti:The Azure platform starts live migration in the following scenarios:
- Manutenzione pianificata
- Errore hardware
- Ottimizzazioni dell'allocazione

Alcuni scenari di manutenzione pianificata usano la migrazione in tempo reale ed è possibile usare gli eventi pianificati per sapere in anticipo quando verranno avviate le operazioni di migrazione in tempo reale.

La migrazione in tempo reale può essere usata anche per spostare le macchine virtuali quando gli algoritmi di Azure Machine Learning prevedono un errore hardware imminente o quando si vuole ottimizzare le allocazioni di macchine virtuali. Per altre informazioni sulla modellazione predittiva che rileva istanze di hardware danneggiato, vedere Miglioramento della resilienza delle macchine virtuali di [Azure con l'apprendimento macchina predittivo e](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure)la migrazione in tempo reale. Le notifiche di migrazione in tempo reale vengono visualizzate nel portale di Azure nei log di Monitoraggio e integrità dei servizi, nonché in Eventi pianificati se si usano questi servizi.

## <a name="maintenance-that-requires-a-reboot"></a>Manutenzione che richiede un riavvio

Nel raro caso in cui le macchine virtuali devono essere riavviate per la manutenzione pianificata, si riceverà una notifica in anticipo. La manutenzione pianificata si conclude con due fasi: la fase self-service e una fase di manutenzione programmata.

Durante la *fase self-service,* che in genere dura quattro settimane, si avvia la manutenzione nelle macchine virtuali. Come parte del self-service, è possibile eseguire una query su ogni macchina virtuale per visualizzarne lo stato e il risultato dell'ultima richiesta di manutenzione.

Quando si avvia la manutenzione self-service, la macchina virtuale viene ridistribuita su un nodo già aggiornato. Poiché la macchina virtuale viene riavviata, il disco temporaneo viene perso e gli indirizzi IP dinamici associati all'interfaccia di rete virtuale vengono aggiornati.

Se si verifica un errore durante la manutenzione self-service, l'operazione viene interrotta, la macchina virtuale non viene aggiornata e si ottiene la possibilità di ritentare la manutenzione self-service. 

Al termine della fase self-service, inizia la fase di *manutenzione pianificata.* Durante questa fase, è comunque possibile eseguire una query per la fase di manutenzione, ma non è possibile avviare la manutenzione manualmente.

Per altre informazioni sulla gestione della manutenzione che richiede un riavvio, vedere Gestione delle notifiche di **manutenzione pianificata** tramite l'interfaccia [della riga di comando](maintenance-notifications-cli.md)di Azure, [PowerShell](maintenance-notifications-powershell.md) o il [portale](maintenance-notifications-portal.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Considerazioni sulla disponibilità durante la manutenzione pianificataAvailability considerations during scheduled maintenance 

Se si decide di attendere la fase di manutenzione pianificata, è necessario considerare alcuni aspetti per mantenere la massima disponibilità delle macchine virtuali. 

#### <a name="paired-regions"></a>Aree abbinate

Ogni area di Azure è associata a un'altra area all'interno della stessa area geografica. Insieme, fanno una coppia di regioni. Durante la fase di manutenzione pianificata, Azure aggiorna solo le macchine virtuali in una singola area di una coppia di aree. Ad esempio, durante l'aggiornamento della macchina virtuale negli Stati Uniti centro-settentrionali, Azure non aggiorna contemporaneamente alcuna macchina virtuale negli Stati Uniti centro-meridionali. Tuttavia, altre aree, ad esempio Europa settentrionale, possono essere sottoposte a manutenzione contemporaneamente a Stati Uniti orientali. Sapendo come funzionano le coppie di aree, è possibile distribuire meglio le VM tra le aree. Per altre informazioni, vedere [Coppie di aree di Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>Set di disponibilità e set di disponibilità

Quando si distribuisce un carico di lavoro in macchine virtuali di Azure, è possibile creare le macchine virtuali all'interno di un set di *disponibilità* per fornire disponibilità elevata all'applicazione. Usando i set di disponibilità, è possibile garantire che durante un'interruzione o un evento di manutenzione che richiedono un riavvio sia disponibile almeno una macchina virtuale.

All'interno di un set di disponibilità, le singole macchine virtuali vengono distribuite su un massimo di 20 domini di aggiornamento. Durante la manutenzione pianificata, viene aggiornato un solo dominio di aggiornamento alla volta. I domini di aggiornamento non vengono necessariamente aggiornati in sequenza. 

I set di *scalabilità* delle macchine virtuali sono una risorsa di calcolo di Azure che è possibile usare per distribuire e gestire un set di macchine virtuali identiche come singola risorsa. Il set di scalabilità viene distribuito automaticamente tra gli UL, ad esempio le macchine virtuali in un set di disponibilità. Come per i set di disponibilità, quando si usano i set di scalabilità, solo un UD viene aggiornato in un determinato momento durante la manutenzione pianificata.

Per altre informazioni sulla configurazione delle macchine virtuali per la disponibilità elevata, vedere [Gestire la disponibilità delle macchine virtuali per Windows](./windows/manage-availability.md) o l'articolo corrispondente per [Linux.](./linux/manage-availability.md)

#### <a name="availability-zones"></a>Zone di disponibilità

Le zone di disponibilità sono posizioni fisiche univoche all'interno di un'area di Azure.Availability zones are unique physical locations within an Azure region. Ogni zona è costituita da uno o più data center dotati di impianti indipendenti per l'alimentazione, il raffreddamento e la connettività di rete. Per garantire la resilienza, sono presenti almeno tre zone separate in tutte le aree abilitate. 

Una zona di disponibilità è una combinazione di un dominio di errore e un dominio di aggiornamento. Se si creano tre o più macchine virtuali in tre aree in un'area di Azure, le macchine virtuali vengono distribuite in modo efficace tra tre domini di errore e tre domini di aggiornamento. La piattaforma di Azure riconosce questa distribuzione in domini di aggiornamento per assicurarsi che le macchine virtuali in diverse aree non vengano aggiornate contemporaneamente.

Ogni aggiornamento dell'infrastruttura implementa zona per zona, all'interno di una singola area. Tuttavia, è possibile avere la distribuzione in corso nella zona 1 e una distribuzione diversa nella zona 2, contemporaneamente. Le distribuzioni non vengono tutte serializzate. Tuttavia, una singola distribuzione implementa solo una zona alla volta per ridurre i rischi.

## <a name="next-steps"></a>Passaggi successivi 

È possibile usare l'interfaccia della riga di comando di [Azure,](maintenance-notifications-cli.md) [Azure PowerShell](maintenance-notifications-powershell.md) o il [portale](maintenance-notifications-portal.md) per gestire la manutenzione pianificata. 