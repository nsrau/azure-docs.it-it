---
title: Creare un gruppo di posizionamento di prossimità tramite il portaleCreate a proximity placement group using the portal
description: Informazioni su come creare un gruppo di posizionamento di prossimità usando il portale di Azure.Learn how to create a proximity placement group using the Azure portal.
services: virtual-machines
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: 8512d9b701242dc686d49bbe56e8a2059f14ee3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73180251"
---
# <a name="create-a-proximity-placement-group-using-the-portal"></a>Creare un gruppo di posizionamento di prossimità tramite il portaleCreate a proximity placement group using the portal

Per ottenere le macchine virtuali il più vicino possibile, ottenendo la latenza più bassa possibile, è necessario distribuirle all'interno di un gruppo di [posizionamento di prossimità.](co-location.md#proximity-placement-groups)

Un gruppo di posizionamento di prossimità è un raggruppamento logico usato per assicurarsi che le risorse di calcolo di Azure si trovino fisicamente vicine l'una all'altra. I gruppi di posizionamento di prossimità sono utili per i carichi di lavoro in cui una bassa latenza è un requisito.


## <a name="create-the-proximity-placement-group"></a>Creare il gruppo di posizionamento di prossimità

1. Digitare il gruppo di posizionamento di **prossimità** nella ricerca.
1. In **Servizi** nei risultati della ricerca selezionare **Gruppi di posizionamento di prossimità**.
1. Nella pagina **Gruppi di posizionamento di prossimità** selezionare **Aggiungi**.
1. Nella scheda **Informazioni di base**, in **Dettagli progetto**, verificare che sia selezionata la sottoscrizione corretta.
1. In **Gruppo** di risorse selezionare **Crea nuovo** per creare un nuovo gruppo o selezionare un gruppo di risorse esistente dall'elenco a discesa.
1. In **Area** selezionare la posizione in cui si desidera creare il gruppo di posizionamento di prossimità.
1. In **Nome gruppo di posizionamento di prossimità** digitare un nome e quindi selezionare Revisione e **creazione**.
1. Al termine della convalida, selezionare **Crea** per creare il gruppo di posizionamento di prossimità.

    ![Screenshot della creazione di un gruppo di posizionamento di prossimità](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>Creare una macchina virtuale

1. Durante la creazione di una macchina virtuale nel portale, passare alla scheda **Avanzate.While** creating a VM in the portal, go to the Advanced tab. 
1. Nella selezione **Gruppo posizionamento prossimità,** selezionare il gruppo di posizionamento corretto. 

    ![Screenshot della sezione del gruppo di posizionamento di prossimità durante la creazione di una nuova macchina virtuale nel portale](./media/ppg/vm-ppg.png)

1. Al termine delle selezioni necessarie, selezionare **Rivedi e crea**.
1. Dopo aver superato la convalida, selezionare **Crea** per distribuire la macchina virtuale nel gruppo di posizionamento.




## <a name="next-steps"></a>Passaggi successivi

È anche possibile usare [Azure PowerShell](proximity-placement-groups.md) per creare gruppi di posizionamento di prossimità.

