---
title: Eseguire il failover e il failback di macchine virtuali IaaS di Azure replicate in un'area di Azure secondaria per il ripristino di emergenza con il servizio Azure Site Recovery.
description: Informazioni su come eseguire il failover e il failback di macchine virtuali di Azure replicate in un'area di Azure secondaria per il ripristino di emergenza con il servizio Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 46dae28fd6c9eaa3d5e03f5f06c5e92449653679
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737723"
---
# <a name="fail-over-and-fail-back-azure-vms-between-azure-regions"></a>Eseguire il failover e il failback delle macchine virtuali di Azure tra aree di Azure

Il servizio [Azure Site Recovery](site-recovery-overview.md) favorisce l'attuazione della strategia di ripristino di emergenza gestendo e coordinando le operazioni di replica, failover e failback di computer locali e macchine virtuali di Azure.

Questa esercitazione descrive come eseguire il failover di una singola macchina virtuale di Azure in un'area di Azure secondaria. Dopo aver eseguito il failover, si esegue il failback nell'area primaria, non appena torna disponibile. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Eseguire il failover della macchina virtuale di Azure
> * Riproteggere la macchina virtuale secondaria di Azure in modo che possa essere replicata nell'area primaria
> * Eseguire il failback della macchina virtuale secondaria
> * Riprotteggere la macchina virtuale primaria anche nell'area secondaria

> [!NOTE]
> In questa esercitazione l'utente viene guidato nella procedura di failover in un'area di destinazione, seguita dal failback, con una personalizzazione minima. Se si desidera approfondire i vari aspetti associati al failover, incluse considerazioni sulla rete, automazione o risoluzione dei problemi, vedere i documenti relativi alle procedure per le macchine virtuali di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Assicurarsi di avere completato un'[analisi di ripristino di emergenza](azure-to-azure-tutorial-dr-drill.md) per verificare che tutto funzioni come previsto.
- Verificare le proprietà della macchina virtuale prima di eseguire il failover di test. La macchina virtuale deve essere conforme ai [requisiti di Azure](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).

## <a name="run-a-failover-to-the-secondary-region"></a>Eseguire un failover nell'area secondaria

1. In **Elementi replicati** selezionare la macchina virtuale di cui si vuole eseguire il failover e scegliere **Failover**

   ![Failover](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. In **Failover** selezionare un **Punto di ripristino** in cui eseguire il failover. È possibile usare una delle opzioni seguenti.

   * **Più recente** (impostazione predefinita): con questa opzione vengono elaborati tutti i dati presenti nel servizio Site Recovery e viene fornito il valore RPO (Recovery Point Objective) più basso.
   * **Elaborato più recente**: con questa opzione la macchina virtuale viene ripristinata all'ultimo punto di ripristino elaborato dal servizio Site Recovery.
   * **Personalizzato**: questa opzione consente di eseguire il failover in un determinato punto di ripristino ed è particolarmente utile per eseguire un failover di test.

3. Selezionare **Arrestare la macchina prima di iniziare il failover** se si vuole provare ad arrestare le macchine virtuali di origine tramite Site Recovery prima di attivare il failover. Il failover continua anche se l'arresto ha esito negativo. Si noti che Site Recovery non esegue la pulizia dell'origine dopo il failover.

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

> [!NOTE]
> Vedere la [sezione procedurale](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection) per altri dettagli sul flusso di lavoro di riprotezione e cosa accade durante la riprotezione.


## <a name="fail-back-to-the-primary-region"></a>Eseguire il failback nell'area primaria

Dopo la riprotezione delle macchine virtuali, è possibile eseguire il failback all'area primaria in base alle esigenze. A questo scopo, configurare un failover dall'area secondaria all'area primaria, come descritto in questo articolo.

![Fare clic con il pulsante destro del mouse per riproteggere](./media/azure-to-azure-tutorial-failover-failback/failback.png)

Se viene visualizzata la schermata precedente, è stato eseguito il failover della macchina virtuale "ContosoWin2016" da Stati Uniti centrali a Stati Uniti orientali e il failback da Stati Uniti orientali a Stati Uniti centrali.

Il failover arresta la VM nell'area secondaria, ovvero l'area di ripristino di emergenza, e crea e avvia la VM nell'area principale. Si **noti** che macchine virtuali di ripristino di emergenza rimarranno nello stato di arresto deallocato come mostrato sopra. Si tratta del comportamento previsto dalla progettazione, dato che Azure Site Recovery salva le informazioni della macchina virtuale che potrebbero essere utili in caso di failover dall'area primaria all'area secondaria in un secondo momento. Non vengono effettuati addebiti per le macchine virtuali deallocate, quindi non modificare questo comportamento.
