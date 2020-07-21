---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: a5a286753e438b7d65f3d33a82669c4f7e79a282
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86544303"
---
#### <a name="to-create-public-endpoints-on-the-cloud-appliance"></a>Per creare endpoint pubblici nell'appliance cloud

1. Accedere al portale di Azure.
2. Andare a **Macchine virtuali**, quindi selezionare e fare clic sulla macchina virtuale che viene usata come appliance cloud.
    
3. È necessario creare una regola del gruppo di sicurezza di rete (NSG) per controllare il flusso di traffico in ingresso e in uscita della macchina virtuale. Per creare una regola NSG, seguire questa procedura.
    1. Selezionare **Gruppo di sicurezza di rete**.
        ![Screenshot della pagina della macchina virtuale. Nella sezione impostazioni viene evidenziato il gruppo di sicurezza di rete.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt1.png)

    2. Fare clic su gruppo di sicurezza di rete predefinito che viene visualizzato.
        ![Screenshot della pagina del gruppo di sicurezza di rete. Viene evidenziato il gruppo di sicurezza di rete predefinito.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt2.png)

    3. Selezionare **Regole di sicurezza in ingresso**.
        ![Screenshot di una pagina che mostra le proprietà del gruppo di sicurezza di rete predefinito. Nel riquadro di spostamento vengono evidenziate le regole di sicurezza in ingresso.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt3.png)

    4. Fare clic su **+ Aggiungi** per creare una regola di sicurezza in ingresso.
        ![Screenshot della pagina regole di sicurezza in ingresso. Il segno più e la parola Aggiungi sono gli uni accanto agli altri e sono evidenziati.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt4.png)

        Nel pannello Aggiungi regola di sicurezza in ingresso:

        1. Per il **nome**, digitare il nome seguente per l'endpoint: WinRMHttps.
        
        2. In **Priorità** selezionare un numero minore di 1000 (priorità per la regola predefinita). Maggiore è questo numero, minore è la priorità.

        3. Impostare **Origine** su **Qualsiasi**.

        4. In **Servizio** selezionare **WinRM**. **Protocollo** viene automaticamente impostato su **TCP** e **Intervallo di porte** viene impostato su **5986**.

        5. Fare clic su **OK** per creare la regola.

            ![Screenshot del pannello Aggiungi regola di sicurezza in ingresso. I valori vengono compilati come descritto nella procedura e viene evidenziato il pulsante OK.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt5.png)

4. Il passaggio finale consiste nell'associare il gruppo di sicurezza di rete a una subnet o un'interfaccia di rete specifica. Seguire questa procedura per associare il gruppo di sicurezza di rete a una subnet.
    1. Andare a **Subnet**.
    2. Fare clic su **+ Associa**.
        ![Screenshot della pagina subnet. Il segno più e la parola associate sono gli uni accanto agli altri e sono evidenziati.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt7.png)

    3. Selezionare la rete virtuale, quindi selezionare la subnet appropriata.
    4. Fare clic su **OK** per creare la regola.

        ![Screenshot della pagina di associazione della subnet. Viene selezionata la rete virtuale e viene evidenziato il pulsante OK.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt11.png)

Dopo avere creato la regola, è possibile visualizzarne i dettagli e determinare l'indirizzo IP virtuale pubblico (indirizzo VIP). Registrare tale indirizzo.


