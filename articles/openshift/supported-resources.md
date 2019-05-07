---
title: Risorse supportate per Azure Red Hat OpenShift | Microsoft Docs
description: Comprendere quali aree di Azure e dimensioni delle macchine virtuali sono supportati da Microsoft Azure Red Hat OpenShift.
services: container-service
author: tylermsft
ms.author: twhitney
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 22cc195b7808ad4a9111aafcf883a68c51f3709f
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076106"
---
# <a name="azure-red-hat-openshift-resources"></a>Risorse di Azure Red Hat OpenShift

In questo argomento elenca le aree di Azure e le dimensioni di macchina virtuale supportate dal servizio di Microsoft Azure Red Hat OpenShift.

## <a name="azure-regions"></a>Aree di Azure

È possibile distribuire i cluster Azure Red Hat OpenShift in aree di Azure in tutto il mondo:

|Region|Codice dell'interfaccia della riga|
|-|-|
|🇦🇺 Australia orientale|`australiaeast`|
|🇨🇦 Canada centrale|`canadacentral`|
|🇨🇦 Canada orientale|`canadaeast`|
|Stati Uniti orientali 🇺🇸|`eastus`|
|Stati Uniti occidentali 🇺🇸|`westus`|
|Europa occidentale 🇪🇺|`westeurope`|
|Europa settentrionale 🇪🇺|`northeurope`|

## <a name="virtual-machine-sizes"></a>Dimensioni delle macchine virtuali

Di seguito sono le dimensioni di macchina virtuale supportato che è possibile specificare per i nodi di calcolo nel cluster Azure Red Hat OpenShift.

> [!Important]
> Ogni macchina virtuale ha un numero diverso di unità che possono essere collegati. Ciò potrebbe non essere chiaro come immediatamente come le dimensioni di CPU o memoria.
> Non tutte le dimensioni di macchina virtuale sono disponibili in tutte le aree. Anche se l'API supporta le dimensioni specificate, è possibile ottenere un errore se la dimensione non è disponibile nell'area specificata.
> Visualizzare [Ridimensiona elenco corrente di macchine Virtuali supportate per ogni area](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) per altre informazioni.

## <a name="compute-node-sizes"></a>Le dimensioni dei nodi di calcolo

Le dimensioni dei nodi di calcolo seguenti sono supportate dall'API REST Azure Red Hat OpenShift:

|Dimensione|vCPU|RAM|
|-|-|-|
|D4s Standard v3|4|16 GB|
|D8s Standard v3|8|32 GB|
|D16s Standard v3|16|64 GB|
|D32s Standard v3|32|128 GB|
|-|-|-|
|E4s Standard v3|4|32 GB|
|E8s Standard v3|8|64 GB|
|E16s Standard v3|16|128 GB|
|E32s Standard v3|32|256 GB|
|-|-|-|
|F8s Standard v2|8|16 GB|
|F16s Standard v2|16|32 GB|
|F32s Standard v2|32|64 GB|

## <a name="master-node-sizes"></a>Dimensioni dei nodi master

Lo schema seguente / le dimensioni dei nodi di infrastruttura sono supportati dall'API REST Azure Red Hat OpenShift:

|Dimensione|vCPU|RAM|
|-|-|-|
|D4s Standard v3|4|16 GB|
|D8s Standard v3|8|32 GB|
|D16s Standard v3|16|64 GB|
|D32s Standard v3|32|128 GB|

## <a name="next-steps"></a>Passaggi successivi

Provare il [creare un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md) esercitazione.