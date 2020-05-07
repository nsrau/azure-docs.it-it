---
title: Esercitazione sull'installazione - Disimballare, installare in rack e collegare il dispositivo fisico Azure Stack Edge | Microsoft Docs
description: La seconda esercitazione sull'installazione di Azure Stack Edge include le informazioni su come disimballare, montare su rack e collegare il dispositivo fisico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/17/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Azure Stack Edge in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: 429fe0c4db4a7825a6a98aa5d2cd6af609a34a61
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82564989"
---
# <a name="tutorial-install-azure-stack-edge"></a>Esercitazione: Installare Azure Stack Edge

Questa esercitazione descrive come installare un dispositivo fisico Azure Stack Edge. La procedura di installazione comporta il disimballaggio, il montaggio su rack e il collegamento del dispositivo. 

Il completamento dell'installazione può richiedere circa due ore.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Disimballare il dispositivo
> * Montare su rack il dispositivo
> * Cablare il dispositivo

## <a name="prerequisites"></a>Prerequisiti

I prerequisiti per installare un dispositivo fisico sono i seguenti:

### <a name="for-the-azure-stack-edge-resource"></a>Per la risorsa Azure Stack Edge

Prima di iniziare, verificare che:

* Siano stati completati tutti i passaggi descritti in [Preparare la distribuzione di Azure Stack Edge](azure-stack-edge-deploy-prep.md).
    * Sia stata creata una risorsa Azure Stack Edge per distribuire il dispositivo.
    * È stata generata la chiave di attivazione per attivare il dispositivo con la risorsa Azure Stack Edge.

 
### <a name="for-the-azure-stack-edge-physical-device"></a>Per il dispositivo fisico Azure Stack Edge

Prima di distribuire un dispositivo:

- Assicurarsi che il dispositivo sia collocato in sicurezza su una superficie di lavoro orizzontale, stabile e piana.
- Verificare che il sito in cui si intende installare abbia:
    - Alimentazione CA standard da una fonte di energia indipendente

        -OPPURE-
    - Un'unità PDU installabile su rack con un gruppo di continuità (UPS)
    - Uno slot 1U disponibile sul rack in cui si intende montare il dispositivo

### <a name="for-the-network-in-the-datacenter"></a>Per la rete nel datacenter

Prima di iniziare:

