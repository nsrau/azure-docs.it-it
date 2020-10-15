---
title: Risolvere gli errori di spostamento
description: Risolvere i problemi di trasferimento delle risorse in un nuovo gruppo di risorse o una nuova sottoscrizione.
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: 41b1e2435caf9874f3582a3394664c7b7f5a8d29
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90054163"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Risoluzione degli errori di spostamento delle risorse di Azure in un nuovo gruppo di risorse o in una nuova sottoscrizione

Questo articolo fornisce suggerimenti per la risoluzione dei problemi durante lo trasferimento delle risorse.

## <a name="upgrade-a-subscription"></a>Aggiornare una sottoscrizione

Se si desidera effettivamente aggiornare l'offerta della sottoscrizione di Azure (ad esempio passando da gratuito a pagamento a consumo) è necessario convertire la sottoscrizione.

* Per aggiornare una versione di valutazione gratuita, vedere [aggiornare la versione di valutazione gratuita o Microsoft Imagine sottoscrizione di Azure a pagamento in base al](../../cost-management-billing/manage/upgrade-azure-subscription.md)consumo.
* Per modificare un account con pagamento in base al consumo, vedere [modificare la sottoscrizione di Azure con pagamento in base al consumo in un'offerta diversa](../../cost-management-billing/manage/switch-azure-offer.md).

Se non è possibile convertire la sottoscrizione, [creare una richiesta di supporto tecnico di Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Selezionare **Gestione delle sottoscrizioni** per il tipo di problema.

## <a name="service-limitations"></a>Limitazioni del servizio

Alcuni servizi richiedono considerazioni aggiuntive quando si trasferiscono le risorse. Se si stanno migrando i servizi seguenti, assicurarsi di controllare le linee guida e le limitazioni.

* [Servizi app](./move-limitations/app-service-move-limitations.md)
* [Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Modello di distribuzione classica](./move-limitations/classic-model-move-limitations.md)
* [Rete](./move-limitations/networking-move-limitations.md)
* [Servizi di ripristino](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Macchine virtuali](./move-limitations/virtual-machines-move-limitations.md)

## <a name="large-requests"></a>Richieste di grandi dimensioni

Quando possibile, suddividere spostamenti di grandi dimensioni in operazioni di spostamento separate. Resource Manager restituisce immediatamente un errore quando sono presenti più di 800 risorse in un'unica operazione. Anche lo spostamento di meno di 800 risorse può non riuscire a causa di un timeout.

## <a name="resource-not-in-succeeded-state"></a>Lo stato della risorsa non è riuscito

Quando viene ricevuto un messaggio di errore che indica che non è possibile spostare una risorsa perché non è in uno stato Succeeded, può essere effettivamente una risorsa dipendente che blocca lo spostamento. Il codice di errore è in genere **MoveCannotProceedWithResourcesNotInSucceededState**.

Se il gruppo di risorse di origine o di destinazione contiene una rete virtuale, durante lo spostamento vengono controllati gli Stati di tutte le risorse dipendenti per la rete virtuale. Il controllo include le risorse direttamente e indirettamente dipendenti dalla rete virtuale. Se una di queste risorse si trova in uno stato di errore, lo spostamento è bloccato. Se ad esempio una macchina virtuale che usa la rete virtuale non è riuscita, lo spostamento è bloccato. Lo spostamento viene bloccato anche quando la macchina virtuale non è una delle risorse spostate e non si trova in uno dei gruppi di risorse per lo spostamento.

Quando si riceve questo errore, sono disponibili due opzioni. Spostare le risorse in un gruppo di risorse che non dispone di una rete virtuale o [contattare il supporto tecnico](../../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>Passaggi successivi

Per i comandi sullo spostamento di risorse, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](move-resource-group-and-subscription.md).
