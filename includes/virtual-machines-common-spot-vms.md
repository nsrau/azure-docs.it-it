---
title: file di inclusione
description: file di inclusione
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/23/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: b671fe9a4ecd35cbe9d70f398f8d39664203fc58
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74782112"
---
L'uso di macchine virtuali con spot consente di sfruttare la capacità inutilizzata con un notevole risparmio sui costi. In qualsiasi momento, quando Azure necessita della capacità, l'infrastruttura di Azure eliminerà le VM spot. Quindi, le VM spot sono ottime per i carichi di lavoro in grado di gestire le interruzioni, ad esempio processi di elaborazione batch, ambienti di sviluppo/test, carichi di lavoro di calcolo di grandi dimensioni e altro ancora.

La quantità di capacità disponibile può variare in base alle dimensioni, all'area, all'ora del giorno e così via. Quando si distribuiscono macchine virtuali spot, Azure alloca le VM se la capacità è disponibile, ma non esiste alcun contratto di contratto per queste macchine virtuali. Una macchina virtuale spot non offre garanzie di disponibilità elevata. In qualsiasi momento, quando Azure necessita della capacità, l'infrastruttura di Azure eliminerà le macchine virtuali con preavviso di 30 secondi. 

> [!IMPORTANT]
> Le istanze di spot sono attualmente in anteprima pubblica.
> Questa versione di anteprima non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Per la prima parte dell'anteprima pubblica, le istanze di spot avranno un prezzo fisso, quindi non vi saranno eliminazioni basate sul prezzo.

## <a name="eviction-policy"></a>Criteri di rimozione

Le macchine virtuali possono essere eliminate in base alla capacità o al prezzo massimo impostato. Per le macchine virtuali, i criteri di rimozione sono impostati su *deallocate* , che sposta le VM eliminate nello stato arrestato-deallocato, consentendo di ridistribuire le VM rimosse in un secondo momento. Tuttavia, la riallocazione delle macchine virtuali spot dipende da una capacità di spot disponibile. Le macchine virtuali deallocate verranno conteggiate rispetto alla quota vCPU di spot e verranno addebitati i costi per i dischi sottostanti. 

Gli utenti possono acconsentire esplicitamente a ricevere notifiche in-VM tramite [Azure eventi pianificati](../articles/virtual-machines/linux/scheduled-events.md). In questo modo si riceverà una notifica se le macchine virtuali vengono eliminate e saranno disponibili 30 secondi per completare i processi ed eseguire le attività di arresto prima dell'eliminazione. 

> [!IMPORTANT]
> Per la prima parte dell'anteprima pubblica, è possibile impostare un prezzo massimo, che verrà ignorato. Le macchine virtuali spot avranno un prezzo fisso, quindi non vi saranno eliminazioni basate sul prezzo.


| Opzione | Risultato |
|--------|---------|
| Il prezzo massimo è impostato su > = il prezzo corrente. | La macchina virtuale viene distribuita se la capacità e la quota sono disponibili. |
| Il prezzo massimo è impostato su < il prezzo corrente. | La macchina virtuale non è stata distribuita. Verrà ricevuto un messaggio di errore che segnala che il prezzo massimo deve essere > = prezzo corrente. |
| Riavvio di una macchina virtuale stop/deallocate se il prezzo massimo è > = il prezzo corrente | Se sono presenti capacità e quota, la macchina virtuale viene distribuita. |
| Riavvio di una macchina virtuale stop/deallocate se il prezzo massimo è < il prezzo corrente | Verrà ricevuto un messaggio di errore che segnala che il prezzo massimo deve essere > = prezzo corrente. | 
| Il prezzo della macchina virtuale è scaduto e ora è > il prezzo massimo. | La macchina virtuale viene eliminata. Si riceverà una notifica 30s prima dell'eliminazione effettiva. | 
| Dopo la rimozione, il prezzo per la macchina virtuale torna a essere < il prezzo massimo. | La macchina virtuale non verrà riavviata automaticamente. Puoi riavviare la macchina virtuale manualmente e verrà addebitato il prezzo corrente. |
| Se il prezzo massimo è impostato su `-1` | La macchina virtuale non verrà rimossa per motivi tariffari. Il prezzo massimo corrisponderà al prezzo corrente, fino al prezzo per le macchine virtuali standard. Non verrà mai addebitato il prezzo superiore al prezzo standard.| 
| Modifica del prezzo massimo | È necessario deallocare la macchina virtuale per modificare il prezzo massimo. Deallocare la VM, impostare t un nuovo prezzo massimo, quindi aggiornare la macchina virtuale. |

