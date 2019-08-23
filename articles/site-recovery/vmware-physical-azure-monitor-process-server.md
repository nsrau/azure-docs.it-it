---
title: Monitorare il server di elaborazione Azure Site Recovery
description: Questo articolo descrive come monitorare Azure Site Recovery Server di elaborazione.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: raynew
ms.openlocfilehash: 5d746385a034fdf742b8958b3d1fe51ea2a3c5cf
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972184"
---
# <a name="monitor-the-process-server"></a>Monitorare il server di elaborazione

Questo articolo descrive come monitorare il server di elaborazione [Site Recovery](site-recovery-overview.md) .

- Il server di elaborazione viene usato quando si configura il ripristino di emergenza di macchine virtuali VMware locali e server fisici in Azure.
- Per impostazione predefinita, il server di elaborazione viene eseguito nel server di configurazione. Viene installato per impostazione predefinita quando si distribuisce il server di configurazione.
- Facoltativamente, per scalare e gestire un numero maggiore di computer replicati e volumi più elevati di traffico di replica, è possibile distribuire server di elaborazione aggiuntivi e con scalabilità orizzontale.

[Altre](vmware-physical-azure-config-process-server-overview.md) informazioni sul ruolo e la distribuzione dei server di elaborazione.

## <a name="monitoring-overview"></a>Panoramica del monitoraggio

Poiché il server di elaborazione ha un numero così elevato di ruoli, in particolare nella memorizzazione nella cache dei dati replicati, nella compressione e nel trasferimento in Azure, è importante monitorare l'integrità del server di elaborazione su base continuativa.

Esistono diverse situazioni che influiscono comunemente sulle prestazioni del server di elaborazione. I problemi che influiscono sulle prestazioni avranno un effetto a catena sull'integrità della macchina virtuale. in questo modo, il server di elaborazione e i computer replicati vengono inseriti in uno stato critico. Le situazioni includono:

- Un numero elevato di macchine virtuali usa un server di elaborazione, che si avvicina o supera le limitazioni consigliate.
- Le macchine virtuali che usano il server di elaborazione hanno una percentuale di varianza elevata.
- La velocità effettiva di rete tra le macchine virtuali e il server di elaborazione non è sufficiente per caricare i dati di replica nel server di elaborazione.
- La velocità effettiva di rete tra il server di elaborazione e Azure non è sufficiente per caricare i dati di replica dal server di elaborazione in Azure.

Tutti questi problemi possono influenzare l'obiettivo del punto di ripristino (RPO) delle macchine virtuali. 

**Perché?** Poiché la generazione di un punto di ripristino per una macchina virtuale richiede che tutti i dischi della macchina virtuale abbiano un punto comune. Se un disco presenta una frequenza di varianza elevata, la replica è lenta o il server di elaborazione non è ottimale, influisca sul modo in cui vengono creati i punti di ripristino.

## <a name="monitor-proactively"></a>Monitorare in modo proattivo

Per evitare problemi con il server di elaborazione, è importante:

- Comprendere i requisiti specifici per i server di elaborazione utilizzando la [capacità e le linee guida](site-recovery-plan-capacity-vmware.md#capacity-considerations)per il ridimensionamento e assicurarsi che i server di elaborazione vengano distribuiti e in esecuzione secondo le raccomandazioni.
- Monitorare gli avvisi e risolvere i problemi non appena si verificano, per tenere i server di elaborazione in esecuzione in modo efficiente.


## <a name="process-server-alerts"></a>Avvisi del server di elaborazione

Il server di elaborazione genera un certo numero di avvisi di integrità, riepilogati nella tabella seguente.

**Tipo di avviso** | **Dettagli**
--- | ---
![Integra][green] | Il server di elaborazione è connesso e integro.
![Avviso][yellow] | Utilizzo CPU > 80% per gli ultimi 15 minuti
![Avviso][yellow] | Utilizzo memoria > 80% per gli ultimi 15 minuti
![Avviso][yellow] | Spazio disponibile cartella cache < 30% per gli ultimi 15 minuti
![Avviso][yellow] | I servizi del server di elaborazione non sono in esecuzione negli ultimi 15 minuti
![Critico][red] | Utilizzo CPU > 95% per gli ultimi 15 minuti
![Critico][red] | Utilizzo memoria > 95% per gli ultimi 15 minuti
![Critico][red] | Spazio disponibile cartella cache < il 25% per gli ultimi 15 minuti
![Critico][red] | Nessun heartbeat dal server di elaborazione per 15 minuti.

![Chiave tabella](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> Lo stato di integrità generale del server di elaborazione è basato sul peggior avviso generato.



## <a name="monitor-process-server-health"></a>Monitorare l'integrità del server di elaborazione

È possibile monitorare lo stato di integrità dei server di elaborazione come indicato di seguito: 

1. Per monitorare l'integrità e lo stato della replica di un computer replicato e del relativo server di elaborazione, in insieme di credenziali > **elementi replicati**fare clic sul computer che si desidera monitorare.
2. Nell' **integrità della replica**è possibile monitorare lo stato di integrità della macchina virtuale. Fare clic sullo stato per eseguire il drill-down per i dettagli dell'errore.

    ![Integrità del server di elaborazione nel dashboard della macchina virtuale](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. In **integrità server di elaborazione**è possibile monitorare lo stato del server di elaborazione. Eseguire il drill-down per i dettagli.

    ![Dettagli del server di elaborazione nel dashboard della macchina virtuale](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. L'integrità può essere monitorata anche usando la rappresentazione grafica nella pagina VM.
    - Un server di elaborazione con scalabilità orizzontale verrà evidenziato in arancione se sono associati avvisi e rosso in caso di problemi critici. 
    - Se il server di elaborazione è in esecuzione nella distribuzione predefinita nel server di configurazione, il server di configurazione verrà evidenziato di conseguenza.
    - Per eseguire il drill-down, fare clic sul server di configurazione o sul server di elaborazione. Prendere nota di eventuali problemi ed eventuali consigli per la correzione.

È anche possibile monitorare i server di elaborazione nell'insieme di credenziali in **Site Recovery infrastruttura**. In **Gestisci l'infrastruttura Site Recovery**fare clic su **server di configurazione**. Selezionare il server di configurazione associato al server di elaborazione ed eseguire il drill-down nei dettagli del server di elaborazione.


## <a name="next-steps"></a>Passaggi successivi

- Se si verificano problemi con i server di elaborazione, seguire le indicazioni per la [risoluzione dei problemi](vmware-physical-azure-troubleshoot-process-server.md)
- Se è necessaria assistenza ulteriore, pubblicare una domanda nel [forum per Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
