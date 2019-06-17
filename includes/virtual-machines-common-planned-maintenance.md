---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 4/30/2019
ms.author: shants
ms.custom: include file
ms.openlocfilehash: c2931fa410cf92755a5df5b7129dcf93de900930
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66155918"
---
Azure Aggiorna periodicamente la piattaforma per migliorare l'affidabilità, prestazioni e sicurezza dell'infrastruttura host per le macchine virtuali. Lo scopo di questi intervalli di aggiornamenti di patch ai componenti software nell'ambiente di hosting per l'aggiornamento dei componenti di rete o rimozione di componenti hardware. 

Gli aggiornamenti interessano raramente le VM ospitate. Quando gli aggiornamenti hanno un effetto, Azure sceglie il metodo meno forte impatto per gli aggiornamenti:

- Se l'aggiornamento non richiede un riavvio, la macchina virtuale viene sospesa durante l'aggiornamento dell'host o la macchina virtuale sia in tempo reale-eseguire la migrazione in un host già aggiornato.

- Se la manutenzione richiede un riavvio, si riceverà una notifica di manutenzione pianificata. Azure offre inoltre un intervallo di tempo in cui è possibile avviare la manutenzione manualmente, in un momento adatto per l'utente. La finestra di manutenzione automatica è in genere di 30 giorni, a meno che la manutenzione è urgente. Azure investe in tecnologie per ridurre il numero di case in cui la manutenzione pianificata della piattaforma richiede le macchine virtuali da riavviare. 

Questa pagina descrive come Azure esegue entrambi i tipi di manutenzione. Per altre informazioni sugli eventi non pianificati (interruzioni), vedere [gestire la disponibilità delle macchine virtuali per Windows](../articles/virtual-machines/windows/manage-availability.md) o l'articolo corrispondente per [Linux](../articles/virtual-machines/linux/manage-availability.md).

All'interno di una macchina virtuale, è possibile ricevere notifiche sulla prossima manutenzione dal [usando gli eventi pianificati per Windows](../articles/virtual-machines/windows/scheduled-events.md) o per [Linux](../articles/virtual-machines/linux/scheduled-events.md).

Per istruzioni sulla gestione della manutenzione pianificata, vedere [gestione degli elementi relativi alla manutenzione pianificata per Linux](../articles/virtual-machines/linux/maintenance-notifications.md) o l'articolo corrispondente per [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="maintenance-that-doesnt-require-a-reboot"></a>Manutenzione che non richiede un riavvio

Come indicato in precedenza, la maggior parte degli aggiornamenti della piattaforma non influiscono sulle macchine virtuali dei clienti. Quando un aggiornamento senza impatto non è possibile, Azure seleziona il meccanismo di aggiornamento che è il caso minimi per le macchine virtuali dei clienti. 

La maggior parte delle diverso da zero a impatto manutenzione sospende la macchina virtuale per meno di 10 secondi. In alcuni casi, Azure Usa i meccanismi di manutenzione con mantenimento della memoria. Questi meccanismi di sospendere la macchina virtuale fino a 30 secondi e conservare la memoria nella RAM. La macchina virtuale viene quindi ripresa e l'orologio viene sincronizzato automaticamente. 

Manutenzione con mantenimento della memoria funziona per più del 90% delle macchine virtuali di Azure. Non funziona per la serie G, M, N e H. Azure Usa le tecnologie di migrazione in tempo reale sempre più e migliora i meccanismi di manutenzione con mantenimento della memoria per ridurre la durata della pausa.  

Queste operazioni di manutenzione che non richiedono un riavvio vengono applicato un dominio di errore alla volta. Arrestino se ricevere eventuali segnali di integrità avviso. 

Questi tipi di aggiornamenti possono influire sulle alcune applicazioni. Quando la VM è live-eseguire la migrazione in un host diverso, alcuni carichi di lavoro sensibili potrebbe mostrare un lieve peggioramento delle prestazioni di alcuni minuti che precedono la sospensione della macchina virtuale. Per preparare per la manutenzione delle macchine Virtuali e ridurre l'impatto durante la manutenzione di Azure, provare [usando gli eventi pianificati per Windows](../articles/virtual-machines/windows/scheduled-events.md) oppure [Linux](../articles/virtual-machines/linux/scheduled-events.md) per tali applicazioni. Azure sta lavorando a manutenzione-controllare le funzionalità per queste applicazioni sensibili. 

### <a name="live-migration"></a>Migrazione in tempo reale

Migrazione in tempo reale è un'operazione che non richiede un riavvio e che mantiene memoria per la macchina virtuale. Viene generata un'interruzione o blocco, che dura in genere non più di 5 secondi. Fatta eccezione G, serie H, M e N, come macchine virtuali a un servizio (IaaS), l'intera infrastruttura sono idonee per la migrazione in tempo reale. Le macchine virtuali idonee rappresentano più del 90% delle macchine virtuali IaaS distribuite della flotta di Azure. 

La piattaforma Azure avvia migrazione in tempo reale negli scenari seguenti:
- Manutenzione pianificata
- Errore hardware
- Ottimizzazioni di allocazione

