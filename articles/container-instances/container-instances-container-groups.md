---
title: Gruppi di contenitori in Istanze di Azure Container
description: Informazioni sui gruppi multicontenitore come funzionano in istanze di contenitore di Azure
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/20/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: cba57875daf9b570d274ec8c4e9c4146af0dc045
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65072840"
---
# <a name="container-groups-in-azure-container-instances"></a>Gruppi di contenitori in Istanze di Azure Container

La risorsa di livello principale in Istanze di Azure Container è il *gruppo di contenitori*. Questo articolo descrive le caratteristiche dei gruppi di contenitori e i tipi di scenari possibili.

## <a name="how-a-container-group-works"></a>Come funziona un gruppo di contenitori

Un gruppo di contenitori è una raccolta di contenitori che vengono pianificati nello stesso computer host I contenitori in un gruppo di contenitori condividono un ciclo di vita, le risorse, rete locale e i volumi di archiviazione. È concettualmente analoga a un *pod* nelle [Kubernetes][kubernetes-pod].

Il diagramma seguente mostra un esempio di un gruppo che include più contenitori:

![Diagramma di gruppi di contenitori][container-groups-example]

Questo gruppo di contenitori di esempio:

* È pianificato su un singolo computer host.
* Ha un'etichetta del nome DNS assegnata.
* Espone un singolo indirizzo IP pubblico, con una sola porta esposta.
* È costituito da due contenitori. Un contenitore è in ascolto sulla porta 80, mentre l'altro è in ascolto sulla porta 5000.
* Include due condivisioni file di Azure come punti di montaggio di volume e ogni contenitore monta una delle due condivisioni in locale.

> [!NOTE]
> I gruppi multicontenitore supportano attualmente solo i contenitori Linux. Per i contenitori di Windows, le istanze di contenitore di Azure supporta solo la distribuzione di una singola istanza. Microsoft sta lavorando per trasferire tutte le funzionalità in contenitori Windows, è possibile trovare le differenze di piattaforma corrente nel servizio [Panoramica](container-instances-overview.md#linux-and-windows-containers).

## <a name="deployment"></a>Distribuzione

Esistono due modi comuni per distribuire un gruppo multicontenitore: usare una [modello di Resource Manager] [ resource-manager template] o un [file YAML][yaml-file]. Un modello di Resource Manager è consigliato quando è necessario distribuire le risorse dei servizi Azure aggiuntivi (ad esempio, un' [condivisione file di Azure][azure-files]) quando si distribuiscono le istanze di contenitore. A causa di natura più concisa del formato YAML, un file YAML è consigliato quando la distribuzione include solo le istanze di contenitore.

Per mantenere la configurazione di un gruppo contenitore, è possibile esportare la configurazione in un file YAML usando il comando di Azure CLI [esportazione di contenitore di az][az-container-export]. Esportazione consente di archiviare le configurazioni dei gruppi contenitore nel controllo della versione di "configurazione come codice". In alternativa, usare il file esportato come punto di partenza per lo sviluppo di una nuova configurazione in YAML.

## <a name="resource-allocation"></a>Allocazione delle risorse

Istanze di contenitore di Azure alloca le risorse, ad esempio CPU, memoria e facoltativamente [GPU] [ gpus] (anteprima) per un gruppo di contenitori tramite l'aggiunta del [richieste di risorse] [ resource-requests] delle istanze del gruppo. Accettando le risorse di CPU come ad esempio, se si crea un gruppo di contenitori con due istanze, ogni richiedente 1 CPU, quindi il gruppo di contenitori viene allocato 2 CPU.

La quantità massima di risorse disponibile per un gruppo di contenitori dipende il [area di Azure] [ region-availability] usata per la distribuzione.

### <a name="container-resource-requests-and-limits"></a>Limiti e le richieste di risorse di contenitore

* Per impostazione predefinita, le istanze di contenitore in un gruppo condividono le risorse richieste del gruppo. In un gruppo con due istanze di ogni richiedente 1 CPU, il gruppo nel suo può accedere a 2 CPU. Ogni istanza può usare alle 2 CPU e le istanze possono competere per delle risorse della CPU durante l'esecuzione.

* Per limitare l'utilizzo delle risorse da un'istanza in un gruppo, impostare facoltativamente un [limite di risorse] [ resource-limits] per l'istanza. In un gruppo con due istanze che richiede 1 CPU, uno dei contenitori potrebbe richiedere più CPU rispetto a altro.

  In questo scenario, è possibile impostare un limite di risorse pari a 0,5 della CPU per un'istanza e un limite di 2 CPU per il secondo. Questa configurazione limita l'utilizzo delle risorse del contenitore prima a 0,5 della CPU, consentendo il secondo contenitore da cui è possibile utilizzare le CPU completo 2 se disponibile.

Per altre informazioni, vedere la [ResourceRequirements] [ resource-requirements] API REST di gruppi di proprietà nel contenitore.

### <a name="minimum-and-maximum-allocation"></a>Valore minimo e massimo

* Allocare una **minimo** 1 CPU e 1 GB di memoria per un gruppo di contenitori. Istanze di contenitore singolo all'interno di un gruppo possono eseguire il provisioning con meno di 1 CPU e 1 GB di memoria. 

* Per il **massima** le risorse in un gruppo di contenitori, vedere la [disponibilità delle risorse] [ region-availability] per istanze di contenitore di Azure nell'area di distribuzione.

## <a name="networking"></a>Rete

I gruppi di contenitori condividono un indirizzo IP e uno spazio dei nomi di porta su tale indirizzo IP. Per consentire a client esterni di raggiungere un contenitore all'interno del gruppo, è necessario esporre la porta sull'indirizzo IP e dal contenitore. Poiché i contenitori all'interno del gruppo condividono uno spazio dei nomi di porta, il mapping della porta non è supportato. I contenitori all'interno di un gruppo possono raggiungersi tramite localhost sulle porte che hanno esposto, anche se queste porte non sono esposte esternamente sull'indirizzo IP del gruppo.

Se lo si desidera distribuire gruppi di contenitori in un [rete virtuale di Azure] [ virtual-network] (anteprima) per consentire di contenitori di comunicare in modo sicuro con altre risorse nella rete virtuale.

## <a name="storage"></a>Archiviazione

È possibile impostare il montaggio di volumi esterni all'interno di un gruppo di contenitori ed eseguire il mapping di tali volumi in percorsi specifici all'interno dei singoli contenitori di un gruppo.

## <a name="common-scenarios"></a>Scenari comuni

I gruppi di più contenitori sono utili nei casi in cui si vuole dividere una singola attività funzionale in un numero ridotto di immagini del contenitore. Queste immagini possono essere distribuite da team diversi e hanno requisiti separati in termini di risorse.

Un esempio di utilizzo può includere gli elementi seguenti:

* Un contenitore che serve un'applicazione Web e un altro che esegue il pull del contenuto più recente dal controllo del codice sorgente.
* Un contenitore di applicazione e un contenitore di registrazione. Il contenitore di registrazione raccoglie l'output dei log e delle metriche generato dall'applicazione principale e lo scrive in una risorsa di archiviazione a lungo termine.
* Un contenitore di applicazione e un contenitore di monitoraggio. Il contenitore di monitoraggio invia periodicamente una richiesta all'applicazione per verificare che sia in esecuzione e risponda correttamente e genera un avviso nel caso in cui la verifica abbia esito negativo.
* Un contenitore front-end e un contenitore di back-end. Il front-end potrebbe gestire un'applicazione web, con il back-end che eseguono un servizio per recuperare i dati. 

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
