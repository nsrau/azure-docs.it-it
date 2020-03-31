---
title: Panoramica del console
description: Ottieni una panoramica del Console
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: c518985b360fa3264bd5ac1e3fe76d61b2810b9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594210"
---
# <a name="consul"></a>Console

## <a name="overview"></a>Panoramica

[Consul][consul] è una soluzione di rete di servizi compatibile con più data center per connettere e proteggere i servizi tra piattaforme di runtime. [Connect][consul-features] è il componente che fornisce funzionalità mesh di servizio.

## <a name="architecture"></a>Architecture

Consul fornisce un piano dati composto da [sidecar][consul-sidecar] basati su [envoy][envoy-proxy]per impostazione predefinita. Il console ha un'architettura proxy collegabile. Questi proxy intelligenti controllano tutto il traffico di rete in e in uscita dalle app e dai carichi di lavoro mesh.

Il piano di controllo gestisce la configurazione e i criteri tramite i seguenti [componenti:][consul-architecture]

- **Server** - Un agente console in esecuzione in modalità Server che mantiene lo stato del cluster Consul.

- **Client** - Un agente console in esecuzione in modalità client leggera. Ogni nodo di calcolo deve avere un agente client in esecuzione. Questa configurazione e i criteri dei broker client tra i carichi di lavoro e la configurazione Consul. 

Nel diagramma dell'architettura seguente viene illustrato l'interazione tra i vari componenti all'interno del piano dati e del piano di controllo.

![Panoramica dei componenti e dell'architettura del console.](media/servicemesh/consul/about-architecture.png)


## <a name="selection-criteria"></a>Criteri di selezione

È importante comprendere e considerare le aree seguenti quando si valuta Conconsole per i carichi di lavoro:It's important to understand and consider the following areas when evaluating Consul for your workloads:

- [Principi console](#consul-principles)
- [Capabilities](#capabilities)
- [Scenari](#scenarios)


### <a name="consul-principles"></a>Principi del console

I seguenti principi [guidano][consul-principles] il progetto Consul:

- **Basato su API** - Codificare tutte le configurazioni e i criteri.

- **Esegui e connetti ovunque:** connetti i carichi di lavoro su piattaforme di runtime (Kubernetes, VM, Serverless).

- **Estendi e integra:** connetti in modo sicuro i carichi di lavoro nell'infrastruttura.


### <a name="capabilities"></a>Capabilities

Consul fornisce il seguente set di funzionalità:

- **Mesh** – gateway (multi data center), macchine virtuali (nodi fuori cluster), sincronizzazione del servizio, opzione di debug integrata

- **Proxy:** inviato, proxy integrato, collegabile, proxy l4 disponibile per i carichi di lavoro di Windows

- **Gestione del traffico** – routing, divisione, risoluzione

- **Politica** – intenzioni, ACL

- **Sicurezza:** autorizzazione, autenticazione, crittografia, identità basate su SPIFFE, CA esterna (Vault), gestione dei certificati e rotazione

- **Osservabilità** – metriche, dashboard ui, prometeo, grafana


### <a name="scenarios"></a>Scenari

Console è adatto e consigliato per i seguenti scenari:

- Estensione dei carichi di lavoro connessi al console esistenteExtending existing Consul connected workloads

- Requisiti di conformità relativi alla gestione dei certificati

- Rete mesh del servizio cluster multicluster

- Carichi di lavoro basati su macchine virtuali da includere nella rete del servizioVM-based workloads to be included in the service mesh



## <a name="next-steps"></a>Passaggi successivi

La documentazione seguente descrive come installare Consul nel servizio Azure Kubernetes (AKS):The following documentation describes how you can install Consul on Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Installare Console nel servizio Azure Kubernetes (AKS)Install Consul in Azure Kubernetes Service (AKS)][consul-install]

È inoltre possibile esplorare ulteriormente le caratteristiche e l'architettura di Consul:

- [Caratteristiche del console][consul-features]
- [Architettura console][consul-architecture]
- [Console - Come funziona Connect][consul-how-connect-works]

<!-- LINKS - external -->
[consul]: https://www.consul.io/mesh.html
[consul-features]: https://www.consul.io/docs/connect/index.html
[consul-architecture]: https://www.consul.io/docs/internals/architecture.html
[consul-sidecar]: https://www.consul.io/docs/connect/proxies.html
[consul-how-connect-works]: https://www.consul.io/docs/connect/connect-internals.html
[consul-principles]: https://www.consul.io/

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[consul-install]: ./servicemesh-consul-install.md
