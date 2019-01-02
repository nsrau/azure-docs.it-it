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
ms.date: 11/07/2018
ms.author: magoedte
ms.openlocfilehash: 69aa2cbcaa6861b1d5c5c71769be2fb8046d9ea5
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53188508"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>Descrizione di Monitoraggio di Azure per le macchine virtuali (anteprima)

Monitoraggio di Azure per le macchine virtuali consente di monitorare le macchine virtuali di Azure e i set di scalabilità di macchine virtuali su larga scala. Il servizio analizza le prestazioni e l'integrità delle macchine virtuali Windows e Linux, monitorando i processi e le relative dipendenze da altre risorse e processi esterni. 

La soluzione Monitoraggio di Azure per le macchine virtuali include il supporto per il monitoraggio delle prestazioni e delle dipendenze delle applicazioni per le macchine virtuali ospitate in locale o in un altro provider di servizi cloud. Tre funzionalità chiave offrono informazioni dettagliate:

* **Componenti logici di macchine virtuali di Azure che eseguono Windows e Linux**: vengono misurati in base ai criteri di integrità preconfigurati e vengono generati avvisi quando viene soddisfatta la condizione valutata.  

* **Grafici di tendenza delle prestazioni predefiniti**: mostrano le metriche delle prestazioni principali del sistema operativo della macchina virtuale guest.

* **Mappa delle dipendenze**: mostra i componenti interconnessi con la macchina virtuale da più gruppi di risorse e sottoscrizioni.  

Le funzionalità sono organizzate in tre prospettive:

* Integrità
* Prestazioni
* Mappa

>[!NOTE]
>Attualmente, la funzionalità Integrità è offerta solo per le macchine virtuali e i set di scalabilità di macchine virtuali di Azure. Le funzionalità Prestazioni e Mappa supportano sia le macchine virtuali di Azure che le macchine virtuali ospitate nell'ambiente o in un altro provider di servizi cloud.

L'integrazione con Log Analytics offre efficaci caratteristiche di aggregazione e filtro e la possibilità di eseguire analisi delle tendenze dei dati nel tempo. Un monitoraggio così completo dei carichi di lavoro non può essere realizzato solo con Monitoraggio di Azure, Mapping dei servizi o Log Analytics.  

È possibile visualizzare questi dati in una singola macchina virtuale direttamente dalla macchina virtuale oppure si può usare Monitoraggio di Azure per una visualizzazione aggregata delle macchine virtuali. Questa visualizzazione è basata sulla prospettiva di ogni funzionalità:

* **Integrità**: le macchine virtuali correlate a un gruppo di risorse.
* **Mappa** e **Prestazioni**: le macchine virtuali configurate per inviare report a una specifica area di lavoro di Log Analytics.

![Prospettiva di informazioni dettagliate della macchina virtuale nel portale di Azure](./media/vminsights-overview/vminsights-azmon-directvm-01.png)

Azure DevOps può offrire disponibilità e prestazioni prevedibili delle applicazioni vitali. Identifica eventi critici del sistema operativo, colli di bottiglia delle prestazioni e problemi di rete. Può anche essere d'aiuto per comprendere se un problema è correlato ad altre dipendenze.  

## <a name="data-usage"></a>Utilizzo dei dati 

Quando si distribuisce Monitoraggio di Azure per le macchine virtuali, i dati raccolti dalle macchine virtuali vengono inseriti e archiviati in Monitoraggio di Azure. Sulla base delle tariffe pubblicate nella [pagina dei prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/), la fatturazione di Monitoraggio di Azure per le macchine virtuali viene effettuata per:
* Dati inseriti e archiviati.
* Numero di serie temporali delle metriche di criteri di integrità monitorate.
* Regole di avviso create.
* Notifiche inviate. 

Le dimensioni del log variano in base alle lunghezze delle stringhe dei contatori e possono aumentare con il numero di dischi logici e schede di rete. Se è già disponibile un'area di lavoro ed è in corso la raccolta di questi contatori, non vengono applicati addebiti doppi. Se si usa già Mapping dei servizi, l'unico cambiamento riguarderà i dati di connessione aggiuntivi inviati a Monitoraggio di Azure.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sui requisiti e i metodi per il monitoraggio delle macchine virtuali, vedere [Distribuire Monitoraggio di Azure per le macchine virtuali](vminsights-onboard.md).
