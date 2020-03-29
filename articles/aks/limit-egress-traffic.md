---
title: Limitare il traffico in uscita nel servizio Azure Kubernetes (AKS)Restrict egress traffic in Azure Kubernetes Service (AKS)
description: Informazioni sulle porte e gli indirizzi necessari per controllare il traffico in uscita nel servizio Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: 2cd7aeea272d22615d3ba3d3db6acc2c84d22cca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080172"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>Controllare il traffico in uscita per i nodi del cluster nel servizio Azure Kubernetes (AKS)Control egress traffic for cluster nodes in Azure Kubernetes Service (AKS)

Per impostazione predefinita, i cluster AKS hanno accesso a Internet in uscita (in uscita) senza restrizioni. Questo livello di accesso alla rete consente ai nodi e ai servizi eseguiti di accedere alle risorse esterne in base alle esigenze. Se si desidera limitare il traffico in uscita, un numero limitato di porte e indirizzi deve essere accessibile per mantenere sane le attività di manutenzione del cluster. Il cluster è configurato per impostazione predefinita per usare solo le immagini del contenitore del sistema di base da Microsoft Container Registry (MCR) o Azure Container Registry (ACR). Configurare il firewall e le regole di sicurezza preferite per consentire le porte e gli indirizzi necessari.

In questo articolo vengono descritte in dettaglio quali porte di rete e nomi di dominio completi (FQDN) sono obbligatori e facoltativi se si limita il traffico in uscita in un cluster AKS.

> [!IMPORTANT]
> Questo documento illustra solo come bloccare il traffico in uscita dalla subnet AKS. AKS non ha requisiti in ingresso.  Il blocco del traffico di subnet interne tramite gruppi di sicurezza di rete e firewall non è supportato. Per controllare e bloccare il traffico all'interno del cluster, utilizzare [Criteri di rete][network-policy].

## <a name="before-you-begin"></a>Prima di iniziare

È necessaria l'interfaccia della riga di comando di Azure versione 2.0.66 o successiva installata e configurata. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure.If][install-azure-cli]you need to install or upgrade, see Install Azure CLI.

## <a name="egress-traffic-overview"></a>Panoramica sul traffico in uscita

Per scopi di gestione e operativi, i nodi in un cluster AKS devono accedere a determinate porte e nomi di dominio completi (FQDN). Queste azioni potrebbero essere per comunicare con il server API o per scaricare e quindi installare i componenti del cluster Kubernetes di base e gli aggiornamenti della sicurezza dei nodi. Per impostazione predefinita, il traffico Internet in uscita (in uscita) non è limitato per i nodi in un cluster AKS. Il cluster può estrarre le immagini del contenitore di sistema di base da repository esterni.

Per aumentare la sicurezza del cluster AKS, è possibile limitare il traffico in uscita. Il cluster è configurato per eseguire il pull delle immagini del contenitore di sistema di base da MCR o ACR. Se si blocca il traffico in uscita in questo modo, definire porte e FQDN specifici per consentire ai nodi AKS di comunicare correttamente con i servizi esterni necessari. Senza queste porte autorizzate e FQDN, i nodi AKS non possono comunicare con il server API o installare componenti di base.

È possibile usare Firewall di [Azure][azure-firewall] o un'appliance firewall di terze parti per proteggere il traffico in uscita e definire le porte e gli indirizzi necessari. AKS non crea automaticamente queste regole per te. Le porte e gli indirizzi seguenti sono di riferimento quando si creano le regole appropriate nel firewall di rete.

