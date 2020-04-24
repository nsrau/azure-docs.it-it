---
title: Risorse supportate per Azure Red Hat OpenShift
description: Informazioni sulle aree di Azure e sulle dimensioni delle macchine virtuali supportate da Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 1b794852390141d31a3218a1a434c21133db914c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243666"
---
# <a name="azure-red-hat-openshift-resources"></a>Risorse di Azure Red Hat OpenShift

Questo argomento elenca le aree di Azure e le dimensioni delle macchine virtuali supportate dal servizio Microsoft Azure Red Hat OpenShift.

## <a name="azure-regions"></a>Aree di Azure

Vedere i [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all) per un elenco aggiornato delle aree in cui è possibile distribuire i cluster OpenShift di Azure Red Hat.

## <a name="virtual-machine-sizes"></a>Dimensioni delle macchine virtuali

Di seguito sono riportate le dimensioni delle macchine virtuali supportate che è possibile specificare per i nodi di calcolo nel cluster Azure Red Hat OpenShift.

> [!Important]
> Ogni macchina virtuale ha un numero diverso di unità che possono essere collegate. Questo potrebbe non essere immediatamente chiaro come la memoria o le dimensioni della CPU.
> Non tutte le dimensioni di macchina virtuale sono disponibili in tutte le aree. Anche se l'API supporta le dimensioni specificate, potrebbe essere presente un errore se le dimensioni non sono disponibili nell'area specificata.
> Per ulteriori informazioni, vedere l' [elenco corrente delle dimensioni delle VM supportate per area](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) .

## <a name="compute-node-sizes"></a>Dimensioni dei nodi di calcolo

Le dimensioni dei nodi di calcolo seguenti sono supportate dall'API REST di Azure Red Hat OpenShift:

|Dimensione|vCPU|RAM|
|-|-|-|
|D4S standard V3|4|16 GB|
|D8S standard V3|8|32 GB|
|D16s standard V3|16|64 GB|
|D32s standard V3|32|128 GB|
|-|-|-|
|E4s standard V3|4|32 GB|
|E8s standard V3|8|64 GB|
|E16s standard V3|16|128 GB|
|E32s standard V3|32|256 GB|
|-|-|-|
|F8s standard v2|8|16 GB|
|F16 standard v2|16|32 GB|
|F32s standard v2|32|64 GB|

## <a name="master-node-sizes"></a>Dimensioni dei nodi master

Le dimensioni dei nodi master/infrastruttura seguenti sono supportate dall'API REST di Azure Red Hat OpenShift:

|Dimensione|vCPU|RAM|
|-|-|-|
|D4S standard V3|4|16 GB|
|D8S standard V3|8|32 GB|
|D16s standard V3|16|64 GB|
|D32s standard V3|32|128 GB|

## <a name="next-steps"></a>Passaggi successivi

Provare l'esercitazione [creare un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md) .
