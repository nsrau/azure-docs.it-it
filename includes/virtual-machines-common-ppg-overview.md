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
ms.openlocfilehash: 3215f5952daef053c94432bc8fdef15e1775047a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73171076"
---
L'inserimento di macchine virtuali in una singola area riduce la distanza fisica tra le istanze. Collocarli all'interno di una singola zona di disponibilità li avvicinerà fisicamente. Tuttavia, con l'aumentare del footprint di Azure, una singola zona di disponibilità può estendersi su più data center fisici, il che può comportare un impatto sulla latenza di rete sull'applicazione. 

Per ottenere le macchine virtuali il più vicino possibile, ottenendo la latenza più bassa possibile, è necessario distribuirle all'interno di un gruppo di posizionamento di prossimità.

Un gruppo di posizionamento di prossimità è un raggruppamento logico usato per assicurarsi che le risorse di calcolo di Azure si trovino fisicamente vicine l'una all'altra. I gruppi di posizionamento di prossimità sono utili per i carichi di lavoro in cui una bassa latenza è un requisito.


- Bassa latenza tra macchine virtuali autonome.
- Latenza bassa tra macchine virtuali in un singolo set di disponibilità o in un set di scalabilità di macchine virtuali. 
- Bassa latenza tra macchine virtuali autonome, macchine virtuali in più set di disponibilità o più set di scalabilità. È possibile avere più risorse di calcolo in un singolo gruppo di posizionamento per riunire un'applicazione a più livelli. 
- Bassa latenza tra più livelli applicazione utilizzando diversi tipi di hardware. Ad esempio, l'esecuzione del back-end usando M-series in un set di disponibilità e il front-end in un'istanza della serie D, in un set di scalabilità, in un singolo gruppo di posizionamento di prossimità.


![Grafica per i gruppi di posizionamento di prossimità](./media/virtual-machines-common-ppg/ppg.png)

## <a name="using-proximity-placement-groups"></a>Utilizzo dei gruppi di posizionamento di prossimità 

A proximity placement group is a new resource type in Azure. È necessario crearne uno prima di utilizzarlo con altre risorse. Una volta creato, può essere usato con macchine virtuali, set di disponibilità o set di scalabilità di macchine virtuali. Si specifica un gruppo di posizionamento di prossimità durante la creazione di risorse di calcolo che forniscono l'ID del gruppo di posizionamento di prossimità. 

È inoltre possibile spostare una risorsa esistente in un gruppo di posizionamento di prossimità. Quando si sposta una risorsa in un gruppo di posizionamento di prossimità, è necessario arrestare (deallocare) prima l'asset poiché verrà ridistribuito potenzialmente in un data center diverso nell'area, in modo da soddisfare il vincolo di colocation. 

Nel caso di set di disponibilità e set di scalabilità di macchine virtuali, è necessario impostare il gruppo di posizionamento di prossimità a livello di risorsa anziché alle singole macchine virtuali. 

Un gruppo di posizionamento di prossimità è un vincolo di colocation anziché un meccanismo di blocco. Viene aggiunto a un data center specifico con la distribuzione della prima risorsa per utilizzarlo. Una volta che tutte le risorse che utilizzano il gruppo di posizionamento di prossimità sono state arrestate (deallocate) o eliminate, non sono più aggiunte. Pertanto, quando si usa un gruppo di posizionamento di prossimità con più serie di macchine virtuali, è importante specificare tutti i tipi necessari in anticipo in un modello quando possibile o seguire una sequenza di distribuzione che migliorerà le possibilità di una distribuzione corretta. Se la distribuzione non riesce, riavviare la distribuzione con la dimensione della macchina virtuale non riuscita come prima dimensione da distribuire.


## <a name="best-practices"></a>Procedure consigliate 
- Per la latenza più bassa, usare i gruppi di posizionamento di prossimità insieme alla rete accelerata. Per altre informazioni, vedere [Creare una macchina virtuale Linux con Accelerated Networking](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) o Creare una macchina virtuale Windows con [Accelerated Networking](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Distribuire tutte le dimensioni delle macchine virtuali in un unico modello. Per evitare l'atterraggio su hardware che non supporta tutti gli SKU delle macchine virtuali necessari, includere tutti i livelli applicazione in un unico modello in modo che vengano distribuiti tutti contemporaneamente.
- Se si crea uno script per la distribuzione tramite PowerShell, CLI o l'SDK, è possibile che venga visualizzato un errore `OverconstrainedAllocationRequest`di allocazione. In questo caso, è necessario arrestare/deallocare tutte le macchine virtuali esistenti e modificare la sequenza nello script di distribuzione per iniziare con lo SKU/dimensioni della macchina virtuale non riuscita. 
- Quando si riutilizza un gruppo di posizionamento esistente da cui sono state eliminate le macchine virtuali, attendere il completamento dell'eliminazione prima di aggiungervi macchine virtuali.
- Se la latenza è la prima priorità, inserire le macchine virtuali in un gruppo di posizionamento di prossimità e l'intera soluzione in una zona di disponibilità. Tuttavia, se la resilienza è la priorità assoluta, distribuire le istanze in più zone di disponibilità (un singolo gruppo di posizionamento di prossimità non può estendersi sulle zone).