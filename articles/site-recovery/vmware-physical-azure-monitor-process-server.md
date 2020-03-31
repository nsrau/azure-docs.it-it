---
title: Monitorare il server di elaborazione di Azure Site RecoveryMonitor the Azure Site Recovery process server
description: In questo articolo viene descritto come monitorare il server di elaborazione di Azure Site Recovery usato per il ripristino di emergenza di VM/server fisici VMware
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 54c161c40c881d7626f79fc9bfe1ec1c160480ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257069"
---
# <a name="monitor-the-process-server"></a>Monitorare il server di elaborazione

In questo articolo viene descritto come monitorare il server di elaborazione [di Site Recovery.](site-recovery-overview.md)

- Il server di elaborazione viene usato quando si configura il ripristino di emergenza delle macchine virtuali VMware locali e dei server fisici in Azure.The process server is used when you set up disaster recovery of on-premises VMware VMs and physical servers to Azure.
- Per impostazione predefinita, il server di elaborazione viene eseguito nel server di configurazione. Viene installato per impostazione predefinita quando si distribuisce il server di configurazione.
- Facoltativamente, per scalare e gestire un numero maggiore di computer replicati e volumi più elevati di traffico di replica, è possibile distribuire server di elaborazione aggiuntivi con scalabilità orizzontale.

[Ulteriori informazioni](vmware-physical-azure-config-process-server-overview.md) sul ruolo e sulla distribuzione dei server di elaborazione.

## <a name="monitoring-overview"></a>Panoramica del monitoraggio

Poiché il server di elaborazione ha così tanti ruoli, in particolare nella memorizzazione nella cache dei dati replicati, nella compressione e nel trasferimento in Azure, è importante monitorare l'integrità del server di elaborazione in modo continuativo.

Esistono diverse situazioni che in genere influiscono sulle prestazioni del server di elaborazione. I problemi relativi alle prestazioni avranno un effetto a catena sull'integrità della macchina virtuale, spingendo infine sia il server di elaborazione che i relativi computer replicati in uno stato critico. Le situazioni includono:

- Un numero elevato di macchine virtuali usa un server di elaborazione, avvicinandosi o superando le limitazioni consigliate.
- Le macchine virtuali che usano il server di elaborazione hanno una frequenza di varianza elevata.
- La velocità effettiva di rete tra le macchine virtuali e il server di elaborazione non è sufficiente per caricare i dati di replica nel server di elaborazione.
- La velocità effettiva di rete tra il server di elaborazione e Azure non è sufficiente per caricare i dati di replica dal server di elaborazione in Azure.Network throughput between the process server and Azure isn't sufficient to upload replication data from the process server to Azure.

Tutti questi problemi possono influire sull'obiettivo del punto di ripristino (RPO) delle macchine virtuali. 

**Perché?** Poiché la generazione di un punto di ripristino per una macchina virtuale richiede che tutti i dischi nella macchina virtuale dispongano di un punto comune. Se un disco ha una frequenza di varianza elevata, la replica è lenta o il server di elaborazione non è ottimale, influisce sulla creazione efficiente dei punti di ripristino.

## <a name="monitor-proactively"></a>Monitorare in modo proattivo

Per evitare problemi con il server di elaborazione, è importante:

- Comprendere i requisiti specifici per i server di elaborazione utilizzando la capacità e le indicazioni per il [dimensionamento](site-recovery-plan-capacity-vmware.md#capacity-considerations)e assicurarsi che i server di elaborazione siano distribuiti e in esecuzione in base alle raccomandazioni.
- Monitorare gli avvisi e risolvere i problemi man mano che si verificano, per mantenere i server di elaborazione in esecuzione in modo efficiente.


## <a name="process-server-alerts"></a>Avvisi del server di elaborazione

Il server di elaborazione genera una serie di avvisi di integrità, riepilogati nella tabella seguente.

**Tipo di avviso** | **Dettagli**
--- | ---
![Healthy][green] | Il server di elaborazione è connesso e integro.
![Avviso][yellow] | Utilizzo della CPU > 80% negli ultimi 15 minuti
![Avviso][yellow] | Utilizzo della memoria > 80% negli ultimi 15 minuti
![Avviso][yellow] | Spazio libero cartella cache < 30% negli ultimi 15 minuti
![Avviso][yellow] | Site Recovery monitora i dati in sospeso/in uscita ogni cinque minuti e stima che i dati nella cache del server di elaborazione non possano essere caricati in Azure entro 30 minuti.
![Avviso][yellow] | I servizi server di elaborazione non sono in esecuzione negli ultimi 15 minuti
![Critico][red] | Utilizzo della CPU > 95% negli ultimi 15 minuti
![Critico][red] | Utilizzo della memoria > 95% negli ultimi 15 minuti
![Critico][red] | Spazio libero cartella cache < 25% negli ultimi 15 minuti
![Critico][red] | Site Recovery monitora i dati in sospeso/in uscita ogni cinque minuti e stima che i dati nella cache del server di elaborazione non possano essere caricati in Azure entro 45 minuti.
![Critico][red] | Nessun heartbeat dal server di elaborazione per 15 minuti.

![Chiave tabella](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> Lo stato di integrità generale del server di elaborazione si basa sull'avviso più visualizzato.



## <a name="monitor-process-server-health"></a>Monitorare l'integrità del server di elaborazione

È possibile monitorare lo stato di integrità dei server di elaborazione come segue: 

1. Per monitorare l'integrità e lo stato della replica di un computer replicato e del relativo server di elaborazione, nell'insieme > **elementi replicati**fare clic sul computer che si desidera monitorare.
2. In **Integrità replica**è possibile monitorare lo stato di integrità della macchina virtuale. Fare clic sullo stato per eseguire il drill-down per i dettagli dell'errore.

    ![Integrità del server di elaborazione nel dashboard della macchina virtualeProcess server health in VM dashboard](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. In Integrità server di **elaborazione**, è possibile monitorare lo stato del server di elaborazione. Eseguire il drill-down per i dettagli.

    ![Dettagli del server di elaborazione nel dashboard della macchina virtualeProcess server details in VM dashboard](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. L'integrità può anche essere monitorata usando la rappresentazione grafica nella pagina della macchina virtuale.
    - Un server di elaborazione con scalabilità orizzontale verrà evidenziato in arancione se sono presenti avvisi associati e rosso in caso di problemi critici. 
    - Se il server di elaborazione è in esecuzione nella distribuzione predefinita nel server di configurazione, il server di configurazione verrà evidenziato di conseguenza.
    - Per eseguire il drill-down, fare clic sul server di configurazione o sul server di elaborazione. Prendere nota di eventuali problemi ed eventuali consigli per la correzione.

È inoltre possibile monitorare i server di elaborazione nell'insieme di credenziali in **Infrastruttura di Site Recovery**. In **Gestisci infrastruttura di Site Recovery**fare clic su Server di **configurazione**. Selezionare il server di configurazione associato al server di elaborazione ed eseguire il drill-down dei dettagli del server di elaborazione.


## <a name="next-steps"></a>Passaggi successivi

- In caso di problemi con i server di elaborazione, segui le nostre [linee guida per la risoluzione dei problemi](vmware-physical-azure-troubleshoot-process-server.md)
- Se è necessaria assistenza ulteriore, pubblicare una domanda nel [forum per Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
