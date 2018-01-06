---
title: Calcoli delle valutazioni in Azure Migrate | Microsoft Docs
description: Offre una panoramica dei calcoli delle valutazioni nel servizio Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: b8075f0e1149a6fc5194347fc34e2a16d5eb2ffc
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="assessment-calculations"></a>Calcoli delle valutazioni

[Azure Migrate](migrate-overview.md) valuta i carichi di lavoro locali per la migrazione ad Azure. Questo articolo fornisce informazioni sulle modalità di calcolo delle valutazioni.



## <a name="overview"></a>Panoramica

Una valutazione di Azure Migrate si articola in tre fasi. La valutazione inizia con un'analisi di idoneità, seguita da stime delle dimensioni in base alle prestazioni e termina con una stima dei costi mensili. Un computer passa alla fase successiva soltanto se supera quella precedente. Ad esempio, se un computer non supera il controllo di idoneità di Azure, viene contrassegnato come non adatto per Azure e le dimensioni e i costi non vengono calcolati. 


## <a name="azure-suitability-analysis"></a>Analisi di idoneità di Azure

I computer di cui si vuole eseguire la migrazione ad Azure devono soddisfare i requisiti e le limitazioni di Azure. Ad esempio, il disco di una macchina virtuale locale che supera i 4 TB non può essere ospitato in Azure. I controlli di conformità di Azure sono riepilogati nella tabella seguente. 

**Controllo** | **Dettagli**
--- | ---
**Tipo di avvio** | L'avvio del disco del sistema operativo guest deve essere di tipo BIOS e non UEFI.
**Core** | Il numero di core nei computer deve essere uguale o inferiore al numero massimo di core (32) supportati per una macchina virtuale di Azure.<br/><br/> Se è disponibile la cronologia delle prestazioni, Azure Migrate prende in considerazione per il confronto i core utilizzati. Se nelle impostazioni di valutazione è specificato un fattore di comfort, il numero di core utilizzati viene moltiplicato per questo fattore.<br/><br/> In mancanza di una cronologia delle prestazioni, Azure Migrate usa i core allocati senza applicare il fattore di comfort.
**Memoria** | Le dimensioni della memoria del computer devono essere uguali o inferiori alla memoria massima (448 GB) consentita per una macchina virtuale di Azure. <br/><br/> Se è disponibile la cronologia delle prestazioni, Azure Migrate prende in considerazione per il confronto la memoria utilizzata. Se è specificato un fattore di comfort, la memoria utilizzata viene moltiplicata per questo fattore.<br/><br/> In mancanza di una cronologia, si usa la memoria allocata senza applicare il fattore di comfort.<br/><br/> 
**Windows Server 2003-2008 R2** | Supporto a 32 e 64 bit.<br/><br/> Azure offre solo supporto di tipo massimo sforzo.
**Windows Server 2008 R2 con tutti i Service Pack** | Supporto a 64 bit.<br/><br/> Azure offre supporto completo.
**Windows Server 2012 e tutti i Service Pack** | Supporto a 64 bit.<br/><br/> Azure offre supporto completo.
**Windows Server 2012 R2 e tutti i Service Pack** | Supporto a 64 bit.<br/><br/> Azure offre supporto completo.
**Windows Server 2016 e tutti i Service Pack** | Supporto a 64 bit.<br/><br/> Azure offre supporto completo.
**Client Windows 7 e versioni successive** | Supporto a 64 bit.<br/><br/> Azure offre supporto solo con la sottoscrizione di Visual Studio.
**Linux** | Supporto a 64 bit.<br/><br/> Azure offre supporto completo per questi [sistemi operativi](../virtual-machines/linux/endorsed-distros.md).
**Disco di archiviazione** | Le dimensioni allocate di un disco non devono superare i 4 TB (4096 GB).<br/><br/> I dischi collegati al computer non devono essere più di 65, incluso il disco del sistema operativo. 
**Rete** | A un computer non devono essere collegati più di 32 NIC.


## <a name="performance-based-sizing"></a>Dimensioni in base alle prestazioni

Dopo che un computer è stato contrassegnato come adatto per Azure, Azure Migrate esegue il mapping alle dimensioni di una macchina virtuale in Azure usando i criteri seguenti:

