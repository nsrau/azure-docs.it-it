---
title: Manutenzione e aggiornamenti
description: Panoramica della manutenzione e degli aggiornamenti per le macchine virtuali in esecuzione in Azure.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/22/2020
ms.author: shants
ms.openlocfilehash: 3cf126caaaa0c518574418aca194ebd82cc4d6b9
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91972068"
---
# <a name="maintenance-for-virtual-machines-in-azure"></a>Manutenzione per macchine virtuali in Azure

Azure aggiorna periodicamente la piattaforma per migliorare l'affidabilità, le prestazioni e la sicurezza dell'infrastruttura host per le macchine virtuali. Lo scopo di questi aggiornamenti include l'applicazione di patch ai componenti software nell'ambiente host, l'aggiornamento dei componenti di rete e la rimozione delle autorizzazioni per l'hardware. 

Gli aggiornamenti influiscono raramente sulle macchine virtuali ospitate. Nel caso in cui producano invece un effetto, Azure sceglie il metodo di aggiornamento a minor impatto:

- Se l'aggiornamento non richiede il riavvio, durante l'aggiornamento dell'host la macchina virtuale viene sospesa o migrata in tempo reale su un host già aggiornato. 
- Se un intervento di manutenzione richiede il riavvio, si riceve una notifica relativa alla manutenzione pianificata. Azure offre anche una finestra temporale in cui avviare la manutenzione manualmente, in un momento opportuno per l'utente. La finestra di manutenzione automatica è in genere di 35 giorni, a meno che l'intervento non sia urgente. Azure investe in tecnologie che consentano di ridurre i casi in cui la manutenzione pianificata della piattaforma richiede il riavvio delle macchine virtuali. Per istruzioni sulla gestione della manutenzione pianificata, vedere Gestire gli avvisi relativi alla manutenzione pianificata usando l'[interfaccia della riga di comando](maintenance-notifications-cli.md) di Azure, [PowerShell](maintenance-notifications-powershell.md) o il [portale](maintenance-notifications-portal.md).

Questa pagina descrive come Azure esegue entrambi i tipi di manutenzione. Per altre informazioni sugli eventi non pianificati (interruzioni), vedere  [Gestire la disponibilità delle macchine virtuali per Windows](./manage-availability.md) o l'articolo corrispondente per [Linux](./manage-availability.md).

È possibile ottenere notifiche sulla manutenzione programmata direttamente nella macchina virtuale tramite [Eventi pianificati per Windows](./windows/scheduled-events.md) o [Linux](./linux/scheduled-events.md).



## <a name="maintenance-that-doesnt-require-a-reboot"></a>Interventi di manutenzione che non richiedono il riavvio

La maggior parte degli aggiornamenti della piattaforma non influisce sulle macchine virtuali dei clienti. Quando non è possibile un aggiornamento senza alcun effetto, Azure sceglie il metodo di aggiornamento a minor impatto sulle macchine virtuali dei clienti. 

Gli interventi di manutenzione per i quali non è possibile un impatto zero sospendono le macchine virtuali per meno di 10 secondi. In alcuni casi, Azure usa meccanismi di manutenzione con mantenimento della memoria, che sospendono la macchina virtuale per un massimo di 30 secondi e mantengono la memoria nella RAM. Quando viene ripresa l'esecuzione della macchina virtuale, l'orologio viene automaticamente sincronizzato. 

La manutenzione con mantenimento della memoria è supportata da oltre il 90% delle macchine virtuali di Azure. Non può essere usata con le serie G, M, N e H. Azure usa sempre più spesso tecnologie di migrazione in tempo reale e meccanismi di manutenzione con mantenimento della memoria per ridurre la durata delle interruzioni.  

Queste operazioni di manutenzione che non richiedono un riavvio vengono applicate per ogni dominio di errore e vengono interrotte solo se ricevono segnali di avviso di integrità dagli strumenti di monitoraggio delle piattaforme. 

Questi tipi di aggiornamenti possono influire su alcune applicazioni. Nel caso in cui la macchina virtuale venga migrata in tempo reale su un host diverso, è possibile che alcuni carichi di lavoro subiscano un lieve peggioramento delle prestazioni nei pochi minuti che precedono la sospensione della macchina virtuale. Per preparare la manutenzione della macchina virtuale e ridurre al minimo l'impatto durante la manutenzione di Azure, può essere utile [usare Eventi pianificati per Windows](./windows/scheduled-events.md) o [Linux](./linux/scheduled-events.md). 

