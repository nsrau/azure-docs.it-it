---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 12/14/2018
ms.author: shants
ms.custom: include file
ms.openlocfilehash: 34723a6ee37e54ea2d81e6d1143672e3ccb30d1e
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/27/2018
ms.locfileid: "53805740"
---
Azure aggiorna periodicamente la piattaforma per migliorare l'affidabilità, le prestazioni e la sicurezza dell'infrastruttura host per le macchine virtuali. Questi aggiornamenti includono l'applicazione di patch ai componenti software nell'ambiente host, l'aggiornamento dei componenti di rete e la rimozione delle autorizzazioni per l'hardware. La maggior parte di questi aggiornamenti non ha alcuna conseguenza sulle macchine virtuali ospitate. È possibile, tuttavia, che gli aggiornamenti abbiano conseguenze sulle macchine virtuali ospitate e, in questo caso, Azure sceglie il metodo di aggiornamento a minor impatto:

- Se è possibile un aggiornamento senza riavvio, durante l'aggiornamento dell'host la macchina virtuale viene sospesa o migrata in tempo reale su un host già aggiornato.

- Se la manutenzione richiede un riavvio, si riceve un avviso che informa per quando è pianificata la manutenzione. Azure indicherà anche una finestra temporale in cui avviare la manutenzione manualmente, in un momento opportuno per l'utente. Azure investe in tecnologie che consentano di ridurre i casi in cui le macchine virtuali devono essere riavviate per la manutenzione pianificata della piattaforma. 

Questa pagina descrive come Azure esegue entrambi i tipi di manutenzione. Per altre informazioni sugli eventi non pianificati (interruzioni), vedere Gestire la disponibilità delle macchine virtuali per [Windows](../articles/virtual-machines/windows/manage-availability.md) o [Linux](../articles/virtual-machines/linux/manage-availability.md).

È possibile ottenere notifiche sulla manutenzione programmata direttamente nella macchina virtuale tramite Eventi pianificati per [Windows](../articles/virtual-machines/windows/scheduled-events.md) o [Linux](../articles/virtual-machines/linux/scheduled-events.md).