## <a name="limitations"></a>Limitazioni

Le dimensioni delle macchine virtuali seguenti non sono supportate per le VM spot:
 - Serie B
 - Versioni promo di qualsiasi dimensione (ad esempio, dv2, NV, NC, H promo size)

Le VM spot non possono attualmente usare dischi del sistema operativo temporanei.

Le VM spot possono essere distribuite in qualsiasi area, ad eccezione Microsoft Azure Cina 21Vianet e Department of Defense (DoD) nell'area di Azure per enti pubblici.

## <a name="pricing"></a>Prezzi

I prezzi per le VM spot sono variabili in base all'area e allo SKU. Per altre informazioni, vedere prezzi delle VM per [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 


Con i prezzi variabili è possibile impostare un prezzo massimo, in dollari statunitensi (USD), usando un massimo di 5 cifre decimali. Ad esempio, il valore `0.98765`sarebbe un prezzo massimo di $0,98765 USD all'ora. Se si imposta il prezzo massimo da `-1`, la macchina virtuale non verrà rimossa in base al prezzo. Il prezzo della macchina virtuale corrisponderà al prezzo corrente per spot o al prezzo di una macchina virtuale standard, che sempre è inferiore, purché siano disponibili capacità e quota.


##  <a name="frequently-asked-questions"></a>Domande frequenti

**D:** Una volta creata, è una macchina virtuale spot identica alla normale macchina virtuale standard?

**R:** Sì, ad eccezione del fatto che non esiste alcun contratto di contratto per le macchine virtuali spot e che possono essere rimossi in qualsiasi momento.


**D:** Cosa fare quando si viene eliminati, ma è ancora necessaria una capacità?

**R:** È consigliabile usare VM standard anziché macchine virtuali spot se è necessaria immediatamente la capacità.


**D:** Come viene gestita la quota per le VM spot?

**R:** Le macchine virtuali spot avranno un pool di quote separato. La quota spot verrà condivisa tra le macchine virtuali e le istanze del set di scalabilità. Per altre informazioni, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](https://docs.microsoft.com/azure/azure-subscription-service-limits).


**D:** È possibile richiedere una quota aggiuntiva per spot?

**R:** Sì, sarà possibile inviare la richiesta per aumentare la quota per le VM spot tramite il [processo standard di richiesta di quota](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).


**D:** Quali canali supportano le VM spot?

**R:** Vedere la tabella seguente per la disponibilità della macchina virtuale spot.

<a name="channel"></a>

| Canali di Azure               | Disponibilità di macchine virtuali di Azure spot       |
|------------------------------|-----------------------------------|
| Contratto Enterprise Agreement         | SÌ                               |
| Pagamento in base al consumo                | SÌ                               |
| Provider di servizi cloud | [Contatta il tuo partner](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Vantaggi                     | Non disponibile                     |
| Eventi sponsorizzati                    | Non disponibile                     |
| Versione di valutazione gratuita                   | Non disponibile                     |


**D:** Dove è possibile pubblicare domande?

**R:** È possibile pubblicare e contrassegnare la domanda con `azure-spot` a [Q & a](https://docs.microsoft.com/answers/topics/azure-spot.html). 



