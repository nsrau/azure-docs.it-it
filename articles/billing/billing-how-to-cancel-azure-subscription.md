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
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "68666440"
---
# <a name="cancel-your-azure-subscription"></a>Annullare la sottoscrizione di Azure

Se non è più necessaria, è possibile annullare la sottoscrizione di Azure nel portale di Azure. 

Prima di annullare la sottoscrizione:
* Eseguire il backup dei dati. Se ad esempio si archiviano i dati in Archiviazione di Azure o SQL, scaricare una copia. Se si ha una macchina virtuale, salvare un'immagine in locale.
* Arrestare i servizi. Passare alla [pagina delle risorse nel portale di gestione](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources) e **arrestare** qualsiasi macchina virtuale, applicazione o altri servizi.
* Prendere in considerazione la migrazione dei dati. Vedere [Spostare le risorse in un nuovo gruppo o sottoscrizione](../azure-resource-manager/resource-group-move-resources.md).
* Eliminare tutte le risorse e tutti i gruppi di risorse. Questa eliminazione è necessaria per poter annullare una sottoscrizione. È necessario eliminare ogni gruppo di risorse singolarmente. Durante l'eliminazione, è necessario confermare l'operazione digitando il nome del gruppo di risorse.
* Se sono presenti ruoli personalizzati che fanno riferimento a questa sottoscrizione in `AssignableScopes`, è necessario aggiornarli per rimuovere la sottoscrizione. Se si prova ad aggiornare un ruolo personalizzato dopo aver annullato una sottoscrizione, è possibile che venga generato un errore. Per altre informazioni, vedere [Risolvere i problemi dei ruoli personalizzati](../role-based-access-control/troubleshooting.md#problems-with-custom-roles) e [Ruoli personalizzati per le risorse di Azure](../role-based-access-control/custom-roles.md).

Se si annulla un piano di supporto di Azure a pagamento, verrà emessa una fattura per il resto del periodo di validità della sottoscrizione. Per altre informazioni, vedere [Piani di supporto per Azure](https://azure.microsoft.com/support/plans/).

## <a name="cancel-subscription-in-the-azure-portal"></a>Annullare la sottoscrizione nel portale di Azure

1. Selezionare la sottoscrizione nella [pagina Sottoscrizioni del portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Selezionare la sottoscrizione da annullare.
3. Selezionare **Panoramica** e quindi selezionare **Annulla sottoscrizione**.
    ![Screenshot che mostra il pulsante Annulla](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
3. Seguire le istruzioni e completare la procedura di annullamento.


## <a name="who-can-cancel-a-subscription"></a>Chi può annullare una sottoscrizione?

La tabella seguente indica l'autorizzazione necessaria per annullare una sottoscrizione.

|Tipo di sottoscrizione     |Chi può annullare  |
|---------|---------|
|Sottoscrizioni create quando ci si iscrive ad Azure tramite il sito Web di Azure, ad esempio, quando ci si iscrive per ricevere un [account Azure gratuito](https://azure.microsoft.com/offers/ms-azr-0044p/), un [account con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) o un account di [sottoscrittore di Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/). |  Amministratore account, proprietari e collaboratori per la sottoscrizione  |
|[Microsoft Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/) e [Sviluppo/test Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  Proprietario dell'account, proprietari e collaboratori per la sottoscrizione       |
|[Piano di Azure](https://azure.microsoft.com/offers/ms-azr-0017g/) e [Piano di Azure per DevTest](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  Proprietario e collaboratori per la sottoscrizione      |


## <a name="what-happens-after-i-cancel-my-subscription"></a>Cosa accade quando si annulla la sottoscrizione?

Dopo l'annullamento, la fatturazione viene interrotta immediatamente. Per essere visualizzata sul portale, tuttavia, è possibile che siano necessari fino a 10 minuti. Se si annulla la sottoscrizione nel corso di un periodo di fatturazione, la fattura finale verrà inviata alla normale data della fattura al termine del periodo.

Dopo l'annullamento, i servizi vengono disabilitati. Le macchine virtuali vengono deallocate, gli indirizzi IP temporanei vengono liberati e la risorsa di archiviazione diventa di sola lettura.

Microsoft attenderà 90 giorni prima di eliminare definitivamente i dati, nel caso in cui fosse necessario accedervi o si cambiasse idea. Non verrà comunque addebitato alcun costo per questo servizio di conservazione dei dati. Per altre informazioni, vedere [Microsoft Trust Center - Come vengono gestiti i dati](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="reactivate-subscription"></a>Riattivare la sottoscrizione

Se la sottoscrizione con pagamento in base al consumo viene annullata erroneamente, è possibile [riattivarla nel Centro account](billing-subscription-become-disable.md).

Se la sottoscrizione non è con pagamento in base al consumo, contattare il supporto tecnico entro 90 giorni dall'annullamento per riattivarla.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).
