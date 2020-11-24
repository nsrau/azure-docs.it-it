---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/30/2019
ms.author: zivr
ms.custom: include file
ms.openlocfilehash: daed8dc62fdfd86ecf785a0bfd83b2b6c0b4cc03
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95557254"
---
L'inserimento di macchine virtuali in una singola area riduce la distanza fisica tra le istanze. L'inserimento di tali elementi all'interno di una singola zona di disponibilità li rende anche fisicamente più vicini. Tuttavia, man mano che il footprint di Azure cresce, una singola zona di disponibilità può estendersi su più data center fisici, causando una latenza di rete che influisca sull'applicazione. 

Per ottenere le macchine virtuali il più vicino possibile, ottenendo la latenza più bassa possibile, è consigliabile distribuirle all'interno di un gruppo di posizionamento di prossimità.

Un gruppo di posizionamento di prossimità è un raggruppamento logico usato per assicurarsi che le risorse di calcolo di Azure si trovino una vicino all'altra. I gruppo di posizionamento di prossimità sono utili per i carichi di lavoro che richiedono una latenza ridotta.


- Bassa latenza tra macchine virtuali autonome.
- Bassa latenza tra le macchine virtuali in un singolo set di disponibilità o un set di scalabilità di macchine virtuali. 
- Bassa latenza tra macchine virtuali autonome, VM in più set di disponibilità o più set di scalabilità. È possibile disporre di più risorse di calcolo in un singolo gruppo di posizionamento per riunire un'applicazione a più livelli. 
- Bassa latenza tra più livelli applicazione con diversi tipi di hardware. Ad esempio, l'esecuzione del back-end usando la serie M in un set di disponibilità e il front-end in un'istanza della serie D, in un set di scalabilità, in un singolo gruppo di posizionamento di prossimità.


![Immagine per gruppi di posizionamento di prossimità](./media/virtual-machines-common-ppg/ppg.png)

## <a name="using-proximity-placement-groups"></a>Uso di gruppi di posizionamento di prossimità 

Un gruppo di posizionamento di prossimità è un nuovo tipo di risorsa in Azure. È necessario crearne uno prima di usarlo con altre risorse. Una volta creata, può essere usata con macchine virtuali, set di disponibilità o set di scalabilità di macchine virtuali. È possibile specificare un gruppo di posizionamento di prossimità quando si creano risorse di calcolo che forniscono l'ID del gruppo di posizionamento di prossimità. 

È anche possibile spostare una risorsa esistente in un gruppo di posizionamento di prossimità. Quando si trasferisce una risorsa in un gruppo di posizionamento di prossimità, è necessario arrestare (deallocare) l'asset prima che venga ridistribuito potenzialmente in un data center diverso dell'area per soddisfare il vincolo di condivisione percorso. 

Nel caso dei set di disponibilità e dei set di scalabilità di macchine virtuali, è necessario impostare il gruppo di posizionamento vicino a livello di risorsa anziché le singole macchine virtuali. 

Un gruppo di posizionamento di prossimità è un vincolo di condivisione percorso anziché un meccanismo di blocco. Viene aggiunto a un data center specifico con la distribuzione della prima risorsa per usarlo. Una volta che tutte le risorse che usano il gruppo di posizionamento di prossimità sono state arrestate (deallocate) o eliminate, non vengono più bloccate. Pertanto, quando si usa un gruppo di posizionamento di prossimità con più serie di macchine virtuali, è importante specificare tutti i tipi richiesti in anticipo in un modello quando possibile o seguire una sequenza di distribuzione che consentirà di migliorare le probabilità di una distribuzione corretta. Se la distribuzione ha esito negativo, riavviare la distribuzione con le dimensioni della macchina virtuale non riuscite come prima dimensione da distribuire.

## <a name="what-to-expect-when-using-proximity-placement-groups"></a>Cosa aspettarsi quando si usano i gruppi di posizionamento di prossimità 
I gruppi di posizionamento di prossimità offrono la condivisione percorso nella stessa data center. Tuttavia, poiché i gruppi di posizionamento di prossimità rappresentano un vincolo di distribuzione aggiuntivo, possono verificarsi errori di allocazione. Esistono pochi casi d'uso in cui è possibile che si verifichino errori di allocazione quando si usano gruppi di posizionamento di prossimità:

- Quando si richiede la prima macchina virtuale nel gruppo di posizionamento prossimità, il data center viene selezionato automaticamente. In alcuni casi, una seconda richiesta per uno SKU di macchina virtuale diverso potrebbe non riuscire se non esiste in tale data center. In questo caso, viene restituito un errore **OverconstrainedAllocationRequest** . Per evitare questo problema, provare a modificare l'ordine in cui vengono distribuiti gli SKU o avere entrambe le risorse distribuite usando un singolo modello ARM.
-   Nel caso di carichi di lavoro elastici, in cui si aggiungono e rimuovono le istanze di macchina virtuale, la presenza di un vincolo di gruppo di posizionamento di prossimità nella distribuzione può comportare un errore di soddisfazione della richiesta che ha generato un errore **AllocationFailure** . 
- Arrestare (deallocare) e avviare le VM in base alle esigenze è un altro modo per ottenere l'elasticità. Poiché la capacità non viene mantenuta quando si arresta (dealloca) una macchina virtuale, il suo avvio può causare un errore **AllocationFailure** .

