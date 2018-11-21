---
title: Come eseguire l'aggiornamento dell'agente di Monitoraggio di Azure per i contenitori (anteprima) | Microsoft Docs
description: Questo articolo descrive come aggiornare l'agente di Log Analytics usato da Monitoraggio di Azure per i contenitori.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2018
ms.author: magoedte
ms.openlocfilehash: fb45002d284bc28dfb7093f69cfac7aae0681e8d
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2018
ms.locfileid: "51628511"
---
# <a name="how-to-upgrade-the-azure-monitor-for-containers-preview-agent"></a>Come eseguire l'aggiornamento dell'agente di Monitoraggio di Azure per i contenitori (anteprima)
Monitoraggio di Azure per i contenitori usa una versione con contenitori dell'agente di Log Analytics per Linux. Quando viene rilasciata una nuova versione dell'agente, l'agente non viene aggiornato automaticamente nei cluster Kubernetes gestito ospitati nel servizio Kubernetes di Azure.

Questo articolo descrive il processo di aggiornamento dell'agente.

## <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>Aggiornamento dell'agente nel cluster Kubernetes monitorato
Il processo per aggiornare l'agente è costituito da due semplici passaggi. Il primo passaggio prevede la disabilitazione del monitoraggio con Monitoraggio di Azure per i contenitori tramite l'interfaccia della riga di comando di Azure.  Seguire i passaggi descritti nell'articolo [Disabilitare il monitoraggio](monitoring-container-insights-optout.md?toc=%2fazure%2fmonitoring%2ftoc.json#azure-cli). Usando l'interfaccia della riga di comando di Azure è possibile rimuovere l'agente dai nodi del cluster senza conseguenze per la soluzione e i dati corrispondenti archiviati nell'area di lavoro. 

>[!NOTE]
>Mentre si esegue questa attività di manutenzione, i nodi del cluster non inoltrano i dati raccolti e le viste Prestazioni non mostreranno i dati tra la rimozione dell'agente e l'installazione della nuova versione. 
>

Per installare la nuova versione dell'agente, seguire i passaggi descritti nell'articolo [Eseguire l'onboarding del monitoraggio](monitoring-container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json#enable-monitoring-using-azure-cli) usando l'interfaccia della riga di comando di Azure per completare questo processo.  

Dopo aver riabilitato il monitoraggio, possono essere necessari circa 15 minuti prima di poter visualizzare le metriche di integrità aggiornate per il cluster. 

## <a name="next-steps"></a>Passaggi successivi
Se si verificano problemi durante l'aggiornamento dell'agente, esaminare la [guida risoluzione dei problemi](monitoring-container-insights-troubleshoot.md) per il supporto.