---
title: Panoramica degli host dedicati di Azure per le macchine virtuali
description: Altre informazioni sul modo in cui è possibile usare gli host dedicati di Azure per la distribuzione di macchine virtuali.
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: 940f27612b26c5baace7a19d2212fa6d8899e6e5
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834904"
---
# <a name="azure-dedicated-hosts"></a>Host dedicati di Azure

Host dedicato di Azure è un servizio che fornisce server fisici in grado di ospitare una o più macchine virtuali, dedicate a una sottoscrizione di Azure. Gli host dedicati sono gli stessi server fisici usati nei data center, forniti come risorsa. È possibile eseguire il provisioning di host dedicati in un'area, una zona di disponibilità e un dominio di errore. Quindi, è possibile inserire le VM direttamente negli host di cui è stato eseguito il provisioning, in base a qualsiasi configurazione più adatta alle proprie esigenze.



[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]


## <a name="next-steps"></a>Passaggi successivi

- È possibile distribuire un host dedicato usando l' [interfaccia](dedicated-hosts-cli.md)della riga di comando di Azure, il [portale](dedicated-hosts-portal.md)e [PowerShell](../windows/dedicated-hosts-powershell.md).

- Per ulteriori informazioni, vedere Cenni preliminari sugli [host dedicati](dedicated-hosts.md) .

- È disponibile un [modello di esempio](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), che usa sia le zone che i domini di errore per la resilienza massima in un'area.