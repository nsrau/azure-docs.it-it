---
title: Manutenzione e aggiornamenti per le macchine virtuali in Azure | Microsoft Docs
description: Panoramica della manutenzione e degli aggiornamenti per le macchine virtuali in esecuzione in Azure.
services: virtual-machines
documentationcenter: ''
author: shants123
editor: ''
tags: azure-resource-manager,azure-service-management
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/18/2019
ms.author: shants
ms.openlocfilehash: 45dba8de8ef735bdb9df102ac367e6a1653bf481
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74807086"
---
# <a name="maintenance-for-virtual-machines-in-azure"></a>Manutenzione per macchine virtuali in Azure

Azure aggiorna periodicamente la propria piattaforma per migliorare l'affidabilità, le prestazioni e la sicurezza dell'infrastruttura host per le macchine virtuali. Lo scopo di questi aggiornamenti include l'applicazione di patch ai componenti software nell'ambiente host, l'aggiornamento dei componenti di rete e la rimozione delle autorizzazioni per l'hardware. 

Gli aggiornamenti influiscono raramente sulle macchine virtuali ospitate. Quando gli aggiornamenti hanno effetto, Azure sceglie il metodo meno significativo per gli aggiornamenti:

- Se l'aggiornamento non richiede un riavvio, la macchina virtuale viene sospesa durante l'aggiornamento dell'host oppure viene eseguita la migrazione in tempo reale della macchina virtuale a un host già aggiornato. 
- Se la manutenzione richiede un riavvio, si riceve una notifica della manutenzione pianificata. Azure fornisce anche un intervallo di tempo in cui è possibile avviare la manutenzione manualmente, in un momento adatto per l'utente. La finestra di manutenzione automatica è in genere di 30 giorni, a meno che la manutenzione non sia urgente. Azure investe nelle tecnologie per ridurre il numero di casi in cui la manutenzione della piattaforma pianificata richiede il riavvio delle macchine virtuali. Per istruzioni sulla gestione della manutenzione pianificata, vedere Gestione delle notifiche di manutenzione pianificata con l' [interfaccia](maintenance-notifications-cli.md)della riga di comando di Azure, [PowerShell](maintenance-notifications-powershell.md) o [portale](maintenance-notifications-portal.md).

Questa pagina descrive come Azure esegue entrambi i tipi di manutenzione. Per altre informazioni sugli eventi non pianificati (interruzioni), vedere [gestire la disponibilità delle macchine virtuali per Windows](./windows/manage-availability.md) o l'articolo corrispondente per [Linux](./linux/manage-availability.md).

All'interno di una macchina virtuale è possibile ricevere notifiche sulla manutenzione imminente [usando eventi pianificati per Windows](./windows/scheduled-events.md) o per [Linux](./linux/scheduled-events.md).



## <a name="maintenance-that-doesnt-require-a-reboot"></a>Manutenzione che non richiede il riavvio

La maggior parte degli aggiornamenti della piattaforma non influisce sulle VM dei clienti Quando non è possibile eseguire un aggiornamento a nessun effetto, Azure sceglie il meccanismo di aggiornamento che è meno incisivo per le macchine virtuali dei clienti. 

La manutenzione con un effetto più diverso da zero sospende la macchina virtuale per meno di 10 secondi. In alcuni casi, Azure usa meccanismi di manutenzione con mantenimento della memoria. Questi meccanismi sospendono la VM per un massimo di 30 secondi e mantengono la memoria in RAM. La macchina virtuale viene quindi ripresa e il relativo clock viene sincronizzato automaticamente. 

La manutenzione con mantenimento della memoria funziona per più del 90% delle VM di Azure. Non funziona per la serie G, M, N e H. Azure usa sempre le tecnologie di migrazione in tempo reale e migliora i meccanismi di manutenzione con mantenimento della memoria per ridurre le durate di sospensione.  

Per queste operazioni di manutenzione che non richiedono un riavvio viene applicato un dominio di errore alla volta. Si fermano se ricevono segnali di avviso di integrità. 

