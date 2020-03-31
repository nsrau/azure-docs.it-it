---
title: Panoramica di Linkerd
description: Ottenere una panoramica di Linkerd
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 3181be62a14ec1b3450bd181172b5323ca176427
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593768"
---
# <a name="linkerd"></a>Linkerd (Al linker)

## <a name="overview"></a>Panoramica

[Il linkerd][linkerd] è una mesh di servizio semplice e leggera.

## <a name="architecture"></a>Architecture

Linkerd fornisce un piano dati composto da sidecar proxy specializzati [ultraleggeri Linkerd.][linkerd-proxy] Questi proxy intelligenti controllano tutto il traffico di rete in e in uscita dalle app e dai carichi di lavoro mesh. I proxy espongono anche le metriche tramite gli endpoint delle metriche [Prometheus.The][prometheus] proxyies also expose metrics via Prometheus metrics endpoints.

Il piano di controllo gestisce la configurazione e la telemetria aggregata tramite i [componenti][linkerd-architecture]seguenti:

- **Controller** - Fornisce api che guida l'interfaccia della riga di comando e il dashboard di Linkerd. Fornisce la configurazione per i proxy.

- **Tocca** - Stabilisci orologi in tempo reale su richieste e risposte.

- **Identità:** fornisce funzionalità di identità e sicurezza che consentono l'mTLS tra i servizi.

- **Web** - Fornisce il dashboard del linker.


Nel diagramma dell'architettura seguente viene illustrato l'interazione tra i vari componenti all'interno del piano dati e del piano di controllo.


![Panoramica dei componenti e dell'architettura del linker.](media/servicemesh/linkerd/about-architecture.png)


## <a name="selection-criteria"></a>Criteri di selezione

È importante comprendere e considerare le aree seguenti durante la valutazione di Linkerd per i carichi di lavoro:It's important to understand and consider the following areas when evaluating Linkerd for your workloads:

- [Principi di progettazione](#design-principles)
- [Capabilities](#capabilities)
- [Scenari](#scenarios)


### <a name="design-principles"></a>Principi di progettazione

I seguenti principi di progettazione [guidano][design-principles] il progetto Linkerd:

- **Semplicità** - Deve essere facile da usare e capire.

- **Riduci i requisiti delle risorse** : impone prestazioni e costi delle risorse minimi.

- **Solo lavoro** - Non rompere le applicazioni esistenti e non richiedono una configurazione complessa.


### <a name="capabilities"></a>Capabilities

Linkerd fornisce il set di funzionalità seguente:

- **Mesh** – built in opzione di debug

- **Gestione del traffico:** suddivisione, timeout, tentativi, ingresso

- **Sicurezza** – crittografia (mTLS), certificati ruotati automaticamente ogni 24 ore

- **Osservabilità** – metriche dorate, tocco, traccia, profili di servizio e metriche per percorso, dashboard web con grafici di topologia, prometeo, grafana


### <a name="scenarios"></a>Scenari

Linkerd è adatto e consigliato per gli scenari seguenti:Linkerd is wellsuited to and suggested for the following scenarios:

- Semplice da usare con solo l'essenziale set di requisiti di capacità

- Bassa latenza, basso sovraccarico, con particolare attenzione all'osservabilità e alla semplice gestione del traffico


## <a name="next-steps"></a>Passaggi successivi

La documentazione seguente descrive come è possibile installare Linkerd nel servizio Azure Kubernetes (AKS):The following documentation describes how you can install Linkerd on Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Installare Linker nel servizio Azure Kubernetes (AKS)Install Linkerd in Azure Kubernetes Service (AKS)][linkerd-install]

È inoltre possibile esplorare ulteriormente le funzionalità e l'architettura del linker:You can also explore Linkerd features and architecture:

- [Funzionalità del linker][linkerd-features]
- [Architettura del linker][linkerd-architecture]

<!-- LINKS - external -->
[linkerd]: https://linkerd.io/2/overview/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-features]: https://linkerd.io/2/features/
[design-principles]: https://linkerd.io/2/design-principles/
[linkerd-proxy]: https://github.com/linkerd/linkerd2-proxy

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[linkerd-install]: ./servicemesh-linkerd-install.md