Per un maggiore controllo su tutte le attività di manutenzione, inclusi gli aggiornamenti senza riavvio e con impatto zero, è possibile usare la funzionalità Controllo di manutenzione. È necessario usare [host dedicati di Azure](./dedicated-hosts.md) o una [macchina virtuale isolata](../security/fundamentals/isolation-choices.md). L'opzione Controllo di manutenzione offre la possibilità di ignorare tutti gli aggiornamenti della piattaforma e di applicare gli aggiornamenti nel giorno e all'ora che si preferisce entro un intervallo di 35 giorni. Per altre informazioni, vedere [Gestione degli aggiornamenti con il controllo di manutenzione e l'interfaccia della riga di comando di Azure](maintenance-control.md).


### <a name="live-migration"></a>Migrazione in tempo reale

La migrazione in tempo reale è un'operazione che non richiede il riavvio e mantiene la memoria per la macchina virtuale. Genera una pausa o un blocco, che in genere non dura più di 5 secondi. Ad eccezione delle serie G, M, N e H, in tutte le macchine virtuali IaaS (Infrastructure as a Service) è possibile eseguire la migrazione in tempo reale. Le macchine virtuali idonee rappresentano più del 90% delle macchine virtuali IaaS distribuite nell'ecosistema Azure. 

La piattaforma Azure avvia la migrazione in tempo reale negli scenari seguenti:
- Manutenzione pianificata
- Errore hardware
- Ottimizzazioni di allocazione

In alcuni scenari di manutenzione pianificata viene usata la migrazione in tempo reale ed è possibile usufruire di Eventi pianificati per stabilire in anticipo l'ora di avvio delle operazioni di migrazione.

La migrazione in tempo reale può essere usata anche per spostare le macchine virtuali quando gli algoritmi di Azure Machine Learning prevedono un errore hardware imminente o quando si vogliono ottimizzare le allocazioni delle macchine virtuali. Per altre informazioni sulla modellazione predittiva in grado di rilevare eventuali istanze di hardware danneggiato, vedere [Improving Azure VM resiliency with predictive machine learning and live migration](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure) (Migliorare la resilienza delle macchine virtuali di Azure con l'apprendimento automatico predittivo e la migrazione in tempo reale). Se si usano questi servizi, le notifiche di migrazione in tempo reale vengono visualizzate nei log di Monitoraggio di Azure e di integrità dei servizi del portale di Azure e in Eventi pianificati.

## <a name="maintenance-that-requires-a-reboot"></a>Interventi di manutenzione che richiedono il riavvio

Nel caso raro in cui sia necessario riavviare le macchine virtuali per la manutenzione pianificata, si riceve una notifica in anticipo. La manutenzione pianificata prevede due fasi: la fase self-service e una fase di manutenzione pianificata.

Nel corso della *fase self-service*, che in genere dura quattro settimane, viene avviata la manutenzione delle macchine virtuali. Durante questa fase, è possibile eseguire query su ogni macchina virtuale per visualizzarne lo stato e controllare il risultato dell'ultima richiesta di manutenzione.

Quando si avvia la manutenzione self-service, la macchina virtuale viene ridistribuita su un nodo già aggiornato. A causa dei riavvii della macchina virtuale, il disco temporaneo viene perso e gli indirizzi IP dinamici associati all'interfaccia di rete virtuale vengono aggiornati.

Se si verifica un errore durante il processo di manutenzione self-service, l'operazione viene arrestata, la macchina virtuale non viene aggiornata ed è possibile scegliere di riprovare ad avviare la manutenzione self-service. 

Al termine della fase self-service, ha inizio la *fase di manutenzione pianificata*. Durante questa fase, è ancora possibile eseguire una query sulla fase di manutenzione, ma non avviare la manutenzione manualmente.

Per altre informazioni sulla gestione degli interventi di manutenzione che richiedono il riavvio, vedere **Gestire gli avvisi relativi alla manutenzione pianificata** usando l'[interfaccia della riga di comando](maintenance-notifications-cli.md) di Azure, [PowerShell](maintenance-notifications-powershell.md) o il [portale](maintenance-notifications-portal.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Considerazioni sulla disponibilità durante la manutenzione pianificata 

Se si decide di attendere fino alla fase di manutenzione pianificata, sono necessarie alcune considerazioni per mantenere la disponibilità più elevata possibile delle macchine virtuali. 

#### <a name="paired-regions"></a>Aree abbinate

Ogni area di Azure è abbinata a un'altra area con la stessa collocazione geografica; formano insieme una coppia di aree. Durante la fase di manutenzione pianificata, Azure aggiornerà solo le macchine virtuali di una sola area della coppia. Se, ad esempio, si aggiornano le macchine virtuali negli Stati Uniti centro-settentrionali, Azure non aggiorna contemporaneamente anche le macchine virtuali negli Stati Uniti centro-meridionali. Tuttavia, altre aree, ad esempio Europa settentrionale, possono essere sottoposte a manutenzione contemporaneamente a Stati Uniti orientali. Sapendo come funzionano le coppie di aree, è possibile distribuire meglio le VM tra le aree. Per altre informazioni, vedere [Coppie di aree di Azure](../best-practices-availability-paired-regions.md).

#### <a name="availability-sets-and-scale-sets"></a>Set di disponibilità e set di disponibilità

Quando si distribuisce un carico di lavoro nelle VM di Azure, è possibile creare le VM in un *set di disponibilità* per garantire una disponibilità elevata per l'applicazione. Usando i set di disponibilità, è possibile avere la certezza che, durante un'interruzione o un evento di manutenzione che richiede il riavvio, sia disponibile almeno una macchina virtuale.

All'interno di un set di disponibilità, le singole macchine virtuali vengono distribuite su un massimo di 20 domini di aggiornamento. Durante la manutenzione pianificata, in un dato momento viene aggiornato un unico dominio di aggiornamento. I domini di aggiornamento non vengono necessariamente aggiornati in modo sequenziale. 

I *set di scalabilità* delle macchine virtuali sono una risorsa di calcolo di Azure che consente di distribuire e gestire un set di macchine virtuali identiche come una singola risorsa. Il set di scalabilità viene distribuito automaticamente nei domini di aggiornamento, ad esempio le macchine virtuali in un set di disponibilità. Come con i set di disponibilità, anche con i set di scalabilità viene aggiornato un solo dominio di aggiornamento per volta durante la manutenzione pianificata.

Per altre informazioni sulla configurazione delle macchine virtuali per la disponibilità elevata, vedere  [Gestire la disponibilità delle macchine virtuali per Windows](./manage-availability.md) o l'articolo corrispondente per [Linux](./manage-availability.md).

#### <a name="availability-zones"></a>Zone di disponibilità

Le zone di disponibilità sono località fisiche esclusive all'interno di un'area di Azure. Ogni zona è costituita da uno o più data center dotati di impianti indipendenti per l'alimentazione, il raffreddamento e la connettività di rete. Per garantire la resilienza, sono presenti almeno tre zone separate in tutte le aree abilitate. 

Una zona di disponibilità è una combinazione di un dominio di errore e un dominio di aggiornamento. Se si creano tre o più macchine virtuali in tre zone di un'area di Azure, le macchine virtuali vengono distribuite in modo efficace in tre domini di errore e tre domini di aggiornamento. La piattaforma di Azure riconosce questa distribuzione in domini di aggiornamento per assicurarsi che le macchine virtuali in diverse aree non vengano aggiornate contemporaneamente.

All'interno di un'area, ogni aggiornamento dell'infrastruttura viene distribuito zona per zona. È possibile, tuttavia, che contemporaneamente a una distribuzione in Zona 1 venga eseguita anche una distribuzione Zona 2. Le distribuzioni non sono tutte serializzate. La distribuzione zona per zona contribuisce tuttavia a ridurre il rischio.

## <a name="next-steps"></a>Passaggi successivi 

È possibile usare l'[interfaccia della riga di comando di Azure](maintenance-notifications-cli.md), [Azure PowerShell](maintenance-notifications-powershell.md) o il [portale](maintenance-notifications-portal.md) per gestire la manutenzione pianificata.