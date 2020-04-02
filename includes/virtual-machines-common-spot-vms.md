---
title: File di inclusione
description: File di inclusione
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/23/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d56964b7415e4ca5903950cd46c02b3c27f62d5e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547396"
---
L'uso di Spot VMs consente di sfruttare la nostra capacità inutilizzata con un notevole risparmio sui costi. In qualsiasi momento in cui Azure richiede la capacità, l'infrastruttura di Azure rimuovere le macchine virtuali Spot.At any in time when Azure needs the capacity back, the Azure infrastructure will evict Spot VMs. Pertanto, le macchine virtuali Spot sono ideali per i carichi di lavoro in grado di gestire interruzioni come processi di elaborazione batch, ambienti di sviluppo/test, carichi di lavoro di elaborazione di grandi dimensioni e altro ancora.

La quantità di capacità disponibile può variare in base alle dimensioni, all'area, all'ora del giorno e altro ancora. Quando si distribuiscono macchine virtuali Spot, Azure allocherà le macchine virtuali se è disponibile capacità, ma non è disponibile alcun sla per queste macchine virtuali. Una macchina virtuale Spot non offre garanzie di disponibilità elevata. In qualsiasi momento in cui Azure richiede la riesecuzione della capacità, l'infrastruttura di Azure rimuovere le macchine virtuali Spot con 30 secondi di preavviso. 


## <a name="eviction-policy"></a>Politica di sfratto

Le macchine virtuali possono essere rimosse in base alla capacità o al prezzo massimo impostato. Per le macchine virtuali, il criterio di rimozione è impostato su *Deallocate* che sposta le macchine virtuali rimosse allo stato di disinstallazione arrestata, consentendo di ridistribuire le macchine virtuali rimosse in un secondo momento. Tuttavia, la riallocazione delle macchine virtuali Spot dipenderà dalla capacità Spot disponibile. Le macchine virtuali deallocate verranno conteggiate rispetto alla quota vCPU spot e verranno addebitati i dischi sottostanti. 

Gli utenti possono acconsentire esplicitamente a ricevere notifiche nella macchina virtuale tramite [gli eventi pianificati](../articles/virtual-machines/linux/scheduled-events.md)di Azure. In questo modo verrà notificato se le macchine virtuali vengono rimosse e si avranno 30 secondi per completare i processi ed eseguire le attività di arresto prima dello sfratto. 


| Opzione | Risultato |
|--------|---------|
| Il prezzo massimo è impostato su >- il prezzo corrente. | La macchina virtuale viene distribuita se sono disponibili capacità e quote. |
| Il prezzo massimo è impostato per < il prezzo corrente. | La macchina virtuale non è distribuita. Si otterrà un messaggio di errore che il prezzo massimo deve essere >- prezzo corrente. |
| Riavvio di una macchina virtuale di arresto/deallocazione se il prezzo massimo è >il prezzo corrente | Se sono presenti capacità e quote, la macchina virtuale viene distribuita. |
| Riavvio di una macchina virtuale di arresto/deallocamento se il prezzo massimo è < il prezzo corrente | Si otterrà un messaggio di errore che il prezzo massimo deve essere >- prezzo corrente. | 
| Prezzo per la macchina virtuale è salito ed è ora > il prezzo massimo. | La macchina virtuale viene rimossa. Si ottiene una notifica di 30s prima dello sfratto effettivo. | 
| Dopo lo sfratto il prezzo per la macchina virtuale torna ad essere < il prezzo massimo. | La macchina virtuale non verrà riavviata automaticamente. È possibile riavviare la macchina virtuale manualmente e verrà addebitata al prezzo corrente. |
| Se il prezzo massimo è impostato su`-1` | La macchina virtuale non verrà rimossa per motivi di prezzo. Il prezzo massimo sarà il prezzo corrente, fino al prezzo per le macchine virtuali standard. Non ti verrà mai addebitato un costo superiore al prezzo standard.| 
| Modifica del prezzo massimo | È necessario deallocare la macchina virtuale per modificare il prezzo massimo. Deallocate la macchina virtuale, impostare t un nuovo prezzo massimo, quindi aggiornare la macchina virtuale. |

## <a name="limitations"></a>Limitazioni

Le dimensioni delle macchine virtuali seguenti non sono supportate per le macchine virtuali Spot:The following VM sizes are not supported for Spot VMs:
 - Serie B
 - Versioni promozionali di qualsiasi dimensione (come dv2, NV, NC, H formati promo)

Individuare le macchine virtuali attualmente non è possibile usare dischi del sistema operativo effimeri.

Le macchine virtuali spot possono essere distribuite in qualsiasi area, ad eccezione di Microsoft Azure China 21Vianet.Spot VMs can be deployed to any region, except Microsoft Azure China 21Vianet.

## <a name="pricing"></a>Prezzi

I prezzi per le macchine virtuali Spot sono variabili, in base all'area e all'SKU. Per altre informazioni, vedere Prezzi delle macchine virtuali per [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows.](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) 


Con prezzi variabili, hai la possibilità di impostare un prezzo massimo, in dollari USA (USD), utilizzando fino a 5 cifre decimali. Ad esempio, `0.98765`il valore sarebbe un prezzo massimo di 0,98765 USD all'ora. Se si imposta il `-1`prezzo massimo su , la macchina virtuale non verrà rimossa in base al prezzo. Il prezzo per la macchina virtuale sarà il prezzo corrente per spot o il prezzo per una macchina virtuale standard, che è sempre inferiore, purché siano disponibili capacità e quote.


##  <a name="frequently-asked-questions"></a>Domande frequenti

**D:** Una volta creata, una macchina virtuale Spot è uguale alla normale macchina virtuale standard?

**A:** Sì, ad eccezione del fatto che non esiste alcun servizio di controllo di servizio per le macchine virtuali Spot e che possono essere rimosse in qualsiasi momento.


**D:** Cosa fare quando si viene sfrattati, ma si ha ancora bisogno di capacità?

**A:** È consigliabile usare macchine virtuali standard anziché macchine virtuali Spot se è necessaria immediatamente la capacità.


**D:** Come viene gestita la quota per le macchine virtuali Spot?

**A:** Le macchine virtuali spot diranno un pool di quote separato. La quota spot verrà condivisa tra le macchine virtuali e le istanze con set di scalabilità. Per altre informazioni, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).


**D:** Posso richiedere una quota aggiuntiva per Spot?

**A:** Sì, sarà possibile inviare la richiesta per aumentare la quota per le macchine virtuali Spot tramite il processo di richiesta di [quota standard.](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)


**D:** Quali canali supportano le macchine virtuali Spot?

**A:** Vedere la tabella seguente per la disponibilità di VM Spot.See the table below for Spot VM availability.

<a name="channel"></a>

| Canali di AzureAzure Channels               | Azure Spot VMs Availability       |
|------------------------------|-----------------------------------|
| Enterprise Agreement         | Sì                               |
| Pagamento in base al consumo                | Sì                               |
| Provider di servizi cloud | [Contatta il tuo partner](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Vantaggi                     | Non disponibile                     |
| Sponsorizzato                    | Non disponibile                     |
| Versione di valutazione gratuita                   | Non disponibile                     |


**D:** Dove posso pubblicare le domande?

**A:** Puoi pubblicare e taggare la tua domanda su `azure-spot` Q&[A](https://docs.microsoft.com/answers/topics/azure-spot.html). 



