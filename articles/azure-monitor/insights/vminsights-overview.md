---
title: Descrizione di Monitoraggio di Azure per le macchine virtuali
description: Panoramica di Monitoraggio di Azure per le macchine virtuali che monitora l'integrità e le prestazioni delle macchine virtuali di Azure, oltre all'individuazione e mapping automatica dei componenti dell'applicazione e delle relative dipendenze.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/11/2020
ms.openlocfilehash: 6f65ba96f768a4b88aa13c073cc66920b8c5e049
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480488"
---
# <a name="what-is-azure-monitor-for-vms"></a>Descrizione di Monitoraggio di Azure per le macchine virtuali

Monitoraggio di Azure per le macchine virtuali consente di monitorare le macchine virtuali di Azure e i set di scalabilità di macchine virtuali su larga scala. Analizza le prestazioni e l'integrità delle macchine virtuali Windows e Linux, monitorando i processi e le dipendenze da altre risorse e processi esterni. Include il supporto per il monitoraggio delle prestazioni e delle dipendenze delle applicazioni per le macchine virtuali ospitate in locale o in un altro provider di cloud. Le seguenti caratteristiche chiave offrono approfondimenti:

- **Grafici predefiniti**delle prestazioni di tendenza: visualizzare le metriche delle prestazioni principali dal sistema operativo della macchina virtuale guest.

- **Mappa delle dipendenze:** visualizza i componenti interconnessi con la macchina virtuale di vari gruppi di risorse e sottoscrizioni.  

>[!NOTE]
>Recentemente abbiamo [annunciato le modifiche](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) che stiamo apportando alla funzione Salute in base al feedback che abbiamo ricevuto dai nostri clienti in anteprima pubblica. Dato il numero di modifiche che aplereremo, smetteremo di offrire la funzione Salute per i nuovi clienti. I clienti esistenti possono continuare a utilizzare la funzionalità di integrità. Per maggiori dettagli, consulta le nostre FAQ generali sulla [disponibilità.](vminsights-ga-release-faq.md)  

L'integrazione con i log di monitoraggio di Azure offre potenti aggregazioni e filtri, consentendo al monitoraggio di Azure per le macchine virtuali di analizzare le tendenze dei dati nel tempo. È possibile visualizzare questi dati direttamente in una singola macchina virtuale dalla macchina virtuale oppure usare Monitoraggio di Azure per offrire una visualizzazione aggregata delle macchine virtuali in cui la visualizzazione supporta le modalità di contesto delle risorse o dell'area di lavoro di Azure.You can view this data in a single VM from the virtual machine directly, or you can use Azure Monitor to deliver an aggregated view of your VMs where the view supports Azure resource-context or workspace-context modes. Per ulteriori informazioni, consultate [Panoramica delle modalità](../platform/design-logs-deployment.md#access-mode)di accesso.

![Prospettiva di informazioni dettagliate della macchina virtuale nel portale di Azure](media/vminsights-overview/vminsights-azmon-directvm.png)

Monitoraggio di Azure per le macchine virtuali è in grado di offrire disponibilità e prestazioni prevedibili delle applicazioni vitali. Identifica i colli di bottiglia delle prestazioni e i problemi di rete e consente di comprendere se un problema è correlato ad altre dipendenze.  

## <a name="data-usage"></a>Utilizzo dei dati

Quando si distribuisce Monitoraggio di Azure per le macchine virtuali, i dati raccolti dalle macchine virtuali vengono inseriti e archiviati in Monitoraggio di Azure. I dati sulle prestazioni e sulle dipendenze raccolti vengono archiviati in un'area di lavoro di Log Analytics.Performance and dependency data collected are stored in a Log Analytics workspace. Sulla base delle tariffe pubblicate nella [pagina dei prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/), la fatturazione di Monitoraggio di Azure per le macchine virtuali viene effettuata per:

- Dati inseriti e archiviati.
- Regole di avviso create.
- Notifiche inviate. 

La dimensione del log varia in base alla lunghezza delle stringhe dei contatori delle prestazioni e può aumentare con il numero di dischi logici e schede di rete allocate alla macchina virtuale. Se è già disponibile un'area di lavoro ed è in corso la raccolta di questi contatori, non vengono applicati addebiti doppi. Se si usa già la mappa del servizio, l'unica modifica visualizzata sono i dati di connessione aggiuntivi inviati a Monitoraggio di Azure.If you're already using Service Map, the only you'll see is the additional connection data that's sent to Azure Monitor.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sui requisiti e i metodi per il monitoraggio delle macchine virtuali, vedere [Distribuire Monitoraggio di Azure per le macchine virtuali](vminsights-enable-overview.md).