- Verificare i requisiti di rete per distribuire Azure Stack Edge e configurare la rete del data center in base ai requisiti. Per altre informazioni, vedere i [requisiti di rete di Azure Stack Edge](azure-stack-edge-system-requirements.md#networking-port-requirements).

- Assicurarsi che la larghezza di banda Internet sia di almeno 20 Mbps per garantire il funzionamento ottimale del dispositivo.


## <a name="unpack-the-device"></a>Disimballare il dispositivo

Questo dispositivo viene fornito in una singola confezione. Completare la procedura seguente per disimballare il dispositivo. 

1. Posizionare la scatola su una superficie piana e orizzontale.
2. Controllare le scatole e le protezioni in schiuma per verificare l'eventuale presenza di ammaccature, tagli, danni provocati dall'acqua o altri danni evidenti. Se la scatola o la confezione è danneggiata in modo grave, non aprirla. Contattare il supporto tecnico Microsoft per istruzioni su come verificare il corretto funzionamento del dispositivo.
3. Disimballare la scatola. Dopo aver disimballato le due scatole, assicurarsi di avere a disposizione:
    - Un dispositivo Azure Stack Edge a singolo enclosure
    - Due cavi di alimentazione
    - Gruppo kit di guide
    - Opuscolo informativo su sicurezza, ambiente e normative

In caso di mancato ricevimento di uno o più degli elementi elencati sopra, contattare il supporto tecnico di Azure Stack Edge. Il passaggio successivo consiste nel montare su rack il dispositivo.


## <a name="rack-the-device"></a>Montare su rack il dispositivo

Il dispositivo deve essere installato su un rack standard da 19 pollici. Usare la procedura seguente per montare il dispositivo su un rack standard da 19 pollici.

> [!IMPORTANT]
> I dispositivi Azure Stack Edge devono essere montati su rack per il corretto funzionamento.


### <a name="prerequisites"></a>Prerequisiti

- Prima di iniziare, leggere le istruzioni per la sicurezza riportate nell'opuscolo informativo su sicurezza, ambiente e normative spedito con il dispositivo.
- Iniziare a installare le guide nello spazio assegnato più vicino alla parte inferiore dello scaffale rack.
- Per la configurazione di montaggio delle guide con utensili:
    -  Sono necessarie otto viti: 10-32, 12-24, M5 o M6. Il diametro della testa delle viti deve essere inferiore a 10 mm.
    -  Occorre un cacciavite a punta piatta.

### <a name="identify-the-rail-kit-contents"></a>Identificare il contenuto del kit di guide

Individuare i componenti per l'installazione del gruppo kit di guide:
1. Due gruppi di guide scorrevoli A7 Dell ReadyRails II
2. Due fascette a gancio e occhiello

    ![Identificare il contenuto dei kit di guide](./media/azure-stack-edge-deploy-install/identify-rail-kit-contents.png)

### <a name="install-and-remove-tool-less-rails-square-hole-or-round-hole-racks"></a>Installare e rimuovere le guide senza utensili (rack a fori quadrati o rotondi)

> [!TIP]
> Questa opzione non richiede utensili per installare e rimuovere le guide nei fori quadrati o rotondi non filettati nei rack.

1. Posizionare i terminali delle guide di sinistra e di destra con la dicitura **FRONT** rivolti verso l'interno e orientati in modo che ognuno si inserisca nei fori sul lato anteriore delle flange verticali del rack.
2. Allineare ogni terminale ai fori superiore e inferiore degli spazi unità desiderati.
3. Agganciare l'estremità posteriore della guida finché non risulta completamente inserita nella flangia verticale del rack e il fermo non scatta in posizione. Ripetere questi passaggi per posizionare e inserire l'estremità anteriore nella flangia verticale del rack.
4. Per rimuovere le guide, tirare il pulsante di rilascio del fermo al centro dell'estremità e disinserire ogni guida.

    ![Installare e rimuovere le guide senza utensili](./media/azure-stack-edge-deploy-install/installing-removing-tool-less-rails.png)

### <a name="install-and-remove-tooled-rails-threaded-hole-racks"></a>Installare e rimuovere le guide con utensili (rack con fori filettati)

> [!TIP]
> Questa opzione richiede un utensile (_un cacciavite a punta piatta_) per installare e rimuovere le guide nei fori rotondi filettati nei rack.

1. Rimuovere i perni dalle staffe di montaggio anteriore e posteriore usando un cacciavite a punta piatta.
2. Tirare e ruotare i sottogruppi del fermo delle guide per rimuoverli dalle staffe di montaggio.
3. Fissare le guide di montaggio di sinistra e di destra alle flange verticali anteriori del rack con due coppie di viti.
4. Far scorrere le staffe posteriori di sinistra e di destra in avanti contro le flange verticali posteriori del rack e fissarle con due coppie di viti.

    ![Installare e rimuovere le guide con utensili](./media/azure-stack-edge-deploy-install/installing-removing-tooled-rails.png)

### <a name="install-the-system-in-a-rack"></a>Installare il sistema in un rack

1. Estrarre le guide di scorrimento interne dal rack finché non si bloccano in posizione.
2. Individuare i distanziali posteriori delle guide su ogni lato del sistema e abbassarli nelle scanalature posteriori dei gruppi di scorrimento. Ruotare il sistema verso il basso finché tutti i distanziali delle guide non sono posizionati nelle scanalature.
3. Spingere il sistema verso l'interno finché le leve di blocco non scattano in posizione.
4. Premere i pulsanti di blocco scorrimento su entrambe le guide e far scorrere il sistema nel rack.

    ![Installare il sistema in un rack](./media/azure-stack-edge-deploy-install/installing-system-rack.png)

### <a name="remove-the-system-from-the-rack"></a>Rimuovere il sistema dal rack

1. Individuare le leve di blocco sui lati delle guide interne.
2. Sbloccare ogni leva ruotandola fino alla posizione di rilascio.
3. Afferrare saldamente i lati del sistema e tirarlo in avanti finché i distanziali delle guide non si trovano nella parte anteriore delle scanalature. Sollevare il sistema all'esterno del rack e posizionarlo su una superficie piana.

    ![Rimuovere il sistema dal rack](./media/azure-stack-edge-deploy-install/removing-system-rack.png)

### <a name="engage-and-release-the-slam-latch"></a>Agganciare e rilasciare il fermo a scatto

> [!NOTE]
> per i sistemi non dotati di fermo a scatto, fissare il sistema con viti come descritto nel passaggio 3 di questa procedura.

1. Ponendosi di fronte, individuare il fermo a scatto su entrambi i lati del sistema.
2. I fermi si agganciano automaticamente mentre il sistema viene inserito nel rack e vengono rilasciati tirandoli verso l'alto.
3. Per fissare il sistema per la spedizione nel rack o per altri ambienti instabili, individuare la vite di fissaggio sotto ogni fermo e serrarla con un cacciavite a croce n. 2.

    ![Agganciare e rilasciare il fermo a scatto](./media/azure-stack-edge-deploy-install/engaging-releasing-slam-latch.png)


## <a name="cable-the-device"></a>Cablare il dispositivo

Sistemare i cavi e quindi cablare il dispositivo. Le procedure seguenti illustrano come cablare il dispositivo Azure Stack Edge per l'alimentazione e la rete.

Prima di iniziare a collegare il dispositivo, sono necessari gli elementi seguenti:

- Dispositivo fisico Azure Stack Edge, disimballato e montato in rack.
- Due cavi di alimentazione
- Almeno un cavo di rete 1-GbE RJ-45 per il collegamento all'interfaccia di gestione. Nel dispositivo sono presenti due interfacce di rete 1-GbE, una per la gestione e una per i dati.
- Un cavo di rame 25-GbE SFP+ per ogni interfaccia di rete dati da configurare. Almeno una delle interfacce di rete dati tra PORT 2, PORT 3, PORT 4, PORT 5 e PORT 6 deve essere connessa a Internet (con connettività ad Azure).  
- Accesso a due unità PDU (consigliato).

> [!NOTE]
> - Se si connette una sola interfaccia di rete dati, per inviare dati ad Azure è consigliabile usare un'interfaccia di rete 25/10-GbE come PORT 3, PORT 4, PORT 5 o PORT 6. 
> - Per ottenere prestazioni ottimali e per gestire volumi elevati di dati, prendere in considerazione il collegamento di tutte le porte dati.
> - Per poter inserire dati dai server di origine dati, il dispositivo Azure Stack Edge deve essere connesso alla rete del data center.

Nel Dispositivo Azure Stack Edge:

- Il pannello anteriore include unità disco e un pulsante di alimentazione.

    - Nella parte anteriore del dispositivo sono presenti 10 slot disco.
    - Lo slot 0 contiene un'unità SATA da 240 GB usata come disco del sistema operativo. Lo slot 1 è vuoto e gli slot da 2 a 9 sono unità SSD NVMe usate come dischi dati.
- Il backplane include alimentatori ridondanti.
- Il backplane ha sei interfacce di rete:

    - Due interfacce da 1 Gbps.
    - Quattro interfacce da 25 Gbps che possono anche essere usate come interfacce da 10 Gbps.
    - Un controller BMC (Baseboard Management Controller).

- Il backplane ha due schede di rete corrispondenti alle 6 porte:

    - QLogic FastLinQ 41264
    - QLogic FastLinQ 41262

Per un elenco completo dei cavi, dei commutatori e dei ricetrasmettitori supportati per tali schede di rete, vedere [Cavium FastlinQ 41000 Series Interoperability Matrix](https://www.marvell.com/documents/xalflardzafh32cfvi0z/) (Matrice di interoperabilità di Cavium FastlinQ 41000 Series).
 
Per collegare il dispositivo per l'alimentazione e la rete, completare i passaggi seguenti.

1. Identificare le varie porte sul backplane del dispositivo.

    ![Backplane di un dispositivo cablato](./media/azure-stack-edge-deploy-install/backplane-cabled.png)

2. Individuare gli slot disco e il pulsante di accensione sul pannello anteriore del dispositivo.

    ![Pannello anteriore di un dispositivo](./media/azure-stack-edge-deploy-install/device-front-plane-labeled-1.png)

3. Collegare i cavi di alimentazione a ciascun alimentatore nell’enclosure. Per assicurare disponibilità elevata, installare e collegare entrambi gli alimentatori a una fonte di alimentazione diversa.
4. Collegare i cavi di alimentazione alle unità PDU (Power Distribution Unit) del rack. Verificare che i due alimentatori usino fonti di alimentazione separate.
5. Premere il pulsante di alimentazione per accendere il dispositivo.
6. Collegare l'interfaccia di rete PORT 1 a 1 GbE al computer usato per configurare il dispositivo fisico. PORT 1 è l'interfaccia di gestione dedicata.
7. Collegare una o più delle interfacce di rete PORT 2, PORT 3, PORT 4, PORT 5 o PORT 6 alla rete del data center/Internet.

    - Se si collega l'interfaccia di rete PORT 2, usare il cavo di rete RJ-45.
    - Per le interfacce di rete 10/25-GbE, usare i cavi di rame SFP+.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi ad Azure Stack Edge, ad esempio:

> [!div class="checklist"]
> * Disimballare il dispositivo
> * Montare su rack il dispositivo
> * Cablare il dispositivo

Passare all'esercitazione successiva per informazioni su come connettere, configurare e attivare il dispositivo.

> [!div class="nextstepaction"]
> [Connettere e configurare Azure Stack Edge](./azure-stack-edge-deploy-connect-setup-activate.md)
