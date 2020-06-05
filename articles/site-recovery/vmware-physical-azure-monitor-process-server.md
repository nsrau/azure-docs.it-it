---
title: Monitorare il server di elaborazione di Microsoft Azure Site Recovery
description: Questo articolo descrive come monitorare il server di elaborazione di Azure Site Recovery usato per il ripristino di emergenza di server fisici/macchine virtuali VMware.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 296254db83a065623b692d7947c130dfd71cd413
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835054"
---
# <a name="monitor-the-process-server"></a>Monitorare il server di elaborazione

Questo articolo descrive come monitorare il server di elaborazione di [Site Recovery](site-recovery-overview.md).

- Il server di elaborazione viene usato per configurare il ripristino di emergenza per i server fisici e le macchine virtuali VMware locali in Azure.
- Il server di elaborazione viene eseguito per impostazione predefinita nel server di configurazione. È installato per impostazione quando si distribuisce il server di configurazione.
- Facoltativamente, per ridimensionare e gestire un numero maggiore di computer replicati e volumi più elevati di traffico di replica, è possibile distribuire server di elaborazione aggiuntivi con scalabilità orizzontale.

[Altre informazioni](vmware-physical-azure-config-process-server-overview.md) sul ruolo e la distribuzione dei server di elaborazione.

## <a name="monitoring-overview"></a>Panoramica del monitoraggio

Poiché il server di elaborazione ha un numero così elevato di ruoli, in particolare nella memorizzazione nella cache dei dati replicati, nella compressione e nel trasferimento in Azure, è importante monitorare l'integrità del server di elaborazione su base continuativa.

Le situazioni che influiscono comunemente sulle prestazioni del server di elaborazione sono numerose. I problemi che influiscono sulle prestazioni hanno un effetto a catena sull'integrità della macchina virtuale. Il server di elaborazione e i computer replicati vengono quindi inseriti in uno stato critico. Tali situazioni includono:

- Un numero elevato di macchine virtuali che usa un server di elaborazione, prossimo o superiore alle limitazioni consigliate.
- Le macchine virtuali che usano il server di elaborazione hanno una percentuale di varianza elevata.
- La velocità effettiva di rete tra le macchine virtuali e il server di elaborazione non è sufficiente per caricare i dati di replica nel server di elaborazione.
- La velocità effettiva di rete tra il server di elaborazione e Azure non è sufficiente per caricare i dati di replica dal server di elaborazione in Azure.

Tutti questi problemi possono influenzare l'obiettivo del punto di ripristino (RPO) delle macchine virtuali. 

**Perché?** Perché la generazione di un punto di ripristino per una macchina virtuale prevede che tutti i dischi della macchina virtuale abbiano un punto comune. Se un disco presenta una frequenza di varianza elevata, la replica è lenta oppure il server di elaborazione non è ottimale, ciò influisce sul modo in cui i punti di ripristino vengono creati.

## <a name="monitor-proactively"></a>Monitorare in modo proattivo

Per evitare problemi con il server di elaborazione, è importante:

- Comprendere i requisiti specifici per i server di elaborazione usando [le linee guida per la capacità e il ridimensionamento](site-recovery-plan-capacity-vmware.md#capacity-considerations)e assicurarsi che i server di elaborazione siano distribuiti e in esecuzione secondo le raccomandazioni.
- Monitorare gli avvisi e risolvere i problemi non appena si verificano, per tenere i server di elaborazione in esecuzione in modo efficiente.


## <a name="process-server-alerts"></a>Avvisi del server di elaborazione

Il server di elaborazione genera un dato numero di avvisi di integrità, riepilogati nella tabella seguente.

**Tipo di avviso** | **Dettagli**
--- | ---
![Healthy][green] | Il server di elaborazione è connesso e integro.
![Avviso][yellow] | Uso della CPU > 80% negli ultimi 15 minuti
![Avviso][yellow] | Uso della memoria > 80% negli ultimi 15 minuti
![Avviso][yellow] | Spazio disponibile nella cartella cache < 30% negli ultimi 15 minuti
![Avviso][yellow] | Site Recovery monitora i dati in sospeso/in uscita ogni cinque minuti e stima che i dati nella cache del server di elaborazione non possano essere caricati in Azure entro 30 minuti.
![Avviso][yellow] | I servizi del server di elaborazione non erano in esecuzione negli ultimi 15 minuti
![Critico][red] | Uso della CPU > 95% negli ultimi 15 minuti
![Critico][red] | Uso della memoria > 95% negli ultimi 15 minuti
![Critico][red] | Spazio disponibile nella cartella cache < 25% negli ultimi 15 minuti
![Critico][red] | Site Recovery monitora i dati in sospeso/in uscita ogni cinque minuti e stima che i dati nella cache del server di elaborazione non possano essere caricati in Azure entro 45 minuti.
![Critico][red] | Nessun heartbeat dal server di elaborazione per 15 minuti.

![Chiave tabella](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> Lo stato di integrità generale del server di elaborazione è basato sul peggior avviso generato.



## <a name="monitor-process-server-health"></a>Monitorare l'integrità del server di elaborazione

È possibile monitorare lo stato di integrità dei server di elaborazione come indicato di seguito: 

1. Per monitorare l'integrità e lo stato di replica di un computer replicato e del relativo server di elaborazione, nell'insieme di credenziali > **Elementi replicati**, fare clic sul computer da monitorare.
2. In **Stato di replica** è possibile monitorare lo stato di integrità della macchina virtuale. Fare clic sullo stato per eseguire il drill-down per i dettagli dell'errore.

    ![Elaborare l'integrità del server di elaborazione nel dashboard della macchina virtuale](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. In **Integrità del server di elaborazione** è possibile monitorare lo stato del server di elaborazione. Eseguire il drill-down per maggiori dettagli.

    ![Elaborare i dettagli del server nel dashboard della macchina virtuale](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. L'integrità può essere monitorata anche tramite la rappresentazione grafica nella pagina della macchina virtuale.
    - Un server di elaborazione scale-out verrà evidenziato in arancio se ad esso sono associati avvisi e in rosso in caso di problemi critici. 
    - Se il server di elaborazione è in esecuzione nella distribuzione predefinita nel server di configurazione, quest'ultimo verrà evidenziato di conseguenza.
    - Per eseguire il drill-down, fare clic sul server di configurazione o sul server di elaborazione. Prendere nota di eventuali problemi e consigli per la correzione.

È anche possibile monitorare i server di elaborazione nell'insieme di credenziali in **Infrastruttura di Site Recovery**. In **Gestisci infrastruttura di Site Recovery**, fare clic su **Server di configurazione**. Selezionare il server di configurazione associato al server di elaborazione ed eseguire il drill-down nei dettagli del server di elaborazione.


## <a name="next-steps"></a>Passaggi successivi

- Se si verificano problemi con i server di elaborazione, consultare il [materiale sussidiario per la risoluzione dei problemi](vmware-physical-azure-troubleshoot-process-server.md)
- Per richiedere maggiore assistenza, pubblicare una domanda sulla [pagina delle domande di Domande e risposte per Azure Site Recovery](https://docs.microsoft.com/answers/topics/azure-site-recovery.html). 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
