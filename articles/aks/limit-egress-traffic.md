---
title: Limitare il traffico in uscita in Azure Kubernetes Service (AKS)
description: Informazioni sulle porte e sugli indirizzi necessari per controllare il traffico in uscita in Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 08/29/2019
ms.author: mlearned
ms.openlocfilehash: 3010973c7d0af784938e9295bb80fc22b7f718f3
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018651"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>Controllare il traffico in uscita per i nodi del cluster in Azure Kubernetes Service (AKS)

Per impostazione predefinita, i cluster AKS hanno accesso a Internet in uscita senza restrizioni (in uscita). Questo livello di accesso alla rete consente i nodi e i servizi eseguiti per accedere alle risorse esterne, se necessario. Se si vuole limitare il traffico in uscita, un numero limitato di porte e indirizzi deve essere accessibile per mantenere integre le attività di manutenzione del cluster. Per impostazione predefinita, il cluster è configurato in modo da usare solo le immagini del contenitore di sistema di base di Microsoft Container Registry (Container Registry) o Azure (ACR). Configurare le regole firewall e di sicurezza preferite per consentire le porte e gli indirizzi richiesti.

Questo articolo illustra in dettaglio le porte di rete e i nomi di dominio completi (FQDN) necessari e facoltativi se si limita il traffico in uscita in un cluster AKS.

> [!IMPORTANT]
> Questo documento illustra solo come bloccare il traffico in uscita dalla subnet AKS. AKS non ha requisiti di ingresso.  Il blocco del traffico della subnet interna con i gruppi di sicurezza di rete (gruppi) e i firewall non è supportato. Per controllare e bloccare il traffico all'interno del cluster, usare i [criteri di rete][network-policy].

## <a name="before-you-begin"></a>Prima di iniziare

È necessaria l'interfaccia della riga di comando di Azure versione 2.0.66 o successiva installata e configurata. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="egress-traffic-overview"></a>Panoramica sul traffico in uscita

A scopo di gestione e operativi, i nodi in un cluster AKS devono accedere a determinate porte e nomi di dominio completi (FQDN). Queste azioni possono essere la comunicazione con il server API oppure per scaricare e installare i componenti principali del cluster Kubernetes e gli aggiornamenti della sicurezza dei nodi. Per impostazione predefinita, il traffico Internet in uscita (in uscita) non è limitato per i nodi in un cluster AKS. Il cluster può estrarre le immagini del contenitore di sistema di base da repository esterni.

Per aumentare la sicurezza del cluster AKS, è possibile che si desideri limitare il traffico in uscita. Il cluster è configurato per eseguire il pull delle immagini del contenitore di sistema di base da o da o ACR. Se si blocca il traffico in uscita in questo modo, definire porte e FQDN specifici per consentire ai nodi AKS di comunicare correttamente con i servizi esterni richiesti. Senza queste porte e FQDN autorizzati, i nodi AKS non possono comunicare con il server API o installare componenti di base.

È possibile usare il [firewall di Azure][azure-firewall] o un appliance firewall di terze parti per proteggere il traffico in uscita e definire le porte e gli indirizzi richiesti. AKS non crea automaticamente queste regole. Le porte e gli indirizzi seguenti sono per riferimento quando si creano le regole appropriate nel firewall di rete.

> [!IMPORTANT]
> Quando si usa il firewall di Azure per limitare il traffico in uscita e creare una route definita dall'utente (UDR) per forzare tutto il traffico in uscita, assicurarsi di creare una regola DNAT appropriata nel firewall per consentire correttamente il traffico in ingresso. L'uso del firewall di Azure con un UDR interrompe la configurazione in ingresso a causa del routing asimmetrico. Il problema si verifica se la subnet AKS ha una route predefinita che passa all'indirizzo IP privato del firewall, ma si usa un servizio di bilanciamento del carico pubblico-ingress o Kubernetes di tipo: LoadBalancer). In questo caso, il traffico del servizio di bilanciamento del carico in ingresso viene ricevuto tramite l'indirizzo IP pubblico, ma il percorso di ritorno passa attraverso l'indirizzo IP privato del firewall. Poiché il firewall è con stato, Elimina il pacchetto restituito perché il firewall non è a conoscenza di una sessione stabilita. Per informazioni su come integrare il firewall di Azure con il servizio di bilanciamento del carico in ingresso o servizio, vedere [integrare il firewall di Azure con azure Load Balancer standard](https://docs.microsoft.com/azure/firewall/integrate-lb).
> È possibile bloccare il traffico per la porta TCP 9000 e la porta TCP 22 usando una regola di rete tra gli IP del nodo di lavoro in uscita e l'IP per il server API.

In AKS sono disponibili due set di porte e indirizzi:

