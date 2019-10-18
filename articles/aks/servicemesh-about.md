---
title: Informazioni sulle mesh del servizio
description: Ottenere una panoramica delle reti mesh dei servizi, dell'architettura e delle funzionalità e dei criteri da considerare quando si seleziona uno per la distribuzione.
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 226a4e5cf97be2e23ef13a95b80be07b7fbf5d7a
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530706"
---
# <a name="about-service-meshes"></a>Informazioni sulle mesh del servizio

Una rete mesh di servizi offre funzionalità come gestione del traffico, resilienza, criteri, sicurezza, identità avanzata e osservabilità ai carichi di lavoro. L'applicazione è disaccoppiata da queste funzionalità operative e la mesh dei servizi li sposta fuori dal livello dell'applicazione e fino al livello dell'infrastruttura.

## <a name="scenarios"></a>Scenari

Questi sono alcuni degli scenari che possono essere abilitati per i carichi di lavoro quando si usa una rete mesh di servizi:

- **Crittografare tutto il traffico nel cluster** : abilitare TLS reciproco tra i servizi specificati nel cluster. Questo può essere esteso all'ingresso e all'uscita al perimetro della rete. Fornisce un'opzione sicura per impostazione predefinita senza modifiche necessarie per il codice dell'applicazione e l'infrastruttura.

- **Canary e rollout** in più fasi: specificare le condizioni per un subset di traffico da indirizzare a un set di nuovi servizi nel cluster. Al completamento del test della versione Canary, rimuovere il routing condizionale e la fase aumentando gradualmente il% di tutto il traffico al nuovo servizio. Infine tutto il traffico verrà indirizzato al nuovo servizio.

- **Gestione del traffico e manipolazione** : creare criteri per un servizio che consentiranno di limitare tutto il traffico a una versione di un servizio da un'origine specifica. O un criterio che applica una strategia di ripetizione dei tentativi alle classi di errori tra i servizi specificati. Eseguire il mirroring del traffico in tempo reale alle nuove versioni dei servizi durante una migrazione o eseguire il debug dei problemi. Inserire gli errori tra i servizi in un ambiente di test per verificare la resilienza.

- **Osservabilità** : ottenere informazioni sul modo in cui i servizi sono connessi al traffico tra di essi. Ottenere metriche, log e tracce per tutto il traffico in cluster e in ingresso/uscita. Aggiungere funzionalità di traccia distribuita alle applicazioni.

## <a name="architecture"></a>Architecture

Una mesh del servizio è in genere costituita da un piano di controllo e dal piano dati.

Il **piano di controllo** dispone di un numero di componenti che supportano la gestione della mesh dei servizi. In questo modo sarà inclusa un'interfaccia di gestione che può essere un'interfaccia utente o un'API. Sono inoltre in genere presenti componenti che gestiscono la regola e le definizioni dei criteri che definiscono il modo in cui la mesh del servizio deve implementare funzionalità specifiche. Sono inoltre presenti componenti che gestiscono aspetti della sicurezza, ad esempio identità e certificati avanzati per mTLS. Anche le mesh dei servizi avranno in genere un componente di metrica o di osservabilità che raccoglie e aggrega le metriche e i dati di telemetria dai carichi di lavoro.

Il **piano dati** è in genere costituito da un proxy che viene inserito in modo trasparente come sidecar per i carichi di lavoro. Questo proxy è configurato per controllare tutto il traffico di rete all'interno e all'esterno del pod che contiene il carico di lavoro. Questo consente di configurare il proxy in modo da proteggere il traffico tramite mTLS, indirizzare dinamicamente il traffico, applicare criteri al traffico e raccogliere le metriche e le informazioni di traccia. 

![Architettura tipica di mesh di servizi](media/servicemesh/typical-architecture.png)

## <a name="capabilities"></a>Capabilities

Ognuna delle reti mesh dei servizi ha una scelta naturale e si concentra sul supporto di scenari specifici, ma in genere si noterà che la maggior parte implementa alcune delle funzionalità seguenti, se non tutte.

### <a name="traffic-management"></a>Gestione del traffico 

- **Protocollo** -Layer 7 (http, grpc)
- **Routing dinamico** -condizionale, ponderazione, mirroring
- **Resilienza** : timeout, tentativi, interruttori di circuito
- **Criteri** : controllo di accesso, limiti di velocità, quote
- **Test** -fault injection

