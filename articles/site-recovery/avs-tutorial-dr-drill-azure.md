---
title: Eseguire un'analisi del ripristino di emergenza dalla soluzione Azure VMware in Azure con Azure Site Recovery
description: Informazioni su come eseguire un'analisi del ripristino di emergenza da un cloud privato della soluzione Azure VMware in Azure con Azure Site Recovery.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: 46d5e1cf773a24b032874ee54021c780e1f361af
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91814236"
---
# <a name="run-a-disaster-recovery-drill-from-azure-vmware-solution-to-azure"></a>Eseguire un'analisi del ripristino di emergenza dalla soluzione Azure VMware in Azure

Questo articolo descrive come eseguire un'analisi del ripristino di emergenza per una macchina virtuale della soluzione Azure VMware in Azure usando il servizio [Azure Site Recovery](site-recovery-overview.md). Un'analisi convalida la strategia di replica senza perdita di dati.


Questa è la quarta esercitazione di una serie che mostra come configurare il ripristino di emergenza in Azure per macchine della soluzione Azure VMware.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]

> * Configurare una rete isolata per il failover di test
> * Preparare la connessione alla macchina virtuale di Azure dopo il failover
> * Eseguire un failover di test per una singola macchina.

> [!NOTE]
> Le esercitazioni mostrano il percorso di distribuzione più semplice per uno scenario. Quando possibile, vengono usate le opzioni predefinite e non sono riportati tutti i percorsi e le impostazioni possibili. Per altre informazioni sull'esercitazione di ripristino di emergenza, [vedere questo articolo](site-recovery-test-failover-to-azure.md).

## <a name="before-you-start"></a>Prima di iniziare

Completare le esercitazioni precedenti:

1. Assicurarsi di aver [configurato Azure](avs-tutorial-prepare-azure.md) per il ripristino di emergenza in Azure.
2. Seguire [questa procedura](avs-tutorial-prepare-avs.md) per preparare la distribuzione della soluzione Azure VMware per il ripristino di emergenza in Azure.
3. [Configurare](avs-tutorial-replication.md) il ripristino di emergenza per le macchine virtuali della soluzione Azure VMware.
 

## <a name="verify-vm-properties"></a>Verificare le proprietà della macchina virtuale

Prima di eseguire un failover di test, verificare le proprietà della macchina virtuale e assicurarsi che la [macchina virtuale VMware](vmware-physical-azure-support-matrix.md#replicated-machines) sia conforme ai requisiti di Azure.

1. In **Elementi protetti** fare clic su **Elementi replicati** > macchina virtuale.
2. Nel riquadro **Elemento replicato** è possibile vedere un riepilogo relativo a informazioni sulla macchina virtuale, stato integrità e ultimi punti di ripristino disponibili. Fare clic su **Proprietà** per visualizzare altri dettagli.
3. In **Calcolo e rete** è possibile modificare il nome Azure, il gruppo di risorse, le dimensioni di destinazione, il set di disponibilità e le impostazioni del disco gestito.
4. È possibile visualizzare e modificare le impostazioni di rete, tra cui la rete/subnet in cui si troverà la macchina virtuale di Azure dopo il failover e l'indirizzo IP che le verrà assegnato.
5. In **Dischi** è possibile vedere le informazioni sul sistema operativo e sui dischi dati della VM.

## <a name="create-a-network-for-test-failover"></a>Creare una rete per il failover di test

Durante un failover di test, è consigliabile scegliere una rete isolata dalla rete del sito di ripristino di produzione specificata nelle impostazioni **Calcolo e rete** di ogni macchina virtuale. Quando si crea una rete virtuale di Azure, per impostazione predefinita è isolata dalle altre reti. La rete di test dovrà simulare la rete di produzione:

- La rete di test dovrà avere lo stesso numero di subnet della rete di produzione. Le subnet dovranno avere lo stesso nome.
- La rete di test dovrà usare la stessa classe di indirizzi IP e lo stesso intervallo di subnet.
- Aggiornare il DNS della rete di test con l'indirizzo IP specificato per la macchina virtuale DNS nelle impostazioni **Calcolo e rete**. Per altri dettagli, vedere [Considerazioni sul failover di test per Active Directory](site-recovery-active-directory.md#test-failover-considerations).

## <a name="run-a-test-failover-for-a-single-vm"></a>Eseguire un failover di test per una singola macchina virtuale

Quando si esegue un failover di test, si verifica quanto segue:

1. Viene eseguito un controllo dei prerequisiti per verificare che tutte le condizioni necessarie per il failover siano in atto.
2. Il failover elabora i dati, in modo che sia possibile creare una macchina virtuale di Azure. Selezionando il punto di recupero più recente, viene creato un punto di recupero dai dati.
3. Una macchina virtuale di Azure viene creata usando i dati elaborati nel passaggio precedente.

Eseguire il failover di test come descritto di seguito:

1. In **Impostazioni** > **Elementi replicati** fare clic sulla macchina virtuale > **+Failover di test**.
2. Per questa esercitazione, selezionare il punto di ripristino **elaborato più recente**. Verrà così eseguito il failover della VM al punto di ripristino più recente disponibile. Viene visualizzato il timestamp. Con questa opzione, non viene impiegato alcun tempo di elaborazione dati, pertanto viene fornito un RTO (Recovery Time Objective) basso.
3. In **Failover di test** selezionare la rete di Azure di destinazione a cui vengono connesse le macchine virtuali di Azure dopo il failover.
4. Fare clic su **OK** per iniziare il failover. È possibile tenere traccia dell'avanzamento facendo clic sulla macchina virtuale per visualizzarne le proprietà. In alternativa, è possibile fare clic sul processo **Failover di test** nel nome dell'insieme di credenziali, quindi su **Impostazioni** > **Processi** >
   **Site Recovery jobs** (Processi di Site Recovery).
5. Al termine del failover, la macchina virtuale di Azure di replica viene visualizzata nel portale di Azure in **Macchine virtuali**. Verificare che la macchina virtuale sia delle dimensioni appropriate, che sia connessa alla rete corretta e che sia in esecuzione.
6. Sarà ora possibile connettersi alla macchina virtuale replicata in Azure.
7. Per eliminare le macchine virtuali di Azure create durante il failover di test, fare clic su **Pulisci failover di test** nella VM. Fare clic su **Note** per registrare e salvare eventuali osservazioni associate al failover di test.

In alcuni scenari il failover richiede un'altra elaborazione il cui completamento richiede da 8 a 10 minuti. L'esecuzione del failover di test potrebbe richiedere più tempo per computer Linux VMware, macchine virtuali VMware per cui non è abilitato il servizio DHCP e macchine virtuali VMware che non hanno i driver di avvio seguenti: storvsc, vmbus, storflt, intelide, atapi.

## <a name="connect-after-failover"></a>Connettersi dopo il failover

Per connettersi alle macchine virtuali di Azure usando RDP/SSH dopo il failover, [predisporre la connessione](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover). Se si verificano problemi di connettività dopo il failover, seguire la guida alla [risoluzione dei problemi](site-recovery-failover-to-azure-troubleshoot.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Eseguire un failover](avs-tutorial-failover.md)

