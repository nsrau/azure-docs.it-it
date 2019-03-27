---
title: Applicazioni per il rendering - Azure Batch
description: Applicazioni preinstallate per il rendering di Batch
services: batch
ms.service: batch
author: laurenhughes
ms.author: lahugh
ms.date: 03/26/2018
ms.topic: conceptual
ms.openlocfilehash: 84b2ca30f1ccd49e18e2f9d42133f8672d3f8ad6
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58496021"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>Applicazioni preinstallate nelle immagini di VM per il rendering

Con Azure Batch è possibile usare qualsiasi applicazione per il rendering. Tuttavia, le immagini di macchine virtuali (VM) di Azure Marketplace sono disponibili con applicazioni comuni preinstallate.

Dove applicabile, sono disponibili licenze con pagamento in base al consumo per le applicazioni per il rendering preinstallate. Quando viene creato un pool di Batch, è possibile specificare le applicazioni richieste e sia il costo della VM sia quello delle applicazioni verrà addebitato al minuto. I prezzi delle applicazioni sono elencati nella [pagina dei prezzi di Azure Batch](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

Alcune applicazioni supportano solo Windows, ma la maggior parte è supportata sia in Windows che in Linux.

## <a name="applications-on-centos-7-rendering-images"></a>Applicazioni in CentOS 7 il rendering delle immagini

* Autodesk Maya I/O 2017 Update 5 (cut 201708032230)
* Autodesk Maya I/O 2018 Update 2 (cut 201711281015)
* Autodesk Arnold per Maya 2017 (Arnold versione 5.0.1.1) MtoA-2.0.1.1-2017
* Autodesk Arnold per Maya 2018 (Arnold versione 5.0.1.4) MtoA-2.1.0.3-2018
* Chaos Group V-Ray per Maya 2017 (versione 3.60.04)
* Chaos Group V-Ray per Maya 2018 (versione 3.60.04)
* Blender (2.68)

## <a name="applications-on-latest-windows-server-2016-rendering-images"></a>Applicazioni in Windows Server 2016 più recenti, rendering di immagini

Nell'elenco seguente si applica a Windows Server 2016, versione 1.3.4 rendering di immagini.

* Autodesk Maya I/O 2017 Update 5 (versione 17.4.5459)
* Autodesk Maya I/O 2018 Update 4 (versione 18.4.0.7622)
* Autodesk 3ds Max I/O 2019 Update 1 (versione 21.2.0.2219)
* Autodesk 3ds Max I/O 2018 Update 4 (versione 20.4.0.4254)
* Autodesk Arnold per Maya 2017 (Arnold versione 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold per Maya 2018 (Arnold versione 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold per 3ds Max (Arnold versione 5.0.2.4 )(versione 1.2.926)
* CHAOS Group V-Ray per Maya 2018 (versione 3.52.03)
* CHAOS Group V-Ray per 3ds Max 2018 (versione 3.60.02)
* CHAOS Group V-Ray per Maya 2019 (versione 3.52.03)
* CHAOS Group V-Ray per 3ds Max 2019 (versione 4.10.01)
* Blender (2.79)

> [!NOTE]
> CHAOS Group V-Ray per 3ds Max 2019 (versione 4.10.01) introduce importanti modifiche alla V-ray. Per usare la versione precedente (versione 3.60.02), usare Windows Server 2016, i nodi di rendering versione 1.3.2.

## <a name="applications-on-previous-windows-server-2016-rendering-images"></a>Applicazioni in Windows Server 2016 precedenti, rendering di immagini

Nell'elenco seguente si applica a Windows Server 2016, versione 1.3.2 rendering di immagini.

* Autodesk Maya I/O 2017 Update 5 (versione 17.4.5459)
* Autodesk Maya I/O 2018 Update 4 (versione 18.4.0.7622)  
* Autodesk 3ds Max I/O 2019 Update 1 (versione 21.2.0.2219)
* Autodesk 3ds Max I/O 2018 Update 4 (versione 20.4.0.4254)
* Autodesk Arnold per Maya 2017 (Arnold versione 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold per Maya 2018 (Arnold versione 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold per 3ds Max (Arnold versione 5.0.2.4 )(versione 1.2.926)
* CHAOS Group V-Ray per Maya 2019 (versione 3.52.03)
* CHAOS Group V-Ray per 3ds Max 2018 (versione 3.60.02)
* Blender (2.79)

## <a name="next-steps"></a>Passaggi successivi

Per usare le immagini di VM per il rendering, è necessario specificare le immagini nella configurazione del pool al momento della creazione. Vedere le [funzionalità del pool di Batch per il rendering](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools).