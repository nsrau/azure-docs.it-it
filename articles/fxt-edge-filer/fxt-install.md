---
title: Esercitazione sull'installazione di un dispositivo fisico Azure FXT Edge Filer | Microsoft Docs
description: Come disimballare, montare su rack e cablare il componente dispositivo fisico della cache di archiviazione ibrida Microsoft Azure FXT Edge Filer
services: ''
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 0f3c7b01ee9d4a62ec0d563af55f2086894081be
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256057"
---
# <a name="tutorial-install-azure-fxt-edge-filer"></a>Esercitazione: Installare Azure FXT Edge Filer 

Questa esercitazione descrive come installare un nodo hardware per la cache di archiviazione ibrida Azure FXT Edge Filer. È necessario installare almeno tre nodi hardware per creare un cluster Azure FXT Edge Filer.

La procedura di installazione prevede il disimballaggio e il montaggio su rack del dispositivo, nonché il collegamento del braccio di gestione cavi e l'applicazione della cornice anteriore. Un'esercitazione separata illustra le procedure per il collegamento dei cavi di rete e per l'alimentazione. 

È necessaria circa un'ora per installare un nodo Azure FXT Edge Filer. 

Questa esercitazione include i passaggi di installazione seguenti: 

> [!div class="checklist"]
> * Disimballare il dispositivo
> * Montare il dispositivo su un rack
> * Installare la cornice anteriore (facoltativo)

## <a name="installation-prerequisites"></a>Prerequisiti di installazione 

Prima di iniziare, assicurarsi che il data center e il rack che si intende usare abbiano le caratteristiche seguenti:

* Uno slot 1U disponibile sul rack in cui si intende montare il dispositivo.
* Alimentazione CA e sistemi di raffreddamento che soddisfano i requisiti di Azure FXT Edge Filer. Per informazioni sulla pianificazione e sul dimensionamento dell'installazione, leggere [Specifiche termiche e di alimentazione](fxt-specs.md#power-and-thermal-specifications).  

  > [!NOTE] 
  > Per sfruttare al meglio i due alimentatori ridondanti, usare unità PDU in due circuiti ramificati diversi quando si collega l'alimentazione elettrica. Per i dettagli, leggere [Collegare i cavi di alimentazione](fxt-network-power.md#connect-power-cables).  

## <a name="unpack-the-hardware-node"></a>Disimballare il nodo hardware 

Ogni nodo Azure FXT Edge Filer viene fornito in un imballaggio singolo. Completare la procedura seguente per disimballare un dispositivo.

1. Posizionare la scatola su una superficie piana e orizzontale.

2. Controllare le scatole e le protezioni in schiuma per verificare l'eventuale presenza di ammaccature, tagli, danni provocati dall'acqua o altri danni evidenti. Se la scatola o la confezione è danneggiata in modo grave, non aprirla. Contattare il supporto tecnico Microsoft per istruzioni su come verificare il corretto funzionamento del dispositivo.

3. Disimballare la scatola. Assicurarsi che includa gli elementi seguenti:
   * Un dispositivo FXT a chassis singolo
   * Due cavi di alimentazione
   * Una cornice anteriore e una chiave
   * Gruppo kit di guide
   * Un braccio di gestione cavi (CMA)
   * Un opuscolo di istruzioni per l'installazione del CMA
   * Un opuscolo di istruzioni per l'installazione del rack
   * Un opuscolo informativo su sicurezza, ambiente e normative

In caso di mancato ricevimento di uno o più degli elementi elencati, contattare il fornitore del dispositivo per chiedere assistenza. 

Assicurarsi che il dispositivo abbia avuto tempo sufficiente per raggiungere la stessa temperatura dell'ambiente in cui si trova prima di installarlo o di accenderlo. Se si nota la formazione di condensa in qualsiasi parte del dispositivo, attendere almeno 24 ore prima di effettuare l'installazione.

Il passaggio successivo consiste nel montare su rack il dispositivo.

## <a name="rack-the-device"></a>Montare su rack il dispositivo

Il dispositivo Azure FXT Edge Filer deve essere installato su un rack standard da 19 pollici. 

La cache di archiviazione ibrida Azure FXT Edge Filer è costituita da tre o più dispositivi Azure FXT Edge Filer. Ripetere i passaggi di installazione su rack per ogni dispositivo che fa parte del sistema. 

### <a name="rack-install-prerequisites"></a>Prerequisiti dell'installazione su rack

* Prima di iniziare, leggere le istruzioni per la sicurezza riportate nell'opuscolo informativo su sicurezza, ambiente e normative fornito con il dispositivo.

  > [!NOTE]
  > Fare in modo che siano sempre due persone a sollevare il nodo, anche quando viene installato o rimosso dal rack. 

* Identificare il tipo di installazione delle guide usato con il rack per apparecchiature. 
  * Per i rack a incastro con fori quadrati o rotondi, seguire le istruzioni per l'installazione delle guide senza utensili.
  * Per i rack con fori filettati, seguire le istruzioni per l'installazione delle guide con utensili. 
  
    Per la configurazione di montaggio delle guide con utensili, sono necessarie otto viti: 10-32, 12-24, M5 o M6. Il diametro della testa delle viti deve essere inferiore a 10 mm.