## <a name="planned-maintenance-and-proximity-placement-groups"></a>Manutenzione pianificata e gruppi di posizionamento di prossimità

Gli eventi di manutenzione pianificata, ad esempio la rimozione delle autorizzazioni per l'hardware in un Data Center di Azure, potrebbero influire sull'allineamento delle risorse nei gruppi di posizionamento in prossimità. Le risorse possono essere spostate in un data center diverso, interrottando la collocazione e le aspettative di latenza associate al gruppo di posizionamento di prossimità.

### <a name="check-the-alignment-status"></a>Verificare lo stato di allineamento

Per verificare lo stato di allineamento dei gruppi di posizionamento vicini, è possibile eseguire le operazioni seguenti.


- Lo stato di condivisione percorso gruppo posizionamento prossimità può essere visualizzato usando il portale, l'interfaccia della riga di comando e PowerShell.

    -   Quando si usa PowerShell, è possibile ottenere lo stato della condivisione percorso usando Get-AzProximityPlacementGroup cmdlet includendo il parametro facoltativo '-ColocationStatus '.

    -   Quando si usa l'interfaccia della riga di comando, è possibile ottenere lo stato della condivisione percorso usando `az ppg show` includendo il parametro facoltativo '--include-Colocation-status '.

- Per ogni gruppo di posizionamento di prossimità, una proprietà di stato di condivisione **percorso** fornisce il riepilogo dello stato di allineamento corrente delle risorse raggruppate. 

    - **Allineato**: la risorsa rientra nella stessa busta di latenza del gruppo di posizionamento di prossimità.

    - **Sconosciuto**: almeno una delle risorse della macchina virtuale è deallocata. Una volta avviate correttamente, lo stato dovrebbe tornare a **allineato**.

    - **Non allineato**: almeno una risorsa VM non è allineata con il gruppo di posizionamento prossimità. Anche le risorse specifiche non allineate verranno richiamate separatamente nella sezione relativa all'appartenenza

- Per i set di disponibilità è possibile visualizzare informazioni sull'allineamento delle singole VM nella pagina Panoramica del set di disponibilità.

- Per i set di scalabilità, le informazioni sull'allineamento delle singole istanze possono essere visualizzate nella scheda **istanze** della pagina **Panoramica** per il set di scalabilità. 


### <a name="re-align-resources"></a>Allinea di nuovo le risorse 

Se un gruppo di posizionamento di prossimità è `Not Aligned` , è possibile stop\deallocate, quindi riavviare le risorse interessate. Se la macchina virtuale si trova in un set di disponibilità o in un set di scalabilità, tutte le VM del set di disponibilità o del set di scalabilità devono essere stopped\deallocated prima di riavviarle.

Se si verifica un errore di allocazione a causa di vincoli di distribuzione, potrebbe essere necessario stop\deallocate tutte le risorse nel gruppo di posizionamento vicino interessato (incluse le risorse allineate) e quindi riavviarle per ripristinare l'allineamento.

## <a name="best-practices"></a>Procedure consigliate 
- Per la latenza più bassa, usare i gruppi di posizionamento di prossimità insieme alla rete accelerata. Per altre informazioni, vedere [creare una macchina virtuale Linux con rete accelerata](../articles/virtual-network/create-vm-accelerated-networking-cli.md?toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json) o [creare una macchina virtuale Windows con rete accelerata](../articles/virtual-network/create-vm-accelerated-networking-powershell.md?toc=%252fazure%252fvirtual-machines%252fwindows%252ftoc.json).
- Distribuire tutte le dimensioni delle macchine virtuali in un singolo modello. Per evitare di dover atterrare su hardware che non supporta tutti gli SKU e le dimensioni delle VM necessari, includere tutti i livelli applicazione in un singolo modello in modo che vengano distribuiti tutti nello stesso momento.
- Se si crea uno script per la distribuzione usando PowerShell, l'interfaccia della riga di comando o l'SDK, è possibile che si verifichi un errore di allocazione `OverconstrainedAllocationRequest` . In questo caso, è necessario arrestare/deallocare tutte le macchine virtuali esistenti e modificare la sequenza nello script di distribuzione per iniziare con le dimensioni/SKU della macchina virtuale non riuscite. 
- Quando si riutilizza un gruppo di posizionamento esistente dal quale sono state eliminate le macchine virtuali, attendere il completamento dell'eliminazione prima di aggiungere macchine virtuali.
- Se la latenza è la prima priorità, inserire le macchine virtuali in un gruppo di posizionamento vicino e l'intera soluzione in una zona di disponibilità. Tuttavia, se la resilienza è la priorità più alta, distribuire le istanze tra più zone di disponibilità (un singolo gruppo di posizionamento di prossimità non può estendersi alle zone).