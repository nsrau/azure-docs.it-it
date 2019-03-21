---
title: Esercitazione sull'installazione di un dispositivo fisico Azure Data Box Edge | Microsoft Docs
description: La seconda esercitazione sull'installazione di Azure Data Box Edge include le informazioni su come disimballare, montare su rack e collegare il dispositivo fisico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 11/01/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Data Box Edge in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: ddcaca46a2b8f9501337b3591d6ed666876e1de9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58093770"
---
# <a name="tutorial-install-azure-data-box-edge-preview"></a>Esercitazione: Installare Azure Data Box Edge (anteprima)

Questa esercitazione descrive come installare un dispositivo fisico Data Box Edge. La procedura di installazione comporta il disimballaggio, il montaggio su rack e il collegamento del dispositivo. 

Il completamento dell'installazione può richiedere circa due ore.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Disimballare il dispositivo
> * Montare su rack il dispositivo
> * Cablare il dispositivo

> [!IMPORTANT]
> La soluzione Data Box Edge è disponibile in anteprima. Prima di ordinare e distribuire la soluzione, vedere [Condizioni di Azure per i servizi in anteprima](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

I prerequisiti per installare un dispositivo fisico sono i seguenti:

### <a name="for-the-data-box-edge-resource"></a>Per la risorsa Data Box Edge

Prima di iniziare, verificare che:

* Siano stati completati tutti i passaggi descritti in [Preparare la distribuzione di Azure Data Box Edge (anteprima)](data-box-edge-deploy-prep.md).
    * Sia stata creata una risorsa Data Box Edge per distribuire il dispositivo.
    * È stata generata la chiave di attivazione per attivare il dispositivo con la risorsa Data Box Edge.

 
### <a name="for-the-data-box-edge-physical-device"></a>Per il dispositivo fisico Data Box Edge

Prima di distribuire un dispositivo:

- Assicurarsi che il dispositivo sia collocato in sicurezza su una superficie di lavoro orizzontale, stabile e piana.
- Verificare che il sito in cui si intende installare abbia:
    - Alimentazione CA standard da una fonte di energia indipendente

        -OPPURE-
    - Un'unità PDU installabile su rack con un gruppo di continuità (UPS)
    - Uno slot 1U disponibile sul rack in cui si intende montare il dispositivo

### <a name="for-the-network-in-the-datacenter"></a>Per la rete nel datacenter

Prima di iniziare:

- Verificare i requisiti di rete per distribuire Data Box Edge e configurare la rete del data center in base ai requisiti. Per altre informazioni, vedere i [requisiti di rete di Data Box Edge](data-box-edge-system-requirements.md#networking-port-requirements).

- Assicurarsi che la larghezza di banda Internet sia di almeno 20 Mbps per garantire il funzionamento ottimale del dispositivo.


## <a name="unpack-the-device"></a>Disimballare il dispositivo

Questo dispositivo viene fornito in una singola confezione. Completare la procedura seguente per disimballare il dispositivo. 

1. Posizionare la scatola su una superficie piana e orizzontale.
2. Controllare le scatole e le protezioni in schiuma per verificare l'eventuale presenza di ammaccature, tagli, danni provocati dall'acqua o altri danni evidenti. Se la scatola o la confezione è danneggiata in modo grave, non aprirla. Contattare il supporto tecnico Microsoft per istruzioni su come verificare il corretto funzionamento del dispositivo.
3. Disimballare la scatola. Dopo aver disimballato le due scatole, assicurarsi di avere a disposizione:
    - Un dispositivo Edge a singolo enclosure
    - Due cavi di alimentazione
    - Un kit per il montaggio in rack con guide senza utensili (due guide laterali e componenti di montaggio inclusi)

In caso di mancato ricevimento di uno o più degli elementi elencati sopra, contattare il supporto tecnico di Data Box Edge. Il passaggio successivo consiste nel montare su rack il dispositivo.


## <a name="rack-the-device"></a>Montare su rack il dispositivo

Il dispositivo deve essere installato su un rack standard da 19 pollici. Usare la procedura seguente per montare il dispositivo su un rack standard da 19 pollici con montanti anteriori e posteriori.

> [!IMPORTANT]
> I dispositivi Data Box Edge devono essere montati su rack per il corretto funzionamento.


1. Tirare la leva di blocco anteriore per sbloccare la guida interna dal gruppo di slitte. Sbloccare il fermo e spingere la guida intermedia verso l'interno per tirare indietro la guida.  
    A questo punto, le guide interna ed esterna dovrebbero separarsi.

    ![Installare le guide di montaggio su rack](./media/data-box-edge-deploy-install/rack-mount-rail-1.png)

2. Installare le guide esterne sugli elementi verticali dell'armadio rack. Per un migliore orientamento, le guide a scorrimento sono contrassegnate con la dicitura **Front** e questa estremità deve essere a contatto con la parte anteriore dello chassis.    
   1. Individuare i perni nella parte anteriore e posteriore del gruppo guida. Estendere la guida per posizionarla tra i montanti del rack. Prima di tutto, fissare la guida esterna sul retro del rack. Regolare la staffa di montaggio posteriore in modo da posizionarla all'interno dei fori di montaggio posteriori del rack.   

   2. Spingere e tenere premuta la leva sulla staffa posteriore per esporre i ganci metallici. Allineare e inserire la staffa posteriore nei fori di montaggio e quindi rilasciare la leva.

   3. Allineare la staffa anteriore con il foro di montaggio.

   4. La staffa anteriore sarà ora fissata al rack. Se necessario, è possibile usare viti M5 X 10L per fissare le guide ai montanti. 

      ![Installare le guide di montaggio su rack](./media/data-box-edge-deploy-install/rack-mount-rail-2.png)

3. Per fissare la guida interna allo chassis, verificare che i fori di apertura sulla guida interna siano allineati ai perni di posizionamento sul lato dello chassis. Assicurarsi che le teste dei perni di posizionamento dello chassis sporgano attraverso i fori a forma di buco della serratura nella guida interna. Tirare la guida verso la parte anteriore dello chassis finché non scatta in posizione con un clic. Ripetere l'operazione con l'altra guida interna. Spingere lo chassis con la guida interna nella slitta per completare l'installazione su rack.

    ![Installare le guide di montaggio su rack](./media/data-box-edge-deploy-install/rack-mount-rail-3.png)

## <a name="cable-the-device"></a>Cablare il dispositivo

Le procedure seguenti illustrano come cablare il dispositivo Edge per l'alimentazione e la connessione di rete.

Prima di iniziare a collegare il dispositivo, sono necessari gli elementi seguenti:

- Il dispositivo fisico Edge, disimballato e montato in rack
- Due cavi di alimentazione 
- Almeno un cavo di rete 1-GbE RJ-45 per il collegamento all'interfaccia di gestione. Nel dispositivo sono presenti due interfacce di rete 1-GbE, una per la gestione e una per i dati.
- Un cavo di rame 25-GbE SFP+ per ogni interfaccia di rete dati da configurare. Almeno una delle interfacce di rete dati tra PORT 2, PORT 3, PORT 4, PORT 5 e PORT 6 deve essere connessa a Internet (con connettività ad Azure).  
- Accesso a due unità PDU (consigliato).

> [!NOTE]
> - Se si collega solo un'interfaccia di rete dati, si consiglia di usare un'interfaccia di rete 25-GbE come PORT 3, PORT 4, PORT 5 o PORT 6 per inviare dati ad Azure. 
> - Per ottenere prestazioni ottimali e per gestire volumi elevati di dati, prendere in considerazione il collegamento di tutte le porte dati.
> - Connettere il dispositivo Edge alla rete del data center in modo che possa inserire dati dai server di origine dati. 

Il dispositivo Edge ha 8 unità SSD NVMe. Il pannello anteriore è anche dotato di LED di stato e pulsanti di alimentazione. Il dispositivo include alimentatori ridondanti sulla parte posteriore. Il dispositivo è dotato di sei interfacce di rete: due interfacce da 1 Gbps e quattro da 25 Gbps. Il dispositivo è dotato di un controller BMC (Baseboard Management Controller). Identificare le varie porte nel backplane del dispositivo.
 
  ![Backplane del dispositivo cablato](./media/data-box-edge-deploy-install/backplane-cabled.png)
 
Per collegare il dispositivo per l'alimentazione e la rete, completare i passaggi seguenti.

1. Collegare i cavi di alimentazione a ciascun alimentatore nell’enclosure. Per assicurare disponibilità elevata, installare e collegare entrambi gli alimentatori a una fonte di alimentazione diversa.

2. Collegare i cavi di alimentazione alle unità PDU (Power Distribution Unit) del rack. Verificare che i due alimentatori usino fonti di alimentazione separate.

3. Collegare l'interfaccia di rete PORT 1 a 1 GbE al computer usato per configurare il dispositivo fisico. PORT 1 è l'interfaccia di gestione dedicata.

4. Collegare una o più delle interfacce di rete PORT 2, PORT 3, PORT 4, PORT 5 o PORT 6 alla rete del data center/Internet. Se si collega l'interfaccia di rete PORT 2, usare il cavo di rete RJ-45. Per le interfacce di rete 25-GbE, usare i cavi di rame SFP+.  


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state fornite informazioni su Data Box Edge, tra cui su come:

> [!div class="checklist"]
> * Disimballare il dispositivo
> * Montare su rack il dispositivo
> * Cablare il dispositivo

Passare all'esercitazione successiva per informazioni su come connettere, configurare e attivare il dispositivo.

> [!div class="nextstepaction"]
> [Connettere e configurare Data Box Edge](./data-box-edge-deploy-connect-setup-activate.md)


