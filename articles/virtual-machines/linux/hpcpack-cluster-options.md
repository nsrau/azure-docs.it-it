---
title: Opzioni cluster HPC Pack Linux in Azure | Microsoft Docs
description: Informazioni sulle opzioni con Microsoft HPC Pack per creare e gestire un cluster HPC (High Performance Computing) Linux nel cloud di Azure
services: virtual-machines-linux,cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: ac60624e-aefa-40c3-8bc1-ef6d5c0ef1a2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 08/25/2017
ms.author: danlep
ms.openlocfilehash: 99aee6b73242d21842b02ded8c9e4e25bc5cedb4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-linux-hpc-workloads-in-azure"></a>Opzioni con HPC Pack per creare e gestire un cluster per i carichi di lavoro Linux HPC in Azure
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

In questo articolo sono trattate le opzioni per l'uso di HPC Pack per l'esecuzione di carichi di lavoro di Linux. Esistono anche opzioni per l'esecuzione di [carichi di lavoro di Windows HPC con HPC Pack](../windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="hpc-pack-cluster-in-azure-vms"></a>Cluster HPC Pack nelle VM di Azure
### <a name="azure-templates"></a>Modelli di Azure
* (GitHub) [HPC Pack 2016 cluster templates](https://github.com/MsHpcPack/HPCPack2016) (Modelli di cluster HPC Pack 2016)
* (Marketplace) [HPC Pack cluster for Linux workloads](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)
* (Guida introduttiva) [Create an HPC cluster with Linux compute nodes](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn)

### <a name="powershell-deployment-script-for-hpc-pack-2012-r2"></a>Script di distribuzione PowerShell per HPC Pack 2012 R2
* [Creare un cluster HPC Linux con lo script di distribuzione IaaS di HPC Pack](../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="tutorials"></a>Esercitazioni
* [Esercitazione: introduzione all’uso di nodi di calcolo Linux in un cluster HPC Pack in Azure](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Esercitazione: eseguire NAMD con Microsoft HPC Pack su nodi di calcolo Linux in Azure](classic/hpcpack-cluster-namd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Esercitazione: eseguire OpenFOAM con Microsoft HPC Pack in un cluster Linux RDMA in Azure](classic/hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Esercitazione: eseguire STAR-CCM+ con Microsoft HPC Pack in un cluster Linux RDMA in Azure](classic/hpcpack-cluster-starccm.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="cluster-management"></a>Gestione dei cluster
* [Inviare i processi a un cluster HPC Pack in Azure](../windows/hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Job management in HPC Pack](https://technet.microsoft.com/library/jj899585.aspx)

## <a name="rdma-clusters-for-mpi-workloads"></a>Cluster RDMA per carichi di lavoro MPI
* [Esercitazione: eseguire OpenFOAM con Microsoft HPC Pack in un cluster Linux RDMA in Azure](classic/hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Configurazione di un cluster Linux RDMA per eseguire applicazioni MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