### <a name="identify-the-rail-kit-contents"></a>Identificare il contenuto del kit di guide

Individuare i componenti per l'installazione del gruppo kit di guide:

1. Due gruppi di guide scorrevoli A7 Dell ReadyRails II (1)
1. Due fascette a gancio e occhiello (2)

![Disegno numerato del contenuto del kit di guide](media/fxt-install/identify-rail-kit-contents-400.png)

### <a name="rail-assembly---tool-less-rails-square-hole-or-round-hole-racks"></a>Assemblaggio delle guide - Guide senza utensili (rack con fori quadrati o rotondi)

Per i rack con fori a incastro quadrati o rotondi, seguire questa procedura per assemblare e installare le guide. 

1. Posizionare le estremità delle guide sinistra e destra con etichetta **FRONT** in modo che siano rivolte verso l'interno. Posizionare ogni estremità in modo che si inserisca nei fori sulla parte anteriore delle flange verticali del rack. (1)

2. Allineare ogni estremità nei fori inferiori e superiori del rack nello spazio di montaggio. (2)

3. Agganciare l'estremità posteriore della guida finché non risulta completamente inserita nella flangia verticale del rack e il fermo non scatta in posizione. Ripetere questi passaggi per posizionare e inserire l'estremità anteriore nella flangia verticale del rack. (3)

> [!TIP]
> Per rimuovere le guide, tirare il pulsante di rilascio del fermo al centro dell'estremità (4) e disinserire ogni guida.

