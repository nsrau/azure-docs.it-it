---
title: Limitare il traffico in uscita in Azure Kubernetes Service (AKS)
description: Informazioni su quali porte e gli indirizzi sono necessari per controllare il traffico in uscita in Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/14/2019
ms.author: iainfou
ms.openlocfilehash: 4c2dad687d31597954b023dde9d1b9d69788fe04
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241399"
---
# <a name="preview---limit-egress-traffic-for-cluster-nodes-and-control-access-to-required-ports-and-services-in-azure-kubernetes-service-aks"></a>Anteprima - limitare il traffico in uscita per i nodi del cluster e controllare l'accesso alle porte necessarie e i servizi in Azure Kubernetes Service (AKS)

Per impostazione predefinita, i cluster servizio contenitore di AZURE dispongono di illimitato l'accesso a internet in uscita (traffico in uscita). Questo livello di accesso alla rete consente a nodi e i servizi che si esegue per accedere alle risorse esterne in base alle esigenze. Se si desidera limitare il traffico in uscita, un numero limitato di porte e indirizzi deve essere accessibile per mantenere le attività di manutenzione di cluster integro. Il cluster viene quindi configurato per usare solo immagini del contenitore del sistema di base da Microsoft contenitore del Registro di sistema (MCR) o del Registro di sistema contenitore di Azure (ACR), i repository pubblici non esterni.

Questo articolo illustra in dettaglio quali porte di rete e i nomi di dominio completo (FQDN) sono obbligatori e facoltativi se si limita il traffico in uscita in un cluster AKS.  Questa funzionalità è attualmente in anteprima.

> [!IMPORTANT]
> Funzionalità di anteprima del servizio contenitore di AZURE sono self-service, fornire il consenso esplicito. Vengono fornite per raccogliere commenti e suggerimenti e bug dalla community. In fase di anteprima, queste funzionalità non sono destinate all'uso di produzione. Le funzionalità in anteprima pubblica rientrano nel supporto "best effort". Assistenza dai team di supporto tecnico di AKS è disponibile durante le ore lavorative Pacifico (PST) solo timezone. Per altre informazioni, vedere i seguenti articoli di supporto:
>
> * [Criteri di supporto servizio contenitore di AZURE][aks-support-policies]
> * [Domande frequenti sul supporto di Azure][aks-faq]

## <a name="before-you-begin"></a>Prima di iniziare

È necessario la CLI di Azure versione 2.0.61 o versione successiva installato e configurato. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

Per creare un cluster AKS che può limitare il traffico in uscita, è necessario attivare un flag funzionalità per la sottoscrizione. Questa registrazione della funzionalità consente di configurare tutti i cluster servizio contenitore di AZURE create per usare le immagini contenitore di sistema di base da MCR o registro contenitori di AZURE. Per registrare il *AKSLockingDownEgressPreview* flag delle funzionalità, utilizzare il [register funzionalità az] [ az-feature-register] seguente come mostrato nell'esempio seguente:

```azurecli-interactive
az feature register --name AKSLockingDownEgressPreview --namespace Microsoft.ContainerService
```

Sono necessari alcuni minuti per visualizzare lo stato *Registered*. È possibile controllare lo stato di registrazione usando il [elenco delle funzionalità az] [ az-feature-list] comando:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSLockingDownEgressPreview')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione dei *containerservice* provider di risorse usando la [register di az provider] [ az-provider-register] comando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="egress-traffic-overview"></a>Panoramica sul traffico in uscita

Per la gestione e scopi operativi, i nodi in un cluster del servizio contenitore di AZURE necessario per accedere a determinate porte e i nomi di dominio completo (FQDN). Queste azioni può essere per comunicare con il server API, o per scaricare e installare componenti principali del cluster Kubernetes e gli aggiornamenti della sicurezza nodo. Per impostazione predefinita, il traffico internet (in uscita) in uscita non è limitato per i nodi in un cluster AKS. Il cluster può eseguire il pull del sistema di base le immagini del contenitore dai repository esterno.

Per aumentare la sicurezza del cluster servizio contenitore di AZURE, è possibile limitare il traffico in uscita. Il cluster è configurato per estrarre le immagini del contenitore da registro contenitori di AZURE o MCR sistema di base. Se si blocca il traffico in uscita in questo modo, è necessario definire specifiche porte e i nomi di dominio completi per consentire ai nodi AKS comunicare correttamente con i servizi esterni necessari. Senza queste porte autorizzate e un FQDN, i nodi del servizio contenitore di AZURE non possono comunicare con il server API o installare i componenti di base.

È possibile usare [Firewall di Azure] [ azure-firewall] o un'appliance firewall di terze parti 3rd per proteggere il traffico in uscita e definire queste necessarie porte e indirizzi.

