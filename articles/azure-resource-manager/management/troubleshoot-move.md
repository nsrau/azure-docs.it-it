---
title: Risolvere gli errori di spostamentoTroubleshoot move errors
description: Usare Azure Resource Manager per spostare risorse a un nuovo gruppo di risorse o a una nuova sottoscrizione.
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: 5a65f7daa0f5e3b1c8c6ddfdbecc0ff7d53e5afd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75891266"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Risoluzione degli errori di spostamento delle risorse di Azure in un nuovo gruppo di risorse o in una nuova sottoscrizione

In questo articolo vengono forniti suggerimenti per risolvere i problemi durante lo spostamento delle risorse.

## <a name="upgrade-a-subscription"></a>Aggiornare una sottoscrizione

Se si desidera effettivamente aggiornare l'offerta della sottoscrizione di Azure (ad esempio passando da gratuito a pagamento a consumo) è necessario convertire la sottoscrizione.

* Per aggiornare una versione di valutazione gratuita, vedere [Aggiornare la versione di valutazione gratuita o la sottoscrizione di Azure per Microsoft Imagine alla sottoscrizione con pagamento in base al consumo](../../billing/billing-upgrade-azure-subscription.md).
* Per modificare un account con pagamento in base al consumo, vedere [Modificare la sottoscrizione Azure con pagamento in base al consumo in un'offerta diversa](../../billing/billing-how-to-switch-azure-offer.md).

Se non è possibile convertire la sottoscrizione, [creare una richiesta di supporto tecnico di Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Selezionare **Gestione delle sottoscrizioni** per il tipo di problema.

## <a name="service-limitations"></a>Limitazioni del servizio

Alcuni servizi richiedono considerazioni aggiuntive quando si spostano le risorse. Se stai spostando i seguenti servizi, assicurati di controllare le linee guida e le limitazioni.

* [Servizi app](./move-limitations/app-service-move-limitations.md)
* [Servizi DevOps di AzureAzure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Modello di distribuzione classica](./move-limitations/classic-model-move-limitations.md)
* [Rete](./move-limitations/networking-move-limitations.md)
* [Servizi di ripristino](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Macchine virtuali](./move-limitations/virtual-machines-move-limitations.md)

## <a name="large-requests"></a>Richieste di grandi dimensioni

Quando possibile, suddividere spostamenti di grandi dimensioni in operazioni di spostamento separate. Resource Manager restituisce immediatamente un errore quando sono presenti più di 800 risorse in un'unica operazione. Anche lo spostamento di meno di 800 risorse può non riuscire a causa di un timeout.

## <a name="resource-not-in-succeeded-state"></a>Risorsa non riuscita

Quando viene visualizzato un messaggio di errore che indica che una risorsa non può essere spostata perché non è in uno stato riuscito, potrebbe effettivamente essere una risorsa dipendente che blocca lo spostamento. In genere, il codice di errore è **MoveCannotProceedWithResourcesNotInSucceededState**.

Se il gruppo di risorse di origine o di destinazione contiene una rete virtuale, gli stati di tutte le risorse dipendenti per la rete virtuale vengono controllati durante lo spostamento. Il controllo include tali risorse direttamente e indirettamente dipendenti dalla rete virtuale. Se una di queste risorse si trova in uno stato di errore, lo spostamento viene bloccato. Ad esempio, se una macchina virtuale che utilizza la rete virtuale non è riuscita, lo spostamento viene bloccato. Lo spostamento viene bloccato anche quando la macchina virtuale non è una delle risorse da spostare e non si trova in uno dei gruppi di risorse per lo spostamento.

Quando si riceve questo errore, sono disponibili due opzioni. Spostare le risorse in un gruppo di risorse che non dispone di una rete virtuale [oppure contattare](../../azure-portal/supportability/how-to-create-azure-support-request.md)il supporto tecnico.

## <a name="next-steps"></a>Passaggi successivi

Per i comandi sullo spostamento di risorse, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](move-resource-group-and-subscription.md).
