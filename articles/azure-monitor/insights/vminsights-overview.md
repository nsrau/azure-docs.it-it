---
title: Descrizione di Monitoraggio di Azure per le macchine virtuali
description: Panoramica di Monitoraggio di Azure per le macchine virtuali che monitora l'integrità e le prestazioni delle macchine virtuali di Azure, oltre a individuare ed eseguire automaticamente il mapping dei componenti dell'applicazione e delle relative dipendenze.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: f058ce1ae5b7328c6864684994a74f2fd118ca6e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85506991"
---
# <a name="what-is-azure-monitor-for-vms"></a>Descrizione di Monitoraggio di Azure per le macchine virtuali

Monitoraggio di Azure per le macchine virtuali monitora le macchine virtuali, i set di scalabilità di macchine virtuali e i computer Azure Arc su larga scala. Analizza le prestazioni e l'integrità delle macchine virtuali Windows e Linux, monitorando i processi e le dipendenze da altre risorse e processi esterni. Include il supporto per il monitoraggio delle prestazioni e delle dipendenze dell'applicazione per le macchine virtuali ospitate in locale o in un altro provider di servizi cloud. Le funzionalità principali seguenti offrono informazioni approfondite:

- **Grafici delle prestazioni di tendenza**predefiniti: Visualizza le metriche delle prestazioni principali dal sistema operativo della macchina virtuale guest.

- **Mappa delle dipendenze**: Visualizza i componenti interconnessi con la macchina virtuale da diversi gruppi di risorse e sottoscrizioni.  

>[!NOTE]
>Microsoft [ha recentemente annunciato le modifiche apportate](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) alla funzionalità di integrità in base ai commenti ricevuti dai clienti dell'anteprima pubblica. Dato il numero di modifiche che verranno apportate, si smette di offrire la funzionalità di integrità per i nuovi clienti. I clienti esistenti possono continuare a utilizzare la funzionalità di integrità. Per altri dettagli, vedere le [domande frequenti sulla disponibilità generale](vminsights-ga-release-faq.md).  

L'integrazione con i log di monitoraggio di Azure offre funzionalità di aggregazione e filtro potenti, consentendo Monitoraggio di Azure per le macchine virtuali analizzare le tendenze dei dati nel tempo. È possibile visualizzare i dati in una singola VM direttamente dalla macchina virtuale oppure è possibile usare monitoraggio di Azure per fornire una visualizzazione aggregata delle macchine virtuali in cui la visualizzazione supporta le modalità di contesto delle risorse o dell'area di lavoro di Azure. Per altre informazioni, vedere [Panoramica delle modalità di accesso](../platform/design-logs-deployment.md#access-mode).

![Prospettiva di informazioni dettagliate della macchina virtuale nel portale di Azure](media/vminsights-overview/vminsights-azmon-directvm.png)

Monitoraggio di Azure per le macchine virtuali è in grado di offrire disponibilità e prestazioni prevedibili delle applicazioni vitali. Identifica i colli di bottiglia delle prestazioni e i problemi di rete e può anche essere utile per capire se un problema è correlato ad altre dipendenze.  

## <a name="data-usage"></a>Utilizzo dei dati

Quando si distribuisce Monitoraggio di Azure per le macchine virtuali, i dati raccolti dalle macchine virtuali vengono inseriti e archiviati in Monitoraggio di Azure. I dati relativi alle prestazioni e alle dipendenze raccolti vengono archiviati in un'area di lavoro Log Analytics. Sulla base delle tariffe pubblicate nella [pagina dei prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/), la fatturazione di Monitoraggio di Azure per le macchine virtuali viene effettuata per:

- Dati inseriti e archiviati.
- Regole di avviso create.
- Notifiche inviate. 

Le dimensioni del log variano in base alle lunghezze di stringa dei contatori delle prestazioni e possono aumentare con il numero di dischi logici e schede di rete allocate alla VM. Se è già disponibile un'area di lavoro ed è in corso la raccolta di questi contatori, non vengono applicati addebiti doppi. Se si usa già Mapping dei servizi, l'unica modifica che si vedrà sono i dati aggiuntivi della connessione inviati a monitoraggio di Azure.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sui requisiti e i metodi per il monitoraggio delle macchine virtuali, vedere [Distribuire Monitoraggio di Azure per le macchine virtuali](vminsights-enable-overview.md).
