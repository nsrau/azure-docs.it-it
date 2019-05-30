---
title: Risorse supportate per Azure Red Hat OpenShift | Microsoft Docs
description: Comprendere quali aree di Azure e dimensioni delle macchine virtuali sono supportati da Microsoft Azure Red Hat OpenShift.
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/15/2019
ms.openlocfilehash: c226227797802ab58d1bcbaadb7e97e780b30560
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306208"
---
# <a name="azure-red-hat-openshift-resources"></a>Risorse di Azure Red Hat OpenShift

In questo argomento elenca le aree di Azure e le dimensioni di macchina virtuale supportate dal servizio di Microsoft Azure Red Hat OpenShift.

## <a name="azure-regions"></a>Aree di Azure

Visualizzare [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all) per un elenco aggiornato delle aree in cui è possibile distribuire Azure Red Hat OpenShift i cluster.

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
|D4s standard v3|4|16 GB|
|D8s standard v3|8|32 GB|
|Standard D16s v3|16|64 GB|
|Standard D32s v3|32|128 GB|
|-|-|-|
|Standard E4s v3|4|32 GB|
|Standard E8s v3|8|64 GB|
|Standard E16s v3|16|128 GB|
|Standard E32s v3|32|256 GB|
|-|-|-|
|Standard F8s v2|8|16 GB|
|Standard F16s v2|16|32 GB|
|Standard F32s v2|32|64 GB|

## <a name="master-node-sizes"></a>Dimensioni dei nodi master

Lo schema seguente / le dimensioni dei nodi di infrastruttura sono supportati dall'API REST Azure Red Hat OpenShift:

|Dimensione|vCPU|RAM|
|-|-|-|
|D4s standard v3|4|16 GB|
|D8s standard v3|8|32 GB|
|Standard D16s v3|16|64 GB|
|Standard D32s v3|32|128 GB|

## <a name="next-steps"></a>Passaggi successivi

Provare il [creare un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md) esercitazione.