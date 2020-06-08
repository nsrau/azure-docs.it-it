---
title: includere file
description: includere file
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/23/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: e7dbac1f4fad940b817befa3a45447cf7367c28c
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84317447"
---
L'uso delle macchine virtuali spot consente di sfruttare la capacità inutilizzata con un notevole risparmio sui costi. Quando, in qualsiasi momento, Azure avrà di nuovo bisogno di quella capacità, l'infrastruttura di Azure rimuoverà le macchine virtuali spot. Le macchine virtuali spot sono pertanto ideali per i carichi di lavoro in grado di gestire le interruzioni, come i processi di elaborazione batch, gli ambienti di sviluppo/test, i carichi di lavoro di calcolo di grandi dimensioni e altro ancora.

La quantità di capacità disponibile dipende dalle dimensioni, dall'area, dal momento della giornata e da altri fattori. Quando si distribuiscono le macchine virtuali spot, Azure le alloca se c'è capacità disponibile, ma non esiste alcun contratto di servizio per queste macchine virtuali. Una macchina virtuale spot non offre garanzie di disponibilità elevata. Quando, in qualsiasi momento, Azure avrà di nuovo bisogno di quella capacità, l'infrastruttura di Azure rimuoverà le macchine virtuali spot con un preavviso di 30 secondi. 


## <a name="eviction-policy"></a>Criteri di rimozione

Le macchine virtuali possono essere eliminate in base alla capacità o al prezzo massimo impostato. Per le macchine virtuali, i criteri di rimozione sono impostati su *Deallocare* che sposta le VM eliminate nello stato arrestata-deallocata, consentendo di ridistribuire le VM eliminate in un secondo momento. Tuttavia, la riallocazione delle macchine virtuali spot dipende dalla disponibilità di capacità spot. Le macchine virtuali deallocate verranno incluse nel conteggio per la quota di vCPU spot e verranno addebitati i costi dei dischi sottostanti. 

Gli utenti possono acconsentire esplicitamente a ricevere notifiche nelle macchine virtuali con [Eventi pianificati di Azure](../articles/virtual-machines/linux/scheduled-events.md). In questo modo riceveranno una notifica se le macchine virtuali vengono eliminate e avranno a disposizione 30 secondi per completare i processi ed eseguire le attività di arresto prima dell'eliminazione. 


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

Le macchine virtuali spot attualmente non possono usare dischi del sistema operativo temporanei.

Le macchine virtuali spot possono essere distribuite in qualsiasi area, tranne Microsoft Azure Cina (21Vianet).

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


**D:** Quali canali supportano le macchine virtuali spot?

**R:** Vedere la tabella seguente per la disponibilità di macchine virtuali spot.

<a name="channel"></a>

| Canali di Azure               | Disponibilità di macchine virtuali spot di Azure       |
|------------------------------|-----------------------------------|
| Enterprise Agreement         | Sì                               |
| Pagamento in base al consumo                | Sì                               |
| Provider di servizi cloud | [Contattare il partner](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Contratto del cliente Microsoft | Sì                               |
| Vantaggi                     | Non disponibile                     |
| Sponsorizzato                    | Sì                               |
| Versione di valutazione gratuita                   | Non disponibile                     |


**D:** Dove è possibile pubblicare le domande?

**R:** È possibile pubblicare e contrassegnare la domanda con `azure-spot` in [Domande e risposte](https://docs.microsoft.com/answers/topics/azure-spot.html). 