Alcuni scenari di manutenzione pianificata usano migrazione in tempo reale e gli eventi pianificati è possibile usare per sapere in anticipo quando è attiva avvierà operazioni di migrazione.

Migrazione in tempo reale è anche utilizzabile per spostare le macchine virtuali durante gli algoritmi di Azure Machine Learning prevedere un errore imminente dell'hardware o quando si desidera ottimizzare le allocazioni di macchina virtuale. Per altre informazioni sulla modellazione predittiva che consente di rilevare le istanze di hardware danneggiato, vedere [resilienza miglioramento della macchina virtuale di Azure con live migration e apprendimento automatico predittivi](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure). Se si usano questi servizi, le notifiche di migrazione in tempo reale vengono visualizzate nel portale di Azure nei monitoraggio dell'integrità del servizio registri e anche come gli eventi pianificati.

## <a name="maintenance-that-requires-a-reboot"></a>Manutenzione che richiede un riavvio

Nel raro caso in cui le macchine virtuali devono essere riavviati per la manutenzione pianificata, riceve una notifica in anticipo. Manutenzione pianificata prevede due fasi: la fase di self-service e una fase di manutenzione pianificata.

Durante la *self-service fase*, che dura in genere quattro settimane, si avvia la manutenzione nelle VM. Come parte di Self-Service, è possibile eseguire una query ogni macchina virtuale per visualizzare lo stato e il risultato dell'ultima richiesta di manutenzione.

Quando si avvia la manutenzione self-service, la macchina virtuale viene ridistribuita su un nodo già aggiornato. Perché la VM verrà riavviata, il disco temporaneo viene perso e gli indirizzi IP dinamici associati all'interfaccia di rete virtuale vengono aggiornati.

Se si verifica un errore durante la manutenzione self-service, l'operazione viene interrotta, la macchina virtuale non viene aggiornata e verrà visualizzata l'opzione per ripetere la manutenzione self-service. 

Al termine della fase di self-service, il *fase di manutenzione pianificata* inizia. Durante questa fase, è comunque possibile eseguire una query per la fase di manutenzione, ma non è possibile avviare la manutenzione manualmente.

Per altre informazioni sulla gestione della manutenzione che richiede un riavvio, vedere [gestione degli elementi relativi alla manutenzione pianificata per Linux](../articles/virtual-machines/linux/maintenance-notifications.md) o l'articolo corrispondente per [Windows](../articles/virtual-machines/windows/maintenance-notifications.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Considerazioni sulla disponibilità durante la manutenzione pianificata 

Se si decide di attendere fino alla fase di manutenzione pianificata, esistono alcuni aspetti da considerare per mantenere la disponibilità più elevata delle macchine virtuali. 

#### <a name="paired-regions"></a>Aree abbinate

Ogni area di Azure è associata a un'altra area geografica prossimità stesso. Insieme, formano una coppia di aree. Durante la fase di manutenzione pianificata, Azure Aggiorna solo le macchine virtuali in una singola area di una coppia di aree. Ad esempio, durante l'aggiornamento della macchina virtuale in North Central US, Azure non aggiorna tutte le macchine Virtuali in South Central US nello stesso momento. Tuttavia, altre aree, ad esempio Europa settentrionale, possono essere sottoposte a manutenzione contemporaneamente a Stati Uniti orientali. Sapendo come funzionano le coppie di aree, è possibile distribuire meglio le VM tra le aree. Per altre informazioni, vedere [Coppie di aree di Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>Set di disponibilità e set di disponibilità

Quando si distribuisce un carico di lavoro nelle macchine virtuali di Azure, è possibile creare le macchine virtuali all'interno di un' *set di disponibilità* per garantire un'elevata disponibilità all'applicazione. Usa i set di disponibilità, è possibile assicurarsi che durante gli eventi di manutenzione o un'interruzione di servizio che richiedono un riavvio, è disponibile almeno una macchina virtuale.

In un set di disponibilità, le singole macchine virtuali vengono distribuite su un massimo di 20 domini di aggiornamento. Durante la manutenzione pianificata, un solo dominio di aggiornamento viene aggiornato in qualsiasi momento. I domini di aggiornamento non sono necessariamente aggiornati in modo sequenziale. 

Macchina virtuale *set di scalabilità* sono risorse che è possibile usare per distribuire e gestire un set di macchine virtuali identiche come una singola risorsa di calcolo di un'istanza di Azure. Il set di scalabilità viene distribuito automaticamente nei domini di aggiornamento, ad esempio le macchine virtuali in un set di disponibilità. Come con i set di disponibilità, quando si usano set di scalabilità di un dominio di aggiornamento viene aggiornato in qualsiasi momento durante la manutenzione pianificata.

Per altre informazioni sulla configurazione delle macchine virtuali per la disponibilità elevata, vedere [gestire la disponibilità delle macchine virtuali per Windows](../articles/virtual-machines/windows/manage-availability.md) o l'articolo corrispondente per [Linux](../articles/virtual-machines/linux/manage-availability.md).