- **Controllo archiviazione**: eseguire la migrazione di Azure tenta di eseguire il mapping di ogni disco collegato alla macchina un disco in Azure:
    - Migrazione Azure Moltiplica il / o al secondo (IOPS) per il fattore di comodità. Moltiplica anche la velocità effettiva (in Mbps) di ogni disco per il fattore di comfort. In questo modo vengono calcolati l'effettivo numero di operazioni di I/O al secondo e la velocità effettiva del disco. In base a questi dati, Azure Migrate esegue il mapping del disco a un disco standard o premium di Azure.
      - Se il servizio non riesce a trovare un disco con le operazioni di I/O al secondo e la velocità effettiva necessarie, contrassegna il computer come non adatto per Azure.
      - Se trova un set di dischi adatti, Azure Migrate seleziona quelli che supportano il metodo di ridondanza dell'archiviazione e il percorso specificato nelle impostazioni di valutazione.
      - Se sono presenti più dischi idonei, viene selezionato quello con il costo più basso.
- **Velocità effettiva del disco di archiviazione**: sono disponibili [altre informazioni](../azure-subscription-service-limits.md#storage-limits) sulle limitazioni di Azure per disco e macchina virtuale.
- **Tipo di disco**: Azure Migrate supporta solo i dischi gestiti.
- **Controllo di rete**: Azure Migrate prova a trovare una macchina virtuale di Azure in grado di supportare il numero di NIC nel computer locale.
    - A questo scopo, aggrega i dati trasmessi al secondo (Mbps) dal computer (rete in uscita) in tutti i NIC e applica il fattore di comfort al numero aggregato. Questo numero viene usato per trovare una macchina virtuale di Azure in grado di supportare le prestazioni di rete necessarie.
    - Azure Migrate accetta le impostazioni di rete della macchina virtuale presupponendo che la rete sia all'esterno del data center.
    - Se non sono disponibili dati relativi alle prestazioni di rete, per le dimensioni della macchina virtuale viene preso in considerazione solo il numero di NIC.
- **Controllo del calcolo**: dopo aver calcolato i requisiti di archiviazione e di rete, Azure Migrate considera i requisiti di calcolo:
    - Se sono disponibili i dati sulle prestazioni della macchina virtuale, il servizio considera la memoria e i core utilizzati e applica il fattore di comfort. In base al numero risultante, prova a trovare le dimensioni adatte di una macchina virtuale in Azure.
    - Se non si trovano dimensioni adatte, il computer viene contrassegnato come non idoneo per Azure.
    - Se si trovano dimensioni adatte, Azure Migrate esegue i calcoli di archiviazione e di rete. Applica quindi le impostazioni relative alla posizione e al piano tariffario per l'indicazione finale delle dimensioni della macchina virtuale.


## <a name="monthly-cost-estimation"></a>Stima dei costi mensili

Dopo aver fornito tutte le indicazioni relative alle dimensioni, Azure Migrate calcola i costi di calcolo e archiviazione post-migrazione.

- **Costo di calcolo**: sulla base delle dimensioni consigliate della macchina virtuale di Azure, Azure Migrate usa l'API di fatturazione per calcolare i costi mensili della macchina virtuale. Per il calcolo vengono prese in considerazione le impostazioni relative al sistema operativo, al programma Software Assurance, alla posizione e alla valuta. Per calcolare il costo di calcolo mensile totale vengono aggregati i costi di tutti i computer.
- **Costo di archiviazione**: il costo di archiviazione mensile di un computer viene calcolato aggregando il costo mensile di tutti i dischi collegati al computer. Azure Migrate calcola i costi di archiviazione mensili totali aggregando i costi di archiviazione di tutti i computer. Attualmente, il calcolo non prende in considerazione le offerte specificate nelle impostazioni di valutazione.

I costi vengono visualizzati nella valuta specificata nelle impostazioni di valutazione. 


## <a name="next-steps"></a>Passaggi successivi

[Individuare e valutare le macchine virtuali VMware locali per la migrazione ad Azure](tutorial-assessment-vmware.md)
