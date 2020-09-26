---
title: Monitorare i log con la cartella di lavoro del firewall di Azure
description: Le cartelle di lavoro di Azure firewall forniscono un'area di disegno flessibile per l'analisi dei dati del firewall di Azure e la creazione di report visivi avanzati all'interno del portale di Azure.
services: firewall
author: gopimsft
ms.service: firewall
ms.topic: how-to
ms.date: 09/22/2020
ms.author: victorh
ms.openlocfilehash: dabe154535d2bd3ae5550d4bbbafa75adc5ae2c0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91344559"
---
# <a name="monitor-logs-using-azure-firewall-workbook"></a>Monitorare i log con la cartella di lavoro del firewall di Azure

La cartella di lavoro del firewall di Azure offre un'area di disegno flessibile per l'analisi dei dati del firewall È possibile usarlo per creare report visivi avanzati all'interno del portale di Azure. Puoi accedere a più firewall distribuiti in Azure e combinarli in esperienze interattive unificate.

È possibile ottenere informazioni dettagliate sugli eventi del firewall di Azure, informazioni sulle regole di applicazione e di rete e visualizzare statistiche per le attività del firewall tra URL, porte e indirizzi. La cartella di lavoro del firewall di Azure consente di filtrare i firewall e i gruppi di risorse e di filtrare dinamicamente per categoria con set di dati di facile lettura quando si esamina un problema nei log. 

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario [abilitare la registrazione diagnostica](firewall-diagnostics.md#enable-diagnostic-logging-through-the-azure-portal) tramite il portale di Azure. Vedere anche [log e metriche del firewall di Azure](logs-and-metrics.md) per una panoramica dei log di diagnostica e delle metriche disponibili per il firewall di Azure.

## <a name="get-started"></a>Introduzione

Per distribuire la cartella di lavoro, passare alla [cartella di lavoro di monitoraggio di Azure per il firewall di Azure](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20Firewall/Azure%20Monitor%20Workbook) e seguire le istruzioni nella pagina. La cartella di lavoro del firewall di Azure è progettata per funzionare tra multi-tenant, più sottoscrizioni ed è filtrabile in più firewall.

## <a name="overview-page"></a>Pagina di panoramica

La pagina Overview consente di filtrare tra aree di lavoro, ora e firewall. Mostra gli eventi in base all'ora tra i firewall e i tipi di log (applicazione, reti, Intel per le minacce, proxy DNS).

:::image type="content" source="./media/firewall-workbook/firewall-workbook-overview.png" alt-text="Panoramica di cartella di lavoro del firewall di Azure":::

## <a name="application-rule-log-statistics"></a>Statistiche log delle regole dell'applicazione

Questa pagina Mostra origini univoche di indirizzi IP nel tempo, utilizzo dei conteggi delle regole dell'applicazione, FQDN negato/consentito nel tempo e dati filtrati. È possibile filtrare i dati in base all'indirizzo IP.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-application-rule.png" alt-text="Log delle regole dell'applicazione cartella di lavoro del firewall di Azure":::

## <a name="network-rule-log-statistics"></a>Statistiche log della regola di rete

Questa pagina fornisce una visualizzazione in base all'azione della regola, ovvero Consenti/Nega, porta di destinazione per IP e DNAT nel tempo. È anche possibile filtrare in base all'azione, alla porta e al tipo di destinazione.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-network-rule.png" alt-text="Log delle regole di rete della cartella di lavoro firewall di Azure":::

È anche possibile filtrare i log in base all'intervallo di tempo:

:::image type="content" source="./media/firewall-workbook/firewall-workbook-network-rule-time.png" alt-text="Finestra temporale log della regola di rete cartella di lavoro del firewall di Azure":::

## <a name="investigations"></a>Indagini

È possibile esaminare i log e ottenere ulteriori informazioni sulla risorsa in base all'indirizzo IP di origine. È possibile ottenere informazioni come il nome della macchina virtuale e il nome dell'interfaccia di rete. È semplice filtrare la risorsa dai log.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-investigation.png" alt-text="Analisi cartella di lavoro del firewall di Azure":::

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [diagnostica firewall di Azure](firewall-diagnostics.md)