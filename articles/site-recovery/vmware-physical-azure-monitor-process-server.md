---
title: Monitorare il server di elaborazione di Azure Site Recovery
description: Questo articolo descrive come monitorare i server di elaborazione di Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/24/2019
ms.author: rayne
ms.openlocfilehash: 5fac369f15edb3ef0be31d3dc7d7434104c18dfe
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928168"
---
# <a name="monitor-the-process-server"></a>Monitorare il server di elaborazione

Questo articolo descrive come monitorare i [Site Recovery](site-recovery-overview.md) server di elaborazione.

- Il server di elaborazione viene usato quando si configura il ripristino di emergenza di macchine virtuali VMware locali e server fisici in Azure.
- Per impostazione predefinita il server di elaborazione viene eseguito nel server di configurazione. Viene installato per impostazione predefinita quando si distribuisce il server di configurazione.
- Facoltativamente, scalabilità e gestire un numero maggiore di macchine replicate e volumi più elevati di traffico di replica, è possibile distribuire server di elaborazione aggiuntivi, scalabilità orizzontale.

[Altre informazioni](vmware-physical-azure-config-process-server-overview.md) informazioni sul ruolo e la distribuzione del server di elaborazione.

## <a name="monitoring-overview"></a>Panoramica del monitoraggio

Poiché il server di elaborazione ha ruoli così tanti, in particolare nella memorizzazione nella cache i dati replicati, la compressione e il trasferimento in Azure, è importante monitorare l'integrità di server di processo in modo continuativo.

Esistono una serie di situazioni in cui sono in genere sulle prestazioni del processo server. Problemi di prestazioni avrà un effetto a catena sullo stato di integrità della macchina virtuale, alla fine il push sia il server di elaborazione e le macchine replicate in uno stato critico. Situazioni includono:

- Un numero elevato di macchine virtuali Usa un server di elaborazione, per raggiungere o superare le limitazioni consigliate.
- Le macchine virtuali tramite il server di elaborazione con una frequenza di varianza elevata.
- Velocità effettiva di rete tra le macchine virtuali e il server di elaborazione non è sufficiente per caricare i dati di replica al server di elaborazione.
- Velocità effettiva di rete tra il server di elaborazione e Azure non è sufficiente per caricare i dati di replica dal server di elaborazione in Azure.

Tutti questi aspetti possono influire sull'obiettivo del punto di ripristino (RPO) delle macchine virtuali. 

**Perché?** Poiché la generazione di un punto di ripristino per una macchina virtuale richiede tutti i dischi della macchina virtuale per avere una temporizzazione comune. Se un disco ha un tasso di abbandono elevata, la replica è lenta o il server di elaborazione non è ottimale, influisce sull'efficienza vengono creati i punti di ripristino.

## <a name="monitor-proactively"></a>Monitorare in modo proattivo

Per evitare problemi con il server di elaborazione, è importante:

- Informazioni sui requisiti specifici per i server di elaborazione usando [capacità e informazioni sul dimensionamento](site-recovery-plan-capacity-vmware.md#capacity-considerations)e assicurarsi che i server di elaborazione vengono distribuiti e in esecuzione in base alle raccomandazioni.
- Avvisi di monitoraggio e risoluzione dei problemi appena si verificano, per mantenere i server di elaborazione in esecuzione in modo efficiente.


## <a name="process-server-alerts"></a>Avvisi server di elaborazione

Il server di elaborazione genera un numero di avvisi di integrità, riepilogati nella tabella seguente.

**Tipo di avviso** | **Dettagli**
--- | ---
![Healthy][green] | Server di elaborazione sia connesso e integro.
![Avviso][yellow] | CPU utilizzo > 80% negli ultimi 15 minuti
![Avviso][yellow] | Memoria utilizzo > 80% negli ultimi 15 minuti
![Avviso][yellow] | Cartella della cache di spazio libero < 30% negli ultimi 15 minuti
![Avviso][yellow] | Servizi del server di elaborazione non sono in esecuzione negli ultimi 15 minuti
![Critico][red] | Utilizzo di CPU > 95% negli ultimi 15 minuti
![Critico][red] | Utilizzo memoria è > 95% negli ultimi 15 minuti
![Critico][red] | Cartella della cache di spazio libero < 25% negli ultimi 15 minuti
![Critico][red] | Nessun heartbeat dal server di elaborazione per 15 minuti.

![Chiave della tabella](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> Lo stato di integrità generale del server di elaborazione si basa su peggiore avviso generato.



## <a name="monitor-process-server-health"></a>Integrità server di monitoraggio processo

È possibile monitorare lo stato di integrità dei server di processo come indicato di seguito: 

1. Per monitorare l'integrità della replica e lo stato di una macchina replicata e del relativo server di elaborazione, nell'insieme di credenziali > **elementi replicati**, fare clic sulla macchina che si desidera monitorare.
2. Nelle **integrità della replica**, è possibile monitorare lo stato di integrità della macchina virtuale. Scegliere lo stato per il drill-down per informazioni sull'errore.

    ![Integrità server di processo nel dashboard VM](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. Nelle **integrità del processo Server**, è possibile monitorare lo stato del server di elaborazione. Eseguire il drill-per informazioni dettagliate.

    ![Dettagli del processo server nel dashboard VM](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. Integrità può anche essere monitorato mediante la rappresentazione grafica nella pagina della macchina virtuale.
    - Un server di elaborazione scale-out sarà evidenziata in arancione se sono presenti avvisi associati, mentre il rosso in caso affermativo eventuali problemi critici. 
    - Se il server di elaborazione è in esecuzione nel server di configurazione della distribuzione predefinite, quindi il server di configurazione sarà indicato.
    - Per eseguire il drill-, fare clic sul server di configurazione o server di elaborazione. Notare eventuali problemi e alcuna raccomandazione per il monitoraggio e aggiornamento.

È anche possibile monitorare nell'insieme di credenziali nel server di elaborazione **infrastruttura di Site Recovery**. Nelle **gestire l'infrastruttura di Site Recovery**, fare clic su **server di configurazione**. Selezionare il server di configurazione associato con il server di elaborazione e il drill down in dettagli del processo server.


## <a name="next-steps"></a>Passaggi successivi

- Se una qualsiasi processo server problemi, seguire la [indicazioni](vmware-physical-azure-troubleshoot-process-server.md)
- Se è necessaria assistenza ulteriore, pubblicare una domanda nel [forum per Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
