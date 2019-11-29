---
title: Gestire la disponibilità delle macchine virtuali Windows in Azure
description: Informazioni su come usare più macchine virtuali per garantire alta disponibilità per un'applicazione Windows in Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 02351953-7b6a-4657-b9e1-de2ea8f6aa05
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/27/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1f9914b84b63f271c7dd7d1b8f7dbc3b69511605
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561183"
---
# <a name="manage-the-availability-of-windows-virtual-machines-in-azure"></a>Gestire la disponibilità delle macchine virtuali Windows in Azure 

Informazioni su come configurare e gestire più macchine virtuali per garantire disponibilità elevata per un'applicazione Windows in Azure. È anche possibile [gestire la disponibilità delle macchine virtuali Linux](../linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Per istruzioni sulla creazione e l'uso dei set di disponibilità con il modello di distribuzione classica, vedere [Come configurare un set di disponibilità](classic/configure-availability-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

[!INCLUDE [virtual-machines-common-manage-availability](../../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul bilanciamento del carico delle macchine virtuali, vedere [Bilanciamento del traffico di Azure per macchine virtuali](tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Visualizzare le architetture di riferimento per l'esecuzione di applicazioni a più livelli in SQL Server in IaaS

* [Applicazione Windows a più livelli in Azure con SQL Server](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [Eseguire un'applicazione a più livelli in più aree di Azure per la disponibilità elevata](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)
