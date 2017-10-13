---
title: Risolvere i problemi relativi a errori di allocazione delle VM Windows | Microsoft Docs
description: Risolvere i problemi relativi a errori di allocazione quando si crea, riavvia o ridimensiona una macchina virtuale Windows in Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: 
author: JiangChen79
manager: felixwu
editor: 
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: bb939e23-77fc-4948-96f7-5037761c30e8
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/13/2016
ms.author: cjiang
ms.openlocfilehash: 9db1987541f2b64a38e6d0efe1b80b18372ff64e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-windows-vms-in-azure"></a>Risolvere i problemi relativi a errori di allocazione quando si crea, riavvia o ridimensiona una macchina virtuale Windows in Azure
Quando si crea una VM, si riavviano VM arrestate (deallocate) o si ridimensiona una VM, Microsoft Azure alloca risorse di calcolo alla sottoscrizione. In alcuni casi possono verificarsi errori quando si eseguono queste operazioni, anche prima di raggiungere i limiti della sottoscrizione di Azure. Questo articolo illustra le cause di alcuni dei più comuni errori di allocazione e suggerisce una possibile correzione. Queste informazioni potrebbero risultare utili anche quando si pianifica la distribuzione dei servizi. È anche possibile [risolvere i problemi relativi agli errori di allocazione quando si crea, si riavvia o si ridimensiona una VM Linux in Azure](../linux/allocation-failure.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-allocation-failure](../../../includes/virtual-machines-common-allocation-failure.md)]