![Diagramma dell'installazione e della rimozione delle guide senza utensili, con i passaggi numerati](media/fxt-install/installing-removing-tool-less-rails-400.png)

### <a name="rail-assembly---tooled-rails-threaded-hole-racks"></a>Assemblaggio delle guide - Guide con utensili (rack con fori filettati)

Per i rack con fori filettati, seguire questa procedura per assemblare e installare le guide.

1. Rimuovere i perni dalle staffe di montaggio anteriore e posteriore usando un cacciavite a punta piatta. (1)
1. Tirare e ruotare i sottogruppi del fermo delle guide per rimuoverli dalle staffe di montaggio. (2)
1. Fissare le guide di montaggio di sinistra e di destra alle flange verticali anteriori del rack con due coppie di viti. (3)
1. Far scorrere le staffe posteriori di sinistra e di destra in avanti contro le flange verticali posteriori del rack e fissarle con due coppie di viti. (4)

![Diagramma dell'installazione e della rimozione di guide con utensili, con i passaggi numerati](media/fxt-install/installing-removing-tooled-rails-400.png)

### <a name="install-the-system-in-the-rack"></a>Installare il sistema nel rack

Seguire questa procedura per montare il dispositivo Azure FXT Edge Filer nel rack.

1. Estrarre le guide di scorrimento interne dal rack finché non si bloccano in posizione. (1)
1. Individuare i distanziali posteriori delle guide su ogni lato del dispositivo e abbassarli nelle scanalature posteriori dei gruppi di scorrimento. (2) 
1. Ruotare il dispositivo verso il basso finché tutti i distanziali delle guide non sono posizionati nelle scanalature. (3)
1. Spingere il dispositivo verso l'interno finché le leve di blocco non scattano in posizione.
1. Premere i pulsanti di blocco scorrimento su entrambe le guide (4) e far scorrere il dispositivo nel rack.

![Diagramma di installazione del sistema in un rack, con i passaggi numerati](media/fxt-install/installing-system-rack-400.png)

### <a name="remove-the-system-from-the-rack"></a>Rimuovere il sistema dal rack

Per rimuovere il dispositivo dal rack, seguire la procedura. 

1. Individuare le leve di blocco sui lati delle guide interne (1).
2. Sbloccare ogni leva ruotandola fino alla posizione di rilascio (2).
3. Afferrare saldamente i lati del sistema e tirarlo in avanti finché i distanziali delle guide non si trovano nella parte anteriore delle scanalature. Sollevare il sistema allontanandolo dal rack e posizionarlo su una superficie piana (3).

![Illustrazione della rimozione di un sistema dal rack, con i passaggi numerati](media/fxt-install/removing-system-rack-400.png)

### <a name="engage-the-slam-latch"></a>Agganciare il fermo a scatto

1. Ponendosi di fronte, individuare il fermo a scatto (1) su entrambi i lati del sistema.
2. I fermi si agganciano automaticamente mentre il sistema viene inserito nel rack. 

Per rilasciare i fermi quando si rimuove il sistema, tirarli verso l'alto (2).

Vengono fornite viti di fissaggio facoltative per fissare il sistema sul rack per la spedizione o per l'uso in altri ambienti instabili. Individuare le viti sotto ogni fermo e serrarle con un cacciavite a croce n. 2 (3).

![Illustrazione in cui viene mostrato come agganciare e rilasciare il fermo a scatto](media/fxt-install/engaging-releasing-slam-latch-400.png)

### <a name="install-the-cable-management-arm"></a>Installare il braccio di gestione cavi 

Con FXT Edge Filer viene fornito un braccio di gestione cavi (CMA) facoltativo. Nel pacchetto sono disponibili istruzioni stampate per l'installazione. 

1. Disimballare e identificare i componenti del kit braccio di gestione cavi:
   * Cassetto del CMA (1)
   * CMA (2)
   * Fermacavi in nylon (3)
   * Staffe di montaggio del CMA (4)
   * Cavo indicatore di stato (5) 

   > [!TIP] 
   > Per il fissaggio del CMA per la spedizione nel rack, avvolgere i fermacavi intorno alle staffe e al cassetto e stringerli saldamente. Bloccando il CMA in questo modo viene fissato anche il sistema in ambienti instabili.

   ![Illustrazione delle parti del CMA](media/fxt-install/cma-kit-400.png)

2. Installare il cassetto del CMA.

   Il cassetto del CMA fornisce un supporto e agisce come blocco del CMA. 

   1. Allineare e agganciare ogni lato del cassetto alle staffe corrispondenti sui bordi interni delle guide. 
   1. Spingere il cassetto finché non scatta in posizione. (1)
   1. Per rimuovere il cassetto, stringere i pulsanti di rilascio del fermo verso il centro ed estrarre il cassetto dalla staffe corrispondenti (2).

   ![Illustrazione dell'installazione del cassetto del CMA](media/fxt-install/cma-tray-install-400.png)

3. Installare le staffe di montaggio del CMA. 

   > [!NOTE]
   >
   > * È possibile fissare il CMA alla guida di montaggio destra o sinistra, a seconda di come si vuole indirizzare i cavi nel sistema. 
   > * Per praticità, montare il CMA sul lato opposto rispetto agli alimentatori (lato A). Se viene montato sul lato B, sarà necessario scollegare il CMA per rimuovere l'alimentatore esterno. 
   > * Rimuovere sempre il cassetto prima di rimuovere gli alimentatori. 

   ![Illustrazione dell'installazione delle staffe del CMA](media/fxt-install/cma-bracket-l-r-install-400.png)

   1. Scegliere la staffa di montaggio del CMA appropriata per il lato in cui si vuole montare il CMA (lato B o lato A).
   1. Installare la staffa di montaggio del CMA con il contrassegno del lato A o del lato B corrispondente sul retro della guida di scorrimento.
   1. Allineare i fori sulla staffa con i perni sulla guida di scorrimento. Spingere la staffa verso il basso finché non si blocca in posizione. 

4. Installare il CMA.

   1. Sul retro del sistema inserire il fermo nella parte anteriore del CMA sulla staffa più interna del gruppo di scorrimento finché non si aggancia (1). 
   1. Inserire l'altro fermo nell'estremità della staffa più esterna finché non si aggancia (2). 
   1. Per rimuovere il CMA, sganciare entrambi i fermi premendo i pulsanti di rilascio del CMA nella parte superiore degli alloggiamenti dei fermi interno ed esterno (3).

   ![Illustrazione dell'installazione del CMA principale](media/fxt-install/cma-install-400.png)

   Il CMA può essere ruotato e allontanato dal sistema per l'accesso e l'assistenza. Dall'estremità fissata mediante cardini sollevare il CMA allontanandolo dal cassetto in modo da rimuoverlo (1). Una volta disinserito dal cassetto, far oscillare il CMA allontanandolo dal sistema (2).

   ![Illustrazione dell'apertura del CMA tramite rotazione per l'assistenza](media/fxt-install/cma-swing-over-tray-400.png)

## <a name="install-the-front-bezel-optional"></a>Installare la cornice anteriore (facoltativo)

Questa sezione spiega come installare e rimuovere la cornice anteriore (mascherina) dell'hardware Azure FXT Edge Filer. 

Per installare la cornice anteriore: 

1. Individuare e rimuovere la chiave della cornice, che viene fornita nel relativo pacchetto. 
1. Allineare la cornice con la parte anteriore dello chassis e inserire i perni del lato destro della cornice nei fori sulla flangia di montaggio del rack sul lato destro del nodo. 
1. Inserire l'estremità sinistra della cornice nello chassis. Premere la cornice finché il pulsante sul lato sinistro non scatta in posizione.
1. Bloccare la cornice con la chiave.

Per rimuovere la cornice anteriore: 
1. Sbloccare la cornice usando la chiave apposita.
1. Premere il pulsante di rilascio sul lato sinistro e tirare l'estremità sinistra della cornice allontanandola dallo chassis.
1. Sganciare l'estremità destra e rimuovere la cornice.
   
   ![Immagine che mostra il pulsante di rilascio sul lato sinistro della cornice e illustra come rimuovere la cornice spingendola verso l'esterno dal lato sinistro](media/fxt-install/remove-bezel-edited-600.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver disimballato e montato il dispositivo sul rack, continuare l'installazione collegando i cavi di rete e la rete elettrica ad Azure FXT Edge Filer.

> [!div class="nextstepaction"]
> [Cablare le porte di rete e collegare l'alimentazione elettrica](fxt-network-power.md)