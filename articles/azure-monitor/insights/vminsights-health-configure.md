---
title: Configurare il monitoraggio in Monitoraggio di Azure per le macchine virtuali integrità Guest (anteprima)
description: Viene descritto come modificare il monitoraggio predefinito per Monitoraggio di Azure per le macchine virtuali l'integrità Guest (anteprima) utilizzando l'portale di Azure.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/08/2020
ms.openlocfilehash: f41a43e76993a03554d32fc7f3ce3149848989a9
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686989"
---
# <a name="configure-monitoring-in-azure-monitor-for-vms-guest-health-preview"></a>Configurare il monitoraggio in Monitoraggio di Azure per le macchine virtuali integrità Guest (anteprima)
Monitoraggio di Azure per le macchine virtuali integrità Guest consente di visualizzare l'integrità di una macchina virtuale in base a quanto definito da un set di misurazioni delle prestazioni campionate a intervalli regolari. Questo articolo descrive come è possibile modificare il monitoraggio predefinito usando il portale di Azure. Vengono inoltre descritti i concetti fondamentali dei monitoraggi necessari per [la configurazione del monitoraggio tramite una regola di raccolta dati](vminsights-health-configure-dcr.md).

## <a name="open-monitor-configuration"></a>Apri configurazione monitoraggio
Aprire Monitoraggio configurazione bin il portale di Azure selezionando il monitoraggio e quindi la scheda **configurazione** .

[![Configurazione dei dettagli del monitoraggio](media/vminsights-health-overview/monitor-details-configuration.png)](media/vminsights-health-overview/monitor-details-configuration.png#lightbox)

## <a name="enable-or-disable-monitors"></a>Abilitare o disabilitare i monitoraggi
I monitoraggi unità e i monitoraggi aggregati dispongono di un'impostazione di **stato Health Monitor** che consente di abilitare o disabilitare il monitoraggio. Quando un monitoraggio è abilitato, viene visualizzato lo stato di integrità e viene usato per impostare l'integrità della macchina virtuale. Quando un monitoraggio è disabilitato, il relativo stato non viene calcolato e non viene usato per impostare l'integrità della macchina virtuale.

| Impostazione | Descrizione |
|:---|:---|
| Attivato | Il monitoraggio è abilitato indipendentemente dall'impostazione dell'elemento padre. |
| Disabled | Il monitoraggio è disabilitato indipendentemente dall'impostazione dell'elemento padre. |
| Uguale all'elemento padre | Il monitoraggio verrà abilitato o disabilitato a seconda dell'impostazione del padre. |

Quando un monitoraggio è disabilitato, i criteri vengono visualizzati come non disponibili, come illustrato nell'esempio seguente.

![Monitoraggio disabilitato](media/vminsights-health-configure/disabled-monitor.png)

## <a name="enable-or-disable-virtual-machine"></a>Abilitare o disabilitare la macchina virtuale
È possibile disabilitare il monitoraggio di una macchina virtuale per arrestare temporaneamente tutti i monitoraggi. È possibile disabilitare il monitoraggio per una macchina virtuale, ad esempio quando si esegue la manutenzione.

| Impostazione | Descrizione |
|:---|:---|
| Attivato  | Viene visualizzato lo stato di integrità del computer. |
| Disabled | Lo stato di integrità del computer viene visualizzato come **disabilitato**. Gli avvisi non vengono creati. |

## <a name="health-state-logic"></a>Logica dello stato di integrità
La logica dello stato di integrità per un monitoraggio unità definisce i criteri per l'impostazione dello stato di integrità. È possibile specificare il numero di Stati di integrità di un monitoraggio e la soglia per il calcolo dello stato di integrità.

![Criteri di integrità di esempio](media/vminsights-health-configure/sample-health-criteria.png)

I monitoraggi aggregati non specificano alcuna logica dello stato di integrità. Il rispettivo stato di integrità viene impostato dal monitoraggio unità in base al rollup dello stato.

I monitoraggi unità hanno ciascuno due o tre stati di integrità. Ognuno avrà sempre uno stato integro e, facoltativamente, uno stato di avviso, uno stato critico o entrambi. Per gli Stati di avviso e critici sono necessari criteri univoci che definiscono quando il monitoraggio è impostato su questo stato. Lo stato integro non ha criteri perché questo stato viene impostato quando i criteri per gli altri Stati non vengono soddisfatti.

Nell'esempio seguente, l'utilizzo della CPU è impostato sugli stati di integrità seguenti:

- Critico se è maggiore del 90%.
- Avviso se il valore di è maggiore o uguale al 80%.
- Integro se è inferiore al 80%. Questa condizione è implicita poiché si tratta della condizione in cui non si applica nessuna delle altre condizioni.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare i monitoraggi su larga scala usando le regole di raccolta dati.](vminsights-health-configure-dcr.md)