Questi tipi di aggiornamenti possono interessare alcune applicazioni. Quando si esegue la migrazione in tempo reale della macchina virtuale in un host diverso, alcuni carichi di lavoro sensibili potrebbero presentare una lieve riduzione delle prestazioni nei pochi minuti che portano alla pausa della macchina virtuale. Per preparare la manutenzione della macchina virtuale e ridurre l'effetto durante la manutenzione di Azure, provare a [usare eventi pianificati per Windows](./windows/scheduled-events.md) o [Linux](./linux/scheduled-events.md) per tali applicazioni. 

È disponibile anche una funzionalità di controllo della manutenzione, in versione di anteprima pubblica, che può aiutare a gestire la manutenzione che non richiede un riavvio. È necessario usare [host dedicati di Azure](./linux/dedicated-hosts.md) o una [macchina virtuale isolata](../security/fundamentals/isolation-choices.md). Il controllo di manutenzione offre la possibilità di ignorare gli aggiornamenti della piattaforma e di applicare gli aggiornamenti a propria scelta in un intervallo di 35 giorni. Per altre informazioni, vedere [controllare gli aggiornamenti con il controllo di manutenzione e l'interfaccia della](maintenance-control-cli.md)riga di comando di Azure.


### <a name="live-migration"></a>Migrazione in tempo reale

La migrazione in tempo reale è un'operazione che non richiede un riavvio e che conserva la memoria per la macchina virtuale. Causa una pausa o un blocco, che in genere non dura più di 5 secondi. Ad eccezione delle serie G, M, N e H, tutte le VM IaaS (Infrastructure as a Service) sono idonee per la migrazione in tempo reale. Le macchine virtuali idonee rappresentano più del 90% delle VM IaaS distribuite nella flotta di Azure. 

La piattaforma Azure avvia la migrazione in tempo reale negli scenari seguenti:
- Manutenzione pianificata
- Errore hardware
- Ottimizzazioni di allocazione

In alcuni scenari di manutenzione pianificata viene utilizzata la migrazione in tempo reale ed è possibile utilizzare Eventi pianificati per stabilire in anticipo l'avvio delle operazioni di migrazione in tempo reale.

La migrazione in tempo reale può essere usata anche per spostare le macchine virtuali quando Azure Machine Learning algoritmi stimano un errore hardware imminente o quando si desidera ottimizzare le allocazioni delle macchine virtuali. Per altre informazioni sulla modellazione predittiva che rileva le istanze di hardware danneggiato, vedere [miglioramento della resilienza delle macchine virtuali di Azure con apprendimento automatico predittivo e migrazione in tempo reale](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure). Le notifiche di migrazione in tempo reale vengono visualizzate nel portale di Azure nei log di monitoraggio e integrità dei servizi, nonché in Eventi pianificati se si usano questi servizi.

## <a name="maintenance-that-requires-a-reboot"></a>Manutenzione che richiede un riavvio

Nel raro caso in cui le VM debbano essere riavviate per la manutenzione pianificata, si riceverà una notifica in anticipo. La manutenzione pianificata prevede due fasi: la fase self-service e una fase di manutenzione pianificata.

Durante la *fase self-service*, che in genere dura quattro settimane, si avvia la manutenzione sulle VM. Come parte della modalità self-service, è possibile eseguire una query su ogni macchina virtuale per visualizzarne lo stato e il risultato dell'ultima richiesta di manutenzione.

Quando si avvia la manutenzione self-service, la macchina virtuale viene ridistribuita su un nodo già aggiornato. Poiché la macchina virtuale viene riavviata, il disco temporaneo viene perso e gli indirizzi IP dinamici associati all'interfaccia di rete virtuale vengono aggiornati.

Se si verifica un errore durante la manutenzione Self-Service, l'operazione viene arrestata, la macchina virtuale non viene aggiornata e si ottiene l'opzione per ripetere la manutenzione self-service. 

Al termine della fase self-service, viene avviata la *fase di manutenzione pianificata* . Durante questa fase, è ancora possibile eseguire una query per la fase di manutenzione, ma non è possibile avviare manualmente la manutenzione.

Per altre informazioni sulla gestione della manutenzione che richiede un riavvio, vedere **gestione delle notifiche di manutenzione pianificata** usando l' [interfaccia](maintenance-notifications-cli.md)della riga di comando di Azure, [PowerShell](maintenance-notifications-powershell.md) o il [portale](maintenance-notifications-portal.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Considerazioni sulla disponibilità durante la manutenzione pianificata 

Se si decide di attendere fino alla fase di manutenzione pianificata, è necessario prendere in considerazione alcune considerazioni per mantenere la disponibilità più elevata delle VM. 

#### <a name="paired-regions"></a>Aree abbinate

Ogni area di Azure è associata a un'altra area all'interno delle stesse vicinanze geografiche. Insieme, fanno una coppia di aree. Durante la fase di manutenzione pianificata, Azure aggiorna solo le macchine virtuali in una singola area di una coppia di aree. Ad esempio, durante l'aggiornamento della macchina virtuale negli Stati Uniti centro-settentrionali, Azure non aggiorna alcuna macchina virtuale negli Stati Uniti centro-meridionali nello stesso momento. Tuttavia, altre aree, ad esempio Europa settentrionale, possono essere sottoposte a manutenzione contemporaneamente a Stati Uniti orientali. Sapendo come funzionano le coppie di aree, è possibile distribuire meglio le VM tra le aree. Per altre informazioni, vedere [Coppie di aree di Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>Set di disponibilità e set di disponibilità

Quando si distribuisce un carico di lavoro in macchine virtuali di Azure, è possibile creare le macchine virtuali all'interno di un *set di disponibilità* per garantire la disponibilità elevata per l'applicazione. Usando i set di disponibilità, è possibile assicurarsi che durante un'interruzione o un evento di manutenzione che richiede un riavvio sia disponibile almeno una macchina virtuale.

All'interno di un set di disponibilità, le singole macchine virtuali vengono distribuite su un massimo di 20 domini di aggiornamento. Durante la manutenzione pianificata, viene aggiornato un solo dominio di aggiornamento in un determinato momento. I domini di aggiornamento non vengono necessariamente aggiornati in modo sequenziale. 

I *set di scalabilità* di macchine virtuali sono una risorsa di calcolo di Azure che è possibile usare per distribuire e gestire un set di macchine virtuali identiche come una singola risorsa. Il set di scalabilità viene distribuito automaticamente tra i domini di errore, come le macchine virtuali in un set di disponibilità. Come per i set di disponibilità, quando si usano i set di scalabilità, solo un UD viene aggiornato in un determinato momento durante la manutenzione pianificata.

Per altre informazioni sulla configurazione delle macchine virtuali per la disponibilità elevata, vedere [gestire la disponibilità delle macchine virtuali per Windows](./windows/manage-availability.md) o l'articolo corrispondente per [Linux](./linux/manage-availability.md).

#### <a name="availability-zones"></a>Zone di disponibilità

Le zone di disponibilità sono posizioni fisiche univoche all'interno di un'area di Azure. Ogni zona è costituita da uno o più data center dotati di impianti indipendenti per l'alimentazione, il raffreddamento e la connettività di rete. Per garantire la resilienza, sono presenti almeno tre zone separate in tutte le aree abilitate. 

Una zona di disponibilità è una combinazione di un dominio di errore e un dominio di aggiornamento. Se si creano tre o più macchine virtuali in tre zone in un'area di Azure, le macchine virtuali vengono distribuite in modo efficace tra tre domini di errore e tre domini di aggiornamento. La piattaforma di Azure riconosce questa distribuzione in domini di aggiornamento per assicurarsi che le macchine virtuali in diverse aree non vengano aggiornate contemporaneamente.

Ogni aggiornamento dell'infrastruttura esegue il rollup della zona per zona, all'interno di una singola area. Tuttavia, è possibile che la distribuzione sia in corso in Zona 1 e in una distribuzione diversa Zona 2, allo stesso tempo. Le distribuzioni non sono tutte serializzate. Tuttavia, una singola distribuzione esegue il rollup solo di una zona alla volta per ridurre il rischio.

## <a name="next-steps"></a>Passaggi successivi 

È possibile usare l' [interfaccia](maintenance-notifications-cli.md)della riga di comando di Azure, [Azure PowerShell](maintenance-notifications-powershell.md) o il [portale](maintenance-notifications-portal.md) per gestire la manutenzione pianificata. 