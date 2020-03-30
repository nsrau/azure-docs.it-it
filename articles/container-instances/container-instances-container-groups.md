---
title: Introduzione ai gruppi di contenitoriIntroduction to container groups
description: Informazioni sui gruppi di contenitori nelle istanze dei contenitori di Azure, una raccolta di istanze che condividono un ciclo di vita e risorse come archiviazione e reteLearn about container groups in Azure Container Instances, a collection of instances that share a lifecycle and resources such as storage and network
ms.topic: article
ms.date: 11/01/2019
ms.custom: mvc
ms.openlocfilehash: 73781418321c3932bf3e0190b646dcd3bb178195
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247215"
---
# <a name="container-groups-in-azure-container-instances"></a>Gruppi di contenitori in Istanze di Azure Container

La risorsa di livello principale in Istanze di Azure Container è il *gruppo di contenitori*. Questo articolo descrive le caratteristiche dei gruppi di contenitori e i tipi di scenari possibili.

## <a name="what-is-a-container-group"></a>Che cos'è un gruppo di contenitori?

Un gruppo di contenitori è una raccolta di contenitori che vengono pianificati nello stesso computer host I contenitori in un gruppo di contenitori condividono un ciclo di vita, risorse, rete locale e volumi di archiviazione. È simile nel concetto a un *baccello* in [Kubernetes][kubernetes-pod].

Il diagramma seguente mostra un esempio di un gruppo che include più contenitori:

![Diagramma di gruppi di contenitori][container-groups-example]

Questo gruppo di contenitori di esempio:

* È pianificato su un singolo computer host.
* Ha un'etichetta del nome DNS assegnata.
* Espone un singolo indirizzo IP pubblico, con una sola porta esposta.
* È costituito da due contenitori. Un contenitore è in ascolto sulla porta 80, mentre l'altro è in ascolto sulla porta 5000.
* Include due condivisioni file di Azure come punti di montaggio di volume e ogni contenitore monta una delle due condivisioni in locale.

