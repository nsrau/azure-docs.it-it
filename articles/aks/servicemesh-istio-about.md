---
title: Panoramica di Istio
description: Ottenere una panoramica di Istio
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 3db2a3d75df08e7c668b1983ad9b4493446475df
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530511"
---
# <a name="istio"></a>Istio

## <a name="overview"></a>Panoramica

[Istio][istio] è una mesh di servizi con funzionalità complete, personalizzabile ed estendibile.

## <a name="architecture"></a>Architecture

Istio fornisce un piano dati composto da sidecar basati su [invio][envoy-proxy]. Questi proxy intelligenti controllano tutto il traffico di rete all'interno e all'esterno delle app e dei carichi di lavoro in rete.

Il piano di controllo gestisce la configurazione, i criteri e i dati di telemetria tramite i [componenti][what-is-istio]seguenti:

- **Mixer** : applica i criteri di controllo di accesso e di utilizzo. Raccoglie i dati di telemetria dai proxy di cui viene effettuato il push in [Prometheus][prometheus].

- **Progetto pilota** : fornisce la configurazione e i criteri di individuazione dei servizi e gestione del traffico per i proxy.

- **Citadel** : fornisce funzionalità di identità e sicurezza che consentono MTLS tra i servizi.

- **Cambusa** -abstracts e fornisce la configurazione ai componenti.

Il diagramma dell'architettura seguente illustra il modo in cui i vari componenti all'interno del piano dati e del piano di controllo interagiscono.


![Panoramica dei componenti e dell'architettura di Istio.](media/servicemesh/istio/about-architecture.png)


## <a name="selection-criteria"></a>Criteri di selezione

Quando si valuta Istio per i carichi di lavoro, è importante comprendere e considerare le aree seguenti:

- [Obiettivi di progettazione](#design-goals)
- [Capabilities](#capabilities)
- [Scenari](#scenarios)


### <a name="design-goals"></a>Obiettivi della progettazione

Gli obiettivi di progettazione seguenti [guidano][design-goals] il progetto Istio:

- **Massimizza la trasparenza** : consente l'adozione con la quantità minima di lavoro per ottenere un valore reale dal sistema.

- **Estensibilità** : deve essere in grado di crescere e adattarsi alle esigenze mutevoli.

- **Portabilità** : è possibile eseguire facilmente in diversi tipi di ambienti: cloud, locale.

- **Uniformità dei criteri** : coerenza nella definizione dei criteri in diverse risorse.


### <a name="capabilities"></a>Capabilities

In Istio è disponibile il set di funzionalità seguente:

- **Mesh** : Gateway (multicluster), macchine virtuali (espansione mesh)

- **Gestione del traffico** -routing, suddivisione, timeout, interruttori, tentativi, ingresso, in uscita

- **Criteri** : controllo di accesso, limite di velocità, quota, adattatori di criteri personalizzati

- **Sicurezza** -autenticazione (JWT), autorizzazione, crittografia (MTLS), CA esterna (HashiCorp Vault)

- **Osservabilità** : metrica dorata, mirror, traccia, adapter personalizzati, Prometeo, grafana

### <a name="scenarios"></a>Scenari

Istio è particolarmente adatto e consigliato per gli scenari seguenti:

- Richiedi estendibilità e set completo di funzionalità

- Espansione mesh per includere i carichi di lavoro basati su VM

- Mesh di servizi con più cluster

## <a name="next-steps"></a>Passaggi successivi

La documentazione seguente descrive come installare Istio in Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Installare Istio nel servizio Azure Kubernetes (AKS)][istio-install]

È anche possibile esplorare ulteriormente i concetti relativi a Istio e altri modelli di distribuzione:

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
