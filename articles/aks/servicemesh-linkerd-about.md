---
title: Panoramica di Linkerd
description: Ottenere una panoramica di Linkerd
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 9d0a65f2cf557120ec5a551494e697c876ad5c59
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530212"
---
# <a name="linkerd"></a>Linkerd

## <a name="overview"></a>Panoramica

[Linkerd][linkerd] è una rete di servizi facile da usare e leggero.

## <a name="architecture"></a>Architecture

Linkerd fornisce un piano dati costituito da sidecar di [Linkerd][linkerd-proxy] per proxy specialistici ultraleggeri. Questi proxy intelligenti controllano tutto il traffico di rete all'interno e all'esterno delle app e dei carichi di lavoro in rete. I proxy espongono anche le metriche tramite gli endpoint di metrica [Prometeo][prometheus] .

Il piano di controllo gestisce la configurazione e i dati di telemetria aggregati tramite i [componenti][linkerd-architecture]seguenti:

- **Controller** : fornisce un'API che guida l'interfaccia della riga di comando e il dashboard di Linkerd. Fornisce la configurazione per i proxy.

- **Toccare** -stabilisce gli orologi in tempo reale su richieste e risposte.

- **Identity** : fornisce le funzionalità di identità e sicurezza che consentono MTLS tra i servizi.

- **Web** : fornisce il dashboard di Linkerd.


Il diagramma dell'architettura seguente illustra il modo in cui i vari componenti all'interno del piano dati e del piano di controllo interagiscono.


![Panoramica dei componenti e dell'architettura di Linkerd.](media/servicemesh/linkerd/about-architecture.png)


## <a name="selection-criteria"></a>Criteri di selezione

Quando si valuta Linkerd per i carichi di lavoro, è importante comprendere e considerare le aree seguenti:

- [Principi di progettazione](#design-principles)
- [Capabilities](#capabilities)
- [Scenari](#scenarios)


### <a name="design-principles"></a>Principi di progettazione

I principi di progettazione seguenti [guidano][design-principles] il progetto Linkerd:

- **Semplicità** : deve essere facile da usare e comprendere.

- **Ridurre i requisiti delle risorse** : imporre prestazioni minime e costi delle risorse.

- **Basta lavorare** : non interrompere le applicazioni esistenti e non è necessaria una configurazione complessa.


### <a name="capabilities"></a>Capabilities

In Linkerd è disponibile il set di funzionalità seguente:

- **Mesh** : opzione di debug incorporata

- **Gestione del traffico** : suddivisione, timeout, tentativi, ingresso

- **Sicurezza** -crittografia (MTLS), certificati autoruotati ogni 24 ore

- **Osservabilità** : metriche finali, tap, traccia, profili dei servizi e metriche per Route, dashboard Web con grafici della topologia, Prometeo, grafana


### <a name="scenarios"></a>Scenari

Linkerd è particolarmente adatto e consigliato per gli scenari seguenti:

- Semplice da usare con solo il set di requisiti di funzionalità essenziali

- Bassa latenza, sovraccarico basso, concentrandosi sull'osservabilità e sulla semplice gestione del traffico


## <a name="next-steps"></a>Passaggi successivi

La documentazione seguente descrive come installare Linkerd in Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Installare Linkerd nel servizio Azure Kubernetes (AKS)][linkerd-install]

È anche possibile esplorare ulteriormente le funzionalità e l'architettura di Linkerd:

- [Funzionalità di Linkerd][linkerd-features]
- [Architettura Linkerd][linkerd-architecture]

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