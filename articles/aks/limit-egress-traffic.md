---
title: Limitare il traffico in uscita in Azure Kubernetes Service (AKS)
description: Informazioni sulle porte e sugli indirizzi necessari per controllare il traffico in uscita in Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/06/2019
ms.author: mlearned
ms.openlocfilehash: 369729f10de4a55cd14bb866795ea1aa15b3d9da
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639792"
---
# <a name="preview---limit-egress-traffic-for-cluster-nodes-and-control-access-to-required-ports-and-services-in-azure-kubernetes-service-aks"></a>Anteprima: limitare il traffico in uscita per i nodi del cluster e controllare l'accesso alle porte e ai servizi richiesti in Azure Kubernetes Service (AKS)

Per impostazione predefinita, i cluster AKS hanno accesso a Internet in uscita senza restrizioni (in uscita). Questo livello di accesso alla rete consente i nodi e i servizi eseguiti per accedere alle risorse esterne, se necessario. Se si vuole limitare il traffico in uscita, un numero limitato di porte e indirizzi deve essere accessibile per mantenere integre le attività di manutenzione del cluster. Il cluster viene quindi configurato in modo da usare solo le immagini del contenitore di sistema di base di Microsoft Container Registry (Container Registry) o Azure (ACR), non i repository pubblici esterni. È necessario configurare le regole del firewall e di sicurezza preferite per consentire le porte e gli indirizzi richiesti.

Questo articolo illustra in dettaglio le porte di rete e i nomi di dominio completi (FQDN) necessari e facoltativi se si limita il traffico in uscita in un cluster AKS.  Questa funzionalità è attualmente in anteprima.

> [!IMPORTANT]
> Le funzionalità di anteprima di AKS sono il consenso esplicito self-service. Le anteprime vengono fornite "così come sono" e "come disponibili" e sono escluse dai contratti di servizio e dalla garanzia limitata. Le anteprime AKS sono parzialmente coperte dal supporto tecnico per il massimo sforzo. Di conseguenza, queste funzionalità non sono destinate all'uso in produzione. Per ulteriori informazioni, vedere gli articoli di supporto seguenti:
>
> * [Criteri di supporto AKS][aks-support-policies]
> * [Domande frequenti relative al supporto tecnico Azure][aks-faq]

## <a name="before-you-begin"></a>Prima di iniziare

È necessaria l'interfaccia della riga di comando di Azure versione 2.0.66 o successiva installata e configurata. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

Per creare un cluster AKS che può limitare il traffico in uscita, abilitare prima di tutto un flag funzionalità per la sottoscrizione. Questa registrazione delle funzionalità consente di configurare tutti i cluster AKS creati per usare le immagini del contenitore di sistema di base di o. Per registrare il flag della funzionalità *AKSLockingDownEgressPreview* , usare il comando [AZ feature Register][az-feature-register] , come illustrato nell'esempio seguente:

> [!CAUTION]
> Quando si registra una funzionalità in una sottoscrizione, attualmente non è possibile annullare la registrazione di tale funzionalità. Dopo aver abilitato alcune funzionalità di anteprima, è possibile usare i valori predefiniti per tutti i cluster AKS, quindi creati nella sottoscrizione. Non abilitare le funzionalità di anteprima nelle sottoscrizioni di produzione. Usare una sottoscrizione separata per testare le funzionalità di anteprima e raccogliere commenti e suggerimenti.

```azurecli-interactive
az feature register --name AKSLockingDownEgressPreview --namespace Microsoft.ContainerService
```

