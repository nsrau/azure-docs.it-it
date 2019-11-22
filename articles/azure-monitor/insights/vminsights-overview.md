---
title: Descrizione di Monitoraggio di Azure per le macchine virtuali (anteprima) | Documentazione di Microsoft
description: Panoramica di Monitoraggio di Azure per le macchine virtuali che monitora l'integrità e le prestazioni delle macchine virtuali di Azure, oltre a individuare ed eseguire automaticamente il mapping dei componenti dell'applicazione e delle relative dipendenze.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/14/2019
ms.openlocfilehash: e9a3df0172f276800a3049675f6e858db7f0370c
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74307281"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>Descrizione di Monitoraggio di Azure per le macchine virtuali (anteprima)

Monitoraggio di Azure per le macchine virtuali consente di monitorare le macchine virtuali di Azure e i set di scalabilità di macchine virtuali su larga scala. Analizza le prestazioni e l'integrità delle macchine virtuali Windows e Linux, monitorando i processi e le dipendenze da altre risorse e processi esterni. 

Include il supporto per il monitoraggio delle prestazioni e delle dipendenze delle applicazioni per le macchine virtuali ospitate in locale o in un altro provider di servizi cloud. Le funzionalità principali seguenti offrono informazioni approfondite:

- **Grafici delle prestazioni di tendenza**predefiniti: Visualizza le metriche delle prestazioni principali dal sistema operativo della macchina virtuale guest.

- **Mappa delle dipendenze**: Visualizza i componenti interconnessi con la macchina virtuale da diversi gruppi di risorse e sottoscrizioni.  

>[!NOTE]
>Microsoft [ha recentemente annunciato le modifiche apportate](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) alla funzionalità di integrità in base ai commenti ricevuti dai clienti dell'anteprima pubblica. Dato il numero di modifiche che verranno apportate, si smette di offrire la funzionalità di integrità per i nuovi clienti. I clienti esistenti possono continuare a utilizzare la funzionalità di integrità. Per altri dettagli, vedere le [domande frequenti sulla disponibilità generale](vminsights-ga-release-faq.md).  

L'integrazione con i log di Monitoraggio di Azure offre efficaci caratteristiche di aggregazione e filtro e la possibilità di eseguire analisi delle tendenze dei dati nel tempo. Un monitoraggio così completo dei carichi di lavoro non può essere realizzato solo con Monitoraggio di Azure o Mapping dei servizi.  

È possibile visualizzare i dati in una singola VM direttamente dalla macchina virtuale oppure è possibile usare monitoraggio di Azure per fornire una visualizzazione aggregata delle macchine virtuali in cui la visualizzazione supporta le modalità di contesto delle risorse o dell'area di lavoro di Azure. Per altre informazioni, vedere [Panoramica delle modalità di accesso](../platform/design-logs-deployment.md#access-mode).

![Prospettiva di informazioni dettagliate della macchina virtuale nel portale di Azure](./media/vminsights-overview/vminsights-azmon-directvm.png)

Monitoraggio di Azure per le macchine virtuali è in grado di offrire disponibilità e prestazioni prevedibili delle applicazioni vitali. Identifica i colli di bottiglia delle prestazioni e i problemi di rete. Monitoraggio di Azure per le macchine virtuali può anche essere d'aiuto per comprendere se un problema è correlato ad altre dipendenze.  

## <a name="data-usage"></a>Utilizzo dei dati

Quando si distribuisce Monitoraggio di Azure per le macchine virtuali, i dati raccolti dalle macchine virtuali vengono inseriti e archiviati in Monitoraggio di Azure. I dati relativi alle prestazioni e alle dipendenze raccolti vengono archiviati in un'area di lavoro Log Analytics. Sulla base delle tariffe pubblicate nella [pagina dei prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/), la fatturazione di Monitoraggio di Azure per le macchine virtuali viene effettuata per:

- Dati inseriti e archiviati.
- Regole di avviso create.
- Notifiche inviate. 

Le dimensioni del log variano in base alle lunghezze di stringa dei contatori delle prestazioni e possono aumentare con il numero di dischi logici e schede di rete allocate alla VM. Se è già disponibile un'area di lavoro ed è in corso la raccolta di questi contatori, non vengono applicati addebiti doppi. Se si usa già Mapping dei servizi, l'unico cambiamento riguarderà i dati di connessione aggiuntivi inviati a Monitoraggio di Azure.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sui requisiti e i metodi per il monitoraggio delle macchine virtuali, vedere [Distribuire Monitoraggio di Azure per le macchine virtuali](vminsights-enable-overview.md).
