---
title: Introduzione ai gruppi di contenitori
description: Informazioni sui gruppi di contenitori in istanze di contenitore di Azure, una raccolta di istanze che condividono un ciclo di vita e risorse come l'archiviazione e la rete
ms.topic: article
ms.date: 11/01/2019
ms.custom: mvc
ms.openlocfilehash: c4d5217fe96ca2669397bb7f2a94c6394c002534
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896588"
---
# <a name="container-groups-in-azure-container-instances"></a>Gruppi di contenitori in Istanze di Azure Container

La risorsa di livello principale in Istanze di Azure Container è il *gruppo di contenitori*. Questo articolo descrive le caratteristiche dei gruppi di contenitori e i tipi di scenari possibili.

## <a name="what-is-a-container-group"></a>Che cos'è un gruppo di contenitori?

Un gruppo di contenitori è una raccolta di contenitori che vengono pianificati nello stesso computer host I contenitori in un gruppo di contenitori condividono un ciclo di vita, le risorse, la rete locale e i volumi di archiviazione. Si tratta di un concetto simile a un *Pod* in [Kubernetes][kubernetes-pod].

Il diagramma seguente mostra un esempio di un gruppo che include più contenitori:

![Diagramma di gruppi di contenitori][container-groups-example]

Questo gruppo di contenitori di esempio:

* È pianificato su un singolo computer host.
* Ha un'etichetta del nome DNS assegnata.
* Espone un singolo indirizzo IP pubblico, con una sola porta esposta.
* È costituito da due contenitori. Un contenitore è in ascolto sulla porta 80, mentre l'altro è in ascolto sulla porta 5000.
* Include due condivisioni file di Azure come punti di montaggio di volume e ogni contenitore monta una delle due condivisioni in locale.