Nel servizio contenitore di AZURE, sono disponibili due set di porte e indirizzi:

* Il [porte e indirizzi necessari per i cluster AKS](#required-ports-and-addresses-for-aks-clusters) illustra in dettaglio i requisiti minimi per il traffico in uscita autorizzati.
* Il [facoltativi consigliati indirizzi e porte per i cluster AKS](#optional-recommended-addresses-and-ports-for-aks-clusters) non sono necessarie per tutti gli scenari, ma l'integrazione con altri servizi, ad esempio monitoraggio di Azure non funzionerà correttamente. Esaminare l'elenco di FQDN e le porte facoltative e autorizzare tutti i servizi e componenti utilizzati nel cluster AKS.

> [!NOTE]
> Limitare il traffico in uscita funziona solo su nuovi cluster servizio contenitore di AZURE create dopo aver abilitato la registrazione di flag funzionalità. Per i cluster esistenti [eseguire un'operazione di aggiornamento del cluster] [ aks-upgrade] usando il `az aks upgrade` comando prima di limitare il traffico in uscita.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>Porte necessarie e gli indirizzi per i cluster servizio contenitore di AZURE

Le seguenti porte in uscita / regole di rete sono necessarie per un cluster del servizio contenitore di AZURE:

* La porta TCP *443*
* La porta TCP *9000* per i pod front tunnel comunicare con la fine del tunnel sul server API.

Il nome FQDN segue le regole di applicazione sono necessari:

| FQDN                      | Port      | Uso      |
|---------------------------|-----------|----------|
| *.azmk8s.io               | HTTPS:443 | Questo indirizzo è l'endpoint server dell'API. |
| aksrepos.azurecr.io       | HTTPS:443 | Questo indirizzo è obbligatorio per l'accesso immagini nel registro contenitori di Azure (ACR). |
| *.blob.core.windows.net   | HTTPS:443 | Questo indirizzo è l'archivio di back-end per le immagini archiviate nel registro contenitori di AZURE. |
| mcr.microsoft.com         | HTTPS:443 | Questo indirizzo è obbligatorio per l'accesso immagini nel Registro di sistema contenitore Microsoft (MCR). |
| management.azure.com      | HTTPS:443 | Questo indirizzo è obbligatorio per le operazioni di Kubernetes GET/PUT. |
| login.microsoftonline.com | HTTPS:443 | Questo indirizzo è obbligatorio per l'autenticazione di Azure Active Directory. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Facoltativo consigliato indirizzi e porte per i cluster servizio contenitore di AZURE

Le seguenti porte in uscita / regole di rete non sono necessari per i cluster AKS funzionare correttamente, ma sono consigliate:

* La porta UDP *123* per la sincronizzazione dell'ora NTP
* La porta UDP *53* per DNS

Il nome di dominio completo seguente / regole di applicazione sono consigliate per i cluster servizio contenitore di AZURE funzioni correttamente:

| FQDN                                    | Port      | Uso      |
|-----------------------------------------|-----------|----------|
| *.ubuntu.com                            | HTTP:80   | Questo indirizzo consente i nodi del cluster Linux di scaricare le patch di sicurezza necessarie e gli aggiornamenti. |
| packages.microsoft.com                  | HTTPS:443 | Questo indirizzo viene usato il repository di pacchetti Microsoft per memorizzato nella cache *apt-get* operazioni. |
| dc.services.visualstudio.com            | HTTPS:443 | Consigliato per le metriche corrette e il monitoraggio tramite Monitoraggio di Azure. |
| *.opinsights.azure.com                  | HTTPS:443 | Consigliato per le metriche corrette e il monitoraggio tramite Monitoraggio di Azure. |
| *.monitoring.azure.com                  | HTTPS:443 | Consigliato per le metriche corrette e il monitoraggio tramite Monitoraggio di Azure. |
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | Questo indirizzo viene usato per il corretto funzionamento dei criteri di Azure (attualmente in anteprima nel servizio contenitore di AZURE). |
| apt.dockerproject.org                   | HTTPS:443 | Questo indirizzo viene usato per l'installazione di driver corretto e operazione sui nodi basate su GPU. |
| nvidia.github.io                        | HTTPS:443 | Questo indirizzo viene usato per l'installazione di driver corretto e operazione sui nodi basate su GPU. |

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso quali porte e indirizzi di consentire o meno se si limita il traffico in uscita per il cluster. È anche possibile definire la modalità in cui possono comunicare i POD se stessi e quali restrizioni hanno all'interno del cluster. Per altre informazioni, vedere [proteggere il traffico tra i POD usando i criteri di rete in AKS][network-policy].

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
