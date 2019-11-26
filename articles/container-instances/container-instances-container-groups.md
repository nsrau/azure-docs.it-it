---
title: Introduction to container groups
description: Learn about container groups in Azure Container Instances, a collection of instances that share a lifecycle and resources such as storage and network
ms.topic: article
ms.date: 11/01/2019
ms.custom: mvc
ms.openlocfilehash: 9fbf9fea7da0896ee6c0e248d18e18d52798fbd7
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482107"
---
# <a name="container-groups-in-azure-container-instances"></a>Gruppi di contenitori in Istanze di Azure Container

La risorsa di livello principale in Istanze di Azure Container è il *gruppo di contenitori*. Questo articolo descrive le caratteristiche dei gruppi di contenitori e i tipi di scenari possibili.

## <a name="what-is-a-container-group"></a>What is a container group?

Un gruppo di contenitori è una raccolta di contenitori che vengono pianificati nello stesso computer host The containers in a container group share a lifecycle, resources, local network, and storage volumes. It's similar in concept to a *pod* in [Kubernetes][kubernetes-pod].

Il diagramma seguente mostra un esempio di un gruppo che include più contenitori:

![Diagramma di gruppi di contenitori][container-groups-example]

Questo gruppo di contenitori di esempio:

* È pianificato su un singolo computer host.
* Ha un'etichetta del nome DNS assegnata.
* Espone un singolo indirizzo IP pubblico, con una sola porta esposta.
* È costituito da due contenitori. Un contenitore è in ascolto sulla porta 80, mentre l'altro è in ascolto sulla porta 5000.
* Include due condivisioni file di Azure come punti di montaggio di volume e ogni contenitore monta una delle due condivisioni in locale.

> [!NOTE]
> Multi-container groups currently support only Linux containers. For Windows containers, Azure Container Instances only supports deployment of a single instance. While we are working to bring all features to Windows containers, you can find current platform differences in the service [Overview](container-instances-overview.md#linux-and-windows-containers).

## <a name="deployment"></a>Distribuzione

Here are two common ways to deploy a multi-container group: use a [Resource Manager template][resource-manager template] or a [YAML file][yaml-file]. A Resource Manager template is recommended when you need to deploy additional Azure service resources (for example, an [Azure Files share][azure-files]) when you deploy the container instances. Due to the YAML format's more concise nature, a YAML file is recommended when your deployment includes only container instances. For details on properties you can set, see the [Resource Manager template reference](/azure/templates/microsoft.containerinstance/containergroups) or [YAML reference](container-instances-reference-yaml.md) documentation.

To preserve a container group's configuration, you can export the configuration to a YAML file by using the Azure CLI command [az container export][az-container-export]. Export allows you to store your container group configurations in version control for "configuration as code." In alternativa, usare il file esportato come punto di partenza per lo sviluppo di una nuova configurazione in YAML.



## <a name="resource-allocation"></a>Allocazione delle risorse

Azure Container Instances allocates resources such as CPUs, memory, and optionally [GPUs][gpus] (preview) to a container group by adding the [resource requests][resource-requests] of the instances in the group. Taking CPU resources as an example, if you create a container group with two instances, each requesting 1 CPU, then the container group is allocated 2 CPUs.

### <a name="resource-usage-by-instances"></a>Resource usage by instances

Each container instance is allocated the resources specified in its resource request. However, the resource usage by a container instance in a group depends on how you configure its optional [resource limit][resource-limits] property.

* If you don't specify a resource limit, the instance's maximum resource usage is the same as its resource request.

* If you specify a resource limit for an instance, you can adjust the instance's resource usage for its workload, either reducing or increasing usage relative to the resource request. The maximum resource limit you can set is the total resources allocated to the group.
    
    For example, in a group with two instances requesting 1 CPU, one of your containers might run a workload that requires more CPUs to run than the other.

    In this scenario, you could set a resource limit of 0.5 CPU for one instance, and a limit of 2 CPUs for the second. This configuration limits the first container's resource usage to 0.5 CPU, allowing the second container to use up to the full 2 CPUs if available.

For more information, see the [ResourceRequirements][resource-requirements] property in the container groups REST API.

### <a name="minimum-and-maximum-allocation"></a>Minimum and maximum allocation

* Allocate a **minimum** of 1 CPU and 1 GB of memory to a container group. Individual container instances within a group can be provisioned with less than 1 CPU and 1 GB of memory. 

* For the **maximum** resources in a container group, see the [resource availability][region-availability] for Azure Container Instances in the deployment region.

## <a name="networking"></a>Rete

I gruppi di contenitori condividono un indirizzo IP e uno spazio dei nomi di porta su tale indirizzo IP. Per consentire a client esterni di raggiungere un contenitore all'interno del gruppo, è necessario esporre la porta sull'indirizzo IP e dal contenitore. Because containers within the group share a port namespace, port mapping isn't supported. Containers within a group can reach each other via localhost on the ports that they have exposed, even if those ports aren't exposed externally on the group's IP address.

Optionally deploy container groups into an [Azure virtual network][virtual-network] (preview) to allow containers to communicate securely with other resources in the virtual network.

## <a name="storage"></a>Archiviazione

È possibile impostare il montaggio di volumi esterni all'interno di un gruppo di contenitori ed eseguire il mapping di tali volumi in percorsi specifici all'interno dei singoli contenitori di un gruppo.

## <a name="common-scenarios"></a>Scenari comuni

I gruppi di più contenitori sono utili nei casi in cui si vuole dividere una singola attività funzionale in un numero ridotto di immagini del contenitore. Queste immagini possono essere distribuite da team diversi e hanno requisiti separati in termini di risorse.

Un esempio di utilizzo può includere gli elementi seguenti:

* Un contenitore che serve un'applicazione Web e un altro che esegue il pull del contenuto più recente dal controllo del codice sorgente.
* Un contenitore di applicazione e un contenitore di registrazione. Il contenitore di registrazione raccoglie l'output dei log e delle metriche generato dall'applicazione principale e lo scrive in una risorsa di archiviazione a lungo termine.
* Un contenitore di applicazione e un contenitore di monitoraggio. Il contenitore di monitoraggio invia periodicamente una richiesta all'applicazione per verificare che sia in esecuzione e risponda correttamente e genera un avviso nel caso in cui la verifica abbia esito negativo.
* A front-end container and a back-end container. The front end might serve a web application, with the back end running a service to retrieve data. 

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come distribuire un gruppo con più contenitori con un modello di Azure Resource Manager.

> [!div class="nextstepaction"]
> [Distribuire un gruppo di contenitori][resource-manager template]

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/pod/

<!-- LINKS - Internal -->
[resource-manager template]: container-instances-multi-container-group.md
[yaml-file]: container-instances-multi-container-yaml.md
[region-availability]: container-instances-region-availability.md
[resource-requests]: /rest/api/container-instances/containergroups/createorupdate#resourcerequests
[resource-limits]: /rest/api/container-instances/containergroups/createorupdate#resourcelimits
[resource-requirements]: /rest/api/container-instances/containergroups/createorupdate#resourcerequirements
[azure-files]: container-instances-volume-azure-files.md
[virtual-network]: container-instances-vnet.md
[gpus]: container-instances-gpu.md
[az-container-export]: /cli/azure/container#az-container-export