> [!NOTE]
> I gruppi di più contenitori supportano attualmente solo i contenitori Linux. Per i contenitori di Windows, istanze di contenitore di Azure supporta solo la distribuzione di una singola istanza. Mentre stiamo lavorando per riunire tutte le funzionalità nei contenitori di Windows, è possibile trovare le attuali differenze della piattaforma nella [Panoramica](container-instances-overview.md#linux-and-windows-containers)del servizio.

## <a name="deployment"></a>Distribuzione

Ecco due modi comuni per distribuire un gruppo di più contenitori: usare un [modello di gestione risorse][resource-manager template] o un [file YAML][yaml-file]. Quando si distribuiscono le istanze di contenitore, è consigliabile usare un modello di Gestione risorse quando è necessario distribuire altre risorse dei servizi di Azure (ad esempio, una [condivisione file di Azure][azure-files]). A causa della natura più concisa del formato YAML, è consigliabile usare un file YAML quando la distribuzione include solo istanze di contenitore. Per informazioni dettagliate sulle proprietà che è possibile impostare, vedere la documentazione di riferimento del [modello di gestione risorse](/azure/templates/microsoft.containerinstance/containergroups) o di [riferimento YAML](container-instances-reference-yaml.md) .

Per mantenere la configurazione di un gruppo di contenitori, è possibile esportare la configurazione in un file YAML usando il comando dell'interfaccia della riga di comando di Azure [AZ container Export][az-container-export]. L'esportazione consente di archiviare le configurazioni del gruppo di contenitori nel controllo della versione per "configurazione come codice". In alternativa, usare il file esportato come punto di partenza per lo sviluppo di una nuova configurazione in YAML.



## <a name="resource-allocation"></a>Allocazione delle risorse

Istanze di contenitore di Azure alloca le risorse, ad esempio CPU, memoria e facoltativamente [GPU][gpus] (anteprima) a un gruppo di contenitori, aggiungendo le [richieste di risorse][resource-requests] delle istanze nel gruppo. Assunzione di risorse della CPU come esempio, se si crea un gruppo di contenitori con due istanze, ciascuna delle quali richiede 1 CPU, al gruppo di contenitori vengono allocate 2 CPU.

### <a name="resource-usage-by-instances"></a>Utilizzo delle risorse da istanze

A ogni istanza di contenitore vengono allocate le risorse specificate nella relativa richiesta di risorsa. Tuttavia, l'utilizzo delle risorse da parte di un'istanza di contenitore in un gruppo dipende dalla modalità di configurazione della proprietà del [limite di risorse][resource-limits] facoltativo. Il limite di risorse deve essere minore di quello della proprietà obbligatoria della [richiesta di risorse][resource-requests] .

* Se non si specifica un limite di risorse, l'utilizzo massimo delle risorse dell'istanza è uguale a quello della relativa richiesta di risorse.

* Se si specifica un limite di risorse per un'istanza, è possibile modificare l'utilizzo delle risorse dell'istanza per il proprio carico di lavoro, riducendo o aumentando l'utilizzo rispetto alla richiesta di risorse. Il limite massimo di risorse che è possibile impostare è il totale delle risorse allocate al gruppo.
    
Ad esempio, in un gruppo con due istanze che richiedono 1 CPU, è possibile che uno dei contenitori esegua un carico di lavoro che richiede l'esecuzione di più CPU rispetto all'altra.

In questo scenario, è possibile impostare un limite di risorse di 0,5 CPU per un'istanza e un limite di 2 CPU per il secondo. Questa configurazione limita l'utilizzo delle risorse del primo contenitore alla CPU 0,5, consentendo al secondo contenitore di usare fino a 2 CPU complete, se disponibili.

Per altre informazioni, vedere la proprietà [ResourceRequirements][resource-requirements] nell'API REST dei gruppi di contenitori.

### <a name="minimum-and-maximum-allocation"></a>Allocazione minima e massima

* Allocare **almeno** 1 CPU e 1 GB di memoria a un gruppo di contenitori. È possibile eseguire il provisioning di istanze di contenitore singole all'interno di un gruppo con meno di 1 CPU e 1 GB di memoria. 

* Per le risorse **massime** in un gruppo di contenitori, vedere la pagina relativa alla [disponibilità delle risorse][region-availability] per le istanze di contenitore di Azure nell'area di distribuzione.

## <a name="networking"></a>Rete

I gruppi di contenitori possono condividere un indirizzo IP esterno e uno spazio dei nomi di porta su tale indirizzo IP. Per consentire a client esterni di raggiungere un contenitore all'interno del gruppo, è necessario esporre la porta sull'indirizzo IP e dal contenitore. Poiché i contenitori all'interno del gruppo condividono uno spazio dei nomi di porta, il mapping delle porte non è supportato. 

All'interno di un gruppo di contenitori, le istanze dei contenitori possono raggiungere reciprocamente tramite localhost su qualsiasi porta, anche se queste porte non sono esposte esternamente nell'indirizzo IP del gruppo o dal contenitore.

Facoltativamente, distribuire i gruppi di contenitori in una [rete virtuale di Azure][virtual-network] (anteprima) per consentire ai contenitori di comunicare in modo sicuro con altre risorse nella rete virtuale.

## <a name="storage"></a>Archiviazione

È possibile impostare il montaggio di volumi esterni all'interno di un gruppo di contenitori ed eseguire il mapping di tali volumi in percorsi specifici all'interno dei singoli contenitori di un gruppo.

## <a name="common-scenarios"></a>Scenari comuni

I gruppi di più contenitori sono utili nei casi in cui si vuole dividere una singola attività funzionale in un numero ridotto di immagini del contenitore. Queste immagini possono essere distribuite da team diversi e hanno requisiti separati in termini di risorse.

Un esempio di utilizzo può includere gli elementi seguenti:

* Un contenitore che serve un'applicazione Web e un altro che esegue il pull del contenuto più recente dal controllo del codice sorgente.
* Un contenitore di applicazione e un contenitore di registrazione. Il contenitore di registrazione raccoglie l'output dei log e delle metriche generato dall'applicazione principale e lo scrive in una risorsa di archiviazione a lungo termine.
* Un contenitore di applicazione e un contenitore di monitoraggio. Il contenitore di monitoraggio invia periodicamente una richiesta all'applicazione per verificare che sia in esecuzione e risponda correttamente e genera un avviso nel caso in cui la verifica abbia esito negativo.
* Un contenitore front-end e un contenitore back-end. Il front-end potrebbe servire un'applicazione Web, con il back-end che esegue un servizio per recuperare i dati. 

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
