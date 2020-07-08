---
title: Panoramica di Consul
description: Ottenere una panoramica di Consul
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 77cb6a693c5a73d0498b0acf9bc4ad8cc9f8f32f
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773997"
---
# <a name="consul"></a>Consul

## <a name="overview"></a>Panoramica

[Consul][consul] è una soluzione di rete per servizi compatibile con più data center per connettere e proteggere i servizi tra piattaforme di runtime. [Connect][consul-features] è il componente che fornisce le funzionalità di mesh per i servizi.

## <a name="architecture"></a>Architecture

Per impostazione predefinita Consul fornisce un piano dati costituito da [sidecar][envoy-proxy] basati su [Envoy][consul-sidecar]. Console dispone di un'architettura proxy modulare. Questi proxy intelligenti controllano tutto il traffico di rete in ingresso e in uscita da carichi di lavoro e app con mesh.

Il piano di controllo gestisce la configurazione e i criteri tramite i [componenti][consul-architecture] seguenti:

- **Server**: un agente Consul in esecuzione in modalità server che gestisce lo stato del cluster Consul.

- **Client**: un agente Consul in esecuzione in modalità client leggera. In ogni nodo di calcolo deve essere presente un agente client in esecuzione. Questo client negozia la configurazione e i criteri tra i carichi di lavoro e la configurazione Consul. 

Il diagramma dell'architettura seguente illustra il modo in cui i vari componenti all'interno del piano dati e del piano di controllo interagiscono.

![Panoramica dei componenti e dell'architettura Consul.](media/servicemesh/consul/about-architecture.png)


## <a name="selection-criteria"></a>Criteri di selezione

Quando si valuta Consul per i carichi di lavoro, è importante comprendere e valutare i fattori seguenti:

- [Principi di Consul](#consul-principles)
- [Capabilities](#capabilities)
- [Scenari](#scenarios)


### <a name="consul-principles"></a>Principi di Consul

Il progetto Consul è [guidato][consul-principles] dai principi seguenti:

- **Basato su API**: tutti i criteri e la configurazione sono scritti nel codice.

- **Esecuzione e connessione ovunque**: connessione dei carichi di lavoro tra piattaforme di runtime (Kubernetes, macchine virtuali, serverless).

- **Estensione e integrazione**: connessione sicura dei carichi di lavoro all'interno dell'infrastruttura.


### <a name="capabilities"></a>Capabilities

Consul offre le funzionalità seguenti:

- **Mesh**: gateway (più data center), macchine virtuali (nodi esterni al cluster), sincronizzazione dei servizi, opzione di debug predefinita

- **Proxy**: Envoy, proxy predefinito, modulare, proxy l4 disponibile per i carichi di lavoro Windows

- **Gestione traffico**: routing, divisione, risoluzione

- **Criteri**: intenzioni, ACL

- **Sicurezza**: autorizzazione, autenticazione, crittografia, identità basate su SPIFFE, CA esterne (insieme di credenziali), gestione dei certificati e rotazione

- **Osservabilità**: metriche, dashboard dell'interfaccia utente, Prometheus, Grafana


### <a name="scenarios"></a>Scenari

Consul è particolarmente indicato e suggerito per gli scenari seguenti:

- Estensione dei carichi di lavoro connessi Consul esistenti

- Requisiti di conformità relativi alla gestione dei certificati

- Mesh dei servizi multicluster

- Carichi di lavoro basati su macchina virtuali da includere nella mesh dei servizi



## <a name="next-steps"></a>Passaggi successivi

La documentazione seguente illustra come installare Consul nel servizio Azure Kubernetes:

> [!div class="nextstepaction"]
> [Installare Consul nel servizio Azure Kubernetes][consul-install]

È anche possibile esplorare ulteriormente le funzionalità e l'architettura di Consul:

- [Esercitazioni introduttive su Consul][consul-getting-started]
- [Funzionalità di Consul][consul-features]
- [Architettura di Consul][consul-architecture]
- [Consul - Funzionamento del componente Connect][consul-how-connect-works]

<!-- LINKS - external -->
[consul]: https://www.consul.io/mesh.html
[consul-features]: https://www.consul.io/docs/connect/index.html
[consul-architecture]: https://www.consul.io/docs/internals/architecture.html
[consul-sidecar]: https://www.consul.io/docs/connect/proxies.html
[consul-how-connect-works]: https://www.consul.io/docs/connect/connect-internals.html
[consul-principles]: https://www.consul.io/
[consul-getting-started]:https://learn.hashicorp.com/consul?track=gs-consul-service-mesh#gs-consul-service-mesh

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[consul-install]: ./servicemesh-consul-install.md
