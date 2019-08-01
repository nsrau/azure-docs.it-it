---
title: Annullare la sottoscrizione di Azure | Microsoft Docs
description: Descrive come annullare la sottoscrizione di Azure, ad esempio la sottoscrizione di valutazione gratuita
author: bandersmsft
manager: amberb
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: banders
ms.openlocfilehash: 7756174f01e3fede17bec3e2ac185e89caddc097
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68666440"
---
# <a name="cancel-your-azure-subscription"></a>Annullare la sottoscrizione di Azure

È possibile annullare la sottoscrizione di Azure nel portale di Azure se la sottoscrizione non è più necessaria. 

Prima di annullare la sottoscrizione:
* Eseguire il backup dei dati. Se ad esempio si archiviano i dati in Archiviazione di Azure o SQL, scaricare una copia. Se si ha una macchina virtuale, salvare un'immagine in locale.
* Arrestare i servizi. Passare alla [pagina delle risorse nel portale di gestione](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources) e **arrestare** qualsiasi macchina virtuale, applicazione o altri servizi.
* Prendere in considerazione la migrazione dei dati. Vedere [Spostare le risorse in un nuovo gruppo o sottoscrizione](../azure-resource-manager/resource-group-move-resources.md).
* Eliminare tutte le risorse e tutti i gruppi di risorse. Prima di poter annullare una sottoscrizione, è necessario eliminarli. Ogni gruppo di risorse deve essere eliminato singolarmente. Durante l'eliminazione del gruppo di risorse, è necessario confermare l'eliminazione digitando il nome del gruppo di risorse.
* Se sono presenti ruoli personalizzati che fanno riferimento a questa sottoscrizione `AssignableScopes`in, è necessario aggiornare i ruoli personalizzati per rimuovere la sottoscrizione. Se si tenta di aggiornare un ruolo personalizzato dopo aver annullato una sottoscrizione, è possibile che venga ricevuto un errore. Per altre informazioni, vedere [risolvere i problemi relativi ai ruoli personalizzati](../role-based-access-control/troubleshooting.md#problems-with-custom-roles) e [ai ruoli personalizzati per le risorse di Azure](../role-based-access-control/custom-roles.md).

Se si annulla un piano di supporto di Azure a pagamento, viene addebitato il resto del periodo di validità della sottoscrizione. Per altre informazioni, vedere [Piani di supporto per Azure](https://azure.microsoft.com/support/plans/).

## <a name="cancel-subscription-in-the-azure-portal"></a>Annulla sottoscrizione nel portale di Azure

1. Selezionare la sottoscrizione [nella pagina sottoscrizioni del portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Selezionare la sottoscrizione da annullare.
3. Selezionare **Panoramica** e quindi selezionare **Annulla sottoscrizione**.
    ![Screenshot che mostra il pulsante Annulla](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
3. Seguire le istruzioni e completare la procedura di annullamento.


## <a name="who-can-cancel-a-subscription"></a>Chi può annullare una sottoscrizione?

La tabella seguente descrive l'autorizzazione necessaria per annullare una sottoscrizione.

|Tipo di sottoscrizione     |Utenti che possono annullare  |
|---------|---------|
|Sottoscrizioni create quando ci si iscrive ad Azure tramite il sito Web di Azure. Ad esempio, quando si effettua l'iscrizione per ottenere un [account Azure gratuito](https://azure.microsoft.com/offers/ms-azr-0044p/), è possibile [usare un account con tariffe con pagamento in base](https://azure.microsoft.com/offers/ms-azr-0003p/) al consumo o come Sottoscrittore di [Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/). |  Amministratore account, proprietari e collaboratori per la sottoscrizione  |
|[Sviluppo/test](https://azure.microsoft.com/offers/ms-azr-0148p/) di [Microsoft Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/) e Enterprise     |  Proprietario dell'account, proprietari e collaboratori per la sottoscrizione       |
|[Piano di Azure](https://azure.microsoft.com/offers/ms-azr-0017g/) e [piano di Azure per DevTest](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  Proprietari e collaboratori per la sottoscrizione      |


## <a name="what-happens-after-i-cancel-my-subscription"></a>Cosa accade quando si annulla la sottoscrizione?

Dopo l'annullamento, la fatturazione viene arrestata immediatamente. Per essere visualizzata sul portale, tuttavia, è possibile che siano necessari fino a 10 minuti. Se si Annulla durante un periodo di fatturazione, viene inviata la fattura finale alla data di fatturazione tipica al termine del periodo.

Dopo l'annullamento, i servizi vengono disabilitati. Le macchine virtuali vengono deallocate, gli indirizzi IP temporanei vengono liberati e la risorsa di archiviazione diventa di sola lettura.

Microsoft attenderà 90 giorni prima di eliminare definitivamente i dati, nel caso in cui fosse necessario accedervi o si cambiasse idea. Non verrà comunque addebitato alcun costo per questo servizio di conservazione dei dati. Per altre informazioni, vedere [Microsoft Trust Center - Come vengono gestiti i dati](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="reactivate-subscription"></a>Riattivare la sottoscrizione

Se si annulla la sottoscrizione con tariffe con pagamento in base al consumo accidentale, è possibile [riattivarla nel centro account](billing-subscription-become-disable.md).

Se la sottoscrizione non è una sottoscrizione con tariffe con pagamento in base al consumo, contattare il supporto tecnico entro 90 giorni dall'annullamento per riattivare la sottoscrizione.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

Per eventuali domande o per richiedere assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).
