---
title: Panoramica degli host dedicati di Azure per le macchine virtuali
description: Altre informazioni sul modo in cui è possibile usare gli host dedicati di Azure per la distribuzione di macchine virtuali.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 07/28/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 08d5f88ab018f9852da5bba5fe2230ef8148e914
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386529"
---
# <a name="azure-dedicated-hosts"></a>Host dedicati di Azure

L'host dedicato di Azure è un servizio che offre server fisici, in grado di ospitare una o più macchine virtuali, dedicati a una sottoscrizione di Azure. Gli host dedicati sono gli stessi server fisici usati nei data center, forniti come risorsa. È possibile effettuare il provisioning di host dedicati all'interno di un'area, di una zona di disponibilità e di un dominio di errore. In seguito è possibile inserire le VM direttamente negli host con provisioning, nella configurazione più adatta alle proprie esigenze.


[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]

## <a name="next-steps"></a>Passaggi successivi

- È possibile distribuire un host dedicato usando [Azure PowerShell](dedicated-hosts-powershell.md), il [portale](dedicated-hosts-portal.md)e l' [interfaccia](../linux/dedicated-hosts-cli.md)della riga di comando di Azure.

- È disponibile [qui](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) un modello di esempio che usa sia le zone che i domini di errore per la resilienza massima in un'area.

- È anche possibile risparmiare sui costi con un' [istanza riservata di host dedicati di Azure](../prepay-dedicated-hosts-reserved-instances.md).
