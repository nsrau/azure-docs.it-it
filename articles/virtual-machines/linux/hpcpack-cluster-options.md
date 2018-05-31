---
title: Opzioni cluster HPC Pack Linux in Azure | Microsoft Docs
description: Informazioni sulle opzioni con Microsoft HPC Pack per creare e gestire un cluster HPC (High Performance Computing) Linux nel cloud di Azure
services: virtual-machines-linux,cloud-services
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: ac60624e-aefa-40c3-8bc1-ef6d5c0ef1a2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 05/14/2018
ms.author: danlep
ms.openlocfilehash: 281867e30c78c7ed36ac739c8ae1a902463199cd
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166455"
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-linux-hpc-workloads-in-azure"></a>Opzioni con HPC Pack per creare e gestire un cluster per i carichi di lavoro Linux HPC in Azure
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

In questo articolo sono trattate le opzioni di Azure per l'uso di HPC Pack per l'esecuzione di carichi di lavoro di Linux. Esistono anche opzioni per l'esecuzione di [carichi di lavoro di Windows HPC con HPC Pack](../windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="hpc-pack-cluster-in-azure-vms-and-vm-scale-sets"></a>Cluster HPC Pack nelle macchine virtuali e nei set di scalabilità di macchine virtuali di Azure
### <a name="azure-resource-manager-templates"></a>Modelli di Gestione risorse di Azure
* (GitHub) [HPC Pack 2016 Update 1 cluster templates](https://github.com/MsHpcPack/HPCPack2016) (Modelli di cluster HPC Pack 2016 Aggiornamento 1)
* (GitHub) [HPC Pack 2012 R2 cluster templates](https://github.com/MsHpcPack/HPCPack2012R2) (Modelli di cluster HPC Pack 2012 R2)
* (Guida introduttiva) [Create an HPC Pack 2012 R2 cluster with Linux compute nodes](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn) (Creare un cluster HPC Pack 2012 R2 con nodi di calcolo Linux)

### <a name="azure-vm-images"></a>Immagini di macchina virtuale di Azure
Sfogliare le [Immagini di HPC Pack in Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?page=1&search=%22HPC%20%20Pack%22) se si desidera creare il proprio cluster HPC Pack in Azure.

## <a name="hpc-pack-2012-r2-cluster-in-classic-deployment-model"></a>Cluster HPC Pack 2012 R2 nel modello di distribuzione classica
* [Creare un cluster HPC Linux con lo script di distribuzione IaaS di HPC Pack](../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Configurazione di un cluster Linux RDMA per eseguire applicazioni MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Esercitazione: introduzione all’uso di nodi di calcolo Linux in un cluster HPC Pack in Azure](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Esercitazione: eseguire NAMD con Microsoft HPC Pack su nodi di calcolo Linux in Azure](classic/hpcpack-cluster-namd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Esercitazione: eseguire OpenFOAM con Microsoft HPC Pack in un cluster Linux RDMA in Azure](classic/hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Esercitazione: eseguire STAR-CCM+ con Microsoft HPC Pack in un cluster Linux RDMA in Azure](classic/hpcpack-cluster-starccm.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

## <a name="job-submisssion"></a>Invio del processo
* [Inviare i processi a un cluster HPC Pack in Azure](../windows/hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




