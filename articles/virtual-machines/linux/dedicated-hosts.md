---
title: Panoramica degli host dedicati di Azure per le macchine virtualiOverview of Azure Dedicated Hosts for virtual machines
description: Altre informazioni su come usare gli host dedicati di Azure per la distribuzione di macchine virtuali.
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
# <a name="azure-dedicated-hosts"></a>Host dedicati di AzureAzure Dedicated Hosts

Host dedicato di Azure è un servizio che fornisce server fisici, in grado di ospitare una o più macchine virtuali, dedicate a una sottoscrizione di Azure.Azure Dedicated Host is a service that provides physical servers - able to host one or more virtual machines - dedicated to one Azure subscription. Gli host dedicati sono gli stessi server fisici utilizzati nei nostri data center, forniti come risorsa. È possibile eseguire il provisioning di host dedicati all'interno di un'area, di una zona di disponibilità e di un dominio di errore. È quindi possibile inserire le macchine virtuali direttamente negli host di cui è stato eseguito il provisioning, in qualsiasi configurazione soddisfi meglio le proprie esigenze.



[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]


## <a name="next-steps"></a>Passaggi successivi

- È possibile distribuire un host dedicato usando [l'interfaccia della riga di comando,](dedicated-hosts-cli.md)il [portale](dedicated-hosts-portal.md)e [PowerShell](../windows/dedicated-hosts-powershell.md)di Azure.

- Per altre informazioni, vedere la panoramica degli [host dedicati.](dedicated-hosts.md)

- È disponibile un modello di esempio, disponibile [qui](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), che utilizza sia zone che domini di errore per la massima resilienza in un'area.

- È anche possibile risparmiare sui costi con [un'istanza riservata di host dedicati di Azure](../prepay-dedicated-hosts-reserved-instances.md).
