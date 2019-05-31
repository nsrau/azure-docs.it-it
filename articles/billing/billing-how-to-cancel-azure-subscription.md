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
ms.date: 5/30/2019
ms.author: banders
ms.openlocfilehash: 235d93de56289bb2daaa661ee9806732da3b6d6a
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417602"
---
# <a name="cancel-your-subscription-for-azure"></a>Annullare la sottoscrizione di Azure

Solo una sottoscrizione di Azure [amministratore dell'Account](billing-subscription-transfer.md#whoisaa) può annullare una sottoscrizione di Azure. Un amministratore della sottoscrizione di Azure può anche [assegnare un altro utente come amministratore della sottoscrizione di](billing-add-change-azure-subscription-administrator.md#assign-a-user-as-an-administrator-of-a-subscription) in modo che è possibile annullare una sottoscrizione. Dopo aver annullato la sottoscrizione, l'accesso alle risorse e ai servizi di Azure non sarà più possibile.

Prima di annullare la sottoscrizione:

* Eseguire il backup dei dati. Se ad esempio si archiviano i dati in Archiviazione di Azure o SQL, scaricare una copia. Se si ha una macchina virtuale, salvare un'immagine in locale.
* Arrestare i servizi. Passare alla [pagina delle risorse nel portale di gestione](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources) e **arrestare** qualsiasi macchina virtuale, applicazione o altri servizi.
* Prendere in considerazione la migrazione dei dati. Vedere [Spostare le risorse in un nuovo gruppo o sottoscrizione](../azure-resource-manager/resource-group-move-resources.md).
* È necessario eliminare tutte le risorse e tutti i gruppi di risorse. L'eliminazione di loro è necessaria prima di poter annullare una sottoscrizione. Ogni gruppo di risorse deve essere eliminato singolarmente. Durante l'eliminazione del gruppo risorse, è necessario confermare l'eliminazione digitando il nome del gruppo di risorse.

Se si annulla un piano di supporto di Azure a pagamento, verrà comunque emessa una fattura per il resto del periodo di validità della sottoscrizione. Per altre informazioni, vedere [Piani di supporto per Azure](https://azure.microsoft.com/support/plans/).

## <a name="cancel-subscription-using-the-azure-portal"></a>Annullare la sottoscrizione usando il portale di Azure

1. Selezionare la sottoscrizione dalla [pagina Sottoscrizioni del portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Selezionare la sottoscrizione da annullare.
3. Selezionare **Panoramica** e quindi selezionare **Annulla sottoscrizione**.

    ![Screenshot che mostra il pulsante Annulla](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
3. Seguire le istruzioni e completare la procedura di annullamento.

## <a name="what-happens-after-i-cancel-my-subscription"></a>Cosa accade quando si annulla la sottoscrizione?

Nel momento in cui viene annullata, la fatturazione viene interrotta immediatamente. Per essere visualizzata sul portale, tuttavia, è possibile che siano necessari fino a 10 minuti.

Dopo questo intervallo, i servizi vengono disabilitati. Le macchine virtuali vengono deallocate, gli indirizzi IP temporanei vengono liberati e la risorsa di archiviazione diventa di sola lettura.

Se si annulla la sottoscrizione nel corso di un periodo di fatturazione, la fattura finale verrà inviata alla normale data della fattura al termine del periodo.

Microsoft attenderà 90 giorni prima di eliminare definitivamente i dati, nel caso in cui fosse necessario accedervi o si cambiasse idea. Non verrà comunque addebitato alcun costo per questo servizio di conservazione dei dati. Per altre informazioni, vedere [Microsoft Trust Center - Come vengono gestiti i dati](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="reactivate-subscription"></a>Riattivare la sottoscrizione

Se la sottoscrizione con pagamento in base al consumo viene annullata erroneamente, è possibile [riattivarla nel Centro account](billing-subscription-become-disable.md).

Se la sottoscrizione non è con pagamento in base al consumo, contattare il supporto tecnico entro 90 giorni dall'annullamento per riattivare la sottoscrizione.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

Se si hanno domande o assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).
