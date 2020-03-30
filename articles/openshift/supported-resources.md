---
title: Risorse supportate per Azure Red Hat OpenShift
description: Informazioni sulle aree di Azure e sulle dimensioni delle macchine virtuali supportate da Microsoft Azure Red Hat OpenShift.Understand which Azure regions and virtual machine sizes are supported by Microsoft Azure Red Hat OpenShift.
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
# <a name="azure-red-hat-openshift-resources"></a>Risorse di Azure Red Hat OpenShiftAzure Red Hat OpenShift resources

Questo argomento elenca le aree di Azure e le dimensioni delle macchine virtuali supportate dal servizio Microsoft Azure Red Hat OpenShift.This topic lists the Azure regions and virtual machine sizes supported by the Microsoft Azure Red Hat OpenShift service.

## <a name="azure-regions"></a>Aree di Azure

Vedere Prodotti disponibili per area per un elenco corrente di aree in cui è possibile distribuire cluster Azure Red Hat OpenShift.See [Products available by region](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all) for a current list of regions where you can deploy Azure Red Hat OpenShift clusters.

## <a name="virtual-machine-sizes"></a>Dimensioni delle macchine virtuali

Ecco le dimensioni delle macchine virtuali supportate che è possibile specificare per i nodi di calcolo nel cluster Azure Red Hat OpenShift.Here are the supported virtual machine sizes you can specify for the compute nodes in your Azure Red Hat OpenShift cluster.

> [!Important]
> Ogni macchina virtuale ha un numero diverso di unità che possono essere collegate. Questo potrebbe non essere immediatamente chiaro come la memoria o la dimensione della CPU.
> Non tutte le dimensioni di macchina virtuale sono disponibili in tutte le aree. Anche se l'API supporta la dimensione specificata, è possibile che venga visualizzato un errore se la dimensione non è disponibile nell'area specificata.
> Per altre informazioni, vedere [Elenco corrente delle dimensioni di VM supportate per ogni area.](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)

## <a name="compute-node-sizes"></a>Dimensioni dei nodi di calcolo

The following compute node sizes are supported by the Azure Red Hat OpenShift REST API:

|Dimensione|vCPU|RAM|
|-|-|-|
|Standard D4s v3|4|16 GB|
|Standard D8s v3|8|32 GB|
|Standard D16s v3|16|64 GB|
|Standard D32s v3|32|128 GB|
|-|-|-|
|Standard E4s v3|4|32 GB|
|Standard E8s v3|8|64 GB|
|Standard E16s v3|16|128 GB|
|Standard E32s v3|32|256 GB|
|-|-|-|
|F8 s standard v2|8|16 GB|
|F16 standard v2|16|32 GB|
|F32 standard v2|32|64 GB|

## <a name="master-node-sizes"></a>Dimensioni dei nodi master

Le seguenti dimensioni dei nodi master/infrastruttura sono supportate dall'API REST Azure Red Hat OpenShift:

|Dimensione|vCPU|RAM|
|-|-|-|
|Standard D4s v3|4|16 GB|
|Standard D8s v3|8|32 GB|
|Standard D16s v3|16|64 GB|
|Standard D32s v3|32|128 GB|

## <a name="next-steps"></a>Passaggi successivi

Provare [l'esercitazione Creare un cluster OpenShift di Azure Red Hat.Try](tutorial-create-cluster.md) the Create a Azure Red Hat OpenShift cluster tutorial.
