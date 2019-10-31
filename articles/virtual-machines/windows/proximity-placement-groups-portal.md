---
title: Creare un gruppo di posizionamento di prossimità usando il portale
description: Informazioni su come creare un gruppo di posizionamento di prossimità usando il portale di Azure.
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
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73180251"
---
# <a name="create-a-proximity-placement-group-using-the-portal"></a>Creare un gruppo di posizionamento di prossimità usando il portale

Per ottenere le macchine virtuali il più vicino possibile, ottenendo la latenza più bassa possibile, è consigliabile distribuirle all'interno di un [gruppo di posizionamento di prossimità](co-location.md#proximity-placement-groups).

Un gruppo di posizionamento vicino è un raggruppamento logico usato per assicurarsi che le risorse di calcolo di Azure siano posizionate fisicamente tra loro. I gruppi di posizionamento prossimità sono utili per i carichi di lavoro in cui è necessario un livello di latenza bassa.


## <a name="create-the-proximity-placement-group"></a>Creare il gruppo di posizionamento vicino

1. Digitare il **gruppo di posizionamento vicino** nella ricerca.
1. In **Servizi** nei risultati della ricerca selezionare **gruppi di posizionamento prossimità**.
1. Nella pagina **gruppi di posizionamento prossimità** selezionare **Aggiungi**.
1. Nella scheda **Informazioni di base**, in **Dettagli progetto**, verificare che sia selezionata la sottoscrizione corretta.
1. In **gruppo di risorse** selezionare **Crea nuovo** per creare un nuovo gruppo o selezionare un gruppo di risorse esistente dall'elenco a discesa.
1. In **area** selezionare la località in cui si vuole creare il gruppo di posizionamento di prossimità.
1. In **nome gruppo di posizionamento prossimità** Digitare un nome e quindi selezionare **Verifica + crea**.
1. Al termine della convalida, selezionare **Crea** per creare il gruppo di posizionamento di prossimità.

    ![Screenshot della creazione di un gruppo di posizionamento vicino](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>Creare una VM

1. Durante la creazione di una macchina virtuale nel portale, passare alla scheda **Avanzate** . 
1. Nella selezione del **gruppo di posizionamento vicino** selezionare il gruppo di posizionamento corretto. 

    ![Screenshot della sezione del gruppo di posizionamento vicino quando si crea una nuova macchina virtuale nel portale](./media/ppg/vm-ppg.png)

1. Al termine dell'esecuzione di tutte le altre selezioni necessarie, selezionare **Verifica + crea**.
1. Dopo aver superato la convalida, selezionare **Crea** per distribuire la VM nel gruppo di selezione host.




## <a name="next-steps"></a>Passaggi successivi

È anche possibile usare la [Azure PowerShell](proximity-placement-groups.md) per creare gruppi di posizionamento di prossimità.

