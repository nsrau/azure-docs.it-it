---
title: Risolvere gli errori quando si spostano le risorse di Azure alla nuova sottoscrizione o gruppo di risorse
description: Usare Azure Resource Manager per spostare risorse a un nuovo gruppo di risorse o a una nuova sottoscrizione.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: e23d7c571a010e5bfb42e5f15368e0194272ed53
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723467"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Risolvere i problemi di spostare le risorse di Azure in un nuovo gruppo di risorse o sottoscrizione

Questo articolo fornisce suggerimenti per risolvere i problemi quando si spostano risorse.

## <a name="upgrade-a-subscription"></a>Aggiornare una sottoscrizione

Se si desidera effettivamente aggiornare l'offerta della sottoscrizione di Azure (ad esempio passando da gratuito a pagamento a consumo) è necessario convertire la sottoscrizione.

* Per aggiornare una versione di valutazione gratuita, vedere [Aggiornare la versione di valutazione gratuita o la sottoscrizione di Azure per Microsoft Imagine alla sottoscrizione con pagamento in base al consumo](../billing/billing-upgrade-azure-subscription.md).
* Per modificare un account con pagamento in base al consumo, vedere [Modificare la sottoscrizione Azure con pagamento in base al consumo in un'offerta diversa](../billing/billing-how-to-switch-azure-offer.md).

Se non è possibile convertire la sottoscrizione, [creare una richiesta di supporto tecnico di Azure](../azure-supportability/how-to-create-azure-support-request.md). Selezionare **Gestione delle sottoscrizioni** per il tipo di problema.

## <a name="service-limitations"></a>Limitazioni del servizio

Alcuni servizi richiedono considerazioni aggiuntive quando si spostano risorse. Se si spostano i servizi seguenti, accertarsi di controllare le linee guida e limitazioni.

* [Servizi app](./move-limitations/app-service-move-limitations.md)
* [Servizi di Azure DevOps](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Modello di distribuzione classica](./move-limitations/classic-model-move-limitations.md)
* [Servizi di ripristino](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Macchine virtuali](./move-limitations/virtual-machines-move-limitations.md)
* [Reti virtuali](./move-limitations/virtual-network-move-limitations.md)

## <a name="large-requests"></a>Richieste di grandi dimensioni

Quando possibile, suddividere spostamenti di grandi dimensioni in operazioni di spostamento separate. Resource Manager restituisce immediatamente un errore quando sono presenti più di 800 risorse in un'unica operazione. Anche lo spostamento di meno di 800 risorse può non riuscire a causa di un timeout.

## <a name="next-steps"></a>Passaggi successivi

Per i comandi sullo spostamento di risorse, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](resource-group-move-resources.md).
