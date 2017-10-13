---
title: "Gestire la disponibilità delle macchine virtuali Linux in Azure | Microsoft Docs"
description: "Informazioni su come usare più macchine virtuali per garantire alta disponibilità per un'applicazione Linux in Azure."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 891c852a-84c0-4940-a61e-ada6e185bf37
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f153a740e4814e2573e53b9c051d24c30ff9088f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-the-availability-of-linux-virtual-machines"></a>Gestire la disponibilità delle macchine virtuali Linux

Informazioni su come configurare e gestire più macchine virtuali per garantire disponibilità elevata per un'applicazione Linux in Azure. È anche possibile [gestire la disponibilità delle macchine virtuali Windows](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Per istruzioni sulla creazione di un set di disponibilità tramite l'interfaccia della riga di comando nel modello di distribuzione di Resource Manager, vedere [azure availset - Comandi per gestire i set di disponibilità](../azure-cli-arm-commands.md#azure-availset-commands-to-manage-your-availability-sets).

[!INCLUDE [virtual-machines-common-manage-availability](../../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul bilanciamento del carico delle macchine virtuali, vedere [Bilanciamento del traffico di Azure per macchine virtuali](../virtual-machines-linux-load-balance.md).