* Le [porte e gli indirizzi richiesti per i cluster AKS](#required-ports-and-addresses-for-aks-clusters) illustrano in dettaglio i requisiti minimi per il traffico in uscita autorizzato.
* Gli [indirizzi e le porte consigliati facoltativi per i cluster AKS](#optional-recommended-addresses-and-ports-for-aks-clusters) non sono necessari per tutti gli scenari, ma l'integrazione con altri servizi, ad esempio monitoraggio di Azure, non funzionerà correttamente. Esaminare questo elenco di porte e FQDN facoltativi e autorizzare i servizi e i componenti usati nel cluster AKS.

> [!NOTE]
> La limitazione del traffico in uscita funziona solo nei nuovi cluster AKS. Per i cluster esistenti, [eseguire un'operazione di aggiornamento][aks-upgrade] del cluster `az aks upgrade` usando il comando prima di limitare il traffico in uscita.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>Porte e indirizzi obbligatori per i cluster AKS

Per un cluster AKS sono necessarie le seguenti regole di rete/porte in uscita:

* Porta TCP *443*
* TCP [IPAddrOfYourAPIServer]: la porta 443 è obbligatoria se si dispone di un'app che deve comunicare con il server API.  Questa modifica può essere impostata dopo la creazione del cluster.
* Porta TCP *9000* e la porta TCP *22* per il Pod anteriore del tunnel per comunicare con l'estremità del tunnel nel server API.
    * Per ottenere informazioni più specifiche, vedere * *. HPC.\< location\>. azmk8s.io* e * *. tun.\< location\>. azmk8s.io* indirizzi nella tabella seguente.
* La porta UDP *53* per DNS è necessaria anche se si dispone di pod che accedono direttamente al server API.

Sono necessarie le seguenti regole di FQDN/applicazione:

| FQDN                       | Port      | Utilizzo      |
|----------------------------|-----------|----------|
| *.hcp.\<location\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000 | Questo indirizzo è l'endpoint del server API. *Sostituire\<location\>* con l'area in cui è distribuito il cluster AKS. |
| *.tun.\<location\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000 | Questo indirizzo è l'endpoint del server API. *Sostituire\<location\>* con l'area in cui è distribuito il cluster AKS. |
| aksrepos.azurecr.io        | HTTPS:443 | Questo indirizzo è necessario per accedere alle immagini in Azure Container Registry (ACR). Questo registro contiene immagini o grafici di terze parti (ad esempio, server delle metriche, DNS di base e così via) necessari per il funzionamento del cluster durante l'aggiornamento e la scalabilità del cluster|
| *.blob.core.windows.net    | HTTPS:443 | Questo indirizzo è l'archivio back-end per le immagini archiviate in ACR. |
| mcr.microsoft.com          | HTTPS:443 | Questo indirizzo è necessario per accedere alle immagini in Microsoft Container Registry. Questo registro contiene immagini o grafici di terze parti (ad esempio, Moby e così via) necessari per il funzionamento del cluster durante l'aggiornamento e la scalabilità del cluster |
| *.cdn.mscr.io              | HTTPS:443 | Questo indirizzo è necessario per l'archiviazione di un sistema di archiviazione con supporto per la rete per la distribuzione di contenuti (CDN) di Azure. |
| management.azure.com       | HTTPS:443 | Questo indirizzo è obbligatorio per le operazioni GET/PUT Kubernetes. |
| login.microsoftonline.com  | HTTPS:443 | Questo indirizzo è necessario per l'autenticazione Azure Active Directory. |
| ntp.ubuntu.com             | UDP: 123   | Questo indirizzo è necessario per la sincronizzazione dell'ora NTP nei nodi Linux. |
| packages.microsoft.com     | HTTPS:443 | Questo indirizzo è il repository dei pacchetti Microsoft usato per le operazioni *apt-get* memorizzate nella cache.  I pacchetti di esempio includono Moby, PowerShell e l'interfaccia della riga di comando di Azure. |
| acs-mirror.azureedge.net   | HTTPS:443 | Questo indirizzo è per il repository necessario per installare i file binari necessari, ad esempio kubenet e Azure CNI. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Indirizzi e porte consigliati facoltativi per i cluster AKS

Le seguenti regole di rete/porte in uscita sono facoltative per un cluster AKS:

Per il corretto funzionamento dei cluster AKS sono consigliate le seguenti regole per il nome di dominio completo/applicazione:

| FQDN                                    | Port      | Utilizzo      |
|-----------------------------------------|-----------|----------|
| security.ubuntu.com, azure.archive.ubuntu.com, changelogs.ubuntu.com | HTTP:80   | Questo indirizzo consente ai nodi del cluster Linux di scaricare le patch di sicurezza e gli aggiornamenti necessari. |

## <a name="required-addresses-and-ports-for-gpu-enabled-aks-clusters"></a>Indirizzi e porte richiesti per i cluster AKS abilitati per la GPU

Per i cluster AKS per i quali è abilitata la GPU sono necessarie le seguenti regole di FQDN/applicazione:

| FQDN                                    | Port      | Utilizzo      |
|-----------------------------------------|-----------|----------|
| nvidia.github.io | HTTPS:443 | Questo indirizzo viene usato per l'installazione e il funzionamento corretto dei driver nei nodi basati su GPU. |
| us.download.nvidia.com | HTTPS:443 | Questo indirizzo viene usato per l'installazione e il funzionamento corretto dei driver nei nodi basati su GPU. |
| apt.dockerproject.org | HTTPS:443 | Questo indirizzo viene usato per l'installazione e il funzionamento corretto dei driver nei nodi basati su GPU. |

## <a name="required-addresses-and-ports-with-azure-monitor-for-containers-enabled"></a>Indirizzi e porte necessari con monitoraggio di Azure per i contenitori abilitati

Per i cluster AKS in cui è abilitato il monitoraggio di Azure per i contenitori sono necessarie le seguenti regole di FQDN/applicazione:

| FQDN                                    | Port      | Utilizzo      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | HTTPS:443  | Questo è per le metriche corrette e per il monitoraggio dei dati di telemetria con monitoraggio di Azure. |
| *.ods.opinsights.azure.com    | HTTPS:443 | Viene usato da monitoraggio di Azure per l'inserimento dei dati di log Analytics. |
| *.oms.opinsights.azure.com | HTTPS:443 | Questo indirizzo viene usato da omsagent, che viene usato per autenticare il servizio log Analytics. |
|*.microsoftonline.com | HTTPS:443 | Viene usato per l'autenticazione e l'invio di metriche a monitoraggio di Azure. |
|*.monitoring.azure.com | HTTPS:443 | Questa operazione viene usata per inviare i dati delle metriche a monitoraggio di Azure. |

## <a name="required-addresses-and-ports-for-aks-clusters-with-azure-policy-in-public-preview-enabled"></a>Indirizzi e porte richiesti per i cluster AKS con criteri di Azure (in anteprima pubblica) abilitati

> [!CAUTION]
> Alcune delle funzionalità seguenti sono disponibili in anteprima.  I suggerimenti in questo articolo sono soggetti a modifiche quando la funzionalità passa alla versione di anteprima pubblica e alle fasi successive.

Le seguenti regole di dominio completo/applicazione sono necessarie per i cluster AKS per i quali è abilitato il criterio di Azure.

| FQDN                                    | Port      | Utilizzo      |
|-----------------------------------------|-----------|----------|
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | Questo indirizzo viene usato per il corretto funzionamento di criteri di Azure. (attualmente in anteprima in AKS) |
| raw.githubusercontent.com | HTTPS:443 | Questo indirizzo viene usato per eseguire il pull dei criteri predefiniti da GitHub per assicurare il corretto funzionamento di criteri di Azure. (attualmente in anteprima in AKS) |
| *. GK. <location>. azmk8s.io | HTTPS:443 | Il componente aggiuntivo criteri di Azure comunica con l'endpoint di controllo gatekeeper in esecuzione nel server master per ottenere i risultati del controllo. |
| dc.services.visualstudio.com | HTTPS:443 | Il componente aggiuntivo criteri di Azure invia i dati di telemetria all'endpoint di Application Insights. |

## <a name="required-by-windows-server-based-nodes-in-public-preview-enabled"></a>Richiesto dai nodi basati su Windows Server (in anteprima pubblica) abilitato

> [!CAUTION]
> Alcune delle funzionalità seguenti sono disponibili in anteprima.  I suggerimenti in questo articolo sono soggetti a modifiche quando la funzionalità passa alla versione di anteprima pubblica e alle fasi successive.

Per i cluster AKS basati su Windows Server sono necessarie le seguenti regole di FQDN/applicazione:

| FQDN                                    | Port      | Utilizzo      |
|-----------------------------------------|-----------|----------|
| onegetcdn.azureedge.net, winlayers.blob.core.windows.net, winlayers.cdn.mscr.io, go.microsoft.com | HTTPS:443 | Per installare i file binari correlati a Windows |
| mp.microsoft.com, www<span></span>. msftconnecttest.com, ctldl.windowsupdate.com | HTTP:80 | Per installare i file binari correlati a Windows |
| kms.core.windows.net | TCP: 1688 | Per installare i file binari correlati a Windows |


## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state apprese le porte e gli indirizzi da consentire se si limita il traffico in uscita per il cluster. È anche possibile definire il modo in cui i baccelli possono comunicare e le restrizioni presenti all'interno del cluster. Per altre informazioni, vedere [proteggere il traffico tra i pod usando i criteri di rete in AKS][network-policy].

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
