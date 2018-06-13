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
ms.openlocfilehash: e484dac645ff2e5867d2e652c389a9950e8bac12
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "30196441"
---
Azure esegue periodicamente aggiornamenti per migliorare l'affidabilità, le prestazioni e la sicurezza dell'infrastruttura host per le macchine virtuali. Questi aggiornamenti includono l'applicazione di patch ai componenti software nell'ambiente host (ad esempio, sistema operativo, hypervisor e diversi agenti distribuiti nell'host), l'aggiornamento dei componenti di rete e la rimozione delle autorizzazioni per l'hardware. La maggior parte di questi aggiornamenti viene eseguita senza alcuna conseguenza per le macchine virtuali ospitate. Ci sono casi tuttavia in cui gli aggiornamenti hanno conseguenze:

- Se un aggiornamento senza riavvio del computer è possibile, Azure utilizza la manutenzione con mantenimento della memoria per sospendere la VM durante l'aggiornamento dell'host o lo spostamento della VM in un host già aggiornato.

- Se la manutenzione richiede un riavvio, si riceve un avviso che informa per quando è pianificata la manutenzione. In questi casi, verrà anche indicata una finestra temporale in cui avviare la manutenzione manualmente, in un momento opportuno per l'utente.

Questa pagina descrive come Microsoft Azure esegue entrambi i tipi di manutenzione. Per altre informazioni sugli eventi non pianificati (interruzioni), vedere Gestire la disponibilità delle macchine virtuali [Windows] (../articles/virtual-machines/windows/manage-availability.md) o [Linux](../articles/virtual-machines/linux/manage-availability.md).

Le applicazioni in esecuzione in una macchina virtuale possono raccogliere informazioni sugli aggiornamenti futuri usando il servizio metadati di Azure per [Windows](../articles/virtual-machines/windows/instance-metadata-service.md) o [Linux] (../articles/virtual-machines/linux/instance-metadata-service.md).

Per informazioni sulle procedure di gestione della manutenzione pianificata, vedere "Gestire gli avvisi relativi alla manutenzione pianificata" per [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) o [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="memory-preserving-maintenance"></a>Manutenzione con mantenimento della memoria

Quando gli aggiornamenti non richiedono un riavvio completo, i meccanismi di manutenzione con mantenimento della memoria consentono di limitare l'impatto sulla macchina virtuale. La macchina virtuale viene sospesa per un massimo di 30 secondi e la memoria viene mantenuta nella RAM, mentre l'ambiente host applica gli aggiornamenti e le patch necessari o sposta la VM in un host già aggiornato. La macchina virtuale viene quindi ripresa e l'orologio della macchina virtuale viene sincronizzato automaticamente. 

Per le VM nei set di disponibilità, i domini di aggiornamento vengono aggiornati uno alla volta. Tutte le VM in un dominio di aggiornamento vengono sospese, aggiornate e quindi riprese prima che la manutenzione pianificata passi al dominio di aggiornamento successivo.

Alcune applicazioni potrebbero essere interessate da questi tipi di aggiornamenti. Le applicazioni che eseguono l'elaborazione di eventi in tempo reale, ad esempio i flussi multimediali o la transcodifica oppure scenari con velocità effettiva di rete, possono non essere progettate per tollerare una pausa di 30 secondi. <!-- sooooo, what should they do? --> Nel caso in cui la VM venga spostata in un host diverso, alcuni carichi di lavoro sensibili potrebbero subire un lieve peggioramento delle prestazioni nei pochi minuti che precedono la sospensione della macchina virtuale. 


## <a name="maintenance-requiring-a-reboot"></a>Manutenzione per cui è necessario un riavvio

Quando è necessario riavviare le VM per la manutenzione pianificata, si riceve una notifica in anticipo. La manutenzione pianificata prevede due fasi: la finestra self-service e una finestra di manutenzione pianificata.

La **finestra self-service** consente di avviare la manutenzione nelle VM. Durante questa fase, è possibile eseguire query su ogni VM per visualizzarne lo stato e controllare il risultato dell'ultima richiesta di manutenzione.

Quando si avvia la manutenzione self-service, la VM viene spostata in un nodo già aggiornato e quindi riaccesa. A causa dei riavvii della VM, il disco temporaneo viene perso e gli indirizzi IP dinamici associati all'interfaccia di rete virtuale vengono aggiornati.

Se si avvia la manutenzione self-service e si verifica un errore durante il processo, l'operazione viene arrestata, la VM non viene aggiornata e viene anche rimossa dall'iterazione della manutenzione pianificata. L'utente verrà contattato in un secondo momento per una nuova pianificazione e potrà nuovamente eseguire la manutenzione self-service. 

Quando la finestra self-service è trascorsa, inizia la **finestra di manutenzione pianificata**. Durante questa finestra temporale, è ancora possibile eseguire una query sulla finestra di manutenzione, ma non avviare la manutenzione manualmente.

Per informazioni sulla gestione della manutenzione che richiede un riavvio, vedere "Gestione degli avvisi di manutenzione pianificata" per [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) o [Windows](../articles/virtual-machines/windows/maintenance-notifications.md). 

## <a name="availability-considerations-during-planned-maintenance"></a>Considerazioni sulla disponibilità durante la manutenzione pianificata 

Se si decide di attendere fino alla finestra di manutenzione pianificata, sono necessarie alcune considerazioni per mantenere la disponibilità più elevata delle VM. 

### <a name="paired-regions"></a>Aree associate

Ogni area di Azure è associata a un'altra area con la stessa ubicazione geografica, che insieme formano una coppia di aree. Durante la manutenzione pianificata, Azure aggiornerà solo le VM di una sola area di una coppia di aree. Ad esempio, quando si aggiornano le macchine virtuali negli Stati Uniti centro-settentrionali, Azure non aggiorna contemporaneamente le macchine virtuali negli Stati Uniti centro-meridionali. Tuttavia, altre aree, ad esempio Europa settentrionale, possono essere sottoposte a manutenzione contemporaneamente a Stati Uniti orientali. Sapendo come funzionano le coppie di aree, è possibile distribuire meglio le VM tra le aree. Per altre informazioni, vedere [Coppie di aree di Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

### <a name="availability-sets-and-scale-sets"></a>Set di disponibilità e set di disponibilità

Quando si distribuisce un carico di lavoro nelle VM di Azure, è possibile creare le VM in un set di disponibilità per garantire una disponibilità elevata per l'applicazione. Ciò assicura che in caso di interruzione o di eventi di manutenzione sia disponibile almeno una macchina virtuale.

In un set di disponibilità, le singole macchine virtuali vengono distribuite su un massimo di 20 domini di aggiornamento. Durante la manutenzione pianificata, l'impatto prodotto interessa un solo dominio di aggiornamento in un dato momento Tenere presente che l'ordine in cui i domini di aggiornamento vengono interessati non è necessariamente sequenziale. 

I set di scalabilità delle macchine virtuali sono una risorsa di calcolo di Azure che consente di distribuire e gestire un set di macchine virtuali identiche come una singola risorsa. Il set di scalabilità viene distribuito automaticamente nei domini di aggiornamento, ad esempio le VM in un set di disponibilità. Esattamente come con i set di disponibilità, con i set di scalabilità viene interessato un solo dominio di aggiornamento per volta.

Per altre informazioni sulla configurazione delle macchine virtuali per la disponibilità elevata, vedere Gestire la disponibilità delle macchine virtuali per [Windows](../articles/virtual-machines/windows/manage-availability.md) o [Linux](../articles/virtual-machines/linux/manage-availability.md).