Sono necessari alcuni minuti per visualizzare lo stato *Registered*. È possibile controllare lo stato della registrazione usando il comando [AZ feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSLockingDownEgressPreview')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione del provider di risorse *Microsoft. servizio contenitore* usando il comando [AZ provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="egress-traffic-overview"></a>Panoramica sul traffico in uscita

A scopo di gestione e operativi, i nodi in un cluster AKS devono accedere a determinate porte e nomi di dominio completi (FQDN). Queste azioni possono essere la comunicazione con il server API oppure per scaricare e installare i componenti principali del cluster Kubernetes e gli aggiornamenti della sicurezza dei nodi. Per impostazione predefinita, il traffico Internet in uscita (in uscita) non è limitato per i nodi in un cluster AKS. Il cluster può estrarre le immagini del contenitore di sistema di base da repository esterni.

Per aumentare la sicurezza del cluster AKS, è possibile che si desideri limitare il traffico in uscita. Il cluster è configurato per eseguire il pull delle immagini del contenitore di sistema di base da o da o ACR. Se si blocca il traffico in uscita in questo modo, è necessario definire porte e FQDN specifici per consentire ai nodi AKS di comunicare correttamente con i servizi esterni richiesti. Senza queste porte e FQDN autorizzati, i nodi AKS non possono comunicare con il server API o installare componenti di base.

È possibile usare il [firewall di Azure][azure-firewall] o un appliance firewall di terze parti per proteggere il traffico in uscita e definire le porte e gli indirizzi richiesti. AKS non crea automaticamente queste regole. Le porte e gli indirizzi seguenti sono per riferimento quando si creano le regole appropriate nel firewall di rete.

> [!IMPORTANT]
> Quando si usa il firewall di Azure per limitare il traffico in uscita e creare una route definita dall'utente (UDR) per forzare tutto il traffico in uscita, assicurarsi di creare una regola DNAT appropriata nel firewall per consentire correttamente il traffico in ingresso. L'uso del firewall di Azure con un UDR interrompe la configurazione in ingresso a causa del routing asimmetrico. Il problema si verifica perché la subnet AKS ha una route predefinita che passa all'indirizzo IP privato del firewall, ma si usa un servizio di bilanciamento del carico pubblico-ingress o Kubernetes di tipo: LoadBalancer). In questo caso, il traffico del servizio di bilanciamento del carico in ingresso viene ricevuto tramite l'indirizzo IP pubblico, ma il percorso di ritorno passa attraverso l'indirizzo IP privato del firewall. Poiché il firewall è con stato, Elimina il pacchetto restituito perché il firewall non è a conoscenza di una sessione stabilita. Per informazioni su come integrare il firewall di Azure con il servizio di bilanciamento del carico in ingresso o servizio, vedere [integrare il firewall di Azure con azure Load Balancer standard](https://docs.microsoft.com/en-us/azure/firewall/integrate-lb).
>

In AKS sono disponibili due set di porte e indirizzi:

* Le [porte e gli indirizzi richiesti per i cluster AKS](#required-ports-and-addresses-for-aks-clusters) illustrano in dettaglio i requisiti minimi per il traffico in uscita autorizzato.
* Gli [indirizzi e le porte consigliati facoltativi per i cluster AKS](#optional-recommended-addresses-and-ports-for-aks-clusters) non sono necessari per tutti gli scenari, ma l'integrazione con altri servizi, ad esempio monitoraggio di Azure, non funzionerà correttamente. Esaminare questo elenco di porte e FQDN facoltativi e autorizzare i servizi e i componenti usati nel cluster AKS.

> [!NOTE]
> La limitazione del traffico in uscita funziona solo nei nuovi cluster AKS creati dopo aver abilitato la registrazione del flag funzionalità. Per i cluster esistenti, [eseguire un'operazione di aggiornamento][aks-upgrade] del cluster `az aks upgrade` usando il comando prima di limitare il traffico in uscita.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>Porte e indirizzi obbligatori per i cluster AKS

Per un cluster AKS sono necessarie le seguenti regole di rete/porte in uscita:

* Porta TCP *443*
* Porta TCP *9000* e la porta TCP *22* per il Pod anteriore del tunnel per comunicare con l'estremità del tunnel nel server API.
    * Per ottenere informazioni più specifiche, vedere * *. HPC.\< location\>. azmk8s.io* e * *. tun.\< location\>. azmk8s.io* indirizzi nella tabella seguente.

Sono necessarie le seguenti regole di FQDN/applicazione:

| FQDN                       | Port      | Utilizzo      |
|----------------------------|-----------|----------|
| *.hcp.\<location\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000 | Questo indirizzo è l'endpoint del server API. *Sostituire\<location\>* con l'area in cui è distribuito il cluster AKS. |
| *.tun.\<location\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000 | Questo indirizzo è l'endpoint del server API. *Sostituire\<location\>* con l'area in cui è distribuito il cluster AKS. |
| aksrepos.azurecr.io        | HTTPS:443 | Questo indirizzo è necessario per accedere alle immagini in Azure Container Registry (ACR). |
| *.blob.core.windows.net    | HTTPS:443 | Questo indirizzo è l'archivio back-end per le immagini archiviate in ACR. |
| mcr.microsoft.com          | HTTPS:443 | Questo indirizzo è necessario per accedere alle immagini in Microsoft Container Registry. |
| *.cdn.mscr.io              | HTTPS:443 | Questo indirizzo è necessario per l'archiviazione di un sistema di archiviazione con supporto per la rete per la distribuzione di contenuti (CDN) di Azure. |
| management.azure.com       | HTTPS:443 | Questo indirizzo è obbligatorio per le operazioni GET/PUT Kubernetes. |
| login.microsoftonline.com  | HTTPS:443 | Questo indirizzo è necessario per l'autenticazione Azure Active Directory. |
| api.snapcraft.io           | HTTPS:443, HTTP:80 | Questo indirizzo è necessario per installare i pacchetti di snap nei nodi Linux. |
| ntp.ubuntu.com             | UDP: 123   | Questo indirizzo è necessario per la sincronizzazione dell'ora NTP nei nodi Linux. |
| *. docker.io                | HTTPS:443 | Questo indirizzo è necessario per eseguire il pull delle immagini del contenitore necessarie per il tunnel anteriore. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Indirizzi e porte consigliati facoltativi per i cluster AKS

* Porta UDP *53* per DNS

Per il corretto funzionamento dei cluster AKS sono consigliate le seguenti regole per il nome di dominio completo/applicazione:

| FQDN                                    | Port      | Utilizzo      |
|-----------------------------------------|-----------|----------|
| *.ubuntu.com                            | HTTP:80   | Questo indirizzo consente ai nodi del cluster Linux di scaricare le patch di sicurezza e gli aggiornamenti necessari. |
| packages.microsoft.com                  | HTTPS:443 | Questo indirizzo è il repository dei pacchetti Microsoft usato per le operazioni *apt-get* memorizzate nella cache. |
| dc.services.visualstudio.com            | HTTPS:443 | Consigliato per la metrica e il monitoraggio corretti con monitoraggio di Azure. |
| *.opinsights.azure.com                  | HTTPS:443 | Consigliato per la metrica e il monitoraggio corretti con monitoraggio di Azure. |
| *.monitoring.azure.com                  | HTTPS:443 | Consigliato per la metrica e il monitoraggio corretti con monitoraggio di Azure. |
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | Questo indirizzo viene usato per il corretto funzionamento di criteri di Azure (attualmente in anteprima in AKS). |
| apt.dockerproject.org                   | HTTPS:443 | Questo indirizzo viene usato per l'installazione e il funzionamento corretto dei driver nei nodi basati su GPU. |
| nvidia.github.io                        | HTTPS:443 | Questo indirizzo viene usato per l'installazione e il funzionamento corretto dei driver nei nodi basati su GPU. |

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
