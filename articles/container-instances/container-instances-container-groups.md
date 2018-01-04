---
title: Gruppi di contenitori in Istanze di contenitore di Azure
description: Informazioni sul funzionamento di gruppi di contenitori in Istanze di contenitore di Azure
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 12/19/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: b4a0af8fffd3ce012bf9addeec7029884d4ccf25
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="container-groups-in-azure-container-instances"></a>Gruppi contenitore in istanze di contenitori di Azure

La risorsa di primo livello in istanze di contenitori di Azure è il *gruppo contenitore*. Questo articolo descrive i gruppi contenitore e i tipi di scenari consentiti.

## <a name="how-a-container-group-works"></a>Come funziona un gruppo di contenitori

Un gruppo contenitore è una raccolta di contenitori che pianificazione nello stesso computer host. I contenitori in un gruppo contenitore condividono un ciclo di vita, rete locale e i volumi di archiviazione. È simile al concetto di un *pod* in [Kubernetes] [ kubernetes-pod] e [DC/OS][dcos-pod].

Il diagramma seguente mostra un esempio di un gruppo che include più contenitori.

![Diagramma dei gruppi contenitore][container-groups-example]

Questo gruppo di contenitore di esempio:

* Viene pianificata su un computer singolo host.
* Espone un singolo indirizzo IP pubblico, con una porta esposto.
* È costituito da due contenitori. Un contenitore è in ascolto sulla porta 80, mentre l'altro è in ascolto sulla porta 5000.
* Sono inclusi due condivisioni di file di Azure come volume mount e ogni contenitore Monta una delle condivisioni in locale.

### <a name="networking"></a>Rete

I gruppi di contenitori condividono un indirizzo IP e uno spazio dei nomi di porta su tale indirizzo IP. Per consentire a client esterni di raggiungere un contenitore all'interno del gruppo, è necessario esporre la porta sull'indirizzo IP e dal contenitore. Poiché i contenitori all'interno del gruppo condividono uno spazio dei nomi di porta, il mapping delle porte non è supportato. I contenitori all'interno di un gruppo possono raggiungersi tramite localhost sulle porte che hanno esposto, anche se queste porte non sono esposte esternamente sull'indirizzo IP del gruppo.

### <a name="storage"></a>Archiviazione

È possibile impostare il montaggio di volumi esterni all'interno di un gruppo di contenitori ed eseguire il mapping di tali volumi in percorsi specifici all'interno dei singoli contenitori di un gruppo.

## <a name="common-scenarios"></a>Scenari comuni

I gruppi di più contenitori sono utili nei casi in cui si vuole dividere una singola attività funzionale in un numero ridotto di immagini di contenitore, che possono essere distribuite da team diversi e hanno requisiti separati in termini di risorse.

Un esempio di utilizzo può includere gli elementi seguenti:

* Un contenitore di applicazione e un contenitore di registrazione. Il contenitore di registrazione raccoglie l'output dei log e delle metriche generato dall'applicazione principale e lo scrive in una risorsa di archiviazione a lungo termine.
* Un contenitore di applicazione e uno di monitoraggio. Il contenitore di monitoraggio esegue periodicamente una richiesta all'applicazione per assicurarsi che sia in esecuzione e risponde correttamente e genera un avviso in caso contrario.
* Un contenitore che serve un'applicazione Web e un altro che esegue il pull del contenuto più recente dal controllo del codice sorgente.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [distribuire un gruppo di più contenitori](container-instances-multi-container-group.md) con un modello di Azure Resource Manager.

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/pod/
