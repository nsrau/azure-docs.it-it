---
title: Applicazioni per il rendering - Azure Batch
description: Con Azure Batch è possibile usare qualsiasi applicazione per il rendering. Tuttavia, le immagini di macchine virtuali (VM) di Azure Marketplace sono disponibili con applicazioni comuni preinstallate.
services: batch
ms.service: batch
author: LauraBrenner
ms.author: labrenne
ms.date: 09/19/2019
ms.topic: conceptual
ms.openlocfilehash: 77672534b2aad993a44e9b637fbed58df8610e97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022988"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>Applicazioni preinstallate nelle immagini di VM per il rendering

Con Azure Batch è possibile usare qualsiasi applicazione per il rendering. Tuttavia, le immagini di macchine virtuali (VM) di Azure Marketplace sono disponibili con applicazioni comuni preinstallate.

Dove applicabile, sono disponibili licenze con pagamento in base al consumo per le applicazioni per il rendering preinstallate. Quando viene creato un pool di Batch, è possibile specificare le applicazioni richieste e sia il costo della VM sia quello delle applicazioni verrà addebitato al minuto. I prezzi delle applicazioni sono elencati nella [pagina dei prezzi di Azure Batch](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

Alcune applicazioni supportano solo Windows, ma la maggior parte è supportata sia in Windows che in Linux.

## <a name="applications-on-centos-7-rendering-images"></a>Applicazioni su immagini di rendering CentOS 7

L'elenco seguente si applica alle immagini di rendering di CentOS 7.6, versione 1.1.6.

* Autodesk Maya I/O 2017 Update 5 (cut 201708032230)
* Autodesk Maya I/O 2018 Update 2 (cut 201711281015)
* Autodesk Maya I/O 2019 Aggiornamento 1
* Autodesk Arnold per Maya 2017 (Arnold versione 5.3.1.1) MtoA-3.2.1.1-2017
* Autodesk Arnold per Maya 2018 (Arnold versione 5.3.1.1) MtoA-3.2.1.1-2018
* Autodesk Arnold per Maya 2019 (Arnold versione 5.3.1.1) MtoA-3.2.1.1-2019
* Chaos Group V-Ray per Maya 2017 (versione 3.60.04)
* Chaos Group V-Ray per Maya 2018 (versione 3.60.04)
* Blender (2.68)
* Frullatore (2.8)

## <a name="applications-on-latest-windows-server-2016-rendering-images"></a>Applicazioni sulle immagini di rendering di Windows Server 2016 più recenti

L'elenco seguente si applica alle immagini di rendering di Windows Server 2016, versione 1.3.8.The following list applies to Windows Server 2016, version 1.3.8 rendering images.

* Autodesk Maya I/O 2017 Update 5 (versione 17.4.5459)
* Autodesk Maya I/O 2018 Update 6 (versione 18.4.0.7622)
* Autodesk Maya I/O 2019
* Autodesk 3ds Max I/O 2018 Update 4 (versione 20.4.0.4254)
* Autodesk 3ds Max I/O 2019 Update 1 (versione 21.2.0.2219)
* Autodesk 3ds Max I/O 2020 Aggiornamento 2
* Autodesk Arnold per Maya 2017 (Arnold versione 5.3.0.2) MtoA-3.2.0.2-2017
* Autodesk Arnold per Maya 2018 (Arnold versione 5.3.0.2) MtoA-3.2.0.2-2018
* Autodesk Arnold per Maya 2019 (Arnold versione 5.3.0.2) MtoA-3.2.0.2-2019
* Autodesk Arnold per 3ds Max 2018 (Arnold versione 5.3.0.2)(versione 1.2.926)
* Autodesk Arnold per 3ds Max 2019 (Arnold versione 5.3.0.2)(versione 1.2.926)
* Autodesk Arnold per 3ds Max 2020 (Arnold versione 5.3.0.2)(versione 1.2.926)
* Chaos Group V-Ray per Maya 2017 (versione 4.12.01)
* Chaos Group V-Ray per Maya 2018 (versione 4.12.01)
* Chaos Group V-Ray per Maya 2019 (versione 4.04.03)
* Chaos Group V-Ray per 3ds Max 2018 (versione 4.20.01)
* Chaos Group V-Ray per 3ds Max 2019 (versione 4.20.01)
* Chaos Group V-Ray per 3ds Max 2020 (versione 4.20.01)
* Blender (2.79)
* Frullatore (2,80)
* Az 10

> [!IMPORTANT]
> Per eseguire V-Ray con Maya all'esterno `vrayses.exe` dei modelli di estensione Azure [Batch,](https://github.com/Azure/batch-extension-templates)iniziare prima di eseguire il rendering. Per avviare il file vrayses.exe all'esterno `%MAYA_2017%\vray\bin\vrayses.exe"`dei modelli, è possibile utilizzare il comando riportato di seguito.
>
> Per un esempio, vedere l'attività di avvio del [modello Maya e V-Ray](https://github.com/Azure/batch-extension-templates/blob/master/templates/maya/render-vray-windows/pool.template.json) su GitHub.For an example, see the start task of the Maya and V-Ray template on GitHub.

## <a name="applications-on-previous-windows-server-2016-rendering-images"></a>Applicazioni su immagini di rendering precedenti di Windows Server 2016

L'elenco seguente si applica alle immagini di rendering di Windows Server 2016, versione 1.3.7.The following list applies to Windows Server 2016, version 1.3.7 rendering images.

* Autodesk Maya I/O 2017 Update 5 (versione 17.4.5459)
* Autodesk Maya I/O 2018 Update 4 (versione 18.4.0.7622)
* Autodesk 3ds Max I/O 2019 Update 1 (versione 21.2.0.2219)
* Autodesk 3ds Max I/O 2018 Update 4 (versione 20.4.0.4254)
* Autodesk Arnold per Maya 2017 (Arnold versione 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold per Maya 2018 (Arnold versione 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold per 3ds Max 2018 (Arnold versione 5.0.2.4)(versione 1.2.926)
* Autodesk Arnold per 3ds Max 2019 (Arnold versione 5.0.2.4)(versione 1.2.926)
* Chaos Group V-Ray per Maya 2018 (versione 3.52.03)
* Chaos Group V-Ray per 3ds Max 2018 (versione 3.60.02)
* Chaos Group V-Ray per Maya 2019 (versione 3.52.03)
* Chaos Group V-Ray per 3ds Max 2019 (versione 4.10.01)
* Blender (2.79)

> [!NOTE]
> Chaos Group V-Ray per 3ds Max 2019 (versione 4.10.01) introduce modifiche di rilievo ai raggi V. Per usare la versione precedente (versione 3.60.02), usare Windows Server 2016, versione 1.3.2 dei nodi di rendering.

## <a name="next-steps"></a>Passaggi successivi

Per usare le immagini di VM per il rendering, è necessario specificare le immagini nella configurazione del pool al momento della creazione. Vedere le [funzionalità del pool di Batch per il rendering](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools).
