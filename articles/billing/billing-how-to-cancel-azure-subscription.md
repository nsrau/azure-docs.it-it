---
title: Annullare la sottoscrizione di Azure | Microsoft Docs
description: Descrive come annullare la sottoscrizione di Azure, ad esempio la sottoscrizione di valutazione gratuita
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: c415fada30aa0b0bd9b9d1e416bc37ef30653f68
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017


---
# <a name="cancel-your-subscription-for-azure"></a>Annullare la sottoscrizione di Azure

Come [amministratore account](billing-subscription-transfer.md#whoisaa), è possibile annullare la sottoscrizione di Azure. Dopo aver annullato la sottoscrizione, l'accesso alle risorse e ai servizi di Azure non sarà più possibile.

Prima di annullare la sottoscrizione:

* Eseguire il backup dei dati. Se ad esempio si archiviano i dati in Archiviazione di Azure o SQL, scaricare una copia. Se si ha una macchina virtuale, salvare un'immagine in locale.
* Arrestare i servizi. Passare alla [pagina delle risorse nel portale di gestione](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources) e **arrestare** qualsiasi macchina virtuale, applicazione o altri servizi.
* Prendere in considerazione la migrazione dei dati. Vedere [Move resources to new resource group or subscription](../azure-resource-manager/resource-group-move-resources.md) (Spostare le risorse in un nuovo gruppo o sottoscrizione).

Se si annulla un [piano di supporto di Azure](https://azure.microsoft.com/support/plans/) a pagamento, verrà comunque emessa una fattura mensile per il resto del semestre.

## <a name="cancel-subscription-using-the-azure-portal"></a>Annullare la sottoscrizione usando il portale di Azure

1. Selezionare la sottoscrizione nella [pagina delle sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Selezionare la sottoscrizione da annullare e fare clic su **Annulla sottoscrizione**.

    ![Screenshot che mostra il pulsante Annulla](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)

1. Seguire le istruzioni e completare la procedura di annullamento.

## <a name="cancel-subscription-using-the-azure-account-center"></a>Annullare la sottoscrizione usando il Centro account di Azure

1. Accedere al [Centro account di Azure](https://account.windowsazure.com/subscriptions) come Amministratore account.

1. In **Fare clic su una sottoscrizione per visualizzare i dettagli e l'utilizzo**selezionare la sottoscrizione che si vuole annullare.

    ![Screenshot che mostra un esempio di sottoscrizione selezionata](./media/billing-how-to-cancel-azure-subscription/Selectsub.png)

1. Sul lato destro della pagina selezionare **Annulla sottoscrizione**.

    ![Screenshot che mostra il pulsante Annulla sottoscrizione](./media/billing-how-to-cancel-azure-subscription/cancelsub.png)

1. Selezionare **Sì, annulla la sottoscrizione**.

    ![Screenshot che mostra la finestra di dialogo Annulla](./media/billing-how-to-cancel-azure-subscription/cancelbox.png)

1. Fare clic su  ![pulsante con segno di spunta](./media/billing-how-to-cancel-azure-subscription/checkbutton.png) per chiudere la finestra di dialogo e tornare alla pagina di sottoscrizione.

## <a name="what-happens-after-i-cancel-my-subscription"></a>Cosa accade quando si annulla la sottoscrizione?

Nel momento in cui viene annullata, la fatturazione viene interrotta immediatamente. Per essere visualizzata sul portale, tuttavia, è possibile che siano necessari fino a 10 minuti.

Dopo questo intervallo, i servizi vengono disabilitati. Le macchine virtuali vengono quindi deallocate e gli indirizzi IP temporanei liberati e la risorsa di archiviazione diventa di sola lettura.

A meno che non si stia usando una versione di valutazione gratuita o non si abbiano crediti disponibili, verrà addebitato l'importo corrispondente all'uso della sottoscrizione intercorso tra l'ultimo ciclo di fatturazione e la data di annullamento. Al termine del ciclo di fatturazione è possibile richiedere l'ultima fattura.

Dopo aver annullato la sottoscrizione, Microsoft attenderà 90 giorni prima di eliminare definitivamente i dati, nel caso in cui fosse necessario accedervi o si cambiasse idea. Non verrà comunque addebitato alcun costo per questo servizio di conservazione dei dati. Per altre informazioni, vedere [Microsoft Trust Center - Come vengono gestiti i dati](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="reactivate-subscription"></a>Riattivare la sottoscrizione

Se la sottoscrizione con pagamento in base al consumo viene annullata erroneamente, è possibile [riattivarla nel Centro account](billing-subscription-become-disable.md).

Se la sottoscrizione non è con pagamento in base al consumo, contattare il supporto tecnico entro 90 giorni dall'annullamento per riattivare la sottoscrizione.

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.

Per eventuali domande, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

