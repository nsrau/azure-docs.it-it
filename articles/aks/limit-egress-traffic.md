---
title: Limitare il traffico in uscita nel servizio Azure Kubernetes
description: Informazioni sulle porte e sugli indirizzi necessari per controllare il traffico in uscita nel servizio Azure Kubernetes
services: container-service
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: 194e799daf107220c28404001d223e521dceeb3f
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83870898"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>Controllare il traffico in uscita per i nodi del cluster nel servizio Azure Kubernetes

Per impostazione predefinita, i cluster del servizio Azure Kubernetes hanno accesso a Internet in uscita senza restrizioni. Questo livello di accesso alla rete consente a nodi e servizi in esecuzione di accedere alle risorse esterne in base alle esigenze. Se si vuole limitare il traffico in uscita, è necessario rendere accessibile un numero limitato di porte e indirizzi per mantenere l'integrità delle attività di manutenzione del cluster. Per impostazione predefinita, il cluster viene configurato in modo da usare solo le immagini dei contenitori di sistema di base del registro contenitori Microsoft o del Registro Azure Container. Configurare le regole di sicurezza e del firewall preferite per consentire le porte e gli indirizzi richiesti.

Questo articolo illustra in dettaglio le porte di rete e i nomi di dominio completi (FQDN) obbligatori e facoltativi se si limita il traffico in uscita in un cluster del servizio Azure Kubernetes.

> [!IMPORTANT]
> Questo documento illustra solo come bloccare il traffico in uscita dalla subnet del servizio Azure Kubernetes. Il servizio Azure Kubernetes non prevede requisiti per il traffico in ingresso.  Il blocco del traffico interno della subnet usando gruppi di sicurezza di rete e firewall non è supportato. Per controllare e bloccare il traffico all'interno del cluster, usare [criteri di rete][network-policy].

## <a name="before-you-begin"></a>Prima di iniziare

È necessario che sia installata e configurata l'interfaccia della riga di comando di Azure 2.0.66 o versione successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="egress-traffic-overview"></a>Panoramica sul traffico in uscita

A scopi di gestione e operativi, i nodi in un cluster del servizio Azure Kubernetes devono poter accedere a porte e nomi di dominio completi (FQDN) specifici. Queste azioni possono essere la comunicazione con il server API oppure il download e l'installazione dei componenti principali del cluster Kubernetes e degli aggiornamenti della sicurezza dei nodi. Per impostazione predefinita, il traffico Internet in uscita non è limitato per i nodi in un cluster del servizio Azure Kubernetes. Il cluster può eseguire il pull delle immagini dei contenitori di sistema di base dai repository esterni.

Per aumentare la sicurezza del cluster del servizio Azure Kubernetes, può essere necessario limitare il traffico in uscita. Il cluster è configurato per eseguire il pull delle immagini dei contenitori di sistema di base dal registro contenitori Microsoft o dal Registro Azure Container. Se si blocca il traffico in uscita in questo modo, definire porte e nomi di dominio completi specifici per consentire ai nodi del servizio Azure Kubernetes di comunicare correttamente con i servizi esterni necessari. Senza queste porte e questi nomi di dominio completi autorizzati, i nodi del servizio Azure Kubernetes non possono comunicare con il server API o installare i componenti di base.

È possibile usare il servizio [Firewall di Azure][azure-firewall] o un'appliance firewall di terze parti per proteggere il traffico in uscita e definire le porte e gli indirizzi necessari. Il servizio Azure Kubernetes non crea automaticamente queste regole. Le porte e gli indirizzi seguenti sono a scopo di riferimento per creare le regole appropriate nel firewall di rete.

