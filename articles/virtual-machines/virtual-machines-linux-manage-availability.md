---
title: Gestire la disponibilità delle macchine virtuali Linux | Microsoft Docs
description: Informazioni su come usare più macchine virtuali per garantire alta disponibilità per un'applicazione Linux in Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/25/2016
ms.author: cynthn

---
# Gestione della disponibilità delle macchine virtuali
Informazioni su come configurare e gestire più macchine virtuali per garantire disponibilità elevata per un'applicazione Linux in Azure. È anche possibile [gestire la disponibilità delle macchine virtuali Windows](virtual-machines-windows-manage-availability.md).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Per istruzioni sulla creazione di un set di disponibilità tramite l'interfaccia della riga di comando nel modello di distribuzione di Resource Manager, vedere [azure availset - Comandi per gestire i set di disponibilità](azure-cli-arm-commands.md#azure-availset-commands-to-manage-your-availability-sets).

[!INCLUDE [virtual-machines-common-manage-availability](../../includes/virtual-machines-common-manage-availability.md)]

## Passaggi successivi
Per altre informazioni sul bilanciamento del carico delle macchine virtuali, vedere [Bilanciamento del traffico di Azure per macchine virtuali](virtual-machines-linux-load-balance.md).

<!---HONumber=AcomDC_0601_2016-->