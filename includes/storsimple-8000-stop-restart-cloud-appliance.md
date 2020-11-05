---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: a01f91a81629800d3f03b907c65f05433b6163e6
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375845"
---
#### <a name="to-stop-and-start-a-cloud-appliance"></a>Per arrestare e avviare un'appliance cloud

1. Per arrestare un'appliance cloud, passare alla relativa macchina virtuale.
    ![Macchina virtuale appliance cloud StorSimple](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart1.png)

2. Nella barra dei comandi fare clic su **Interrompi**.

    ![Macchina virtuale appliance cloud StorSimple 2](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart2.png)

3. Quando viene richiesta la conferma, fare clic su **Sì**.

    ![Macchina virtuale appliance cloud StorSimple 3](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart3.png)

4. Con l'arresto, la macchina virtuale viene deallocata. Durante l'arresto dell'appliance cloud, il suo stato è **Deallocazione**. Dopo l'arresto dell'appliance cloud, il suo stato è **Arrestato (deallocato)**.

    ![Macchina virtuale appliance cloud StorSimple 4](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart4.png)

5. Dopo l'arresto della macchina virtuale, fare clic su **Avvia** (il pulsante diventa disponibile) per avviare la macchina virtuale. Dopo l'avvio dell'appliance cloud, il suo stato è **Avviato**.

    ![Macchina virtuale appliance cloud StorSimple 5](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart5.png)

Usare i cmdlet seguenti per arrestare e avviare un'appliance cloud.

`Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

`Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

#### <a name="to-restart-a-cloud-appliance"></a>Per riavviare un'appliance cloud

Per riavviare un'appliance cloud, passare alla relativa macchina virtuale. Nella barra dei comandi fare clic su **Riavvia**. Quando richiesto, confermare il riavvio. Quando l'appliance cloud è pronta per l'uso, lo stato viene indicato come **In esecuzione**.

![Macchina virtuale appliance cloud StorSimple 6](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart6.png)

Usare il cmdlet seguente per riavviare un'appliance cloud.

`Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

