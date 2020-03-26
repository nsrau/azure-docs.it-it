---
title: Eseguire un'esercitazione sul ripristino di emergenza delle VM di Azure con Azure Site Recovery
description: Informazioni su come eseguire un'analisi del ripristino di emergenza in un'area secondaria per le VM di Azure usando il servizio Azure Site Recovery.
services: site-recovery
ms.topic: tutorial
ms.date: 01/16/2020
ms.custom: mvc
ms.openlocfilehash: b2ce157f0f192135ab0507e4aae4c0a282bda1ea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76166185"
---
# <a name="run-a-disaster-recovery-drill-to-a-secondary-region-for-azure-vms"></a>Eseguire un'esercitazione sul ripristino di emergenza in un'area secondaria per le VM di Azure

Il servizio [Azure Site Recovery](site-recovery-overview.md) contribuisce a realizzare la strategia di continuità aziendale e ripristino di emergenza (BCDR) mantenendo disponibili e operative le app aziendali durante interruzioni pianificate e non pianificate. Site Recovery gestisce e coordina il ripristino di emergenza di computer locali e macchine virtuali di Azure, incluse le operazioni di replica, failover e failback.

Questa esercitazione illustra come eseguire un'analisi del ripristino di emergenza per una macchina virtuale di Azure, da un'area di Azure a un'altra, con un failover di test. Un'analisi consente di convalidare la strategia di replica senza tempi di inattività o perdite di dati e non ha alcun impatto sull'ambiente di produzione. In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Verificare i prerequisiti
> * Eseguire un failover di test per una singola macchina virtuale

> [!NOTE]
> Questa esercitazione consente di eseguire un'analisi di ripristino di emergenza con un numero minimo di passaggi. Per altre informazioni sulle varie funzioni correlate a un'analisi di ripristino di emergenza, vedere la documentazione relativa a [replica](azure-to-azure-how-to-enable-replication.md), [rete](azure-to-azure-about-networking.md), [automazione](azure-to-azure-powershell.md) o [risoluzione dei problemi](azure-to-azure-troubleshoot-errors.md) delle macchine virtuali di Azure.

## <a name="prerequisites"></a>Prerequisiti

Prima di procedere con questa esercitazione, verificare quanto segue:

- Prima di eseguire un failover di test, è consigliabile controllare le proprietà della macchina virtuale per assicurarsi che sia configurata per il ripristino di emergenza. Passare a **Operazioni** > **Ripristino di emergenza** > **Proprietà** nella macchina virtuale per visualizzare le proprietà di replica e failover.
- **È consigliabile usare una rete di una macchina virtuale di Azure separata per il failover di test** e non la rete predefinita che è stata configurata al momento di abilitare la replica.
- A seconda delle configurazioni di rete di origine per ogni scheda di interfaccia di rete, è possibile specificare **subnet**, **indirizzo IP privato**, **indirizzo IP pubblico**, **gruppo di sicurezza di rete** o **Load Balancer** da collegare a ogni scheda di interfaccia di rete nelle impostazioni di failover di test in **Calcolo e rete** prima di eseguire l'esercitazione sul ripristino di emergenza.

## <a name="run-a-test-failover"></a>Eseguire un failover di test

Questo esempio illustra come usare un insieme di credenziali di Servizi di ripristino per eseguire un failover di test della macchina virtuale.

1. Selezionare un insieme di credenziali e passare a **Elementi protetti** > **Elementi replicati** e selezionare una macchina virtuale.
1. In **Failover di test** selezionare un punto di ripristino da usare per il failover:
   - **Più recente**: elabora tutti i dati in Site Recovery e fornisce il valore RPO (obiettivo del punto di ripristino) minimo.
   - **Elaborato più recente**: esegue il failover della macchina virtuale al punto di ripristino più recente elaborato da Site Recovery. Viene visualizzato il timestamp. Questa opzione non prevede elaborazione di dati e offre quindi un RTO (Recovery Time Objective) minimo.
   - **Coerente con l'app più recente**: questa opzione esegue il failover di tutte le macchine virtuali al più recente punto di recupero coerente con l'app. Viene visualizzato il timestamp.
   - **Custom**: esegue il failover a un determinato punto di ripristino. Questa opzione è disponibile solo per il failover di una singola macchina virtuale e non per quello con un piano di ripristino.
1. Selezionare la rete virtuale di Azure di destinazione a cui si connetteranno le macchine virtuali di Azure nell'area secondaria dopo il failover.

   > [!NOTE]
   > Il menu a discesa per selezionare la rete virtuale di Azure non sarà visibile se le impostazioni di failover di test sono preconfigurate per l'elemento replicato.

1. Per avviare il failover, fare clic su **OK**. Per tenere traccia dello stato di avanzamento dall'insieme di credenziali, passare a **Monitoraggio** > **Processi di Site Recovery** e selezionare il processo **Failover di test**.
1. Al termine del failover, la macchina virtuale di Azure di replica viene visualizzata nel portale di Azure in **Macchine virtuali**. Verificare che la macchina virtuale sia in esecuzione, che sia stata dimensionata in modo adeguato e che sia connessa alla rete appropriata.
1. Per eliminare le macchine virtuali create durante il failover di test, fare clic su **Pulisci failover di test** nell'elemento replicato o nel piano di ripristino. Fare clic su **Note** per registrare e salvare eventuali osservazioni associate al failover di test.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Eseguire un failover di produzione](azure-to-azure-tutorial-failover-failback.md)
