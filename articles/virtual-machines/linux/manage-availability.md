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
ms.date: 12/06/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ac83373c1c8a8077a235be2a7b5f5df9353add45
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="manage-the-availability-of-linux-virtual-machines"></a>Gestire la disponibilità delle macchine virtuali Linux

Informazioni su come configurare e gestire più macchine virtuali per garantire disponibilità elevata per un'applicazione Linux in Azure. È anche possibile [gestire la disponibilità delle macchine virtuali Windows](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Per istruzioni sulla creazione di un set di disponibilità tramite l'interfaccia della riga di comando nel modello di distribuzione di Resource Manager, vedere [azure availset - Comandi per gestire i set di disponibilità](../azure-cli-arm-commands.md#azure-availset-commands-to-manage-your-availability-sets).

[!INCLUDE [virtual-machines-common-manage-availability](../../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul bilanciamento del carico delle macchine virtuali, vedere [Bilanciamento del traffico di Azure per macchine virtuali](../virtual-machines-linux-load-balance.md).

