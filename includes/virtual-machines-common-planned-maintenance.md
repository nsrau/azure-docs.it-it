---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 8/22/2019
ms.author: shants
ms.custom: include file
ms.openlocfilehash: b2a7bbef2c421281780c0191fa32381468899bbf
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70020338"
---
Azure aggiorna periodicamente la propria piattaforma per migliorare l'affidabilità, le prestazioni e la sicurezza dell'infrastruttura host per le macchine virtuali. Lo scopo di questi aggiornamenti è quello di applicare patch ai componenti software nell'ambiente host per aggiornare i componenti di rete o rimuovere le autorizzazioni hardware. 

Gli aggiornamenti influiscono raramente sulle macchine virtuali ospitate. Quando gli aggiornamenti hanno effetto, Azure sceglie il metodo meno significativo per gli aggiornamenti:

- Se l'aggiornamento non richiede un riavvio, la macchina virtuale viene sospesa durante l'aggiornamento dell'host oppure viene eseguita la migrazione in tempo reale della macchina virtuale a un host già aggiornato.

- Se la manutenzione richiede un riavvio, si riceve una notifica della manutenzione pianificata. Azure fornisce anche un intervallo di tempo in cui è possibile avviare la manutenzione manualmente, in un momento adatto per l'utente. La finestra di manutenzione automatica è in genere di 35 giorni, a meno che la manutenzione non sia urgente. Azure investe nelle tecnologie per ridurre il numero di casi in cui la manutenzione della piattaforma pianificata richiede il riavvio delle macchine virtuali. 

Questa pagina descrive come Azure esegue entrambi i tipi di manutenzione. Per altre informazioni sugli eventi non pianificati (interruzioni), vedere [gestire la disponibilità delle macchine virtuali per Windows](../articles/virtual-machines/windows/manage-availability.md) o l'articolo corrispondente per [Linux](../articles/virtual-machines/linux/manage-availability.md).

All'interno di una macchina virtuale è possibile ricevere notifiche sulla manutenzione imminente [usando eventi pianificati per Windows](../articles/virtual-machines/windows/scheduled-events.md) o per [Linux](../articles/virtual-machines/linux/scheduled-events.md).

Per istruzioni sulla gestione della manutenzione pianificata, vedere [gestione delle notifiche di manutenzione pianificata per Linux](../articles/virtual-machines/linux/maintenance-notifications.md) o l'articolo corrispondente per [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="maintenance-that-doesnt-require-a-reboot"></a>Manutenzione che non richiede il riavvio

Come affermato in precedenza, la maggior parte degli aggiornamenti della piattaforma non influisce sulle macchine virtuali Quando non è possibile eseguire un aggiornamento a nessun effetto, Azure sceglie il meccanismo di aggiornamento che è meno incisivo per le macchine virtuali dei clienti. 

La manutenzione con un effetto più diverso da zero sospende la macchina virtuale per meno di 10 secondi. In alcuni casi, Azure usa meccanismi di manutenzione con mantenimento della memoria. Questi meccanismi sospendono la VM per un massimo di 30 secondi e mantengono la memoria in RAM. La macchina virtuale viene quindi ripresa e il relativo clock viene sincronizzato automaticamente. 

La manutenzione con mantenimento della memoria funziona per più del 90% delle VM di Azure. Non funziona per la serie G, M, N e H. Azure usa sempre le tecnologie di migrazione in tempo reale e migliora i meccanismi di manutenzione con mantenimento della memoria per ridurre le durate di sospensione.  

Per queste operazioni di manutenzione che non richiedono un riavvio viene applicato un dominio di errore alla volta. Si fermano se ricevono segnali di avviso di integrità. 

Questi tipi di aggiornamenti possono interessare alcune applicazioni. Quando si esegue la migrazione in tempo reale della macchina virtuale in un host diverso, alcuni carichi di lavoro sensibili potrebbero presentare una lieve riduzione delle prestazioni nei pochi minuti che portano alla pausa della macchina virtuale. Per preparare la manutenzione della macchina virtuale e ridurre l'effetto durante la manutenzione di Azure, provare a [usare eventi pianificati per Windows](../articles/virtual-machines/windows/scheduled-events.md) o [Linux](../articles/virtual-machines/linux/scheduled-events.md) per tali applicazioni. Azure fornisce anche il controllo completo su una manutenzione della piattaforma di tipo Impact diverso da zero in [host dedicati di Azure](../articles/virtual-machines/windows/dedicated-hosts.md) e [VM isolate](../articles/security/fundamentals/isolation-choices.md). La funzionalità di controllo della manutenzione è in anteprima ed è possibile richiedere l'accesso inviando [il modulo di iscrizione](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR6lJf7DwiQxNmz51ksQvxV9UNUM3UllWUjBMTFZQUFhHUDI0VTBPQlJFNS4u). Offre la possibilità di ignorare gli aggiornamenti della piattaforma con conseguenze diverse da zero e di applicare gli aggiornamenti come batch alla volta in una finestra in sequenza di 35 giorni.

### <a name="live-migration"></a>Migrazione in tempo reale

La migrazione in tempo reale è un'operazione che non richiede un riavvio e che conserva la memoria per la macchina virtuale. Causa una pausa o un blocco, che in genere non dura più di 5 secondi. Ad eccezione delle serie G, M, N e H, tutte le VM IaaS (Infrastructure as a Service) sono idonee per la migrazione in tempo reale. Le macchine virtuali idonee rappresentano più del 90% delle VM IaaS distribuite nella flotta di Azure. 

