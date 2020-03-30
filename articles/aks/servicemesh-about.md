---
title: Informazioni sulle maglie di servizio
description: Ottenere una panoramica delle emagrifiche del servizio, la relativa architettura e funzionalità e i criteri da considerare quando si seleziona uno da distribuire.
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: eca49a3fac1ea0398ebe1d05bde20fbca3c81232
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594312"
---
# <a name="about-service-meshes"></a>Informazioni sulle maglie di servizio

Una rete mesh di servizio offre funzionalità come la gestione del traffico, la resilienza, i criteri, la sicurezza, l'identità avanzata e l'osservabilità dei carichi di lavoro. L'applicazione è disaccoppiata da queste funzionalità operative e la rete di servizio le sposta al di fuori del livello dell'applicazione e al livello dell'infrastruttura.

## <a name="scenarios"></a>Scenari

Di seguito sono riportati alcuni degli scenari che possono essere abilitati per i carichi di lavoro quando si usa una rete di servizi:These are some of the scenarios that can be enabled for your workloads when you use a service mesh:

- Crittografa tutto il **traffico nel cluster:** abilita Mutual TLS tra i servizi specificati nel cluster. Questo può essere esteso all'ingresso e all'uscita nel perimetro della rete. Fornisce un'opzione sicura per impostazione predefinita senza modifiche necessarie per il codice e l'infrastruttura dell'applicazione.

- **Implementazioni canariche e graduali:** specificare le condizioni per un sottoinsieme di traffico da instradare a un set di nuovi servizi nel cluster. Al termine del test del rilascio canarino, rimuovere il routing condizionale e la fase che aumenta gradualmente la percentuale di tutto il traffico verso il nuovo servizio. Alla fine tutto il traffico sarà indirizzato a un nuovo servizio.

- **Gestione e manipolazione del traffico:** creare un criterio in un servizio che limiterà tutto il traffico a una versione di un servizio da un'origine specifica. O un criterio che applica una strategia di ripetizione dei tentativi alle classi di errori tra i servizi specificati. Eseguire il mirroring del traffico in tempo reale verso nuove versioni dei servizi durante una migrazione o problemi di debug. Iniettare errori tra i servizi in un ambiente di test per testare la resilienza.

- **Osservabilità:** consente di ottenere informazioni dettagliate su come i servizi sono connessi al traffico che scorre tra di essi. Ottenere metriche, log e tracce per tutto il traffico nel cluster e in ingresso/uscita. Aggiungi funzionalità di tracciamento distribuito alle tue applicazioni.

## <a name="architecture"></a>Architecture

Una mesh di servizio è in genere composta da un piano di controllo e dal piano dati.

Il piano di **controllo** dispone di una serie di componenti che supportano la gestione della rete di servizio. Questo includerà in genere un'interfaccia di gestione che potrebbe essere un'interfaccia utente o un'API. In genere sono inoltre presenti componenti che gestiscono le definizioni delle regole e dei criteri che definiscono il modo in cui la mesh di servizio deve implementare funzionalità specifiche. Ci sono anche componenti che gestiscono aspetti della sicurezza come l'identità forte e i certificati per mTLS. Le meshe del servizio hanno anche in genere una metrica o un componente di osservabilità che raccoglie e aggrega metriche e dati di telemetria dai carichi di lavoro.

Il **piano dati** è in genere costituito da un proxy che viene iniettato in modo trasparente come sidecar per i carichi di lavoro. Questo proxy è configurato per controllare tutto il traffico di rete in e dentro e fuori dal pod contenente il carico di lavoro. Ciò consente di configurare il proxy per proteggere il traffico tramite mTLS, instradare dinamicamente il traffico, applicare criteri al traffico e raccogliere metriche e informazioni di tracciatura. 

![Architettura tipica della rete mesh di servizioTypical service mesh architecture](media/servicemesh/typical-architecture.png)

## <a name="capabilities"></a>Capabilities

Ognuna delle maglie del servizio ha un adattamento naturale e concentrarsi sul supporto di scenari specifici, ma in genere si noterà che la maggior parte implementerà un numero di, se non tutte, delle seguenti funzionalità.

### <a name="traffic-management"></a>Gestione del traffico 

- **Protocollo** – livello 7 (http, grpc)
- **Routing dinamico** – condizionale, ponderazione, mirroring
- **Resilienza** – timeout, tentativi, interruttori di circuito
- **Politica:** controllo degli accessi, limiti di frequenza, quote
- **Test** - iniezione di guasto

### <a name="security"></a>Security

