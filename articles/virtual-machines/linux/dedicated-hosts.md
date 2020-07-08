---
title: Panoramica degli host dedicati di Azure per le macchine virtuali
description: Altre informazioni sul modo in cui è possibile usare gli host dedicati di Azure per la distribuzione di macchine virtuali.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: c19b3410e106aaf5fed53aba45d06eac6dd9d601
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "78970735"
---
# <a name="azure-dedicated-hosts"></a>Host dedicati di Azure

L'host dedicato di Azure è un servizio che offre server fisici, in grado di ospitare una o più macchine virtuali, dedicati a una sottoscrizione di Azure. Gli host dedicati sono gli stessi server fisici usati nei data center, forniti come risorsa. È possibile effettuare il provisioning di host dedicati all'interno di un'area, di una zona di disponibilità e di un dominio di errore. In seguito è possibile inserire le VM direttamente negli host con provisioning, nella configurazione più adatta alle proprie esigenze.



[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]


## <a name="next-steps"></a>Passaggi successivi

- È possibile distribuire un host dedicato usando l' [interfaccia](dedicated-hosts-cli.md)della riga di comando di Azure, il [portale](dedicated-hosts-portal.md)e [PowerShell](../windows/dedicated-hosts-powershell.md).

- Per ulteriori informazioni, vedere la panoramica [Host dedicati](dedicated-hosts.md).

- È disponibile [qui](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) un modello di esempio che usa sia le zone che i domini di errore per la resilienza massima in un'area.

- È anche possibile risparmiare sui costi con un' [istanza riservata di host dedicati di Azure](../prepay-dedicated-hosts-reserved-instances.md).
