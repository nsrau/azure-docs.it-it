---
title: Panoramica degli host dedicati di Azure per le macchine virtuali
description: Altre informazioni sul modo in cui è possibile usare gli host dedicati di Azure per la distribuzione di macchine virtuali.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: c19b3410e106aaf5fed53aba45d06eac6dd9d601
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970735"
---
# <a name="azure-dedicated-hosts"></a>Host dedicati di Azure

Host dedicato di Azure è un servizio che fornisce server fisici in grado di ospitare una o più macchine virtuali, dedicate a una sottoscrizione di Azure. Gli host dedicati sono gli stessi server fisici usati nei data center, forniti come risorsa. È possibile eseguire il provisioning di host dedicati in un'area, una zona di disponibilità e un dominio di errore. Quindi, è possibile inserire le VM direttamente negli host di cui è stato eseguito il provisioning, in base a qualsiasi configurazione più adatta alle proprie esigenze.



[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]


## <a name="next-steps"></a>Passaggi successivi

- È possibile distribuire un host dedicato usando l' [interfaccia](dedicated-hosts-cli.md)della riga di comando di Azure, il [portale](dedicated-hosts-portal.md)e [PowerShell](../windows/dedicated-hosts-powershell.md).

- Per ulteriori informazioni, vedere Cenni preliminari sugli [host dedicati](dedicated-hosts.md) .

- È disponibile un modello di esempio [, che](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)USA sia le zone che i domini di errore per la resilienza massima in un'area.

- È anche possibile risparmiare sui costi con un' [istanza riservata di host dedicati di Azure](../prepay-dedicated-hosts-reserved-instances.md).