> [!IMPORTANT]
> Quando si usa Firewall di Azure per limitare il traffico in uscita e creare una route definita dall'utente per forzare tutto il traffico in uscita, assicurarsi di creare una regola DNAT appropriata in Firewall per consentire correttamente il traffico in ingresso. L'uso di Firewall di Azure con un UDR interrompe l'installazione in ingresso a causa del routing asimmetrico. (Il problema si verifica se la subnet AKS ha una route predefinita che va all'indirizzo IP privato del firewall, ma si utilizza un servizio di bilanciamento del carico pubblico - in ingresso o Kubernetes di tipo: LoadBalancer). In questo caso, il traffico del servizio di bilanciamento del carico in ingresso viene ricevuto tramite l'indirizzo IP pubblico, ma il percorso di ritorno passa attraverso l'indirizzo IP privato del firewall. Poiché il firewall è con stato, elimina il pacchetto restituito perché il firewall non è a conoscenza di una sessione stabilita. Per informazioni su come integrare Firewall di Azure con il servizio di bilanciamento del carico in ingresso o nel servizio, vedere [Integrare Firewall di Azure con Azure Standard Load Balancer.](https://docs.microsoft.com/azure/firewall/integrate-lb)
> È possibile bloccare il traffico per la porta TCP 9000, la porta TCP 22 e la porta UDP 1194 utilizzando una regola di rete tra gli IP del nodo di lavoro in uscita e l'indirizzo IP per il server API.

In AKS sono disponibili due set di porte e indirizzi:

* Le [porte e l'indirizzo necessari per i cluster AKS](#required-ports-and-addresses-for-aks-clusters) illustrano in dettaglio i requisiti minimi per il traffico in uscita autorizzato.
* Gli [indirizzi e le porte consigliate facoltativi per i cluster AKS](#optional-recommended-addresses-and-ports-for-aks-clusters) non sono necessari per tutti gli scenari, ma l'integrazione con altri servizi, ad esempio Monitoraggio di Azure, non funzionerà correttamente. Esaminare questo elenco di porte facoltative e FQDN e autorizzare i servizi e i componenti utilizzati nel cluster AKS.

> [!NOTE]
> La limitazione del traffico in uscita funziona solo sui nuovi cluster AKS. Per i cluster esistenti, [eseguire un'operazione][aks-upgrade] di aggiornamento del cluster utilizzando il `az aks upgrade` comando prima di limitare il traffico in uscita.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>Porte e indirizzi obbligatori per i cluster del servizio Azure Kubernetes

Per un cluster AKS sono necessarie le seguenti porte in uscita/regole di rete:

* Porta TCP *443*
* TCP [IPAddrOfYourAPIServer]:443 è necessario se si dispone di un'app che deve comunicare con il server API.  Questa modifica può essere impostata dopo la creazione del cluster.
* La porta TCP *9000*, la porta TCP *22* e la porta UDP *1194* affinché il pod front del tunnel comunichi nolverare con l'estremità del tunnel sul server API.
    * Per ottenere una maggiore specificità, vedere il*file .hcp.\< posizione\>.azmk8s.io* e *.tun.\< indirizzi\>azmk8s.io nella* tabella seguente.
* Porta UDP *123* per la sincronizzazione dell'ora NTP (Network Time Protocol) (nodi Linux).
* La porta UDP *53* per DNS è necessaria anche se si dispone di pod che accedono direttamente al server API.

Sono necessarie le seguenti regole DI nome di dominio completo/applicazione:

> [!IMPORTANT]
> ***.blob.core.windows.net e aksrepos.azurecr.io** non sono più regole FQDN necessarie per il blocco in uscita.  Per i cluster esistenti, [eseguire un'operazione][aks-upgrade] di aggiornamento del cluster utilizzando il `az aks upgrade` comando per rimuovere queste regole.

> [!IMPORTANT]
> Il numero cdn.mscr.io è stato sostituito da data.mcr.microsoft.com per le aree cloud pubbliche di Azure. Aggiornare le regole del firewall esistenti per rendere effettive le modifiche.

- Azure Global

| Nome di dominio completo                       | Porta      | Uso      |
|----------------------------|-----------|----------|
| (Oa.) \<posizione\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Questo indirizzo è necessario per la comunicazione del server API <-> node. Sostituire * \<\> location* con l'area in cui viene distribuito il cluster AKS. |
| .tun. \<posizione\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Questo indirizzo è necessario per la comunicazione del server API <-> node. Sostituire * \<\> location* con l'area in cui viene distribuito il cluster AKS. |
| CDN.MSCR.IO .cdn.mscr.io       | HTTPS:443 | Questo indirizzo è necessario per l'archiviazione MCR supportata dalla rete per la distribuzione di contenuti (CDN) di Azure.This address is required for MCR storage backed by the Azure Content Delivery Network (CDN). |
| mcr.microsoft.com          | HTTPS:443 | Questo indirizzo è necessario per accedere alle immagini in Microsoft Container Registry (MCR). Questo registro contiene immagini/grafici di prima parte (ad esempio, moby e così via) necessari per il funzionamento del cluster durante l'aggiornamento e la scalabilità del cluster |
| Data.mcr.microsoft.com .data.mcr.microsoft.com             | HTTPS:443 | Questo indirizzo è necessario per l'archiviazione MCR supportata dalla rete per la distribuzione di contenuti (CDN) di Azure.This address is required for MCR storage backed by the Azure content delivery network (CDN). |
| management.azure.com       | HTTPS:443 | Questo indirizzo è necessario per le operazioni GET/PUT di Kubernetes. |
| login.microsoftonline.com  | HTTPS:443 | Questo indirizzo è necessario per l'autenticazione di Azure Active Directory.This address is required for Azure Active Directory authentication. |
| ntp.ubuntu.com             | UDP:123 (udp:123)   | Questo indirizzo è necessario per la sincronizzazione dell'ora NTP nei nodi Linux.This address is required for NTP time synchronization on Linux nodes. |
| packages.microsoft.com     | HTTPS:443 | Questo indirizzo è il repository di pacchetti Microsoft utilizzato per le operazioni *apt-get* memorizzati nella cache.  I pacchetti di esempio includono Moby, PowerShell e l'interfaccia della riga di comando di Azure.Example packages include Moby, PowerShell, and Azure CLI. |
| acs-mirror.azureedge.net      | HTTPS:443 | Questo indirizzo è per il repository necessario per installare i file binari necessari come kubenet e Azure CNI. |

- 21Vianet per Azure Cina

| Nome di dominio completo                       | Porta      | Uso      |
|----------------------------|-----------|----------|
| (Oa.) \<posizione\>.cx.prod.service.azk8s.cn | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Questo indirizzo è necessario per la comunicazione del server API <-> node. Sostituire * \<\> location* con l'area in cui viene distribuito il cluster AKS. |
| .tun. \<posizione\>.cx.prod.service.azk8s.cn | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Questo indirizzo è necessario per la comunicazione del server API <-> node. Sostituire * \<\> location* con l'area in cui viene distribuito il cluster AKS. |
| .AZK8S.CN        | HTTPS:443 | Questo indirizzo è necessario per scaricare i file binari e le immagini necessari|
| mcr.microsoft.com          | HTTPS:443 | Questo indirizzo è necessario per accedere alle immagini in Microsoft Container Registry (MCR). Questo registro contiene immagini/grafici di prima parte (ad esempio, moby e così via) necessari per il funzionamento del cluster durante l'aggiornamento e la scalabilità del cluster |
| CDN.MSCR.IO .cdn.mscr.io       | HTTPS:443 | Questo indirizzo è necessario per l'archiviazione MCR supportata dalla rete per la distribuzione di contenuti (CDN) di Azure.This address is required for MCR storage backed by the Azure Content Delivery Network (CDN). |
| Data.mcr.microsoft.com .data.mcr.microsoft.com             | HTTPS:443 | Questo indirizzo è necessario per l'archiviazione MCR supportata dalla rete per la distribuzione di contenuti (CDN) di Azure.This address is required for MCR storage backed by the Azure content delivery network (CDN). |
| management.chinacloudapi.cn       | HTTPS:443 | Questo indirizzo è necessario per le operazioni GET/PUT di Kubernetes. |
| login.chinacloudapi.cn  | HTTPS:443 | Questo indirizzo è necessario per l'autenticazione di Azure Active Directory.This address is required for Azure Active Directory authentication. |
| ntp.ubuntu.com             | UDP:123 (udp:123)   | Questo indirizzo è necessario per la sincronizzazione dell'ora NTP nei nodi Linux.This address is required for NTP time synchronization on Linux nodes. |
| packages.microsoft.com     | HTTPS:443 | Questo indirizzo è il repository di pacchetti Microsoft utilizzato per le operazioni *apt-get* memorizzati nella cache.  I pacchetti di esempio includono Moby, PowerShell e l'interfaccia della riga di comando di Azure.Example packages include Moby, PowerShell, and Azure CLI. |

- Azure Government

| Nome di dominio completo                       | Porta      | Uso      |
|----------------------------|-----------|----------|
| (Oa.) \<posizione\>.cx.aks.containerservice.azure.us | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Questo indirizzo è necessario per la comunicazione del server API <-> node. Sostituire * \<\> location* con l'area in cui viene distribuito il cluster AKS. |
| .tun. \<posizione\>.cx.aks.containerservice.azure.us | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Questo indirizzo è necessario per la comunicazione del server API <-> node. Sostituire * \<\> location* con l'area in cui viene distribuito il cluster AKS. |
| mcr.microsoft.com          | HTTPS:443 | Questo indirizzo è necessario per accedere alle immagini in Microsoft Container Registry (MCR). Questo registro contiene immagini/grafici di prima parte (ad esempio, moby e così via) necessari per il funzionamento del cluster durante l'aggiornamento e la scalabilità del cluster |
|CDN.MSCR.IO .cdn.mscr.io              | HTTPS:443 | Questo indirizzo è necessario per l'archiviazione MCR supportata dalla rete per la distribuzione di contenuti (CDN) di Azure.This address is required for MCR storage backed by the Azure Content Delivery Network (CDN). |
| Data.mcr.microsoft.com .data.mcr.microsoft.com             | HTTPS:443 | Questo indirizzo è necessario per l'archiviazione MCR supportata dalla rete per la distribuzione di contenuti (CDN) di Azure.This address is required for MCR storage backed by the Azure content delivery network (CDN). |
| management.usgovcloudapi.net       | HTTPS:443 | Questo indirizzo è necessario per le operazioni GET/PUT di Kubernetes. |
| login.microsoftonline.us  | HTTPS:443 | Questo indirizzo è necessario per l'autenticazione di Azure Active Directory.This address is required for Azure Active Directory authentication. |
| ntp.ubuntu.com             | UDP:123 (udp:123)   | Questo indirizzo è necessario per la sincronizzazione dell'ora NTP nei nodi Linux.This address is required for NTP time synchronization on Linux nodes. |
| packages.microsoft.com     | HTTPS:443 | Questo indirizzo è il repository di pacchetti Microsoft utilizzato per le operazioni *apt-get* memorizzati nella cache.  I pacchetti di esempio includono Moby, PowerShell e l'interfaccia della riga di comando di Azure.Example packages include Moby, PowerShell, and Azure CLI. |
| acs-mirror.azureedge.net      | HTTPS:443 | Questo indirizzo è per il repository necessario per installare i file binari necessari come kubenet e Azure CNI. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Indirizzi e porte consigliati facoltativi per i cluster AKSOptional recommended addresses and ports for AKS clusters

Le seguenti porte in uscita / regole di rete sono facoltative per un cluster AKS:

Per il corretto funzionamento dei cluster AKS sono consigliate le seguenti regole di tipo FQDN/applicazione:

| Nome di dominio completo                                    | Porta      | Uso      |
|-----------------------------------------|-----------|----------|
| security.ubuntu.com, azure.archive.ubuntu.com changelogs.ubuntu.com | HTTP:80 (informazioni in due)   | Questo indirizzo consente ai nodi del cluster Linux di scaricare le patch di sicurezza e gli aggiornamenti necessari. |

## <a name="required-addresses-and-ports-for-gpu-enabled-aks-clusters"></a>Indirizzi e porte necessari per i cluster AKS abilitati per GPU

Le seguenti regole di tipo FQDN/applicazione sono necessarie per i cluster AKS con GPU abilitata:

| Nome di dominio completo                                    | Porta      | Uso      |
|-----------------------------------------|-----------|----------|
| nvidia.github.io | HTTPS:443 | Questo indirizzo viene utilizzato per la corretta installazione e funzionamento del driver nei nodi basati su GPU. |
| us.download.nvidia.com | HTTPS:443 | Questo indirizzo viene utilizzato per la corretta installazione e funzionamento del driver nei nodi basati su GPU. |
| apt.dockerproject.org | HTTPS:443 | Questo indirizzo viene utilizzato per la corretta installazione e funzionamento del driver nei nodi basati su GPU. |

## <a name="required-addresses-and-ports-with-azure-monitor-for-containers-enabled"></a>Indirizzi e porte necessari con Monitoraggio di Azure per i contenitori abilitatoRequired addresses and ports with Azure Monitor for containers enabled

Il nome di dominio completo/regole dell'applicazione seguente è necessario per i cluster AKS in cui è abilitato Monitoraggio di Azure per i contenitori:The following FQDN/ application rules are required for AKS clusters that have the Azure Monitor for containers enabled:

| Nome di dominio completo                                    | Porta      | Uso      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | HTTPS:443    | Si tratta di metriche corrette e di monitoraggio dei dati di telemetria tramite Monitoraggio di Azure.This is for correct metrics and monitoring telemetry using Azure Monitor. |
| *.ods.opinsights.azure.com    | HTTPS:443    | Viene usato da Monitoraggio di Azure per l'inserimento dei dati di analisi dei log. |
| *.oms.opinsights.azure.com | HTTPS:443 | Questo indirizzo viene utilizzato da omsagent, che viene utilizzato per autenticare il servizio di analisi dei log. |
|*.microsoftonline.com | HTTPS:443 | Viene usato per l'autenticazione e l'invio di metriche a Monitoraggio di Azure.This is used for authenticating and sending metrics to Azure Monitor. |
|.monitoring.azure.com | HTTPS:443 | Viene usato per inviare i dati delle metriche a Monitoraggio di Azure.This is used to send metrics data to Azure Monitor. |

## <a name="required-addresses-and-ports-with-azure-dev-spaces-enabled"></a>Indirizzi e porte necessari con Azure Dev Spaces abilitato

Le regole FQDN/applicazione seguenti sono necessarie per i cluster AKS in cui sono abilitati i spazi di sviluppo di Azure:The following FQDN/ application rules are required for AKS clusters that have the Azure Dev Spaces enabled:

| Nome di dominio completo                                    | Porta      | Uso      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | Questo indirizzo viene usato per estrarre linux alpino e altre immagini di Azure Dev SpacesThis address is used to pull linux alpine and other Azure Dev Spaces images |
| gcr.io | HTTP:443 (informazioni in due) | Questo indirizzo viene utilizzato per estrarre le immagini del timone/tiller |
| storage.googleapis.com | HTTP:443 (informazioni in due) | Questo indirizzo viene utilizzato per estrarre le immagini del timone/tiller |
| azds-\<\>guid . \<posizione\>.azds.io | HTTPS:443 | Per comunicare con i servizi back-end di Azure Dev Spaces per il controller. L'FQDN esatto è disponibile in "dataplaneFqdn"\.in %USERPROFILE% azds-settings.json |

## <a name="required-addresses-and-ports-for-aks-clusters-with-azure-policy-in-public-preview-enabled"></a>Indirizzi e porte obbligatori per i cluster AKS con Criteri di Azure (in anteprima pubblica) abilitati

> [!CAUTION]
> Alcune delle seguenti funzioni sono in anteprima.  I suggerimenti in questo articolo sono soggetti a modifiche quando la funzionalità passa alle fasi di anteprima e rilascio future.

Il nome di dominio completo/regole dell'applicazione seguente è necessario per i cluster AKS in cui sono abilitati i criteri di Azure.The following FQDN/ application rules are required for AKS clusters with the Azure Policy enabled.

| Nome di dominio completo                                    | Porta      | Uso      |
|-----------------------------------------|-----------|----------|
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | Questo indirizzo viene usato per il corretto funzionamento dei criteri di Azure.This address is used for correct operation of Azure Policy. (attualmente in anteprima in AKS) |
| raw.githubusercontent.com | HTTPS:443 | Questo indirizzo viene usato per eseguire il pull dei criteri predefiniti da GitHub per garantire il corretto funzionamento dei criteri di Azure.This address is used to pull the built-in policies from GitHub to ensure correct operation of Azure Policy. (attualmente in anteprima in AKS) |
| .gk. \<posizione\>.azmk8s.io | HTTPS:443    | Componente aggiuntivo di criteri di Azure che comunica con l'endpoint di controllo Gatekeeper in esecuzione nel server master per ottenere i risultati del controllo. |
| dc.services.visualstudio.com | HTTPS:443 | Componente aggiuntivo Criteri di Azure che invia dati di telemetria all'endpoint delle informazioni dettagliate delle applicazioni. |

## <a name="required-by-windows-server-based-nodes-in-public-preview-enabled"></a>Richiesto dai nodi basati su Windows Server (nell'anteprima pubblica) abilitato

> [!CAUTION]
> Alcune delle seguenti funzioni sono in anteprima.  I suggerimenti in questo articolo sono soggetti a modifiche quando la funzionalità passa alle fasi di anteprima e rilascio future.

Per i cluster AKS basati su Windows Server sono necessarie le seguenti regole di tipo FQDN/applicazione:

| Nome di dominio completo                                    | Porta      | Uso      |
|-----------------------------------------|-----------|----------|
| onegetcdn.azureedge.net, winlayers.blob.core.windows.net, winlayers.cdn.mscr.io, go.microsoft.com | HTTPS:443 | Per installare file binari relativi a Windows |
| mp.microsoft.com,<span></span>www.msftconnecttest.com, ctldl.windowsupdate.com | HTTP:80 (informazioni in due) | Per installare file binari relativi a Windows |
| kms.core.windows.net | TCP:1688 (informazioni in inglese) | Per installare file binari relativi a Windows |


## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati illustrati gli indirizzi e le porte da consentire se si limita il traffico in uscita per il cluster. Potete anche definire come i pod stessi possono comunicare e quali restrizioni hanno all'interno del cluster. Per ulteriori informazioni, consultate Proteggere il traffico tra pod utilizzando i criteri di [rete in AKS.][network-policy]

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
