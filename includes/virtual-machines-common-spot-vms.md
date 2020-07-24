---
title: includere file
description: includere file
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 07/20/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: df78133f602466681da64d2666a311e1649c598f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87028797"
---
L'uso delle macchine virtuali spot consente di sfruttare la capacità inutilizzata con un notevole risparmio sui costi. Quando, in qualsiasi momento, Azure avrà di nuovo bisogno di quella capacità, l'infrastruttura di Azure rimuoverà le macchine virtuali spot. Le macchine virtuali spot sono pertanto ideali per i carichi di lavoro in grado di gestire le interruzioni, come i processi di elaborazione batch, gli ambienti di sviluppo/test, i carichi di lavoro di calcolo di grandi dimensioni e altro ancora.

La quantità di capacità disponibile dipende dalle dimensioni, dall'area, dal momento della giornata e da altri fattori. Quando si distribuiscono le macchine virtuali spot, Azure le alloca se c'è capacità disponibile, ma non esiste alcun contratto di servizio per queste macchine virtuali. Una macchina virtuale spot non offre garanzie di disponibilità elevata. Quando, in qualsiasi momento, Azure avrà di nuovo bisogno di quella capacità, l'infrastruttura di Azure rimuoverà le macchine virtuali spot con un preavviso di 30 secondi. 


## <a name="eviction-policy"></a>Criteri di rimozione

Le macchine virtuali possono essere eliminate in base alla capacità o al prezzo massimo impostato. Quando si crea una VM spot, è possibile impostare i criteri di rimozione per *deallocare* (impostazione predefinita) o *eliminare*. 

Il criterio *deallocate* sposta la macchina virtuale in stato di arresto-deallocato, consentendo di ridistribuirla in un secondo momento. Tuttavia, non è garantito che l'allocazione avrà esito positivo. Le VM deallocate verranno conteggiate rispetto alla quota e verranno addebitati i costi di archiviazione per i dischi sottostanti. 

Se si vuole che la macchina virtuale venga eliminata quando viene rimossa, è possibile impostare i criteri di rimozione da *eliminare*. Le macchine virtuali rimosse vengono eliminate insieme ai relativi dischi sottostanti, quindi non verrà addebitato alcun costo per l'archiviazione. 

È possibile acconsentire esplicitamente a ricevere notifiche in-VM tramite [Azure eventi pianificati](../articles/virtual-machines/linux/scheduled-events.md). In questo modo riceveranno una notifica se le macchine virtuali vengono eliminate e avranno a disposizione 30 secondi per completare i processi ed eseguire le attività di arresto prima dell'eliminazione. 


| Opzione | Risultato |
|--------|---------|
| Il prezzo massimo è impostato su >= il prezzo corrente. | La macchina virtuale viene distribuita se la capacità e la quota sono disponibili. |
| Il prezzo massimo è impostato su < il prezzo corrente. | La macchina virtuale non viene distribuita. Si riceverà un messaggio di errore che segnala che il prezzo massimo deve essere >= il prezzo corrente. |
| Riavvio di una macchina virtuale arrestata/deallocata se il prezzo massimo è >= il prezzo corrente | Se sono disponibili capacità e quota, la macchina virtuale viene distribuita. |
| Riavvio di una macchina virtuale arrestata/deallocata se il prezzo massimo è < il prezzo corrente | Si riceverà un messaggio di errore che segnala che il prezzo massimo deve essere >= il prezzo corrente. | 
| Il prezzo della macchina virtuale è aumentato e ora è > il prezzo massimo. | La macchina virtuale viene eliminata. Si riceve una notifica 30 secondi prima dell'eliminazione effettiva. | 
| Dopo l'eliminazione, il prezzo per la macchina virtuale torna a essere < il prezzo massimo. | La macchina virtuale non verrà riavviata automaticamente. L'utente può riavviare la macchina virtuale autonomamente e verrà addebitato il prezzo corrente. |
| Se il prezzo massimo è impostato su `-1` | La macchina virtuale non verrà eliminata per motivi di prezzo. Il prezzo massimo corrisponderà al prezzo corrente, fino al prezzo per le macchine virtuali standard. Non verrà mai addebitato un prezzo superiore al prezzo standard.| 
| Modifica del prezzo massimo | È necessario deallocare la VM per modificare il prezzo massimo. Deallocare la VM, impostare un nuovo prezzo massimo, quindi aggiornare la VM. |


## <a name="limitations"></a>Limitazioni

Le seguenti dimensioni delle macchine virtuali non sono supportate per le macchine virtuali spot:
 - Serie B
 - Versioni promo di qualsiasi dimensione (ad esempio, dimensioni promo Dv2, NV, NC, H)

Le macchine virtuali spot possono essere distribuite in qualsiasi area, tranne Microsoft Azure Cina (21Vianet).

<a name="channel"></a>

Sono attualmente supportati i [tipi di offerta](https://azure.microsoft.com/support/legal/offer-details/) seguenti:

-   Enterprise Agreement
-   Pagamento in base al consumo
-   Sponsorizzato
- Per il provider di servizi cloud (CSP), contattare il proprio partner


## <a name="pricing"></a>Prezzi

I prezzi per le macchine virtuali spot variano in base all'area e allo SKU. Per altre informazioni, vedere i prezzi delle macchine virtuali per [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 


Con i prezzi variabili è possibile impostare un prezzo massimo, in dollari statunitensi (USD), usando al massimo 5 cifre decimali. Ad esempio, il valore `0.98765`sarebbe un prezzo massimo di 0,98765 USD all'ora. Se si imposta il prezzo massimo su `-1`, la macchina virtuale non verrà eliminata in base al prezzo. Il prezzo della macchina virtuale corrisponderà al prezzo corrente per spot o al prezzo di una macchina virtuale standard, a seconda di quale sia il più basso, purché siano disponibili capacità e quota.


##  <a name="frequently-asked-questions"></a>Domande frequenti

**D:** Se si crea una macchina virtuale spot, è identica a una normale macchina virtuale standard?

**R:** Sì, ad eccezione del fatto che per le macchine virtuali spot non esiste un contratto di servizio e che possono essere eliminate in qualsiasi momento.


**D:** Cosa si può fare se la VM viene eliminata, ma è ancora necessaria capacità?

**R:** È consigliabile usare le macchine virtuali standard anziché le macchine virtuali spot se si ha immediatamente bisogno di capacità.


**D:** Come viene gestita la quota per le macchine virtuali spot?

**R:** Le macchine virtuali spot avranno un pool di quota separato. La quota spot verrà condivisa tra le macchine virtuali e le istanze del set di scalabilità. Per altre informazioni, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).


**D:** È possibile richiedere una quota aggiuntiva per le macchine virtuali spot?

**R:** Sì, è possibile inviare la richiesta di aumento della quota per le macchine virtuali spot attraverso il [processo di richiesta di quota standard](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests).


**D:** Dove è possibile pubblicare le domande?

**R:** È possibile pubblicare e contrassegnare la domanda con `azure-spot` in [Domande e risposte](https://docs.microsoft.com/answers/topics/azure-spot.html). 



