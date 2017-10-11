---
title: Gruppi di contenitori in Istanze di contenitore di Azure
description: Informazioni sul funzionamento di gruppi di contenitori in Istanze di contenitore di Azure
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 25eab41c3f0c986bcce33123f86f4c9638b77191
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="container-groups-in-azure-container-instances"></a>Gruppi di contenitori in Istanze di contenitore di Azure

La risorsa di livello principale in Istanze di contenitore di Azure consiste in un gruppo di contenitori. Questo articolo descrive le caratteristiche dei gruppi di contenitori e i tipi di scenari possibili.

## <a name="how-a-container-group-works"></a>Come funziona un gruppo di contenitori

Un gruppo di contenitori è una raccolta di contenitori che vengono pianificati nello stesso computer host e condividono un ciclo di vita, una rete locale e i volumi di archiviazione. Il concetto è simile a quello di *pod* in [Kubernetes](https://kubernetes.io/docs/concepts/workloads/pods/pod/) e [DC/OS](https://dcos.io/docs/1.9/deploying-services/pods/).

Il diagramma seguente mostra un esempio di un gruppo che include più contenitori.

![Esempio di un gruppo di contenitori][container-groups-example]

Si noti che:

- Il gruppo è pianificato su un singolo computer host.
- Il gruppo espone un singolo indirizzo IP pubblico, con una sola porta esposta.
- Il gruppo è costituito da due contenitori. Un contenitore è in ascolto sulla porta 80, mentre l'altro è in ascolto sulla porta 5000.
- Il gruppo include due condivisioni file di Azure come punti di montaggio di volume e ogni contenitore monta una delle due condivisioni in locale.

### <a name="networking"></a>Rete

I gruppi di contenitori condividono un indirizzo IP e uno spazio dei nomi di porta su tale indirizzo IP. Per consentire a client esterni di raggiungere un contenitore all'interno del gruppo, è necessario esporre la porta sull'indirizzo IP e dal contenitore. Poiché i contenitori all'interno del gruppo condividono uno spazio dei nomi di porta, il mapping delle porte non è supportato. I contenitori all'interno di un gruppo possono raggiungersi tramite localhost sulle porte che hanno esposto, anche se queste porte non sono esposte esternamente sull'indirizzo IP del gruppo.

### <a name="storage"></a>Archiviazione

È possibile impostare il montaggio di volumi esterni all'interno di un gruppo di contenitori ed eseguire il mapping di tali volumi in percorsi specifici all'interno dei singoli contenitori di un gruppo.

## <a name="common-scenarios"></a>Scenari comuni

I gruppi di più contenitori sono utili nei casi in cui si vuole dividere una singola attività funzionale in un numero ridotto di immagini di contenitore, che possono essere distribuite da team diversi e hanno requisiti separati in termini di risorse. Un esempio di utilizzo può includere gli elementi seguenti:

- Un contenitore di applicazione e un contenitore di registrazione. Il contenitore di registrazione raccoglie l'output dei log e delle metriche generato dall'applicazione principale e lo scrive in una risorsa di archiviazione a lungo termine.
- Un contenitore di applicazione e uno di monitoraggio. Il contenitore di monitoraggio invia periodicamente una richiesta all'applicazione per verificare che sia in esecuzione e risponda correttamente e genera un avviso nel caso in cui la verifica abbia esito negativo.
- Un contenitore che serve un'applicazione Web e un altro che esegue il pull del contenuto più recente dal controllo del codice sorgente.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [distribuire un gruppo di più contenitori](container-instances-multi-container-group.md) con un modello di Azure Resource Manager.

<!-- IMAGES -->

[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png