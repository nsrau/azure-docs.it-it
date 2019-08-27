---
title: Risolvere gli errori quando si trasferiscono risorse di Azure alla nuova sottoscrizione o al gruppo di risorse
description: Usare Azure Resource Manager per spostare risorse a un nuovo gruppo di risorse o a una nuova sottoscrizione.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: b688218b871a5f652e7f4de172d23f1b1fb0aa5c
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035512"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Risolvere i problemi di trasferimento delle risorse di Azure in un nuovo gruppo di risorse o sottoscrizione

Questo articolo fornisce suggerimenti per la risoluzione dei problemi durante lo trasferimento delle risorse.

## <a name="upgrade-a-subscription"></a>Aggiornare una sottoscrizione

Se si desidera effettivamente aggiornare l'offerta della sottoscrizione di Azure (ad esempio passando da gratuito a pagamento a consumo) è necessario convertire la sottoscrizione.

* Per aggiornare una versione di valutazione gratuita, vedere [Aggiornare la versione di valutazione gratuita o la sottoscrizione di Azure per Microsoft Imagine alla sottoscrizione con pagamento in base al consumo](../billing/billing-upgrade-azure-subscription.md).
* Per modificare un account con pagamento in base al consumo, vedere [Modificare la sottoscrizione Azure con pagamento in base al consumo in un'offerta diversa](../billing/billing-how-to-switch-azure-offer.md).

Se non è possibile convertire la sottoscrizione, [creare una richiesta di supporto tecnico di Azure](../azure-supportability/how-to-create-azure-support-request.md). Selezionare **Gestione delle sottoscrizioni** per il tipo di problema.

## <a name="service-limitations"></a>Limitazioni del servizio

Alcuni servizi richiedono considerazioni aggiuntive quando si trasferiscono le risorse. Se si stanno migrando i servizi seguenti, assicurarsi di controllare le linee guida e le limitazioni.

* [Servizi app](./move-limitations/app-service-move-limitations.md)
* [Servizi di Azure DevOps](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Modello di distribuzione classica](./move-limitations/classic-model-move-limitations.md)
* [Rete](./move-limitations/networking-move-limitations.md)
* [Servizi di ripristino](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Macchine virtuali](./move-limitations/virtual-machines-move-limitations.md)

## <a name="large-requests"></a>Richieste di grandi dimensioni

Quando possibile, suddividere spostamenti di grandi dimensioni in operazioni di spostamento separate. Resource Manager restituisce immediatamente un errore quando sono presenti più di 800 risorse in un'unica operazione. Anche lo spostamento di meno di 800 risorse può non riuscire a causa di un timeout.

## <a name="resource-not-in-succeeded-state"></a>Lo stato della risorsa non è riuscito

Quando viene ricevuto un messaggio di errore che indica che non è possibile spostare una risorsa perché non è in uno stato Succeeded, può essere effettivamente una risorsa dipendente che blocca lo spostamento.

Se il gruppo di risorse di origine o di destinazione contiene una rete virtuale, durante lo spostamento vengono controllati gli Stati di tutte le risorse dipendenti per la rete virtuale. Se una di queste risorse si trova in uno stato di errore, lo spostamento è bloccato. Se ad esempio una macchina virtuale che usa la rete virtuale non è riuscita, lo spostamento è bloccato. Lo spostamento viene bloccato anche quando la macchina virtuale non è una delle risorse spostate e non si trova in uno dei gruppi di risorse per lo spostamento. Per evitare questo problema, spostare le risorse in un gruppo di risorse che non dispone di una rete virtuale.

## <a name="next-steps"></a>Passaggi successivi

Per i comandi sullo spostamento di risorse, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](resource-group-move-resources.md).
