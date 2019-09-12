---
title: Applicazioni per il rendering - Azure Batch
description: Applicazioni preinstallate per il rendering di Batch
services: batch
ms.service: batch
author: laurenhughes
ms.author: lahugh
ms.date: 09/10/2019
ms.topic: conceptual
ms.openlocfilehash: 2b0a132c156cc12d317bf51488625191bb8091fc
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881472"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>Applicazioni preinstallate nelle immagini di VM per il rendering

Con Azure Batch è possibile usare qualsiasi applicazione per il rendering. Tuttavia, le immagini di macchine virtuali (VM) di Azure Marketplace sono disponibili con applicazioni comuni preinstallate.

Dove applicabile, sono disponibili licenze con pagamento in base al consumo per le applicazioni per il rendering preinstallate. Quando viene creato un pool di Batch, è possibile specificare le applicazioni richieste e sia il costo della VM sia quello delle applicazioni verrà addebitato al minuto. I prezzi delle applicazioni sono elencati nella [pagina dei prezzi di Azure Batch](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

Alcune applicazioni supportano solo Windows, ma la maggior parte è supportata sia in Windows che in Linux.

## <a name="applications-on-centos-7-rendering-images"></a>Applicazioni su immagini di rendering CentOS 7

L'elenco seguente si applica alle immagini di rendering CentOS 7,6, versione 1.1.6.

* Autodesk Maya I/O 2017 Update 5 (cut 201708032230)
* Autodesk Maya I/O 2018 Update 2 (cut 201711281015)
* Autodesk Maya I/O 2019 aggiornamento 1
* Autodesk Arnold per Maya 2017 (Arnold versione 5.3.1.1) MtoA-3.2.1.1-2017
* Autodesk Arnold per Maya 2018 (Arnold versione 5.3.1.1) MtoA-3.2.1.1-2018
* Autodesk Arnold per Maya 2019 (Arnold versione 5.3.1.1) MtoA-3.2.1.1-2019
* Chaos Group V-Ray per Maya 2017 (versione 3.60.04)
* Chaos Group V-Ray per Maya 2018 (versione 3.60.04)
* Blender (2.68)
* Blender (2,8)

## <a name="applications-on-latest-windows-server-2016-rendering-images"></a>Applicazioni sulle immagini di rendering più recenti di Windows Server 2016

L'elenco seguente si applica a Windows Server 2016, Version 1.3.7 rendering images.

* Autodesk Maya I/O 2017 Update 5 (versione 17.4.5459)
* Autodesk Maya I/O 2018 Update 4 (versione 18.4.0.7622)
* Autodesk 3ds Max I/O 2019 Update 1 (versione 21.2.0.2219)
* Autodesk 3ds Max I/O 2018 Update 4 (versione 20.4.0.4254)
* Autodesk Arnold per Maya 2017 (Arnold versione 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold per Maya 2018 (Arnold versione 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold per 3ds Max 2018 (Arnold versione 5.0.2.4) (versione 1.2.926)
* Autodesk Arnold per 3ds Max 2019 (Arnold versione 5.0.2.4) (versione 1.2.926)
* Chaos Group V-Ray per Maya 2018 (versione 3.52.03)
* Chaos Group V-Ray per 3ds Max 2018 (versione 3.60.02)
* Chaos Group V-Ray per Maya 2019 (versione 3.52.03)
* Chaos Group V-Ray per 3ds Max 2019 (versione 4.10.01)
* Blender (2.79)


> [!NOTE]
> Chaos Group V-Ray per 3ds Max 2019 (Version 4.10.01) introduce le modifiche di rilievo apportate a V-Ray. Per usare la versione precedente (versione 3.60.02), usare i nodi di rendering di Windows Server 2016, versione 1.3.2.

## <a name="applications-on-previous-windows-server-2016-rendering-images"></a>Applicazioni su immagini di rendering precedenti di Windows Server 2016

L'elenco seguente si applica a Windows Server 2016, versione 1.3.2 immagini di rendering.

* Autodesk Maya I/O 2017 Update 5 (versione 17.4.5459)
* Autodesk Maya I/O 2018 Update 4 (versione 18.4.0.7622)  
* Autodesk 3ds Max I/O 2019 Update 1 (versione 21.2.0.2219)
* Autodesk 3ds Max I/O 2018 Update 4 (versione 20.4.0.4254)
* Autodesk Arnold per Maya 2017 (Arnold versione 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold per Maya 2018 (Arnold versione 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold per 3ds Max (Arnold versione 5.0.2.4 )(versione 1.2.926)
* Chaos Group V-Ray per Maya 2019 (versione 3.52.03)
* Chaos Group V-Ray per 3ds Max 2018 (versione 3.60.02)
* Blender (2.79)

## <a name="next-steps"></a>Passaggi successivi

Per usare le immagini di VM per il rendering, è necessario specificare le immagini nella configurazione del pool al momento della creazione. Vedere le [funzionalità del pool di Batch per il rendering](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools).
