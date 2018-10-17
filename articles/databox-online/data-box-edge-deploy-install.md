---
title: Esercitazione sull'installazione del dispositivo fisico Azure Data Box Edge | Microsoft Docs
description: La seconda esercitazione sull'installazione di Azure Data Box Edge illustra come disimballare, montare su rack e collegare il dispositivo fisico.
services: databox-edge-gateway
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox-edge-gateway
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/08/2018
ms.author: alkohli
ms.custom: ''
Customer intent: As an IT admin, I need to understand how to install Data Box Edge in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: 17602abd9dab590c806c33779cf127f9a5305f64
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2018
ms.locfileid: "48832904"
---
# <a name="tutorial-install-azure-data-box-edge-preview"></a>Esercitazione: Installare Azure Data Box Edge (anteprima)

Questa esercitazione descrive come installare un dispositivo fisico Data Box Edge. La procedura di installazione comporta il disimballaggio, il montaggio su rack e il cablaggio del dispositivo. 

Il completamento dell'installazione può richiedere circa 2 ore.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Disimballare il dispositivo
> * Montare su rack il dispositivo
> * Collegare i cavi del dispositivo

> [!IMPORTANT]
> Data Box Edge è disponibile in anteprima. Rivedere le [condizioni del servizio per l'anteprima di Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) prima di ordinare e distribuire la soluzione.

## <a name="prerequisites"></a>Prerequisiti

I prerequisiti per installare un dispositivo fisico sono i seguenti:

### <a name="for-the-data-box-edge-resource"></a>Per la risorsa Data Box Edge

Prima di iniziare, verificare che:

* Tutti i passaggi di [preparazione del portale per Data Box Edge](data-box-edge-deploy-prep.md) siano stati completati.
    * È stata creata una risorsa di Data Box Edge per distribuire il dispositivo.
    * È stata generata la chiave di attivazione per attivare il dispositivo con la risorsa Data Box Edge.

 
### <a name="for-the-data-box-edge-physical-device"></a>Per il dispositivo fisico Data Box Edge

Prima di distribuire un dispositivo:

- Assicurarsi che il dispositivo sia collocato in sicurezza su una superficie di lavoro (o simile) orizzontale, stabile e piana.
- Verificare che il sito in cui si intende installare abbia:
    - Alimentazione CA standard da una fonte di energia indipendente oppure
    - Un'unità PDU installabile su rack con un sistema UPS.
- Assicurarsi che nel rack in cui si intende montare il dispositivo sia disponibile uno slot 1U.

### <a name="for-the-network-in-the-datacenter"></a>Per la rete nel datacenter

Prima di iniziare:

- Verificare i requisiti di rete per distribuire un Data Box Edge e configurare la rete del data center in base ai requisiti. Per altre informazioni, vedere i [requisiti di rete di Data Box Edge](data-box-gateway-system-requirements.md#networking-requirements).
- Assicurarsi che la larghezza di banda Internet sia di almeno 20 Mbps per consentire il funzionamento ottimale del dispositivo.


## <a name="unpack-the-device"></a>Disimballare il dispositivo

Questo dispositivo viene fornito in una singola confezione. Completare la procedura seguente per disimballare il dispositivo. 

1. Posizionare la scatola su una superficie piana e orizzontale.
2. Controllare le scatole e le protezioni in schiuma per verificare l'eventuale presenza di ammaccature, tagli, danni provocati dall'acqua o altri danni evidenti. Se la scatola o le protezioni risultano danneggiate in modo grave, non aprire la confezione. Contattare il supporto tecnico Microsoft per istruzioni su come verificare il corretto funzionamento del dispositivo.
3. Disimballare la scatola. Dopo aver disimballato le due scatole, assicurarsi di avere a disposizione:
    - Un dispositivo Edge a singolo enclosure
    - Due cavi di alimentazione
    - Un kit per il montaggio in rack con slitte senza utensili (due guide laterali e componenti di montaggio inclusi)
4. In caso di mancato ricevimento di uno qualsiasi degli elementi sopra elencati, contattare il supporto tecnico di Data Box Edge. Il passaggio successivo consiste nel montaggio su rack del dispositivo.


## <a name="rack-the-device"></a>Montare su rack il dispositivo

Il dispositivo deve essere installato su un rack standard da 19 pollici. Usare la procedura seguente per montare il dispositivo su un rack standard da 48,26 cm (19 pollici) con montanti anteriori e posteriori.

> [!IMPORTANT]
> I dispositivi Data Box Edge devono essere montati su rack per il corretto funzionamento.


1. Tirare la leva di blocco anteriore per sbloccare la guida interna dal gruppo di slitte. Sbloccare il fermo e spingere la guida intermedia verso l'interno per tirare indietro la guida. A questo punto, le guide interna ed esterna dovrebbero separarsi.

    ![Installare le guide di montaggio su rack](./media/data-box-edge-deploy-install/rack-mount-rail-1.png)

2. Ora installare le guide esterne sugli elementi verticali dell'armadio rack. Per un migliore orientamento, le slitte delle guide sono contrassegnate con le diciture Front (anteriore) e tale estremità è a contatto con la parte posteriore dell'enclosure. 
    
    1. Individuare i perni nella parte anteriore e posteriore del gruppo guida. Estendere la guida per posizionarla tra i montanti del rack. Prima di tutto, fissare la guida esterna sul retro del rack. Regolare la staffa di montaggio posteriore in modo da posizionarla all'interno dei fori di montaggio posteriori del rack.   
    2. Spingere e tenere premuta la leva sulla staffa posteriore per esporre i ganci metallici. Allineare e inserire nei fori di montaggio e quindi rilasciare la leva.
    3. Allineare la staffa anteriore con il foro di montaggio.
    4. La staffa anteriore dovrebbe ora essere fissata al rack. Se necessario, è possibile usare facoltativamente viti M5 X 10L per fissare le guide ai montanti. 

    ![Installare le guide di montaggio su rack](./media/data-box-edge-deploy-install/rack-mount-rail-2.png)

3. Per fissare la guida interna allo chassis, verificare che i fori a forma di buco della serratura sulla guida interna siano allineati con i perni di posizionamento sul lato dello chassis. Assicurarsi che le teste dei perni di posizionamento dello chassis sporgano attraverso i fori a forma di buco della serratura nella guida interna. Tirare la guida verso la parte anteriore dello chassis finché non scatta in posizione con un clic. Ripetere l'operazione con l'altra guida interna. Spingere lo chassis con la guida interna nella slitta per completare l'installazione su rack.

    ![Installare le guide di montaggio su rack](./media/data-box-edge-deploy-install/rack-mount-rail-3.png)

## <a name="cable-the-device"></a>Cablare il dispositivo

Le procedure seguenti illustrano come cablare il dispositivo Edge per l'alimentazione e la connessione di rete.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare il cablaggio del dispositivo, è necessario avere a disposizione:

- Il dispositivo fisico Edge, disimballato e montato in rack
- Due cavi di alimentazione 
- Due cavi di rete 1-GbE RJ-45 e quattro cavi di rame 25-GbE SFP+
- Accesso a due unità PDU (Power Distribution Unit) - consigliato

Il dispositivo Edge ha 8 unità SSD NVMe. Il pannello anteriore è anche dotato di LED di stato e pulsanti di alimentazione. Il dispositivo include unità PSU ridondanti sul retro. Il dispositivo è dotato di sei interfacce di rete: due interfacce da 1 Gbps e quattro da 25 Gbps. Il dispositivo è dotato di un controller BMC (Baseboard Management Controller). Identificare le varie porte nel backplane del dispositivo.
 
  ![Backplane del dispositivo cablato](./media/data-box-edge-deploy-install/backplane-cabled.png)
 
Per cablare il dispositivo per l'alimentazione e la rete, usare la procedura seguente.

1. Collegare i cavi di alimentazione a ciascun alimentatore nell’enclosure. Per assicurare disponibilità elevata, installare e collegare entrambi gli alimentatori a una fonte di alimentazione diversa.
2. Collegare i cavi di alimentazione alle unità PDU (Power Distribution Unit) del rack. Verificare che i due alimentatori usino fonti di alimentazione separate.
3. Collegare l'interfaccia di rete 1-GbE PORT 1 al computer usato per configurare il dispositivo fisico. PORT 1 è l'interfaccia di gestione dedicata.
4. Collegare l'interfaccia di rete 1-GbE PORT2 con i cavi di rete RJ-45 alla rete del data center/Internet. 
5. Collegare le quattro interfacce di rete 25-GbE PORT 3, PORT 4, PORT 5 e PORT 6 con i cavi di rame SFP+ alla rete del data center/Internet. 

> [!NOTE]
> - Almeno una delle interfacce di rete dati (PORT 2, 3, 4, 5 o 6) deve essere connessa a Internet (connettività in Azure). 
> - Si consiglia di usare un'interfaccia di rete 25-GbE come PORT 3, PORT 4, PORT 5 o PORT 6 per inviare dati ad Azure. 
> - Connettere il dispositivo Edge alla rete del data center in modo che possa inserire dati dai server di origine dati.  


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi a Data Box Edge, ad esempio:

> [!div class="checklist"]
> * Disimballare il dispositivo
> * Montare su rack il dispositivo
> * Cablare il dispositivo

Passare all'esercitazione successiva per informazioni su come connettere, configurare e attivare il dispositivo.

> [!div class="nextstepaction"]
> [Connettere e configurare Data Box Edge](./data-box-edge-deploy-connect-setup-activate.md)