> [!NOTE]
> I gruppi multi-contenitore attualmente supportano solo contenitori Linux.Multi-container groups currently support only Linux containers. Per i contenitori di Windows, le istanze del contenitore di Azure supportano solo la distribuzione di una singola istanza del contenitore. Mentre stiamo lavorando per portare tutte le funzionalità nei contenitori di Windows, è possibile trovare le differenze di piattaforma correnti nella [panoramica](container-instances-overview.md#linux-and-windows-containers)del servizio .

## <a name="deployment"></a>Distribuzione

Ecco due modi comuni per distribuire un gruppo multicontenitore: usare un [modello di Resource Manager][resource-manager template] o un file [YAML][yaml-file]. Un modello di Resource Manager è consigliato quando è necessario distribuire risorse del servizio di Azure aggiuntive, ad esempio una [condivisione File][azure-files]di Azure, quando si distribuiscono le istanze del contenitore. A causa della natura più concisa del formato YAML, un file YAML è consigliato quando la distribuzione include solo istanze del contenitore. Per informazioni dettagliate sulle proprietà che è possibile impostare, vedere la [documentazione](/azure/templates/microsoft.containerinstance/containergroups) di riferimento del modello resource Manager o [YAML.](container-instances-reference-yaml.md)

Per mantenere la configurazione di un gruppo di contenitori, è possibile esportare la configurazione in un file YAML usando il comando dell'interfaccia della riga di comando di Azure [az container export][az-container-export]. L'esportazione consente di archiviare le configurazioni dei gruppi di contenitori nel controllo della versione per "configurazione come codice". In alternativa, usare il file esportato come punto di partenza per lo sviluppo di una nuova configurazione in YAML.



## <a name="resource-allocation"></a>Allocazione delle risorse

Le istanze del contenitore di Azure allocano risorse quali CPU, memoria e facoltativamente [GPU][gpus] (anteprima) a un gruppo multicontenitore aggiungendo [le richieste][resource-requests] di risorse delle istanze nel gruppo. Prendendo le risorse della CPU come esempio, se si crea un gruppo di contenitori con due istanze del contenitore, ognuna richiede 1 CPU, quindi il gruppo di contenitori vengono allocati 2 CPU.

### <a name="resource-usage-by-container-instances"></a>Utilizzo delle risorse per le istanze del contenitoreResource usage by container instances

A ogni istanza del contenitore in un gruppo vengono allocate le risorse specificate nella relativa richiesta di risorse. Tuttavia, il numero massimo di risorse usate da un'istanza del contenitore in un gruppo potrebbe essere diverso se si configura la relativa proprietà facoltativa del limite di [risorse.][resource-limits] Il limite di risorse di un'istanza del contenitore deve essere maggiore o uguale alla proprietà obbligatoria [della richiesta di risorsa.][resource-requests]

* Se non si specifica un limite di risorse, l'utilizzo massimo delle risorse dell'istanza del contenitore corrisponde alla richiesta di risorse.

* Se si specifica un limite per un'istanza del contenitore, l'utilizzo massimo dell'istanza potrebbe essere maggiore della richiesta, fino al limite impostato. In modo corrispondente, l'utilizzo delle risorse da parte di altre istanze del contenitore nel gruppo potrebbe diminuire. Il limite massimo di risorse che è possibile impostare per un'istanza del contenitore è il totale delle risorse allocate al gruppo.
    
Ad esempio, in un gruppo con due istanze del contenitore ognuna richiede 1 CPU, uno dei contenitori potrebbe eseguire un carico di lavoro che richiede più CPU per l'esecuzione rispetto all'altra.

In questo scenario, è possibile impostare un limite di risorse di 2 CPU per l'istanza del contenitore. Questa configurazione consente all'istanza del contenitore di utilizzare fino alle 2 CPU complete, se disponibili.

### <a name="minimum-and-maximum-allocation"></a>Allocazione minima e massima

* Allocare un **minimo** di 1 CPU e 1 GB di memoria a un gruppo di contenitori. È possibile eseguire il provisioning di singole istanze del contenitore all'interno di un gruppo con meno di 1 CPU e 1 GB di memoria. 

* Per le risorse **massime** in un gruppo di contenitori, vedere la [disponibilità delle risorse][region-availability] per le istanze del contenitore di Azure nell'area di distribuzione.

## <a name="networking"></a>Rete

I gruppi di contenitori possono condividere un indirizzo IP esterno, una o più porte su tale indirizzo IP e un'etichetta DNS con un nome di dominio completo (FQDN). Per consentire a client esterni di raggiungere un contenitore all'interno del gruppo, è necessario esporre la porta sull'indirizzo IP e dal contenitore. Poiché i contenitori all'interno del gruppo condividono uno spazio dei nomi di porta, il mapping delle porte non è supportato. L'indirizzo IP e il nome di dominio completo di un gruppo di contenitori verranno rilasciati quando il gruppo di contenitori viene eliminato. 

All'interno di un gruppo di contenitori, le istanze del contenitore possono raggiungersi a vicenda tramite localhost su qualsiasi porta, anche se tali porte non sono esposte esternamente sull'indirizzo IP del gruppo o dal contenitore.

Facoltativamente, distribuire gruppi di contenitori in una [rete virtuale][virtual-network] di Azure per consentire ai contenitori di comunicare in modo sicuro con altre risorse nella rete virtuale.

## <a name="storage"></a>Archiviazione

È possibile impostare il montaggio di volumi esterni all'interno di un gruppo di contenitori I volumi supportati includono:
* [Condivisione file di Azure][azure-files]
* [Segreto][secret]
* [Directory vuota][empty-directory]
* [Repo git clonato][volume-gitrepo]

ed eseguire il mapping di tali volumi in percorsi specifici all'interno dei singoli contenitori di un gruppo. 

## <a name="common-scenarios"></a>Scenari comuni

I gruppi di più contenitori sono utili nei casi in cui si vuole dividere una singola attività funzionale in un numero ridotto di immagini del contenitore. Queste immagini possono essere distribuite da team diversi e hanno requisiti separati in termini di risorse.

Un esempio di utilizzo può includere gli elementi seguenti:

* Un contenitore che serve un'applicazione Web e un altro che esegue il pull del contenuto più recente dal controllo del codice sorgente.
* Un contenitore di applicazione e un contenitore di registrazione. Il contenitore di registrazione raccoglie l'output dei log e delle metriche generato dall'applicazione principale e lo scrive in una risorsa di archiviazione a lungo termine.
* Un contenitore di applicazione e un contenitore di monitoraggio. Il contenitore di monitoraggio invia periodicamente una richiesta all'applicazione per verificare che sia in esecuzione e risponda correttamente e genera un avviso nel caso in cui la verifica abbia esito negativo.
* Un contenitore front-end e un contenitore back-end. Il front-end potrebbe servire un'applicazione web, con il back-end che esegue un servizio per recuperare i dati. 

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
[secret]: container-instances-volume-secret.md
[volume-gitrepo]: container-instances-volume-gitrepo.md
[gpus]: container-instances-gpu.md
[empty-directory]: container-instances-volume-emptydir.md
[az-container-export]: /cli/azure/container#az-container-export