> [!IMPORTANT]
> Quando si usa Firewall di Azure per limitare il traffico in uscita e creare una route definita dall'utente (UDR) per forzare tutto il traffico in uscita, assicurarsi di creare una regola DNAT appropriata nel firewall per consentire correttamente il traffico in ingresso. L'uso del servizio Firewall di Azure con una route definita dall'utente interrompe la configurazione in ingresso a causa del routing asimmetrico. Questo problema si verifica se la subnet del servizio Azure Kubernetes ha una route predefinita verso l'indirizzo IP privato del firewall, ma si sta usando un servizio di bilanciamento del carico pubblico in ingresso o del servizio Kubernetes di tipo LoadBalancer. In questo caso, il traffico del servizio di bilanciamento del carico in ingresso viene ricevuto tramite l'indirizzo IP pubblico, ma il percorso di ritorno passa attraverso l'indirizzo IP privato del firewall. Poiché il firewall è con stato, elimina il pacchetto di ritorno perché il firewall non è a conoscenza del fatto che è stata stabilita una sessione. Per informazioni su come integrare Firewall di Azure con il servizio di bilanciamento del carico in ingresso o del servizio, vedere [Integrare Firewall di Azure con Azure Load Balancer Standard](https://docs.microsoft.com/azure/firewall/integrate-lb).
> È possibile bloccare il traffico per la porta TCP 9000, la porta TCP 22 e la porta UDP 1194 usando una regola di rete tra gli IP del nodo di lavoro in uscita e l'IP per il server API.

Nel servizio Azure Kubernetes ci sono due set di porte e indirizzi:

* [Le porte e gli indirizzi obbligatori per i cluster del servizio Azure Kubernetes](#required-ports-and-addresses-for-aks-clusters) indicano i requisiti minimi per il traffico in uscita autorizzato.
* [Le porte e gli indirizzi facoltativi consigliati per i cluster del servizio Azure Kubernetes](#optional-recommended-addresses-and-ports-for-aks-clusters) non sono necessari per tutti gli scenari, ma servono per il corretto funzionamento dell'integrazione con altri servizi, ad esempio Monitoraggio di Azure. Esaminare questo elenco di porte e nomi di dominio completi facoltativi e autorizzare i servizi e i componenti usati nel cluster del servizio Azure Kubernetes.

> [!NOTE]
> La limitazione del traffico in uscita funziona solo nei nuovi cluster del servizio Azure Kubernetes. Per i cluster esistenti, [eseguire un'operazione di aggiornamento del cluster][aks-upgrade] usando il comando `az aks upgrade` prima di limitare il traffico in uscita.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>Porte e indirizzi obbligatori per i cluster del servizio Azure Kubernetes

Le porte in uscita/regole di rete seguenti sono obbligatorie per un cluster del servizio Azure Kubernetes:

* Porta TCP *443*
* TCP [IndirizzoIPServerAPI]:443, obbligatoria se è presente un'app che deve comunicare con il server API.  Questa modifica può essere impostata dopo la creazione del cluster.
* Porta TCP *9000*, porta TCP *22* e porta UDP *1194* per la comunicazione del pod anteriore del tunnel con la parte finale del tunnel nel server API.
    * Per informazioni più specifiche, vedere gli indirizzi * *.hcp.\<località\>.azmk8s.io* e * *.tun.\<località\>.azmk8s.io* nella tabella seguente.
* Porta UDP *123* per la sincronizzazione dell'ora del protocollo NTP (Network Time Protocol) (nodi Linux).
* Porta UDP *53* per DNS, necessaria se sono presenti pod che accedono direttamente al server API.

Le regole delle applicazioni e FQDN seguenti sono obbligatorie:

> [!IMPORTANT]
> * **.blob.core.windows.net e aksrepos.azurecr.io** non sono più regole FQDN obbligatorie per il blocco del traffico in uscita.  Per i cluster esistenti, [eseguire un'operazione di aggiornamento del cluster][aks-upgrade] usando il comando `az aks upgrade` per rimuovere queste regole.

> [!IMPORTANT]
> *.cdn.mscr.io è stato sostituito da *.data.mcr.microsoft.com per le aree del cloud pubblico di Azure. Aggiornare le regole del firewall esistenti per rendere effettive le modifiche.

- Azure Global

| Nome di dominio completo                       | Porta      | Uso      |
|----------------------------|-----------|----------|
| *.hcp.\<località\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Questo indirizzo è necessario per la comunicazione tra nodo e server API. Sostituire *\<località\>* con l'area in cui è distribuito il cluster del servizio Azure Kubernetes. |
| *.tun.\<località\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Questo indirizzo è necessario per la comunicazione tra nodo e server API. Sostituire *\<località\>* con l'area in cui è distribuito il cluster del servizio Azure Kubernetes. |
| *.cdn.mscr.io       | HTTPS:443 | Questo indirizzo è necessario per l'archiviazione del registro contenitori Microsoft basata sul servizio Rete di distribuzione dei contenuti di Azure. |
| mcr.microsoft.com          | HTTPS:443 | Questo indirizzo è necessario per accedere alle immagini nel registro contenitori Microsoft. Questo registro contiene immagini e grafici proprietari (ad esempio Moby e così via) necessari per il funzionamento del cluster durante l'aggiornamento e il ridimensionamento |
| *.data.mcr.microsoft.com             | HTTPS:443 | Questo indirizzo è necessario per l'archiviazione del registro contenitori Microsoft basata sul servizio Rete di distribuzione dei contenuti di Azure. |
| management.azure.com       | HTTPS:443 | Questo indirizzo è necessario per le operazioni GET/PUT Kubernetes. |
| login.microsoftonline.com  | HTTPS:443 | Questo indirizzo è necessario per l'autenticazione di Azure Active Directory. |
| ntp.ubuntu.com             | UDP:123   | Questo indirizzo è necessario per la sincronizzazione dell'ora NTP nei nodi Linux. |
| packages.microsoft.com     | HTTPS:443 | Questo indirizzo è il repository di pacchetti Microsoft usato per le operazioni *apt-get* memorizzate nella cache.  I pacchetti di esempio includono Moby, PowerShell e l'interfaccia della riga di comando di Azure. |
| acs-mirror.azureedge.net      | HTTPS:443 | Questo indirizzo è per il repository necessario per installare i file binari richiesti, come Kubenet e Azure CNI. |

- 21Vianet per Azure Cina

| Nome di dominio completo                       | Porta      | Uso      |
|----------------------------|-----------|----------|
| *.hcp.\<località\>.cx.prod.service.azk8s.cn | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Questo indirizzo è necessario per la comunicazione tra nodo e server API. Sostituire *\<località\>* con l'area in cui è distribuito il cluster del servizio Azure Kubernetes. |
| *.tun.\<località\>.cx.prod.service.azk8s.cn | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Questo indirizzo è necessario per la comunicazione tra nodo e server API. Sostituire *\<località\>* con l'area in cui è distribuito il cluster del servizio Azure Kubernetes. |
| *.azk8s.cn        | HTTPS:443 | Questo indirizzo è necessario per scaricare le immagini e i file binari richiesti|
| mcr.microsoft.com          | HTTPS:443 | Questo indirizzo è necessario per accedere alle immagini nel registro contenitori Microsoft. Questo registro contiene immagini e grafici proprietari (ad esempio Moby e così via) necessari per il funzionamento del cluster durante l'aggiornamento e il ridimensionamento |
| *.cdn.mscr.io       | HTTPS:443 | Questo indirizzo è necessario per l'archiviazione del registro contenitori Microsoft basata sul servizio Rete di distribuzione dei contenuti di Azure. |
| *.data.mcr.microsoft.com             | HTTPS:443 | Questo indirizzo è necessario per l'archiviazione del registro contenitori Microsoft basata sul servizio Rete di distribuzione dei contenuti di Azure. |
| management.chinacloudapi.cn       | HTTPS:443 | Questo indirizzo è necessario per le operazioni GET/PUT Kubernetes. |
| login.chinacloudapi.cn  | HTTPS:443 | Questo indirizzo è necessario per l'autenticazione di Azure Active Directory. |
| ntp.ubuntu.com             | UDP:123   | Questo indirizzo è necessario per la sincronizzazione dell'ora NTP nei nodi Linux. |
| packages.microsoft.com     | HTTPS:443 | Questo indirizzo è il repository di pacchetti Microsoft usato per le operazioni *apt-get* memorizzate nella cache.  I pacchetti di esempio includono Moby, PowerShell e l'interfaccia della riga di comando di Azure. |

- Azure Government

| Nome di dominio completo                       | Porta      | Uso      |
|----------------------------|-----------|----------|
| *.hcp.\<località\>.cx.aks.containerservice.azure.us | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Questo indirizzo è necessario per la comunicazione tra nodo e server API. Sostituire *\<località\>* con l'area in cui è distribuito il cluster del servizio Azure Kubernetes. |
| *.tun.\<località\>.cx.aks.containerservice.azure.us | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Questo indirizzo è necessario per la comunicazione tra nodo e server API. Sostituire *\<località\>* con l'area in cui è distribuito il cluster del servizio Azure Kubernetes. |
| mcr.microsoft.com          | HTTPS:443 | Questo indirizzo è necessario per accedere alle immagini nel registro contenitori Microsoft. Questo registro contiene immagini e grafici proprietari (ad esempio Moby e così via) necessari per il funzionamento del cluster durante l'aggiornamento e il ridimensionamento |
|*.cdn.mscr.io              | HTTPS:443 | Questo indirizzo è necessario per l'archiviazione del registro contenitori Microsoft basata sul servizio Rete di distribuzione dei contenuti di Azure. |
| *.data.mcr.microsoft.com             | HTTPS:443 | Questo indirizzo è necessario per l'archiviazione del registro contenitori Microsoft basata sul servizio Rete di distribuzione dei contenuti di Azure. |
| management.usgovcloudapi.net       | HTTPS:443 | Questo indirizzo è necessario per le operazioni GET/PUT Kubernetes. |
| login.microsoftonline.us  | HTTPS:443 | Questo indirizzo è necessario per l'autenticazione di Azure Active Directory. |
| ntp.ubuntu.com             | UDP:123   | Questo indirizzo è necessario per la sincronizzazione dell'ora NTP nei nodi Linux. |
| packages.microsoft.com     | HTTPS:443 | Questo indirizzo è il repository di pacchetti Microsoft usato per le operazioni *apt-get* memorizzate nella cache.  I pacchetti di esempio includono Moby, PowerShell e l'interfaccia della riga di comando di Azure. |
| acs-mirror.azureedge.net      | HTTPS:443 | Questo indirizzo è per il repository necessario per installare i file binari richiesti, come Kubenet e Azure CNI. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Porte e indirizzi facoltativi consigliati per i cluster del servizio Azure Kubernetes

Le porte in uscita/regole di rete seguenti sono facoltative per un cluster del servizio Azure Kubernetes:

Le regole delle applicazioni e FQDN seguenti sono consigliate per il corretto funzionamento dei cluster del servizio Azure Kubernetes:

| Nome di dominio completo                                    | Porta      | Uso      |
|-----------------------------------------|-----------|----------|
| security.ubuntu.com, azure.archive.ubuntu.com, changelogs.ubuntu.com | HTTP:80   | Questo indirizzo consente ai nodi del cluster Linux di scaricare le patch di sicurezza e gli aggiornamenti necessari. |

## <a name="required-addresses-and-ports-for-gpu-enabled-aks-clusters"></a>Porte e indirizzi obbligatori per i cluster del servizio Azure Kubernetes abilitati per la GPU

Le regole delle applicazioni e FQDN seguenti sono obbligatorie per i cluster del servizio Azure Kubernetes con la GPU abilitata:

| Nome di dominio completo                                    | Porta      | Uso      |
|-----------------------------------------|-----------|----------|
| nvidia.github.io | HTTPS:443 | Questo indirizzo viene usato per l'installazione e il funzionamento corretti dei driver nei nodi basati su GPU. |
| us.download.nvidia.com | HTTPS:443 | Questo indirizzo viene usato per l'installazione e il funzionamento corretti dei driver nei nodi basati su GPU. |
| apt.dockerproject.org | HTTPS:443 | Questo indirizzo viene usato per l'installazione e il funzionamento corretti dei driver nei nodi basati su GPU. |

## <a name="required-addresses-and-ports-with-azure-monitor-for-containers-enabled"></a>Porte e indirizzi obbligatori con Monitoraggio di Azure per i contenitori abilitato

Le regole delle applicazioni e FQDN seguenti sono obbligatorie per i cluster del servizio Azure Kubernetes con Monitoraggio di Azure per i contenitori abilitato:

| Nome di dominio completo                                    | Porta      | Uso      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | HTTPS:443    | Questo indirizzo viene usato per garantire la correttezza di metriche e dati di telemetria del monitoraggio con Monitoraggio di Azure. |
| *.ods.opinsights.azure.com    | HTTPS:443    | Questo indirizzo viene usato da Monitoraggio di Azure per l'inserimento dei dati di analisi dei log. |
| *.oms.opinsights.azure.com | HTTPS:443 | Questo indirizzo viene usato da omsagent, che consente di autenticare il servizio di analisi dei log. |
|*.microsoftonline.com | HTTPS:443 | Questo indirizzo viene usato per l'autenticazione e l'invio di metriche a Monitoraggio di Azure. |
|*.monitoring.azure.com | HTTPS:443 | Questo indirizzo viene usato per l'invio dei dati delle metriche a Monitoraggio di Azure. |

## <a name="required-addresses-and-ports-with-azure-dev-spaces-enabled"></a>Porte e indirizzi obbligatori con Azure Dev Spaces abilitato

Le regole delle applicazioni e FQDN seguenti sono obbligatorie per i cluster del servizio Azure Kubernetes con Azure Dev Spaces abilitato:

| Nome di dominio completo                                    | Porta      | Uso      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | Questo indirizzo viene usato per eseguire il pull di immagini di Alpine Linux e di altre immagini di Azure Dev Spaces |
| gcr.io | HTTPS:443 | Questo indirizzo viene usato per eseguire il pull di immagini Helm/Tiller |
| storage.googleapis.com | HTTPS:443 | Questo indirizzo viene usato per eseguire il pull di immagini Helm/Tiller |
| azds-\<guid\>.\<località\>.azds.io | HTTPS:443 | Per comunicare con i servizi back-end di Azure Dev Spaces per il controller. Il nome di dominio completo esatto è reperibile nel valore "dataplaneFqdn" del file %USERPROFILE%\.azds\settings.json |

## <a name="required-addresses-and-ports-for-aks-clusters-with-azure-policy-in-public-preview-enabled"></a>Porte e indirizzi obbligatori per i cluster del servizio Azure Kubernetes con il servizio Criteri di Azure (in anteprima pubblica) abilitato

> [!CAUTION]
> Alcune delle funzionalità seguenti sono disponibili in anteprima.  I suggerimenti in questo articolo sono soggetti a modifiche quando la funzionalità passerà alla versione di anteprima pubblica e alle fasi di rilascio successive.

Le regole delle applicazioni e FQDN seguenti sono obbligatorie per i cluster del servizio Azure Kubernetes con il servizio Criteri di Azure abilitato.

| Nome di dominio completo                                    | Porta      | Uso      |
|-----------------------------------------|-----------|----------|
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | Questo indirizzo viene usato per il corretto funzionamento di Criteri di Azure (attualmente in anteprima nel servizio Azure Kubernetes). |
| raw.githubusercontent.com | HTTPS:443 | Questo indirizzo viene usato per eseguire il pull dei criteri predefiniti da GitHub per assicurare il corretto funzionamento di Criteri di Azure (attualmente in anteprima nel servizio Azure Kubernetes). |
| *.gk.\<località\>.azmk8s.io | HTTPS:443    | Componente aggiuntivo di Criteri di Azure che comunica con l'endpoint di controllo Gatekeeper in esecuzione nel server master per ottenere i risultati del controllo. |
| dc.services.visualstudio.com | HTTPS:443 | Componente aggiuntivo di Criteri di Azure che invia i dati di telemetria all'endpoint di Application Insights. |

## <a name="required-by-windows-server-based-nodes-enabled"></a>Obbligatorio per i nodi basati su Windows Server abilitati

Le regole delle applicazioni e FQDN seguenti sono obbligatorie per l'uso di pool di nodi basati su Windows Server:

| Nome di dominio completo                                    | Porta      | Uso      |
|-----------------------------------------|-----------|----------|
| onegetcdn.azureedge.net, winlayers.blob.core.windows.net, winlayers.cdn.mscr.io, go.microsoft.com | HTTPS:443 | Per installare file binari correlati a Windows |
| mp.microsoft.com, www<span></span>.msftconnecttest.com, ctldl.windowsupdate.com | HTTP:80 | Per installare file binari correlati a Windows |
| kms.core.windows.net | TCP:1688 | Per installare file binari correlati a Windows |

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state fornite informazioni sulle porte e sugli indirizzi da consentire se si limita il traffico in uscita per il cluster. È anche possibile definire il modo in cui i pod possono comunicare e le restrizioni da applicare all'interno del cluster. Per altre informazioni, vedere [Proteggere il traffico tra i pod usando criteri di rete nel servizio Azure Kubernetes][network-policy].

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
