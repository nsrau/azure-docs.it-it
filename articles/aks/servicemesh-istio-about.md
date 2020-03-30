---
title: Panoramica di Istio
description: Ottieni una panoramica di Istio
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 8518e30a54c2486abf84cd9ac026cc4dccb3fa84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593901"
---
# <a name="istio"></a>Istio

## <a name="overview"></a>Panoramica

[Istio][istio] è una rete di servizi completa, personalizzabile ed estensibile.

## <a name="architecture"></a>Architecture

Istio fornisce un piano dati composto da sidecar basati su [Envoy.][envoy-proxy] Questi proxy intelligenti controllano tutto il traffico di rete in e in uscita dalle app e dai carichi di lavoro mesh.

Il piano di controllo gestisce la configurazione, i criteri e i dati di telemetria tramite i [componenti][what-is-istio]seguenti:

- **Mixer** - Impone il controllo di accesso e i criteri di utilizzo. Raccoglie i dati di telemetria dai proxy inseriti in [Prometheus][prometheus].

- **Pilota:** fornisce criteri/configurazione per l'individuazione del servizio e la gestione del traffico per i proxy.

- **Citadel** - Fornisce funzionalità di identità e sicurezza che consentono mTLS tra i servizi.

- **Galley** - Astratti e fornisce la configurazione ai componenti.

Nel diagramma dell'architettura seguente viene illustrato l'interazione tra i vari componenti all'interno del piano dati e del piano di controllo.


![Panoramica dei componenti e dell'architettura Istio.](media/servicemesh/istio/about-architecture.png)


## <a name="selection-criteria"></a>Criteri di selezione

È importante comprendere e considerare le aree seguenti quando si valuta Istio per i carichi di lavoro:It's important to understand and consider the following areas when evaluating Istio for your workloads:

- [Obiettivi di progettazione](#design-goals)
- [Capabilities](#capabilities)
- [Scenari](#scenarios)


### <a name="design-goals"></a>Obiettivi della progettazione

I seguenti obiettivi di progettazione [guidano][design-goals] il progetto Istio:

- **Massimizza la trasparenza:** consente l'adozione con la quantità minima di lavoro per ottenere un valore reale dal sistema.

- **Estendibilità** - Deve essere in grado di crescere e adattarsi alle mutevoli esigenze.

- **Portabilità** - Esegui facilmente in diversi tipi di ambienti: cloud, locale.

- **Uniformità politica** - Coerenza nella definizione delle politiche tra varie risorse.


### <a name="capabilities"></a>Capabilities

Istio fornisce il seguente set di funzionalità:

- **Mesh** – gateway (multi-cluster), macchine virtuali (espansione mesh)

- **Gestione del traffico:** routing, divisione, timeout, interruttori di circuito, tentativi, ingresso, uscita

- **Criterio:** controllo dell'accesso, limite di frequenza, quota, adattatori criteri personalizzati

- **Sicurezza** – autenticazione (jwt), autorizzazione, crittografia (mTLS), CA esterna (HashiCorp Vault)

- **Osservabilità** – metriche dorate, specchio, tracciamento, adattatori personalizzati, prometeo, grafana

### <a name="scenarios"></a>Scenari

Istio è adatto e consigliato per i seguenti scenari:

- Richiedi estendibilità e rich set di funzionalità

- Espansione mesh per includere carichi di lavoro basati su VMMesh expansion to include VM based workloads

- Mesh del servizio multi-cluster

## <a name="next-steps"></a>Passaggi successivi

La documentazione seguente descrive come installare Istio nel servizio Kubernetes di Azure:The following documentation describes how you can install Istio on Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Installare Istio nel servizio Azure Kubernetes][istio-install]

È inoltre possibile esplorare ulteriormente i concetti di Istio e i modelli di distribuzione aggiuntivi:You can also further explore Istio concepts and additional deployment models:

- [Concetti di Istio][what-is-istio]
- [Modelli di distribuzione Istio][deployment-models]

<!-- LINKS - external -->
[istio]: https://istio.io
[what-is-istio]: https://istio.io/docs/concepts/what-is-istio/
[design-goals]: https://istio.io/docs/concepts/what-is-istio/#design-goals
[deployment-models]: https://istio.io/docs/concepts/deployment-models/

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[istio-install]: ./servicemesh-istio-install.md
