---
title: Eseguire il failover e il failback di macchine virtuali di Azure replicate in un'area di Azure secondaria con Azure Site Recovery (anteprima)
description: Informazioni su come eseguire il failover e il failback di macchine virtuali di Azure replicate in un'area di Azure secondaria con Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/08/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: dc7ead9e7d55d1b22118774e98c741991e8af2d9
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="fail-over-and-fail-back-azure-vms-between-azure-regions-preview"></a>Eseguire il failover e il failback di macchine virtuali di Azure tra aree di Azure (anteprima)

Il servizio [Azure Site Recovery](site-recovery-overview.md) favorisce l'attuazione della strategia di ripristino di emergenza gestendo e coordinando le operazioni di replica, failover e failback di computer locali e macchine virtuali di Azure.

Questa esercitazione descrive come eseguire il failover di una singola macchina virtuale di Azure in un'area di Azure secondaria. Dopo aver eseguito il failover, si esegue il failback nell'area primaria, non appena torna disponibile. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Eseguire il failover della macchina virtuale di Azure
> * Riproteggere la macchina virtuale secondaria di Azure in modo che possa essere replicata nell'area primaria
> * Eseguire il failback della macchina virtuale secondaria
> * Riprotteggere la macchina virtuale primaria anche nell'area secondaria

## <a name="prerequisites"></a>prerequisiti

- Assicurarsi di aver completato un'[analisi di ripristino di emergenza](azure-to-azure-tutorial-dr-drill.md) per verificare che tutto funzioni come previsto.
- Verificare le proprietà della macchina virtuale prima di eseguire il failover di test. La macchina virtuale deve essere conforme ai [requisiti di Azure](azure-to-azure-support-matrix.md#support-for-replicated-machine-os-versions).

## <a name="run-a-failover-to-the-secondary-region"></a>Eseguire un failover nell'area secondaria

1. In **Elementi replicati** selezionare la macchina virtuale di cui si vuole eseguire il failover e scegliere **Failover**

   ![Failover](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. In **Failover** selezionare un **Punto di ripristino** in cui eseguire il failover. È possibile usare una delle opzioni seguenti.

   * **Più recente** (impostazione predefinita): con questa opzione vengono elaborati tutti i dati presenti nel servizio Site Recovery e viene fornito il valore RPO (Recovery Point Objective) più basso.
   * **Latest processed** (Punto di ripristino elaborato più recente): con questa opzione la macchina virtuale viene ripristinata all'ultimo punto di ripristino elaborato dal servizio Site Recovery.
   * **Personalizzato**: questa opzione consente di eseguire il failover in un determinato punto di ripristino ed è particolarmente utile per eseguire un failover di test.

3. Selezionare **Arrestare la macchina prima di iniziare il failover** se si vuole provare ad arrestare le macchine virtuali di origine tramite Site Recovery prima di attivare il failover. Il failover continua anche se l'arresto ha esito negativo.

4. Nella pagina **Processi** è possibile seguire lo stato di avanzamento del failover.

5. Dopo il failover, convalidare la macchina virtuale eseguendo l'accesso ad essa. Per passare a un altro punto di ripristino per la macchina virtuale, è possibile usare l'opzione **Modifica punto di ripristino**.

6. Quando la macchina virtuale sottoposta a failover è pronta, è possibile eseguire il **commit** del failover.
   Questa operazione elimina tutti i punti di ripristino disponibili con il servizio. L'opzione **Modifica punto di ripristino** non è più disponibile.

## <a name="reprotect-the-secondary-vm"></a>Riproteggere la macchina virtuale secondaria

Dopo aver eseguito il failover della macchina virtuale, è necessario proteggerla in modo che possa essere nuovamente replicata nell'area primaria.

1. Assicurarsi che lo stato della macchina virtuale sia **Commit del failover eseguito** e verificare che l'area primaria sia disponibile e che sia possibile creare e accedere alle nuove risorse presenti al suo interno.
2. In **Insieme di credenziali** > **Elementi replicati**, fare clic con il pulsante destro del mouse sulla macchina virtuale di cui è stato eseguito il failover e quindi scegliere **Riproteggi**.

   ![Fare clic con il pulsante destro del mouse per riproteggere](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. Osservare come la direzione di protezione, dall'area secondaria a quella primaria, sia già selezionata.
3. Rivedere le informazioni contenute in **Gruppo di risorse, Rete, Archiviazione e Set di disponibilità**. Eventuali risorse contrassegnate (nuove) verranno create nell'ambito dell'operazione di riprotezione.
4. Fare clic su **OK** per avviare un processo di riprotezione. Con questo processo il sito di destinazione viene aggiornato con i dati più recenti e i valori delta vengono replicati nell'area primaria. La macchina virtuale si trova ora in uno stato protetto.

## <a name="fail-back-to-the-primary-region"></a>Eseguire il failback nell'area primaria

Dopo aver riprotetto le macchine virtuali, è possibile eseguirne il failback nell'area primaria, se necessario. A questo scopo, seguire le istruzioni di [failover](#run-a-failover).
