---
title: Panoramica degli host dedicati di Azure per le macchine virtuali
description: Altre informazioni sul modo in cui è possibile usare gli host dedicati di Azure per la distribuzione di macchine virtuali.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure
ms.date: 01/09/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 9709408f006cbcc5786a4e5a854abcd20affd693
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82082833"
---
# <a name="azure-dedicated-hosts"></a>Host dedicati di Azure

Host dedicato di Azure è un servizio che fornisce server fisici in grado di ospitare una o più macchine virtuali, dedicate a una sottoscrizione di Azure. Gli host dedicati sono gli stessi server fisici usati nei data center, forniti come risorsa. È possibile eseguire il provisioning di host dedicati in un'area, una zona di disponibilità e un dominio di errore. Quindi, è possibile inserire le VM direttamente negli host di cui è stato eseguito il provisioning, in base a qualsiasi configurazione più adatta alle proprie esigenze.


[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]

## <a name="next-steps"></a>Passaggi successivi

- È possibile distribuire un host dedicato usando [Azure PowerShell](dedicated-hosts-powershell.md), il [portale](dedicated-hosts-portal.md)e l' [interfaccia](../linux/dedicated-hosts-cli.md)della riga di comando di Azure.

- È disponibile un modello di esempio [, che](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)USA sia le zone che i domini di errore per la resilienza massima in un'area.

- È anche possibile risparmiare sui costi con un' [istanza riservata di host dedicati di Azure](../prepay-dedicated-hosts-reserved-instances.md).
