---
title: Monitoraggio degli schemi di varianza nelle macchine virtuali
description: Informazioni su come monitorare i modelli di varianza sulle macchine virtuali protette usando Azure Site Recovery
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 09/09/2020
ms.author: sharrai
ms.openlocfilehash: 51f45b5c0b8a2729f64eb376e06875719474cbfc
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89664182"
---
# <a name="monitoring-churn-patterns-on-virtual-machines"></a>Monitoraggio degli schemi di varianza nelle macchine virtuali

Questo articolo fornisce una panoramica dei vari strumenti che è possibile usare per monitorare i modelli di varianza in una macchina virtuale. Utilizzando gli strumenti appropriati, è facile scoprire esattamente quale applicazione sta causando una varianza elevata, quindi è possibile eseguire ulteriori azioni per l'applicazione.

## <a name="for-azure-virtual-machines-windows-or-linux"></a>Per macchine virtuali di Azure (Windows o Linux)

Se il computer è ospitato in Azure e usa un disco gestito o non gestito per l'archiviazione, è possibile tenere traccia delle prestazioni tenendo traccia della metrica del disco. In questo modo è possibile monitorare attentamente e fare in modo che la selezione del disco sia adatta al modello di utilizzo dell'applicazione. È anche possibile usarlo per creare avvisi, diagnosi e automazione della compilazione. [Altre informazioni](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/).

Una volta protetti i computer con Azure Site Recovery, è possibile monitorare i computer usando i log di monitoraggio di Azure e Log Analytics. [Altre informazioni](https://docs.microsoft.com/azure/site-recovery/monitor-log-analytics).

Sono disponibili anche alcuni strumenti specifici del sistema operativo che è possibile usare.

## <a name="for-windows-machines"></a>Per macchine Windows

Nel caso in cui si disponga di un computer, in locale o non in cui è in esecuzione il sistema operativo Windows, sono disponibili altri strumenti.

Oltre a controllare l'utilizzo del disco in Gestione attività, è sempre possibile fare riferimento a **monitoraggio risorse** e **Performance Monitor**. Questi strumenti sono già presenti nei computer Windows.

### <a name="resource-monitor"></a>Monitoraggio risorse

**Monitoraggio risorse** Visualizza le informazioni sull'utilizzo delle risorse hardware e software in tempo reale. Per eseguire Monitoraggio risorse in un computer Windows, seguire questa procedura:

1. Premere Win + R e digitare _resmon_.
1. Quando il resmon, ovvero Monitoraggio risorse, viene visualizzata la finestra passa alla scheda disco. Fornisce la vista seguente:

    ![Scheda disco Monitoraggio risorse](./media/monitoring-high-churn/resmon-disk-tab.png)

1. Questa scheda deve essere monitorata continuamente per un po' di tempo per ottenere un quadro chiaro. Nell'esempio precedente si noterà che _wmiprv.exe_ sta per essere varianza alta.

Una volta identificate le applicazioni che hanno causato una varianza elevata nel computer, è possibile intraprendere le azioni necessarie per affrontare la varianza relativa a tali applicazioni.

### <a name="performance-monitor"></a>Monitoraggio delle prestazioni

**Performance Monitor** monitora varie attività in un computer, ad esempio l'utilizzo della CPU o della memoria. Per eseguire Performance Monitor in un computer Windows, attenersi alla procedura seguente:

1. Premere Win + R e digitare _PerfMon_.
1. Quando PerfMon, ovvero performance monitor, viene visualizzata la finestra, viene visualizzata la seguente visualizzazione:

    ![Step1 di performance monitor](./media/monitoring-high-churn/perfmon-step1.png)

1. Espandere la cartella **strumenti di monitoraggio** a destra e fare clic su performance monitor. Verrà visualizzata la visualizzazione seguente che fornisce informazioni in tempo reale sulle prestazioni correnti:

    ![Performance Monitor 2 e 2](./media/monitoring-high-churn/perfmon-step1.png)

1. Questo grafico sta attualmente monitorando un solo monitoraggio, ovvero "% tempo processore", come indicato dalla tabella sotto il grafico. È possibile aggiungere altri elementi per il monitoraggio facendo clic sul pulsante **"+"** nella parte superiore dello strumento.
1. Di seguito è illustrato il modo in cui il monitoraggio delle prestazioni è simile al momento in cui vengono aggiunti altri contatori.

    ![Passaggio 3 di performance monitor](./media/monitoring-high-churn/perfmon-step3.png)

Altre informazioni su performance monitor sono disponibili [qui](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/administration/monitor-use-performance-monitor-collect-event-trace-data).

## <a name="for-linux-machines"></a>Per macchine Linux

Nel caso in cui si disponga di un computer, in locale o non in cui è in esecuzione il sistema operativo Linux, sono disponibili altri strumenti per monitorare i modelli di varianza.

### <a name="iotop"></a>Iotop

Uno degli strumenti più usati è _iotop_. Si tratta di un'utilità per la visualizzazione dell'attività del disco in tempo reale. Consente di elencare i processi che eseguono operazioni di I/O, insieme alla larghezza di banda del disco utilizzata.

Aprire il prompt dei comandi ed eseguire il comando `iotop` .

### <a name="iostat"></a>IoStat

IoStat è uno strumento semplice che raccoglie e Mostra le statistiche dei dispositivi di archiviazione di input e output di sistema. Questo strumento viene spesso usato per tracciare problemi di prestazioni dei dispositivi di archiviazione, tra cui dispositivi, dischi locali e dischi remoti.

Aprire il prompt dei comandi ed eseguire il comando `iostat` .

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come eseguire il monitoraggio con [monitoraggio di Azure](monitor-log-analytics.md).