La piattaforma Azure avvia la migrazione in tempo reale negli scenari seguenti:
- Manutenzione pianificata
- Errore hardware
- Ottimizzazioni di allocazione

In alcuni scenari di manutenzione pianificata viene utilizzata la migrazione in tempo reale ed è possibile utilizzare Eventi pianificati per stabilire in anticipo l'avvio delle operazioni di migrazione in tempo reale.

La migrazione in tempo reale può essere usata anche per spostare le macchine virtuali quando Azure Machine Learning algoritmi stimano un errore hardware imminente o quando si desidera ottimizzare le allocazioni delle macchine virtuali. Per altre informazioni sulla modellazione predittiva che rileva le istanze di hardware danneggiato, vedere miglioramento della resilienza delle macchine [virtuali di Azure con apprendimento automatico predittivo e migrazione in tempo reale](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure). Le notifiche di migrazione in tempo reale vengono visualizzate nel portale di Azure nei log di monitoraggio e integrità dei servizi, nonché in Eventi pianificati se si usano questi servizi.

## <a name="maintenance-that-requires-a-reboot"></a>Manutenzione che richiede un riavvio

Nel raro caso in cui le VM debbano essere riavviate per la manutenzione pianificata, si riceverà una notifica in anticipo. La manutenzione pianificata prevede due fasi: la fase self-service e una fase di manutenzione pianificata.

Durante la *fase self-service*, che in genere dura quattro settimane, si avvia la manutenzione sulle VM. Come parte della modalità self-service, è possibile eseguire una query su ogni macchina virtuale per visualizzarne lo stato e il risultato dell'ultima richiesta di manutenzione.

Quando si avvia la manutenzione self-service, la macchina virtuale viene ridistribuita su un nodo già aggiornato. Poiché la macchina virtuale viene riavviata, il disco temporaneo viene perso e gli indirizzi IP dinamici associati all'interfaccia di rete virtuale vengono aggiornati.

Se si verifica un errore durante la manutenzione Self-Service, l'operazione viene arrestata, la macchina virtuale non viene aggiornata e si ottiene l'opzione per ripetere la manutenzione self-service. 

Al termine della fase self-service, viene avviata la *fase di manutenzione pianificata* . Durante questa fase, è ancora possibile eseguire una query per la fase di manutenzione, ma non è possibile avviare manualmente la manutenzione.

Per altre informazioni sulla gestione della manutenzione che richiede un riavvio, vedere [gestione delle notifiche di manutenzione pianificata per Linux](../articles/virtual-machines/linux/maintenance-notifications.md) o l'articolo corrispondente per [Windows](../articles/virtual-machines/windows/maintenance-notifications.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Considerazioni sulla disponibilità durante la manutenzione pianificata 

Se si decide di attendere fino alla fase di manutenzione pianificata, è necessario prendere in considerazione alcune considerazioni per mantenere la disponibilità più elevata delle VM. 

#### <a name="paired-regions"></a>Aree abbinate

Ogni area di Azure è associata a un'altra area all'interno delle stesse vicinanze geografiche. Insieme, fanno una coppia di aree. Durante la fase di manutenzione pianificata, Azure aggiorna solo le macchine virtuali in una singola area di una coppia di aree. Ad esempio, durante l'aggiornamento della macchina virtuale negli Stati Uniti centro-settentrionali, Azure non aggiorna alcuna macchina virtuale negli Stati Uniti centro-meridionali nello stesso momento. Tuttavia, altre aree, ad esempio Europa settentrionale, possono essere sottoposte a manutenzione contemporaneamente a Stati Uniti orientali. Sapendo come funzionano le coppie di aree, è possibile distribuire meglio le VM tra le aree. Per altre informazioni, vedere [Coppie di aree di Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>Set di disponibilità e set di disponibilità

Quando si distribuisce un carico di lavoro in macchine virtuali di Azure, è possibile creare le macchine virtuali all'interno di un *set di disponibilità* per garantire la disponibilità elevata per l'applicazione. Usando i set di disponibilità, è possibile assicurarsi che durante un'interruzione o un evento di manutenzione che richiede un riavvio sia disponibile almeno una macchina virtuale.

In un set di disponibilità, le singole macchine virtuali vengono distribuite su un massimo di 20 domini di aggiornamento. Durante la manutenzione pianificata, solo un UD viene aggiornato in un determinato momento. Gli UDs non vengono necessariamente aggiornati in modo sequenziale. 

I *set* di scalabilità di macchine virtuali sono una risorsa di calcolo di Azure che è possibile usare per distribuire e gestire un set di macchine virtuali identiche come una singola risorsa. Il set di scalabilità viene distribuito automaticamente tra i domini di errore, come le macchine virtuali in un set di disponibilità. Come per i set di disponibilità, quando si usano i set di scalabilità, solo un UD viene aggiornato in un determinato momento durante la manutenzione pianificata.

Per altre informazioni sulla configurazione delle macchine virtuali per la disponibilità elevata, vedere [gestire la disponibilità delle macchine virtuali per Windows](../articles/virtual-machines/windows/manage-availability.md) o l'articolo corrispondente per [Linux](../articles/virtual-machines/linux/manage-availability.md).
