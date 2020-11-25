---
title: Creare un gruppo di posizionamento di prossimità usando il portale
description: Informazioni su come creare un gruppo di posizionamento di prossimità usando il portale di Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/24/2020
ms.author: cynthn
ms.openlocfilehash: 6a14e2bd7385430c3d0fbec06259a876af556e38
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010725"
---
# <a name="create-a-proximity-placement-group-using-the-portal"></a>Creare un gruppo di posizionamento di prossimità usando il portale

Per ottenere le macchine virtuali il più vicino possibile, ottenendo la latenza più bassa possibile, è consigliabile distribuirle all'interno di un [gruppo di posizionamento di prossimità](co-location.md#proximity-placement-groups).

Un gruppo di posizionamento di prossimità è un raggruppamento logico usato per assicurarsi che le risorse di calcolo di Azure si trovino una vicino all'altra. I gruppo di posizionamento di prossimità sono utili per i carichi di lavoro che richiedono una latenza ridotta.

> [!NOTE]
> Non è possibile usare i gruppi di posizionamento di prossimità con host dedicati.
>
> Se si vogliono usare le zone di disponibilità insieme ai gruppi di posizionamento, è necessario assicurarsi che le macchine virtuali nel gruppo di posizionamento si trovino anche nella stessa zona di disponibilità.
>

## <a name="create-the-proximity-placement-group"></a>Creare il gruppo di posizionamento vicino

1. Digitare il **gruppo di posizionamento vicino** nella ricerca.
1. In **Servizi** nei risultati della ricerca selezionare **gruppi di posizionamento prossimità**.
1. Nella pagina **gruppi di posizionamento prossimità** selezionare **Aggiungi**.
1. Nella scheda **Informazioni di base**, in **Dettagli progetto**, verificare che sia selezionata la sottoscrizione corretta.
1. In **gruppo di risorse** selezionare **Crea nuovo** per creare un nuovo gruppo o selezionare un gruppo di risorse vuoto già esistente dall'elenco a discesa. 
1. In **area** selezionare la località in cui si vuole creare il gruppo di posizionamento di prossimità.
1. In **nome gruppo di posizionamento prossimità** Digitare un nome e quindi selezionare **Verifica + crea**.
1. Al termine della convalida, selezionare **Crea** per creare il gruppo di posizionamento di prossimità.

    ![Screenshot della creazione di un gruppo di posizionamento vicino](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>Creare una macchina virtuale

1. Durante la creazione di una macchina virtuale nel portale, passare alla scheda **Avanzate** . 
1. Nella selezione del **gruppo di posizionamento vicino** selezionare il gruppo di posizionamento corretto. 

    ![Screenshot della sezione del gruppo di posizionamento vicino quando si crea una nuova macchina virtuale nel portale](./media/ppg/vm-ppg.png)

1. Al termine dell'esecuzione di tutte le altre selezioni necessarie, selezionare **Verifica + crea**.
1. Dopo aver superato la convalida, selezionare **Crea** per distribuire la VM nel gruppo di selezione host.


## <a name="add-vms-in-an-availability-set-to-a-proximity-placement-group"></a>Aggiungere macchine virtuali in un set di disponibilità a un gruppo di posizionamento vicino

Se la macchina virtuale fa parte del set di disponibilità, prima di aggiungere le macchine virtuali è necessario aggiungere il set di disponibilità al gruppo di selezione host.

1. Nel [portale](https://portal.azure.com) cercare i *set di disponibilità* e selezionare il set di disponibilità dai risultati.
1. Stop\deallocate ogni macchina virtuale nel set di disponibilità selezionando la macchina virtuale, quindi selezionando **Interrompi** nella pagina della macchina virtuale e quindi selezionando **OK** per arrestare la macchina virtuale.
1. Nella pagina del set di disponibilità, verificare che tutte le macchine virtuali abbiano lo **stato** elencato come **arrestato (deallocato)**.
1. Nel menu a sinistra selezionare **Configurazione**.
1. In **gruppo di posizionamento prossimità** selezionare un gruppo di selezione nell'elenco a discesa, quindi fare clic su **Salva**.
1. Selezionare **Panoramica** dal menu a sinistra per visualizzare di nuovo l'elenco di macchine virtuali. 
1. Selezionare ogni macchina virtuale nel set di disponibilità e quindi selezionare **Avvia** nella pagina per ogni macchina virtuale. 


## <a name="add-existing-vm-to-placement-group"></a>Aggiungi macchina virtuale esistente al gruppo di posizionamento 


1. Nella pagina della macchina virtuale selezionare **Arresta**.
1. Quando lo stato della macchina virtuale è elencato come **arrestato (deallocato)**, selezionare **configurazione** nel menu a sinistra.
1. In **gruppo di posizionamento prossimità** selezionare un gruppo di selezione nell'elenco a discesa, quindi fare clic su **Salva**.
1. Selezionare **Panoramica** dal menu a sinistra, quindi selezionare **Avvia** per riavviare la macchina virtuale.

 

## <a name="next-steps"></a>Passaggi successivi

È anche possibile usare la [Azure PowerShell](proximity-placement-groups.md) per creare gruppi di posizionamento di prossimità.

