---
title: Limitare il traffico in uscita nel servizio Azure Kubernetes
description: Informazioni sulle porte e sugli indirizzi necessari per controllare il traffico in uscita nel servizio Azure Kubernetes
services: container-service
ms.topic: article
ms.author: jpalma
ms.date: 06/29/2020
author: palma21
ms.openlocfilehash: 6aed6c84439e65646c15367cdad3bf13c5573256
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85831694"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>Controllare il traffico in uscita per i nodi del cluster nel servizio Azure Kubernetes

Questo articolo fornisce i dettagli necessari che consentono di proteggere il traffico in uscita dal servizio Azure Kubernetes (AKS). Contiene i requisiti del cluster per una distribuzione di base AKS e requisiti aggiuntivi per le funzionalità e gli addons facoltativi. [Verrà fornito un esempio alla fine di come configurare questi requisiti con il firewall di Azure](#restrict-egress-traffic-using-azure-firewall). Tuttavia, è possibile applicare queste informazioni a qualsiasi dispositivo o metodo di restrizione in uscita.

## <a name="background"></a>Sfondo

I cluster AKS vengono distribuiti in una rete virtuale. Questa rete può essere gestita (creata da AKS) o personalizzata (precedentemente configurata dall'utente). In entrambi i casi, il cluster ha dipendenze in **uscita** da servizi esterni a tale rete virtuale (il servizio non ha dipendenze in ingresso).

A scopi di gestione e operativi, i nodi in un cluster del servizio Azure Kubernetes devono poter accedere a porte e nomi di dominio completi (FQDN) specifici. Questi endpoint sono necessari per la comunicazione tra i nodi e il server API oppure per scaricare e installare i componenti principali del cluster Kubernetes e gli aggiornamenti della sicurezza dei nodi. Ad esempio, il cluster deve eseguire il pull delle immagini del contenitore di sistema di base da Microsoft Container Registry ().

Le dipendenze in uscita AKS sono quasi completamente definite con i nomi di dominio completi, che non hanno indirizzi statici dietro di essi. La mancanza di indirizzi statici significa che non è possibile usare i gruppi di sicurezza di rete per bloccare il traffico in uscita da un cluster AKS. 

Per impostazione predefinita, i cluster del servizio Azure Kubernetes hanno accesso a Internet in uscita senza restrizioni. Questo livello di accesso alla rete consente a nodi e servizi in esecuzione di accedere alle risorse esterne in base alle esigenze. Se si vuole limitare il traffico in uscita, è necessario rendere accessibile un numero limitato di porte e indirizzi per mantenere l'integrità delle attività di manutenzione del cluster. La soluzione più semplice per la protezione degli indirizzi in uscita è l'uso di un dispositivo firewall che consente di controllare il traffico in uscita in base ai nomi di dominio. Il firewall di Azure, ad esempio, può limitare il traffico HTTP e HTTPS in uscita in base al nome di dominio completo (FQDN) della destinazione. È anche possibile configurare le regole del firewall e di sicurezza preferite per consentire le porte e gli indirizzi richiesti.

> [!IMPORTANT]
> Questo documento illustra solo come bloccare il traffico in uscita dalla subnet del servizio Azure Kubernetes. Per impostazione predefinita, AKS non prevede requisiti di ingresso.  Il blocco del **traffico della subnet interna** con i gruppi di sicurezza di rete (gruppi) e i firewall non è supportato. Per controllare e bloccare il traffico all'interno del cluster, usare i [***criteri di rete***][network-policy].

## <a name="required-outbound-network-rules-and-fqdns-for-aks-clusters"></a>Regole di rete in uscita obbligatorie e FQDN per i cluster AKS

Per un cluster AKS sono necessarie le seguenti regole di rete e di FQDN/applicazione, che possono essere usate per configurare una soluzione diversa dal firewall di Azure.

* Le dipendenze degli indirizzi IP sono per il traffico non HTTP/S (traffico sia TCP che UDP)
* Gli endpoint HTTP/HTTPS con nome di dominio completo possono essere posizionati nel dispositivo firewall.
* Gli endpoint HTTP/HTTPS con caratteri jolly sono dipendenze che possono variare con il cluster AKS in base a un numero di qualificatori.
* AKS usa un controller di ammissione per inserire il nome FQDN come variabile di ambiente per tutte le distribuzioni in Kube-System e Gatekeeper-System, che garantisce che tutte le comunicazioni di sistema tra i nodi e il server API utilizzino il nome di dominio completo dell'API del server e non l'indirizzo IP del server API. 
* Se si dispone di un'app o di una soluzione che deve comunicare con il server API, è necessario aggiungere una regola di rete **aggiuntiva** per consentire la *comunicazione TCP alla porta 443 dell'IP del server API*.
* In rari casi, se è presente un'operazione di manutenzione, l'indirizzo IP del server API potrebbe cambiare. Le operazioni di manutenzione pianificate che possono modificare l'IP del server API vengono sempre comunicate in anticipo.


### <a name="azure-global-required-network-rules"></a>Regole di rete obbligatorie globali di Azure

Le regole di rete e le dipendenze degli indirizzi IP richieste sono le seguenti:

| Endpoint di destinazione                                                             | Protocollo | Porta    | Uso  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *O* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *O* <br/> [CIDRs regionali](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *O* <br/> **`APIServerIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | Per la comunicazione protetta con tunnel tra i nodi e il piano di controllo. |
| **`*:9000`** <br/> *O* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *O* <br/> [CIDRs regionali](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *O* <br/> **`APIServerIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | Per la comunicazione protetta con tunnel tra i nodi e il piano di controllo. |
| **`*:123`** o **`ntp.ubuntu.com:123`** (se si usano le regole di rete del firewall di Azure)  | UDP      | 123     | Obbligatorio per la sincronizzazione dell'ora NTP (Network Time Protocol) nei nodi Linux.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Se si usano server DNS personalizzati, è necessario assicurarsi che siano accessibili dai nodi del cluster. |
| **`APIServerIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Obbligatorio se i pod/distribuzioni in esecuzione che accedono al server API, tali Pod/distribuzioni utilizzeranno l'IP dell'API.  |

### <a name="azure-global-required-fqdn--application-rules"></a>Regole dell'applicazione/FQDN globale obbligatoria di Azure 

Le regole delle applicazioni e FQDN seguenti sono obbligatorie:

| FQDN di destinazione                 | Porta            | Uso      |
|----------------------------------|-----------------|----------|
| **`*.hcp.<location>.azmk8s.io`** | **`HTTPS:443`** | Obbligatorio per la comunicazione del server di < di nodo > API. Sostituire *\<location\>* con l'area in cui è distribuito il cluster AKS. |
| **`mcr.microsoft.com`**          | **`HTTPS:443`** | Obbligatorio per accedere alle immagini in Microsoft Container Registry (e). Questo registro contiene immagini o grafici di terze parti, ad esempio coreDNS e così via. Queste immagini sono necessarie per la creazione e il funzionamento corrette del cluster, incluse le operazioni di scalabilità e aggiornamento.  |
| **`*.cdn.mscr.io`**              | **`HTTPS:443`** | Obbligatorio per l'archiviazione del sistema di archiviazione di Azure supportata dalla rete per la distribuzione di contenuti (CDN) di Azure. |
| **`*.data.mcr.microsoft.com`**   | **`HTTPS:443`** | Obbligatorio per l'archiviazione del sistema di archiviazione di Azure supportata dalla rete per la distribuzione di contenuti (CDN) di Azure. |
| **`management.azure.com`**       | **`HTTPS:443`** | Obbligatorio per le operazioni Kubernetes nell'API di Azure. |
| **`login.microsoftonline.com`**  | **`HTTPS:443`** | Obbligatorio per l'autenticazione Azure Active Directory. |
| **`packages.microsoft.com`**     | **`HTTPS:443`** | Questo indirizzo è il repository di pacchetti Microsoft usato per le operazioni *apt-get* memorizzate nella cache.  I pacchetti di esempio includono Moby, PowerShell e l'interfaccia della riga di comando di Azure. |
| **`acs-mirror.azureedge.net`**   | **`HTTPS:443`** | Questo indirizzo è per il repository necessario per scaricare e installare i file binari necessari, ad esempio kubenet e Azure CNI. |

### <a name="azure-china-21vianet-required-network-rules"></a>Regole di rete richieste da Azure Cina 21Vianet

Le regole di rete e le dipendenze degli indirizzi IP richieste sono le seguenti:

| Endpoint di destinazione                                                             | Protocollo | Porta    | Uso  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *O* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.Region:1194`** <br/> *O* <br/> [CIDRs regionali](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *O* <br/> **`APIServerIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | Per la comunicazione protetta con tunnel tra i nodi e il piano di controllo. |
| **`*:9000`** <br/> *O* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *O* <br/> [CIDRs regionali](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *O* <br/> **`APIServerIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | Per la comunicazione protetta con tunnel tra i nodi e il piano di controllo. |
| **`*:22`** <br/> *O* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:22`** <br/> *O* <br/> [CIDRs regionali](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:22`** <br/> *O* <br/> **`APIServerIP:22`** `(only known after cluster creation)`  | TCP           | 22      | Per la comunicazione protetta con tunnel tra i nodi e il piano di controllo. |
| **`*:123`** o **`ntp.ubuntu.com:123`** (se si usano le regole di rete del firewall di Azure)  | UDP      | 123     | Obbligatorio per la sincronizzazione dell'ora NTP (Network Time Protocol) nei nodi Linux.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Se si usano server DNS personalizzati, è necessario assicurarsi che siano accessibili dai nodi del cluster. |
| **`APIServerIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Obbligatorio se si eseguono Pod/distribuzioni che accedono al server API, tali Pod/distribuzioni utilizzeranno l'IP dell'API.  |

### <a name="azure-china-21vianet-required-fqdn--application-rules"></a>Azure Cina 21Vianet richieste FQDN/regole dell'applicazione

Le regole delle applicazioni e FQDN seguenti sono obbligatorie:

| FQDN di destinazione                               | Porta            | Uso      |
|------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | Obbligatorio per la comunicazione del server di < di nodo > API. Sostituire *\<location\>* con l'area in cui è distribuito il cluster AKS. |
| **`*.tun.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | Obbligatorio per la comunicazione del server di < di nodo > API. Sostituire *\<location\>* con l'area in cui è distribuito il cluster AKS. |
| **`mcr.microsoft.com`**                        | **`HTTPS:443`** | Obbligatorio per accedere alle immagini in Microsoft Container Registry (e). Questo registro contiene immagini o grafici di terze parti, ad esempio coreDNS e così via. Queste immagini sono necessarie per la creazione e il funzionamento corrette del cluster, incluse le operazioni di scalabilità e aggiornamento. |
| **`*.cdn.mscr.io`**                            | **`HTTPS:443`** | Obbligatorio per l'archiviazione del sistema di archiviazione di Azure supportata dalla rete per la distribuzione di contenuti (CDN) di Azure. |
| **`.data.mcr.microsoft.com`**                  | **`HTTPS:443`** | Obbligatorio per l'archiviazione del sistema di archiviazione di Azure supportata dalla rete per la distribuzione di contenuti (CDN) di Azure. |
| **`management.chinacloudapi.cn`**              | **`HTTPS:443`** | Obbligatorio per le operazioni Kubernetes nell'API di Azure. |
| **`login.chinacloudapi.cn`**                   | **`HTTPS:443`** | Obbligatorio per l'autenticazione Azure Active Directory. |
| **`packages.microsoft.com`**                   | **`HTTPS:443`** | Questo indirizzo è il repository di pacchetti Microsoft usato per le operazioni *apt-get* memorizzate nella cache.  I pacchetti di esempio includono Moby, PowerShell e l'interfaccia della riga di comando di Azure. |
| **`*.azk8s.cn`**                               | **`HTTPS:443`** | Questo indirizzo è per il repository necessario per scaricare e installare i file binari necessari, ad esempio kubenet e Azure CNI. |

### <a name="azure-us-government-required-network-rules"></a>Regole di rete necessarie per il governo degli Stati Uniti di Azure

Le regole di rete e le dipendenze degli indirizzi IP richieste sono le seguenti:

| Endpoint di destinazione                                                             | Protocollo | Porta    | Uso  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *O* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *O* <br/> [CIDRs regionali](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *O* <br/> **`APIServerIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | Per la comunicazione protetta con tunnel tra i nodi e il piano di controllo. |
| **`*:9000`** <br/> *O* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *O* <br/> [CIDRs regionali](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *O* <br/> **`APIServerIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | Per la comunicazione protetta con tunnel tra i nodi e il piano di controllo. |
| **`*:123`** o **`ntp.ubuntu.com:123`** (se si usano le regole di rete del firewall di Azure)  | UDP      | 123     | Obbligatorio per la sincronizzazione dell'ora NTP (Network Time Protocol) nei nodi Linux.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Se si usano server DNS personalizzati, è necessario assicurarsi che siano accessibili dai nodi del cluster. |
| **`APIServerIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Obbligatorio se i pod/distribuzioni in esecuzione che accedono al server API, tali Pod/distribuzioni utilizzeranno l'IP dell'API.  |

### <a name="azure-us-government-required-fqdn--application-rules"></a>Regole dell'applicazione o FQDN per il governo degli Stati Uniti di Azure obbligatorio 

Le regole delle applicazioni e FQDN seguenti sono obbligatorie:

| FQDN di destinazione                                        | Porta            | Uso      |
|---------------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.aks.containerservice.azure.us`** | **`HTTPS:443`** | Obbligatorio per la comunicazione del server di < di nodo > API. Sostituire *\<location\>* con l'area in cui è distribuito il cluster AKS.|
| **`mcr.microsoft.com`**                                 | **`HTTPS:443`** | Obbligatorio per accedere alle immagini in Microsoft Container Registry (e). Questo registro contiene immagini o grafici di terze parti, ad esempio coreDNS e così via. Queste immagini sono necessarie per la creazione e il funzionamento corrette del cluster, incluse le operazioni di scalabilità e aggiornamento. |
| **`*.cdn.mscr.io`**                                     | **`HTTPS:443`** | Obbligatorio per l'archiviazione del sistema di archiviazione di Azure supportata dalla rete per la distribuzione di contenuti (CDN) di Azure. |
| **`*.data.mcr.microsoft.com`**                          | **`HTTPS:443`** | Obbligatorio per l'archiviazione del sistema di archiviazione di Azure supportata dalla rete per la distribuzione di contenuti (CDN) di Azure. |
| **`management.usgovcloudapi.net`**                      | **`HTTPS:443`** | Obbligatorio per le operazioni Kubernetes nell'API di Azure. |
| **`login.microsoftonline.us`**                          | **`HTTPS:443`** | Obbligatorio per l'autenticazione Azure Active Directory. |
| **`packages.microsoft.com`**                            | **`HTTPS:443`** | Questo indirizzo è il repository di pacchetti Microsoft usato per le operazioni *apt-get* memorizzate nella cache.  I pacchetti di esempio includono Moby, PowerShell e l'interfaccia della riga di comando di Azure. |
| **`acs-mirror.azureedge.net`**                          | **`HTTPS:443`** | Questo indirizzo è per il repository necessario per installare i file binari richiesti, come Kubenet e Azure CNI. |

## <a name="optional-recommended-fqdn--application-rules-for-aks-clusters"></a>Regole dell'applicazione/FQDN consigliate facoltative per i cluster AKS

Le seguenti regole di FQDN/applicazione sono facoltative ma consigliate per i cluster AKS:

| FQDN di destinazione                                                               | Porta          | Uso      |
|--------------------------------------------------------------------------------|---------------|----------|
| **`security.ubuntu.com`, `azure.archive.ubuntu.com`, `changelogs.ubuntu.com`** | **`HTTP:80`** | Questo indirizzo consente ai nodi del cluster Linux di scaricare le patch di sicurezza e gli aggiornamenti necessari. |

Se si sceglie di bloccare o non consentire questi FQDN, i nodi riceveranno solo gli aggiornamenti del sistema operativo quando si esegue l'aggiornamento di un' [immagine del nodo](node-image-upgrade.md) o un [aggiornamento del cluster](upgrade-cluster.md).

## <a name="gpu-enabled-aks-clusters"></a>Cluster AKS abilitati per GPU

### <a name="required-fqdn--application-rules"></a>Regole dell'applicazione o FQDN obbligatorie

Le regole delle applicazioni e FQDN seguenti sono obbligatorie per i cluster del servizio Azure Kubernetes con la GPU abilitata:

| FQDN di destinazione                        | Porta      | Uso      |
|-----------------------------------------|-----------|----------|
| **`nvidia.github.io`**                  | **`HTTPS:443`** | Questo indirizzo viene usato per l'installazione e il funzionamento corretti dei driver nei nodi basati su GPU. |
| **`us.download.nvidia.com`**            | **`HTTPS:443`** | Questo indirizzo viene usato per l'installazione e il funzionamento corretti dei driver nei nodi basati su GPU. |
| **`apt.dockerproject.org`**             | **`HTTPS:443`** | Questo indirizzo viene usato per l'installazione e il funzionamento corretti dei driver nei nodi basati su GPU. |

## <a name="windows-server-based-node-pools"></a>Pool di nodi basati su Windows Server 

### <a name="required-fqdn--application-rules"></a>Regole dell'applicazione o FQDN obbligatorie

Le regole delle applicazioni e FQDN seguenti sono obbligatorie per l'uso di pool di nodi basati su Windows Server:

| FQDN di destinazione                                                           | Porta      | Uso      |
|----------------------------------------------------------------------------|-----------|----------|
| **`onegetcdn.azureedge.net, go.microsoft.com`**                            | **`HTTPS:443`** | Per installare file binari correlati a Windows |
| **`*.mp.microsoft.com, www.msftconnecttest.com, ctldl.windowsupdate.com`** | **`HTTP:80`**   | Per installare file binari correlati a Windows |

## <a name="aks-addons-and-integrations"></a>Addons e integrazioni AKS

### <a name="azure-monitor-for-containers"></a>Monitoraggio di Azure per contenitori

Sono disponibili due opzioni per fornire l'accesso a monitoraggio di Azure per i contenitori. è possibile consentire il monitoraggio di Azure [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) **o** fornire l'accesso alle regole dell'applicazione/FQDN richieste.

#### <a name="required-network-rules"></a>Regole di rete obbligatorie

Le regole delle applicazioni e FQDN seguenti sono obbligatorie:

| Endpoint di destinazione                                                             | Protocollo | Porta    | Uso  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureMonitor:443`**  | TCP           | 443      | Questo endpoint viene usato per inviare i log e i dati di metrica a monitoraggio di Azure e Log Analytics. |

#### <a name="required-fqdn--application-rules"></a>Regole dell'applicazione o FQDN obbligatorie

Le regole delle applicazioni e FQDN seguenti sono obbligatorie per i cluster del servizio Azure Kubernetes con Monitoraggio di Azure per i contenitori abilitato:

| Nome di dominio completo                                    | Porta      | Uso      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | **`HTTPS:443`**    | Questo endpoint viene usato per le metriche e il monitoraggio dei dati di telemetria con monitoraggio di Azure. |
| *.ods.opinsights.azure.com    | **`HTTPS:443`**    | Questo endpoint viene usato da monitoraggio di Azure per l'inserimento dei dati di log Analytics. |
| *.oms.opinsights.azure.com | **`HTTPS:443`** | Questo endpoint viene usato da omsagent, che viene usato per autenticare il servizio log Analytics. |
| *.monitoring.azure.com | **`HTTPS:443`** | Questo endpoint viene usato per inviare i dati di metrica a monitoraggio di Azure. |

### <a name="azure-dev-spaces"></a>Azure Dev Spaces

Aggiornare la configurazione del firewall o della sicurezza per consentire il traffico di rete da e verso tutti i nomi FQDN seguenti e i [servizi di infrastruttura Azure Dev Spaces][dev-spaces-service-tags].

#### <a name="required-network-rules"></a>Regole di rete obbligatorie

| Endpoint di destinazione                                                             | Protocollo | Porta    | Uso  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureDevSpaces`**  | TCP           | 443      | Questo endpoint viene usato per inviare i log e i dati di metrica a monitoraggio di Azure e Log Analytics. |

#### <a name="required-fqdn--application-rules"></a>Regole dell'applicazione o FQDN obbligatorie 

Le regole delle applicazioni e FQDN seguenti sono obbligatorie per i cluster del servizio Azure Kubernetes con Azure Dev Spaces abilitato:

| Nome di dominio completo                                    | Porta      | Uso      |
|-----------------------------------------|-----------|----------|
| `cloudflare.docker.com` | **`HTTPS:443`** | Questo indirizzo viene usato per eseguire il pull di immagini di Alpine Linux e di altre immagini di Azure Dev Spaces |
| `gcr.io` | **`HTTPS:443`** | Questo indirizzo viene usato per eseguire il pull di immagini Helm/Tiller |
| `storage.googleapis.com` | **`HTTPS:443`** | Questo indirizzo viene usato per eseguire il pull di immagini Helm/Tiller |


### <a name="azure-policy-preview"></a>Criteri di Azure (anteprima)

> [!CAUTION]
> Alcune delle funzionalità seguenti sono disponibili in anteprima.  I suggerimenti in questo articolo sono soggetti a modifiche quando la funzionalità passerà alla versione di anteprima pubblica e alle fasi di rilascio successive.

#### <a name="required-fqdn--application-rules"></a>Regole dell'applicazione o FQDN obbligatorie 

Le regole delle applicazioni e FQDN seguenti sono obbligatorie per i cluster del servizio Azure Kubernetes con il servizio Criteri di Azure abilitato.

| Nome di dominio completo                                          | Porta      | Uso      |
|-----------------------------------------------|-----------|----------|
| **`gov-prod-policy-data.trafficmanager.net`** | **`HTTPS:443`** | Questo indirizzo viene usato per il corretto funzionamento di Criteri di Azure (attualmente in anteprima nel servizio Azure Kubernetes). |
| **`raw.githubusercontent.com`**               | **`HTTPS:443`** | Questo indirizzo viene usato per eseguire il pull dei criteri predefiniti da GitHub per assicurare il corretto funzionamento di Criteri di Azure (attualmente in anteprima nel servizio Azure Kubernetes). |
| **`dc.services.visualstudio.com`**            | **`HTTPS:443`** | Componente aggiuntivo di Criteri di Azure che invia i dati di telemetria all'endpoint di Application Insights. |


## <a name="restrict-egress-traffic-using-azure-firewall"></a>Limitare il traffico in uscita con il firewall di Azure

Il firewall di Azure fornisce un tag FQDN del servizio Kubernetes `AzureKubernetesService` di Azure per semplificare questa configurazione. 

> [!NOTE]
> Il tag FQDN contiene tutti i nomi di dominio completi elencati sopra e viene mantenuto automaticamente aggiornato.

Di seguito è riportata un'architettura di esempio della distribuzione:

![Topologia bloccata](media/limit-egress-traffic/aks-azure-firewall-egress.png)

* Il traffico in ingresso pubblico è forzato a passare attraverso i filtri del firewall
  * I nodi dell'agente AKS sono isolati in una subnet dedicata.
  * Il [firewall di Azure](../firewall/overview.md) viene distribuito nella propria subnet.
  * Una regola DNAT converte l'IP pubblico FW nell'IP front-end LB.
* Le richieste in uscita iniziano dai nodi dell'agente all'IP interno del firewall di Azure usando una [route definita dall'utente](egress-outboundtype.md)
  * Le richieste provenienti dai nodi agente del servizio Azure Kubernetes seguono una route definita dall'utente che è stata inserita nella subnet in cui è stato distribuito il cluster del servizio Azure Kubernetes.
  * Uscite di Firewall di Azure dalla rete virtuale da un front-end IP pubblico
  * L'accesso alla rete Internet pubblica o ad altri servizi di Azure viene trasmesso da e verso l'indirizzo IP front-end del firewall
  * Facoltativamente, l'accesso al piano di controllo AKS è protetto da [intervalli IP autorizzati del server API](https://docs.microsoft.com/azure/aks/api-server-authorized-ip-ranges), che include l'indirizzo IP front-end pubblico del firewall.
* Traffico interno
  * Facoltativamente, invece o in aggiunta a un [Load Balancer pubblico](load-balancer-standard.md) è possibile usare una [Load Balancer interna](internal-lb.md) per il traffico interno, che è possibile isolare anche nella propria subnet.


I passaggi seguenti usano il tag FQDN del firewall `AzureKubernetesService` di Azure per limitare il traffico in uscita dal cluster AKS e fornire un esempio di come configurare il traffico in ingresso pubblico attraverso il firewall.


### <a name="set-configuration-via-environment-variables"></a>Impostare la configurazione tramite variabili di ambiente

Definire un set di variabili di ambiente da usare durante la creazione di risorse.

```bash
PREFIX="aks-egress"
RG="${PREFIX}-rg"
LOC="eastus"
PLUGIN=azure
AKSNAME="${PREFIX}"
VNET_NAME="${PREFIX}-vnet"
AKSSUBNET_NAME="aks-subnet"
# DO NOT CHANGE FWSUBNET_NAME - This is currently a requirement for Azure Firewall.
FWSUBNET_NAME="AzureFirewallSubnet"
FWNAME="${PREFIX}-fw"
FWPUBLICIP_NAME="${PREFIX}-fwpublicip"
FWIPCONFIG_NAME="${PREFIX}-fwconfig"
FWROUTE_TABLE_NAME="${PREFIX}-fwrt"
FWROUTE_NAME="${PREFIX}-fwrn"
FWROUTE_NAME_INTERNET="${PREFIX}-fwinternet"
```

### <a name="create-a-virtual-network-with-multiple-subnets"></a>Creare una rete virtuale con più subnet

Effettuare il provisioning di una rete virtuale con due subnet separate, una per il cluster, una per il firewall. Facoltativamente, è anche possibile crearne uno per l'ingresso del servizio interno.

![Topologia di rete vuota](media/limit-egress-traffic/empty-network.png)

Creare un gruppo di risorse che conterrà tutte le risorse.

```azure-cli
# Create Resource Group

az group create --name $RG --location $LOC
```

Creare una rete virtuale con due subnet per ospitare il cluster AKS e il firewall di Azure. Ognuna avrà una propria subnet. Si inizierà con la rete del servizio Azure Kubernetes.

```
# Dedicated virtual network with AKS subnet

az network vnet create \
    --resource-group $RG \
    --name $VNET_NAME \
    --address-prefixes 10.42.0.0/16 \
    --subnet-name $AKSSUBNET_NAME \
    --subnet-prefix 10.42.1.0/24

# Dedicated subnet for Azure Firewall (Firewall name cannot be changed)

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $FWSUBNET_NAME \
    --address-prefix 10.42.2.0/24
```

### <a name="create-and-set-up-an-azure-firewall-with-a-udr"></a>Creare e configurare un firewall di Azure con un UDR

È necessario configurare le regole in ingresso e in uscita di Firewall di Azure. Lo scopo principale del firewall è quello di consentire alle organizzazioni di configurare le regole di traffico in ingresso e in uscita granulari all'interno e all'esterno del cluster AKS.

![Firewall e route definita dall'utente](media/limit-egress-traffic/firewall-udr.png)


> [!IMPORTANT]
> Se il cluster o l'applicazione crea un numero elevato di connessioni in uscita indirizzate allo stesso subset o a un piccolo subset di destinazioni, potrebbe essere necessario un numero maggiore di indirizzi IP front-end del firewall per evitare trafugata delle porte per ogni IP front-end.
> Per altre informazioni su come creare un firewall di Azure con più indirizzi IP, vedere [ **qui**](../firewall/quick-create-multiple-ip-template.md)

Creare una risorsa IP pubblico con SKU standard che verrà usata come indirizzo front-end del firewall di Azure.

```azure-cli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

Registrare l'estensione dell'interfaccia della riga di comando di anteprima per creare un'istanza di Firewall di Azure.
```azure-cli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC --enable-dns-proxy true
```
È ora possibile assegnare l'indirizzo IP creato in precedenza al front-end del firewall.

> [!NOTE]
> La configurazione dell'indirizzo IP pubblico per il firewall di Azure può richiedere alcuni minuti.
> Per sfruttare il nome di dominio completo sulle regole di rete, è necessario abilitare il proxy DNS, se abilitato, il firewall resterà in ascolto sulla porta 53 e inoltrerà le richieste DNS al server DNS specificato in precedenza. Questo consentirà al firewall di tradurre automaticamente il nome di dominio completo.

```azure-cli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

Dopo aver eseguito il comando precedente, salvare l'indirizzo IP del front-end del firewall per eseguire la configurazione in un secondo momento.

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

> [!NOTE]
> Se si usa l'accesso sicuro al server dell'API AKS con [intervalli di indirizzi IP autorizzati](https://docs.microsoft.com/azure/aks/api-server-authorized-ip-ranges), è necessario aggiungere l'IP pubblico del firewall nell'intervallo di indirizzi IP autorizzati.

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>Creare una route definita dall'utente con un hop a Firewall di Azure

Azure effettua il routing automatico del traffico tra subnet di Azure, reti virtuali e reti locali. Per modificare il routing predefinito di Azure è necessario creare una tabella di route.

Creare una tabella di route vuota da associare a una determinata subnet. Firewall di Azure creato in precedenza verrà impostato come hop successivo nella tabella di route. A ogni subnet può essere associata una o nessuna tabella di route.

```azure-cli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

Vedere la [documentazione relativa alle tabelle di route della rete virtuale](../virtual-network/virtual-networks-udr-overview.md#user-defined) per informazioni su come eseguire l'override delle route di sistema predefinite di Azure o su come aggiungere altre route alla tabella di route di una subnet.

### <a name="adding-firewall-rules"></a>Aggiunta di regole del firewall

Di seguito sono riportate tre regole di rete che è possibile usare per configurare sul firewall, potrebbe essere necessario adattare queste regole in base alla distribuzione. La prima regola consente l'accesso alla porta 9000 tramite TCP. La seconda regola consente l'accesso alla porta 1194 e 123 tramite UDP (se si esegue la distribuzione in Azure Cina 21Vianet, potrebbe essere necessario [altro](#azure-china-21vianet-required-network-rules)). Entrambe queste regole consentiranno solo il traffico destinato alla CIDR dell'area di Azure in uso, in questo caso Stati Uniti orientali. Infine, si aggiungerà una terza regola di rete che apre la porta 123 al `ntp.ubuntu.com` nome di dominio completo tramite UDP (l'aggiunta di un FQDN come regola di rete è una delle funzionalità specifiche del firewall di Azure e sarà necessario adattarla quando si usano le proprie opzioni).

Dopo aver impostato le regole di rete, verrà aggiunta anche una regola dell'applicazione con il `AzureKubernetesService` che copre tutti i FQDN necessari accessibili tramite la porta TCP 443 e la porta 80.

```
# Add FW Network Rules

az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apiudp' --protocols 'UDP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 1194 --action allow --priority 100
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apitcp' --protocols 'TCP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 9000
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'time' --protocols 'UDP' --source-addresses '*' --destination-fqdns 'ntp.ubuntu.com' --destination-ports 123

# Add FW Application Rules

az network firewall application-rule create -g $RG -f $FWNAME --collection-name 'aksfwar' -n 'fqdn' --source-addresses '*' --protocols 'http=80' 'https=443' --fqdn-tags "AzureKubernetesService" --action allow --priority 100
```

Per altre informazioni sul servizio Firewall di Azure, vedere la [documentazione di Firewall di Azure](https://docs.microsoft.com/azure/firewall/overview).

### <a name="associate-the-route-table-to-aks"></a>Associare la tabella di route al servizio Azure Kubernetes

Per associare il cluster al firewall, la subnet dedicata per la subnet del cluster deve fare riferimento alla tabella di route creata in precedenza. È possibile eseguire l'associazione inviando un comando alla rete virtuale che contiene sia il cluster che il firewall per aggiornare la tabella di route della subnet del cluster.

```azure-cli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

### <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>Distribuire il servizio Azure Kubernetes con il tipo in uscita con route definita dall'utente nella rete esistente

A questo punto è possibile distribuire un cluster AKS nella rete virtuale esistente. Si userà anche il [tipo `userDefinedRouting` in uscita ](egress-outboundtype.md). questa funzionalità assicura che il traffico in uscita venga forzato attraverso il firewall e che non esistano altri percorsi in uscita (per impostazione predefinita, è possibile usare il Load Balancer tipo in uscita).

![aks-deploy](media/limit-egress-traffic/aks-udr-fw.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>Creare un'entità servizio con accesso per effettuare il provisioning all'interno della rete virtuale esistente

Il servizio Azure Kubernetes usa un'entità servizio per creare le risorse cluster. L'entità servizio passata al momento della creazione viene usata per creare risorse AKS sottostanti, ad esempio le risorse di archiviazione, gli IP e i servizi di bilanciamento del carico usati da AKS (si può anche usare un' [identità gestita](use-managed-identity.md) ). Se non vengono concesse le autorizzazioni appropriate, non sarà possibile effettuare il provisioning del cluster AKS.

```azure-cli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

A questo punto, sostituire `APPID` e `PASSWORD` nell'esempio seguente con i valori di ID app e password dell'entità servizio generati automaticamente dall'output del comando precedente. Si farà riferimento all'ID della risorsa VNET per concedere le autorizzazioni all'entità servizio in modo che AKS possa distribuire le risorse al suo interno.

```azure-cli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role "Network Contributor"
```

È possibile controllare le autorizzazioni dettagliate richieste [qui](kubernetes-service-principal.md#delegate-access-to-other-azure-resources).

> [!NOTE]
> Se si usa il plug-in di rete kubenet, è necessario assegnare le autorizzazioni dell'entità servizio AKS o dell'identità gestita alla tabella di route creata in precedenza, poiché kubenet richiede una tabella di route per aggiungere regole di routing necessario. 
> ```azurecli-interactive
> RTID=$(az network route-table show -g $RG -n $FWROUTE_TABLE_NAME --query id -o tsv)
> az role assignment create --assignee $APPID --scope $RTID --role "Network Contributor"
> ```

### <a name="deploy-aks"></a>Distribuire il servizio Azure Kubernetes

Infine, il cluster AKS può essere distribuito nella subnet esistente dedicata per il cluster. Per definire la subnet di destinazione in cui eseguire la distribuzione, si usa la variabile di ambiente `$SUBNETID`. La variabile `$SUBNETID` non è stata definita nei passaggi precedenti. Per impostare il valore per l'ID subnet, è possibile usare il comando seguente:

```azurecli
SUBNETID=$(az network vnet subnet show -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --query id -o tsv)
```

Si definirà il tipo in uscita per usare il UDR già esistente nella subnet. Questa configurazione consente ad AKS di ignorare la configurazione e il provisioning IP per il servizio di bilanciamento del carico.

> [!IMPORTANT]
> Per altre informazioni sul tipo in uscita UDR, incluse le limitazioni, vedere il tipo in uscita in uscita [**UdR**](egress-outboundtype.md#limitations).


> [!TIP]
> È possibile aggiungere funzionalità aggiuntive alla distribuzione del cluster, ad esempio il [**cluster privato**](private-clusters.md). 
>
> È possibile aggiungere la funzionalità AKS per gli [**intervalli IP autorizzati del server API**](api-server-authorized-ip-ranges.md) per limitare l'accesso al server API solo all'endpoint pubblico del firewall. La funzionalità degli intervalli IP autorizzati è indicata come facoltativa nel diagramma. Quando si abilita la funzionalità degli intervalli IP autorizzati per limitare l'accesso al server API, gli strumenti di sviluppo devono usare un JumpBox dalla rete virtuale del firewall oppure è necessario aggiungere tutti gli endpoint sviluppatore all'intervallo di indirizzi IP autorizzati.

```azure-cli
az aks create -g $RG -n $AKSNAME -l $LOC \
  --node-count 3 --generate-ssh-keys \
  --network-plugin $PLUGIN \
  --outbound-type userDefinedRouting \
  --service-cidr 10.41.0.0/16 \
  --dns-service-ip 10.41.0.10 \
  --docker-bridge-address 172.17.0.1/16 \
  --vnet-subnet-id $SUBNETID \
  --service-principal $APPID \
  --client-secret $PASSWORD \
  --api-server-authorized-ip-ranges $FWPUBLIC_IP
```

### <a name="enable-developer-access-to-the-api-server"></a>Consentire agli sviluppatori di accedere al server API

Se sono stati usati intervalli IP autorizzati per il cluster nel passaggio precedente, è necessario aggiungere gli indirizzi IP degli strumenti per sviluppatori all'elenco di cluster AKS degli intervalli IP approvati per poter accedere al server API da questa posizione. Un'altra opzione consiste nel configurare un JumpBox con gli strumenti necessari all'interno di una subnet separata nella rete virtuale di Firewall.

Aggiungere un altro indirizzo IP agli intervalli approvati con il comando seguente

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKS_NAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

 Usare il comando [AZ AKS Get-credentials] [AZ-AKS-Get-credentials] per configurare `kubectl` la connessione al cluster Kubernetes appena creato. 

 ```azure-cli
 az aks get-credentials -g $RG -n $AKS_NAME
 ```

### <a name="deploy-a-public-service"></a>Distribuire un servizio pubblico
È ora possibile avviare l'esposizione dei servizi e la distribuzione di applicazioni in questo cluster. In questo esempio verrà esposto un servizio pubblico, ma è anche possibile scegliere di esporre un servizio interno tramite il servizio di [bilanciamento del carico interno](internal-lb.md).

![Servizio pubblico DNAT](media/limit-egress-traffic/aks-create-svc.png)

Distribuire l'applicazione Azure Voting copiando il codice YAML seguente in un file denominato `example.yaml`.

```yaml
# voting-storage-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-storage
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-storage
  template:
    metadata:
      labels:
        app: voting-storage
    spec:
      containers:
      - name: voting-storage
        image: mcr.microsoft.com/aks/samples/voting/storage:2.0
        args: ["--ignore-db-dir=lost+found"]
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 3306
          name: mysql
        volumeMounts:
        - name: mysql-persistent-storage
          mountPath: /var/lib/mysql
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_ROOT_PASSWORD
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
      volumes:
      - name: mysql-persistent-storage
        persistentVolumeClaim:
          claimName: mysql-pv-claim
---
# voting-storage-secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: voting-storage-secret
type: Opaque
data:
  MYSQL_USER: ZGJ1c2Vy
  MYSQL_PASSWORD: UGFzc3dvcmQxMg==
  MYSQL_DATABASE: YXp1cmV2b3Rl
  MYSQL_ROOT_PASSWORD: UGFzc3dvcmQxMg==
---
# voting-storage-pv-claim.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
---
# voting-storage-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-storage
  labels: 
    app: voting-storage
spec:
  ports:
  - port: 3306
    name: mysql
  selector:
    app: voting-storage
---
# voting-app-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-app
  template:
    metadata:
      labels:
        app: voting-app
    spec:
      containers:
      - name: voting-app
        image: mcr.microsoft.com/aks/samples/voting/app:2.0
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
          name: http
        env:
        - name: MYSQL_HOST
          value: "voting-storage"
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
        - name: ANALYTICS_HOST
          value: "voting-analytics"
---
# voting-app-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-app
  labels: 
    app: voting-app
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 8080
    name: http
  selector:
    app: voting-app
---
# voting-analytics-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-analytics
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-analytics
      version: "2.0"
  template:
    metadata:
      labels:
        app: voting-analytics
        version: "2.0"
    spec:
      containers:
      - name: voting-analytics
        image: mcr.microsoft.com/aks/samples/voting/analytics:2.0
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
          name: http
        env:
        - name: MYSQL_HOST
          value: "voting-storage"
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
---
# voting-analytics-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-analytics
  labels: 
    app: voting-analytics
spec:
  ports:
  - port: 8080
    name: http
  selector:
    app: voting-analytics
```

Per distribuire il servizio, eseguire:

```bash
kubectl apply -f example.yaml
```

### <a name="add-a-dnat-rule-to-azure-firewall"></a>Aggiungere una regola DNAT a Firewall di Azure

> [!IMPORTANT]
> Quando si usa Firewall di Azure per limitare il traffico in uscita e creare una route definita dall'utente (UDR) per forzare tutto il traffico in uscita, assicurarsi di creare una regola DNAT appropriata nel firewall per consentire correttamente il traffico in ingresso. L'uso del servizio Firewall di Azure con una route definita dall'utente interrompe la configurazione in ingresso a causa del routing asimmetrico. Questo problema si verifica se la subnet del servizio Azure Kubernetes ha una route predefinita verso l'indirizzo IP privato del firewall, ma si sta usando un servizio di bilanciamento del carico pubblico in ingresso o del servizio Kubernetes di tipo LoadBalancer. In questo caso, il traffico del servizio di bilanciamento del carico in ingresso viene ricevuto tramite l'indirizzo IP pubblico, ma il percorso di ritorno passa attraverso l'indirizzo IP privato del firewall. Poiché il firewall è con stato, elimina il pacchetto di ritorno perché il firewall non è a conoscenza del fatto che è stata stabilita una sessione. Per informazioni su come integrare Firewall di Azure con il servizio di bilanciamento del carico in ingresso o del servizio, vedere [Integrare Firewall di Azure con Azure Load Balancer Standard](https://docs.microsoft.com/azure/firewall/integrate-lb).


Per configurare la connettività in ingresso, è necessario scrivere una regola DNAT in Firewall di Azure. Per testare la connettività al cluster, viene definita una regola per l'indirizzo IP pubblico front-end del firewall da indirizzare all'IP interno esposto dal servizio interno.

È possibile personalizzare l'indirizzo di destinazione perché è la porta sul firewall a cui accedere. L'indirizzo convertito deve essere l'indirizzo IP del bilanciamento del carico interno. La porta convertita deve essere la porta esposta per il servizio Kubernetes.

È necessario specificare l'indirizzo IP interno assegnato al servizio di bilanciamento del carico creato dal servizio Kubernetes. Per recuperare l'indirizzo, eseguire:

```bash
kubectl get services
```

L'indirizzo IP necessario sarà indicato nella colonna EXTERNAL-IP e sarà simile a quello illustrato di seguito.

```bash
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes         ClusterIP      10.41.0.1       <none>        443/TCP        10h
voting-analytics   ClusterIP      10.41.88.129    <none>        8080/TCP       9m
voting-app         LoadBalancer   10.41.185.82    20.39.18.6    80:32718/TCP   9m
voting-storage     ClusterIP      10.41.221.201   <none>        3306/TCP       9m
```

Ottenere l'indirizzo IP del servizio eseguendo:
```bash
SERVICE_IP=$(k get svc voting-app -o jsonpath='{.status.loadBalancer.ingress[*].ip}')
```

Aggiungere la regola NAT eseguendo:
```azure-cli
az network firewall nat-rule create --collection-name exampleset --destination-addresses $FWPUBLIC_IP --destination-ports 80 --firewall-name $FWNAME --name inboundrule --protocols Any --resource-group $RG --source-addresses '*' --translated-port 80 --action Dnat --priority 100 --translated-address $SERVICE_IP
```

### <a name="validate-connectivity"></a>Convalidare la connettività

In un browser accedere all'indirizzo IP front-end di Firewall di Azure per convalidare la connettività.

Verrà visualizzata l'app AKS vote. In questo esempio l'indirizzo IP pubblico del firewall era `52.253.228.132` .


![AKS-Voto](media/limit-egress-traffic/aks-vote.png)


### <a name="clean-up-resources"></a>Pulire le risorse

Per pulire le risorse di Azure, eliminare il gruppo di risorse del servizio Azure Kubernetes.

```azure-cli
az group delete -g $RG
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state apprese le porte e gli indirizzi da consentire se si vuole limitare il traffico in uscita per il cluster. È stato anche illustrato come proteggere il traffico in uscita usando il firewall di Azure. 

Se necessario, è possibile generalizzare i passaggi precedenti per inviare il traffico alla soluzione in uscita preferita, seguendo la [ `userDefinedRoute` documentazione relativa al tipo](egress-outboundtype.md)in uscita.

Se si vuole limitare il modo in cui i pod comunicano tra loro e le restrizioni del traffico verso l'area ovest all'interno del cluster, vedere [proteggere il traffico tra i pod usando i criteri di rete][network-policy]

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-upgrade]: upgrade-cluster.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[dev-spaces-service-tags]: ../dev-spaces/configure-networking.md#virtual-network-or-subnet-configurations
