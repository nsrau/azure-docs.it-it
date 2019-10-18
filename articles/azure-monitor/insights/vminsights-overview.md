---
title: Descrizione di Monitoraggio di Azure per le macchine virtuali (anteprima) | Microsoft Docs
description: Monitoraggio di Azure per le macchine virtuali è una funzionalità di Monitoraggio di Azure che combina il monitoraggio dell'integrità e delle prestazioni del sistema operativo delle macchine virtuali di Azure, nonché l'individuazione automatica dei componenti e delle dipendenze delle applicazioni con altre risorse e mappa la comunicazione tra questi elementi. Questo articolo offre una panoramica.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2019
ms.author: magoedte
ms.openlocfilehash: 3569247ad560f985c041eeb11a7d274e15bfeb4c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515384"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>Descrizione di Monitoraggio di Azure per le macchine virtuali (anteprima)

Monitoraggio di Azure per le macchine virtuali consente di monitorare le macchine virtuali di Azure e i set di scalabilità di macchine virtuali su larga scala. Analizza le prestazioni e l'integrità delle macchine virtuali Windows e Linux, monitorando i processi e le dipendenze da altre risorse e processi esterni. 

Include il supporto per il monitoraggio delle prestazioni e delle dipendenze delle applicazioni per le macchine virtuali ospitate in locale o in un altro provider di servizi cloud. Tre funzionalità chiave offrono informazioni dettagliate:

- I **componenti logici delle VM di Azure e dei set di scalabilità di macchine virtuali che eseguono Windows e Linux**sono misurati in base ai criteri di integrità preconfigurati e inviano un avviso quando viene soddisfatta la condizione valutata.  

- **Grafici delle prestazioni di tendenza**predefiniti: Visualizza le metriche delle prestazioni principali dal sistema operativo della macchina virtuale guest.

- **Mappa delle dipendenze**: Visualizza i componenti interconnessi con la macchina virtuale da diversi gruppi di risorse e sottoscrizioni.  

Le funzionalità sono organizzate in tre prospettive:

- Settore sanitario
- Performance
- Mappa

>[!NOTE]
>Microsoft [ha recentemente annunciato modifiche](https://azure.microsoft.com/updates/upcoming-changes-for-azure-monitor-for-vms-as-we-prepare-for-ga) apportate alla funzionalità di integrità in base ai commenti ricevuti dai clienti dell'anteprima pubblica. Dato il numero di modifiche che verranno apportate, si smette di offrire la funzionalità di integrità per i nuovi clienti. I clienti esistenti possono continuare a utilizzare la funzionalità di integrità. Per altri dettagli, vedere le [domande frequenti sulla disponibilità generale](vminsights-ga-release-faq.md).  

L'integrazione con i log di Monitoraggio di Azure offre efficaci caratteristiche di aggregazione e filtro e la possibilità di eseguire analisi delle tendenze dei dati nel tempo. Un monitoraggio così completo dei carichi di lavoro non può essere realizzato solo con Monitoraggio di Azure o Mapping dei servizi.  

È possibile visualizzare questi dati in una singola macchina virtuale direttamente dalla macchina virtuale oppure si può usare Monitoraggio di Azure per una visualizzazione aggregata delle macchine virtuali. Questa visualizzazione è basata sulla prospettiva di ogni funzionalità:

- **Integrità**: le macchine virtuali sono correlate a un gruppo di risorse.
- **Mappa** e **prestazioni**: le macchine virtuali sono configurate per la segnalazione a una specifica area di lavoro log Analytics.

![Prospettiva di informazioni dettagliate della macchina virtuale nel portale di Azure](./media/vminsights-overview/vminsights-azmon-directvm-01.png)

Monitoraggio di Azure per le macchine virtuali è in grado di offrire disponibilità e prestazioni prevedibili delle applicazioni vitali. Identifica eventi critici del sistema operativo, colli di bottiglia delle prestazioni e problemi di rete. Monitoraggio di Azure per le macchine virtuali può anche essere d'aiuto per comprendere se un problema è correlato ad altre dipendenze.  

## <a name="data-usage"></a>Utilizzo dei dati

Quando si distribuisce Monitoraggio di Azure per le macchine virtuali, i dati raccolti dalle macchine virtuali vengono inseriti e archiviati in Monitoraggio di Azure. Le metriche relative ai criteri di integrità vengono archiviate in monitoraggio di Azure in un database di serie temporali, i dati sulle prestazioni e sulle dipendenze raccolti vengono archiviati in un'area di lavoro Log Analytics. Sulla base delle tariffe pubblicate nella [pagina dei prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/), la fatturazione di Monitoraggio di Azure per le macchine virtuali viene effettuata per:

- Dati inseriti e archiviati.
- Numero di serie temporali delle metriche di criteri di integrità monitorate.
- Regole di avviso create.
- Notifiche inviate. 

Le dimensioni del log variano in base alle lunghezze di stringa dei contatori delle prestazioni e possono aumentare con il numero di dischi logici e schede di rete allocate alla VM. Se è già disponibile un'area di lavoro ed è in corso la raccolta di questi contatori, non vengono applicati addebiti doppi. Se si usa già Mapping dei servizi, l'unico cambiamento riguarderà i dati di connessione aggiuntivi inviati a Monitoraggio di Azure.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sui requisiti e i metodi per il monitoraggio delle macchine virtuali, vedere [Distribuire Monitoraggio di Azure per le macchine virtuali](vminsights-enable-overview.md).