- **Crittografia** : mTLS, gestione dei certificati, CA esterna
- **Identità forte** – SPIFFE o simili
- **Autenticazione** – autenticazione, autorizzazione

### <a name="observability"></a>Osservabilità

- **Metriche** – metriche dorate, prometeo, grafana
- **Traccia:** tracce tra carichi di lavoroTracing - traces across workloads
- **Traffico** – cluster, ingresso/uscita

### <a name="mesh"></a>Mesh

- **Calcolo supportato** - Kubernetes, macchine virtuali
- **Multi-cluster** - gateway, federazione

## <a name="selection-criteria"></a>Criteri di selezione

Prima di selezionare una rete mesh di servizio, assicurarsi di aver compreso i requisiti e i motivi dell'installazione di una rete di servizi. Prova a porre le seguenti domande.

- **Un controller in ingresso è sufficiente per le mie esigenze?** - A volte avere una funzionalità come a/b test o suddivisione del traffico in ingresso è sufficiente per supportare lo scenario richiesto. Non aggiungere complessità al tuo ambiente senza alcun vantaggio.

- **I carichi di lavoro e l'ambiente possono tollerare i costi generali aggiuntivi?** - Tutti i componenti aggiuntivi necessari per supportare la rete mesh di servizio richiedono risorse aggiuntive come cpu e memoria. Inoltre, tutti i proxy e i controlli dei criteri associati aggiungono latenza al traffico. Se si dispone di carichi di lavoro molto sensibili alla latenza o che non è possibile fornire le risorse aggiuntive per coprire i componenti mesh di servizio, è consigliabile riconsiderare.

- **Questo aggiunge ulteriore complessità inutilmente?** - Se il motivo per l'installazione di una rete mesh di servizio è quello di ottenere una capacità che non è necessariamente fondamentale per i team aziendali o operativi, quindi considerare se la complessità aggiuntiva di installazione, manutenzione e configurazione vale la pena.

- **Questo può essere adottato con un approccio incrementale?** - Alcune delle maglie del servizio che forniscono molte funzionalità possono essere adottate in un approccio più incrementale.- Some of the service meshes that provide a lot of capabilities can be adopted in a more incremental approach. Installa solo i componenti necessari per garantire il tuo successo. Una volta che si è più sicuri e sono necessarie funzionalità aggiuntive, quindi esplorare quelli. Resistere alla tentazione di installare *tutto* fin dall'inizio.

Se, dopo un'attenta considerazione, si decide che è necessario un reticolo di servizio per fornire le funzionalità necessarie, la decisione successiva è la rete mesh di *servizio?*

Considerare le seguenti aree e quali di esse sono più allineate con le proprie esigenze. Questo ti guiderà verso la soluzione migliore per il tuo ambiente e i tuoi carichi di lavoro. La sezione [Passaggi successivi](#next-steps) consente di ulteriori informazioni dettagliate sulle maglie di servizio specifiche e su come vengono mappate a queste aree.

- **Tecnico** - gestione del traffico, politica, sicurezza, osservabilità

- **Business** - supporto commerciale, fondazione (CNCF), licenza OSS, governance

- **Operativo:** installazione/aggiornamento, requisiti di risorse, requisiti di prestazioni, integrazioni (metriche, telemetria, dashboard, strumenti, SMI), carichi di lavoro misti (pool di nodi Linux e Windows), elaborazione (Kubernetes, macchine virtuali), multi-cluster

- **Sicurezza** - autenticazione, identità, gestione e rotazione dei certificati, CA esterna collegabile


## <a name="next-steps"></a>Passaggi successivi

La documentazione seguente fornisce ulteriori informazioni sulle maglie del servizio che è possibile provare nel servizio Azure Kubernetes (AKS):The following documentation provides more information about service meshes that you can try out on Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Scopri di più su Istio ...][istio-about]

> [!div class="nextstepaction"]
> [Ulteriori informazioni su Linkerd ...][linkerd-about]

> [!div class="nextstepaction"]
> [Scopri di più su Consul ...][consul-about]

È anche possibile esplorare Service Mesh Interface (SMI), un'interfaccia standard per le mesh di servizio su Kubernetes:

- [Interfaccia SMI (Service Mesh Interface)][smi]


<!-- LINKS - external -->
[smi]: https://smi-spec.io/

<!-- LINKS - internal -->
[istio-about]: ./servicemesh-istio-about.md
[linkerd-about]: ./servicemesh-linkerd-about.md
[consul-about]: ./servicemesh-consul-about.md