### <a name="security"></a>Sicurezza

- **Crittografia** -MTLS, gestione certificati, CA esterna
- **Identità complessa** : SPIFFE o simile
- **AUTH** -autenticazione, autorizzazione

### <a name="observability"></a>Osservabilità

- **Metriche** -metriche dorate, Prometeo, grafana
- **Traccia** : tracce tra i carichi di lavoro
- **Traffico** -cluster, in ingresso/in uscita

### <a name="mesh"></a>Mesh

- Kubernetes di **calcolo supportati** , macchine virtuali
- Più **cluster** -gateway, Federazione

## <a name="selection-criteria"></a>Criteri di selezione

Prima di selezionare una mesh di servizi, assicurarsi di comprendere i requisiti e i motivi per l'installazione di una rete mesh di servizi. Provare a porre le domande seguenti.

- **Il controller di ingresso è sufficiente per le mie esigenze?** -A volte è sufficiente avere una funzionalità come test a/b o la suddivisione del traffico in ingresso è sufficiente per supportare lo scenario richiesto. Non aggiungere complessità all'ambiente senza rialzo.

- **I carichi di lavoro e l'ambiente possono tollerare gli overhead aggiuntivi?** -Tutti i componenti aggiuntivi necessari per supportare la mesh di servizi richiedono risorse aggiuntive, ad esempio CPU e memoria. Inoltre, tutti i proxy e i relativi criteri associati controllano l'aggiunta di latenza al traffico. Se si dispone di carichi di lavoro molto sensibili alla latenza o non è possibile fornire le risorse aggiuntive per coprire i componenti della mesh del servizio, riprendere in considerazione.

- **L'aggiunta di ulteriore complessità è inutilmente?** -Se il motivo per cui si installa una rete mesh di servizi è quello di ottenere una funzionalità non necessariamente cruciale per i team aziendali o operativi, valutare se la complessità aggiuntiva di installazione, manutenzione e configurazione valga la pena.

- **È possibile adottare questo approccio in modo incrementale?** -Alcune delle reti mesh dei servizi che forniscono numerose funzionalità possono essere adottate in un approccio più incrementale. Installare solo i componenti necessari per garantire l'esito positivo dell'operazione. Una volta che si è più sicuri e sono necessarie funzionalità aggiuntive, è possibile esaminarle. Resistere alla necessità di installare *tutto* dall'inizio.

Se, dopo un'attenta valutazione, si decide che è necessario disporre di una rete di servizi per fornire le funzionalità necessarie, quindi la decisione successiva è la *mesh dei servizi?*

Prendere in considerazione le aree seguenti e quali sono più allineate con i requisiti. In questo modo verrà illustrata la soluzione migliore per l'ambiente e i carichi di lavoro. La sezione [passaggi successivi](#next-steps) consente di passare ad altre informazioni dettagliate sulle mesh di servizi specifiche e sul modo in cui vengono mappate a queste aree.

- **Tecnica** -gestione del traffico, criteri, sicurezza, osservabilità

- Supporto **commerciale commerciale,** fondamento (CNCF), licenza OSS, governance

- **Operativo** : installazione/aggiornamenti, requisiti delle risorse, requisiti delle prestazioni, integrazioni (metriche, telemetria, dashboard, strumenti, SMI), carichi di lavoro misti (pool di nodi Linux e Windows), calcolo (Kubernetes, macchine virtuali), più cluster

- **Sicurezza** -autenticazione, identità, gestione dei certificati e rotazione, CA esterna collegabile


## <a name="next-steps"></a>Passaggi successivi

La documentazione seguente fornisce altre informazioni sulle mesh dei servizi che è possibile provare in Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Altre informazioni su Istio...][istio-about]

> [!div class="nextstepaction"]
> [Altre informazioni su Linkerd...][linkerd-about]

> [!div class="nextstepaction"]
> [Altre informazioni su Consul...][consul-about]

È anche possibile esplorare Service mesh Interface (SMI), un'interfaccia standard per le mesh dei servizi in Kubernetes:

- [SMI (Service mesh Interface)][smi]


<!-- LINKS - external -->
[smi]: https://smi-spec.io/

<!-- LINKS - internal -->
[istio-about]: ./servicemesh-istio-about.md
[linkerd-about]: ./servicemesh-linkerd-about.md
[consul-about]: ./servicemesh-consul-about.md