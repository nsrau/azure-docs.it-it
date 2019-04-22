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
ms.date: 03/13/2019
ms.author: magoedte
ms.openlocfilehash: a60413ee6614b638db58748ee2c0aea5d7ea32ea
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59697890"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>Descrizione di Monitoraggio di Azure per le macchine virtuali (anteprima)

Monitoraggio di Azure per le macchine virtuali consente di monitorare le macchine virtuali di Azure e i set di scalabilità di macchine virtuali su larga scala. Analizza le prestazioni e l'integrità delle macchine virtuali Windows e Linux, monitorando i processi e le dipendenze da altre risorse e processi esterni. 

La soluzione Monitoraggio di Azure per le macchine virtuali include il supporto per il monitoraggio delle prestazioni e delle dipendenze delle applicazioni per le macchine virtuali ospitate in locale o in un altro provider di servizi cloud. Tre funzionalità chiave offrono informazioni dettagliate:

* **Componenti logici di macchine virtuali di Azure che eseguono Windows e Linux**: vengono misurati in base ai criteri di integrità preconfigurati e vengono generati avvisi quando viene soddisfatta la condizione valutata.  

* **Predefiniti i grafici delle tendenze delle prestazioni**: mostrano le metriche delle prestazioni principali del sistema operativo della macchina virtuale guest.

* **Mappa delle dipendenze**: mostra i componenti interconnessi con la macchina virtuale da più gruppi di risorse e sottoscrizioni.  

Le funzionalità sono organizzate in tre prospettive:

* Integrità
* Prestazioni
* Mappa

>[!NOTE]
>Attualmente, la funzionalità di integrità è disponibile solo per macchine virtuali di Azure. Prestazioni e funzionalità di mappa supporta macchine virtuali di Azure, set di scalabilità di macchine Virtuali di Azure e macchine virtuali ospitate nell'ambiente o altro provider cloud.

L'integrazione con i log di Monitoraggio di Azure offre efficaci caratteristiche di aggregazione e filtro e la possibilità di eseguire analisi delle tendenze dei dati nel tempo. Un monitoraggio così completo dei carichi di lavoro non può essere realizzato solo con Monitoraggio di Azure o Mapping dei servizi.  

È possibile visualizzare questi dati in una singola macchina virtuale direttamente dalla macchina virtuale oppure si può usare Monitoraggio di Azure per una visualizzazione aggregata delle macchine virtuali. Questa visualizzazione è basata sulla prospettiva di ogni funzionalità:

* **Integrità**: le macchine virtuali correlate a un gruppo di risorse.
* **Mappa** e **Prestazioni**: le macchine virtuali configurate per inviare report a una specifica area di lavoro Log Analytics.

![Prospettiva di informazioni dettagliate della macchina virtuale nel portale di Azure](./media/vminsights-overview/vminsights-azmon-directvm-01.png)

Monitoraggio di Azure per le macchine virtuali è in grado di offrire disponibilità e prestazioni prevedibili delle applicazioni vitali. Identifica eventi critici del sistema operativo, colli di bottiglia delle prestazioni e problemi di rete. Monitoraggio di Azure per le macchine virtuali può anche essere d'aiuto per comprendere se un problema è correlato ad altre dipendenze.  

## <a name="data-usage"></a>Utilizzo dei dati 

Quando si distribuisce Monitoraggio di Azure per le macchine virtuali, i dati raccolti dalle macchine virtuali vengono inseriti e archiviati in Monitoraggio di Azure. Le metriche di criteri di integrità vengono archiviate in Monitoraggio di Azure in un database di serie temporali, prestazioni e delle dipendenze dei dati raccolti vengono archiviati in un'area di lavoro di Log Analitica. Sulla base delle tariffe pubblicate nella [pagina dei prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/), la fatturazione di Monitoraggio di Azure per le macchine virtuali viene effettuata per:

* Dati inseriti e archiviati.
* Numero di serie temporali delle metriche di criteri di integrità monitorate.
* Regole di avviso create.
* Notifiche inviate. 

Le dimensioni del log variano a seconda le lunghezze di stringa dei contatori delle prestazioni e aumenta con il numero di dischi logici e schede di rete allocate alla macchina virtuale. Se è già disponibile un'area di lavoro ed è in corso la raccolta di questi contatori, non vengono applicati addebiti doppi. Se si usa già Mapping dei servizi, l'unico cambiamento riguarderà i dati di connessione aggiuntivi inviati a Monitoraggio di Azure.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sui requisiti e i metodi per il monitoraggio delle macchine virtuali, vedere [Distribuire Monitoraggio di Azure per le macchine virtuali](vminsights-onboard.md).
