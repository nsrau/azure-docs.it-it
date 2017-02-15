---
title: Annullare la sottoscrizione di Azure | Documentazione Microsoft
description: Descrive come annullare la sottoscrizione di Azure, ad esempio la sottoscrizione di valutazione gratuita
services: 
documentationcenter: 
author: genlin
manager: narmstr
editor: 
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/6/2016
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: c23c92234b38eeb911bd316fffe5127bbad4de91
ms.openlocfilehash: e64e4a3baf48ecd5b16bf5a141455152b7a486f5


---
# <a name="cancel-your-azure-subscription"></a>Annullare la sottoscrizione di Azure
Come amministratore account, è possibile annullare la sottoscrizione di Azure. Dopo aver annullato la sottoscrizione, l'accesso alle risorse e ai servizi di Azure non sarà più possibile.

Prima di annullare la sottoscrizione:

* Eseguire il backup dei dati. Se ad esempio si archiviano i dati in Archiviazione di Azure o SQL, scaricare una copia. Se si ha una macchina virtuale, salvare un'immagine in locale.
* Arrestare i servizi. Passare alla [pagina delle risorse nel portale di gestione](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources) e **arrestare** qualsiasi macchina virtuale, applicazione o altri servizi.
* Prendere in considerazione la migrazione dei dati. Vedere [Spostare le risorse in un nuovo gruppo o sottoscrizione](./azure-resource-manager/resource-group-move-resources.md).

Se si annulla un [piano di supporto di Azure](https://azure.microsoft.com/support/plans/) a pagamento, verrà comunque addebitato un importo mensile per il resto del semestre.

## <a name="cancel-subscription-via-the-azure-portal"></a>Annullare la sottoscrizione nel portale di Azure
1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore account.

   > [!NOTE]
   > Solo l'amministratore account può annullare le sottoscrizioni. Per altre informazioni su come trovare l'amministratore account della sottoscrizione, vedere [Domande frequenti](billing-subscription-transfer.md#faq).
   >
   >

2. Nel menu Hub selezionare **Sottoscrizioni**.

    ![Pulsante Sottoscrizioni](./media/billing-download-azure-invoice-daily-usage-date/submenu.png)

3. Nel pannello delle sottoscrizioni selezionare la sottoscrizione da annullare e fare clic su **Cancel Sub** (Annulla sottoscrizione) per aprire il pannello per l'annullamento.

    ![cancel_ibiza](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
4. Immettere il nome della sottoscrizione, scegliere un motivo per l'annullamento e fare clic sul pulsante **Cancel Sub** (Annulla sottoscrizione) nella parte inferiore del pannello di annullamento.

> [!NOTE]
> Per essere effettivo sul portale, l'annullamento richiederà fino a 10 minuti, ma la fatturazione verrà interrotta immediatamente.
>
>

## <a name="cancel-subscription-via-the-azure-account-center"></a>Annullare la sottoscrizione nel Centro account di Azure
1. Accedere al [Centro account Azure](https://account.windowsazure.com/subscriptions) come amministratore account.
2. In **Fare clic su una sottoscrizione per visualizzare i dettagli e l'utilizzo**selezionare la sottoscrizione che si vuole annullare.

    ![selectsub](./media/billing-how-to-cancel-azure-subscription/Selectsub.png)
3. Sul lato destro della pagina selezionare **Annulla sottoscrizione**.

    ![cancelsub](./media/billing-how-to-cancel-azure-subscription/cancelsub.png)
4. Selezionare **Sì, annulla la sottoscrizione**.

    ![cancelbox](./media/billing-how-to-cancel-azure-subscription/cancelbox.png)
5. Fare clic su  ![checkButton](./media/billing-how-to-cancel-azure-subscription/checkbutton.png) per chiudere la finestra di dialogo e tornare alla pagina di sottoscrizione.

Dopo aver annullato la sottoscrizione, Microsoft attenderà 90 giorni prima di eliminare definitivamente i dati, nel caso in cui fosse necessario accedervi o si cambiasse idea. Per altre informazioni, vedere [Microsoft Trust Center - Come vengono gestiti i dati](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="reactivate-subscription"></a>Riattivare la sottoscrizione
Se la sottoscrizione con pagamento in base al consumo è stata annullata erroneamente, è [riattivarla nel Centro account](billing-subscription-become-disable.md).

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.
Per altre domande, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.



<!--HONumber=Dec16_HO2-->