Per informazioni sulle procedure di gestione della manutenzione pianificata, vedere "Gestire gli avvisi relativi alla manutenzione pianificata" per [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) o [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="memory-preserving-maintenance"></a>Manutenzione con mantenimento della memoria

Per la maggior parte degli aggiornamenti senza riavvio, l'obiettivo è un'interruzione della macchina virtuale inferiore a 10 secondi. Nei casi in cui vengono usati meccanismi di manutenzione con mantenimento della memoria, la macchina virtuale viene sospesa per un massimo di 30 secondi e la memoria viene mantenuta nella RAM. La macchina virtuale viene quindi ripresa e l'orologio della macchina virtuale viene sincronizzato automaticamente. Azure usa sempre più spesso tecnologie di migrazione in tempo reale e meccanismi di manutenzione con mantenimento della memoria per ridurre la durata delle interruzioni.

Queste operazioni di manutenzione senza riavvio sono applicate dominio di errore per dominio di errore e l'avanzamento viene arrestato se vengono ricevuti segnali di avviso di integrità. 

Alcune applicazioni potrebbero essere interessate da questi tipi di aggiornamenti. Nel caso in cui la macchina virtuale venga migrata in tempo reale su un host diverso, è possibile che alcuni carichi di lavoro subiscano un lieve peggioramento delle prestazioni nei pochi minuti che precedono la sospensione della macchina virtuale. In questi casi potrebbe essere utile usare Eventi pianificati per [Windows](../articles/virtual-machines/windows/scheduled-events.md) o [Linux](../articles/virtual-machines/linux/scheduled-events.md) per preparare la manutenzione della macchina virtuale e non generare alcun impatto durante la manutenzione di Azure. I team di Azure stanno lavorando anche su funzionalità di controllo della manutenzione per scenari particolarmente sensibili. 


## <a name="maintenance-requiring-a-reboot"></a>Manutenzione per cui è necessario un riavvio

Nel caso raro in cui sia necessario riavviare le macchine virtuali per la manutenzione pianificata, si riceve una notifica in anticipo. La manutenzione pianificata prevede due fasi: la finestra self-service e una finestra di manutenzione pianificata.

La **finestra self-service** consente di avviare la manutenzione delle macchine virtuali. Durante questa fase, è possibile eseguire query su ogni VM per visualizzarne lo stato e controllare il risultato dell'ultima richiesta di manutenzione.

Quando si avvia la manutenzione self-service, la macchina virtuale viene ridistribuita su un nodo già aggiornato. A causa dei riavvii della VM, il disco temporaneo viene perso e gli indirizzi IP dinamici associati all'interfaccia di rete virtuale vengono aggiornati.

Se si avvia la manutenzione self-service e si verifica un errore durante il processo, l'operazione viene arrestata, la VM non viene aggiornata ed è possibile scegliere di riprovare ad avviare la manutenzione self-service. 

Quando la finestra self-service è trascorsa, inizia la **finestra di manutenzione pianificata**. Durante questa finestra temporale, è ancora possibile eseguire una query sulla finestra di manutenzione, ma non avviare la manutenzione manualmente.

Per informazioni sulla gestione della manutenzione che richiede un riavvio, vedere "Gestione degli avvisi di manutenzione pianificata" per [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) o [Windows](../articles/virtual-machines/windows/maintenance-notifications.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Considerazioni sulla disponibilità durante la manutenzione pianificata 

Se si decide di attendere fino alla visualizzazione della finestra di manutenzione pianificata, sono necessarie alcune considerazioni per mantenere la disponibilità più elevata possibile delle VM. 

#### <a name="paired-regions"></a>Aree associate

Ogni area di Azure è associata a un'altra area con la stessa ubicazione geografica e insieme formano una coppia di aree. Durante la manutenzione pianificata, Azure aggiornerà solo le macchine virtuali di una sola area di una coppia di aree. Ad esempio, quando si aggiornano le macchine virtuali negli Stati Uniti centro-settentrionali, Azure non aggiornerà contemporaneamente le macchine virtuali negli Stati Uniti centro-meridionali. Tuttavia, altre aree, ad esempio Europa settentrionale, possono essere sottoposte a manutenzione contemporaneamente a Stati Uniti orientali. Sapendo come funzionano le coppie di aree, è possibile distribuire meglio le VM tra le aree. Per altre informazioni, vedere [Coppie di aree di Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>Set di disponibilità e set di disponibilità

Quando si distribuisce un carico di lavoro nelle VM di Azure, è possibile creare le VM in un set di disponibilità per garantire una disponibilità elevata per l'applicazione. In questo modo si garantisce che, in caso di interruzione o di eventi di manutenzione che comportano molti riavvii, sia disponibile almeno una macchina virtuale.

In un set di disponibilità, le singole macchine virtuali vengono distribuite su un massimo di 20 domini di aggiornamento. Durante la manutenzione pianificata, in un dato momento viene aggiornato un unico dominio di aggiornamento. L'ordine di aggiornamento dei domini non è necessariamente sequenziale. 

I set di scalabilità delle macchine virtuali sono una risorsa di calcolo di Azure che consente di distribuire e gestire un set di macchine virtuali identiche come una singola risorsa. Il set di scalabilità viene distribuito automaticamente nei domini di aggiornamento, ad esempio le VM in un set di disponibilità. Come con i set di disponibilità, anche con i set di scalabilità viene aggiornato un solo dominio di aggiornamento per volta durante la manutenzione pianificata.

Per altre informazioni sulla configurazione delle macchine virtuali per la disponibilità elevata, vedere Gestire la disponibilità delle macchine virtuali per [Windows](../articles/virtual-machines/windows/manage-availability.md) o [Linux](../articles/virtual-machines/linux/manage-availability.md).
