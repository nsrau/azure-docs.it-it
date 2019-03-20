---
title: Modificare la carta di credito per Azure | Microsoft Docs
description: Descrive come modificare la carta di credito usata per pagare una sottoscrizione di Azure
services: ''
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.assetid: 15252ced-1841-4a66-ae79-2e58af1d3370
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 3/13/2019
ms.author: banders
ms.openlocfilehash: b910cb3061b1451ea80b9843e2aa4047a784548f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57876868"
---
# <a name="add-update-or-remove-a-credit-or-debit-card-for-azure"></a>Aggiungere, aggiornare o rimuovere una carta di credito o di debito per Azure

Nel portale di Azure, è possibile aggiungere una nuova carta di credito, aggiornare una carta di credito esistente o eliminare una carta di credito che non vengono usati. Per apportare queste modifiche, è necessario essere un [amministratore account](billing-subscription-transfer.md#whoisaa).

<!-- If your Angola, Belize, Algeria, Vietnam, or Virgin Islands. -->

**Per passare al modello di pagamento con fattura**, vedere [Pagare le sottoscrizioni di Azure tramite fattura](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## <a name="add-a-new-credit-or-debit-card-to-an-azure-subscription"></a>Aggiungere una nuova carta di credito o di debito per una sottoscrizione di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore account.
1. Eseguire ricerche in **Gestione dei costi e fatturazione**.

    ![Screenshot che mostra Cerca](./media/billing-how-to-change-credit-card/search.png)

1. Selezionare la sottoscrizione che si desidera aggiungere la carta di credito o di debito per.
1. Selezionare **metodi di pagamento**.

    ![Screenshot che mostra l'opzione Gestisci i metodi di pagamento selezionata.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Nell'angolo superiore sinistro, seleziona "+" per aggiungere una carta. Sulla destra verrà visualizzato un form di carta di credito.
1. Immettere i dettagli della carta di credito o di debito.

    ![Screenshot che mostra l'aggiunta di una nuova scheda.](./media/billing-how-to-change-credit-card/sub-add-new-x.png)

1. Rendere questo metodo di pagamento attivo di schede, selezionare la casella accanto a **imposta il metodo di pagamento attivo** sopra il modulo. Questa scheda diventerà lo strumento di pagamento attivo per tutte le sottoscrizioni che usano la stessa scheda come la sottoscrizione selezionata.

1. Selezionare **Avanti**.

Se si verifica un errore dopo aver aggiunto la carta di credito, vedere [La carta di credito viene rifiutata al momento dell'iscrizione ad Azure](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="update-existing-credit-or-debit-card"></a>Aggiornare una carta di credito o di debito esistente

Se la carta di credito viene rinnovata e il numero rimane invariato, aggiornare i dettagli della carta di credito esistente, ad esempio la data di scadenza. Se il numero di carta di credito viene modificato in seguito alla perdita, al furto o alla scadenza della carta, seguire la procedura nella sezione [Aggiungere una carta di credito come metodo di pagamento](#addcard). Non è necessario aggiornare il codice CVV.

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore account.
1. Eseguire ricerche in **Gestione dei costi e fatturazione**.

    ![Screenshot che mostra Cerca](./media/billing-how-to-change-credit-card/search.png)

1. Selezionare **metodi di pagamento**.

    ![Screenshot che mostra l'opzione Gestisci i metodi di pagamento selezionata.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Fare clic sulla carta di credito o debito che si desidera modificare. Sulla destra verrà visualizzato un form di carta di credito.

    ![Screenshot che mostra una carta di credito o debito selezionato.](./media/billing-how-to-change-credit-card/edit-card-x.png)

1. Aggiornare i dettagli della carta di credito o di debito.
1. Selezionare **Salva**.

## <a name="use-a-different-credit-card-for-the-azure-subscription"></a>Usare una carta di credito diversa per la sottoscrizione di Azure

Se più di una delle sottoscrizioni hanno lo stesso metodo di pagamento attivo, quindi modificare il metodo di pagamento attivo su ognuna di queste sottoscrizioni aggiorna anche il metodo di pagamento attivo per gli altri.

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore account.
1. Eseguire ricerche in **Gestione dei costi e fatturazione**.

    ![Screenshot che mostra Cerca](./media/billing-how-to-change-credit-card/search.png)

1. Selezionare la sottoscrizione che si desidera aggiungere la carta di credito o di debito per.
1. Selezionare **metodi di pagamento**.

    ![Screenshot che mostra l'opzione Gestisci i metodi di pagamento selezionata.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Selezionare la casella accanto alla carta che si desidera impostare il metodo di pagamento attivo.
1. Fare clic su **impostata attiva**.
    ![Screenshot che mostra una carta di credito o debito selezionato e impostare active.](./media/billing-how-to-change-credit-card/sub-change-active-x.png)

## <a name="remove-a-credit-or-debit-card-from-the-account"></a>Rimuovere una carta di credito o di debito dall'account

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore account.
1. Selezionare **gestione costi + fatturazione** sul lato sinistro della pagina.

    ![Screenshot che mostra Cerca](./media/billing-how-to-change-credit-card/search.png)

1. Sotto **fatturazione**, selezionare **metodi di pagamento**.

    ![Screenshot che mostra l'opzione Gestisci i metodi di pagamento selezionata.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Selezionare la casella accanto alla carta che si desidera rimuovere.
1. Fare clic su **Elimina**.

Se la carta di credito è il metodo di pagamento attivo per tutte le sottoscrizioni di Microsoft, sarà possibile rimuoverlo dal proprio account Azure. Modificare il metodo di pagamento attivo per tutti gli abbonamenti collegati a questa carta di credito o debito e riprovare
<!-- # Add, update, or remove a credit or debit card for Azure

In the Account Center, you can add a new credit card, update an existing credit card, or delete a credit card that you don't use. You must be an [Account Administrator](billing-subscription-transfer.md#whoisaa) to make these changes.

**Want to switch to pay by invoice?** See [Pay for Azure subscriptions by invoice](billing-how-to-pay-by-invoice.md).
 
<a id="addcard"></a>

## Add a new credit or debit card

1. Sign in to the [Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select a subscription.
1. On the right side of the page, select **Manage payment methods**.

    ![Screenshot that shows Manage payment methods option selected.](./media/billing-how-to-change-credit-card/changesub_new.png)
1. Select “+” to add a card.

    ![Screenshot that shows the edit option next to the payment method.](./media/billing-how-to-change-credit-card/editcard_new.png)
1. Enter credit or debit card details.
1. Select **Save**. 

If you get an error after you add the credit card, see [Credit card declined at Azure sign-up](billing-credit-card-fails-during-azure-sign-up.md).

## Update existing credit or debit card

If your credit card gets renewed and the number remains the same, update the existing credit card details like the expiration date. If your credit card number changes because the card is lost, stolen, or expired, follow the steps in the [Add a credit card as a payment method](#addcard) section. You don't need to update the CVV.

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. Select **Manage payment methods**.
1. Select **Edit** next to the card you want to update.
1. Update the credit or debit card details.
1. Select **Save**.

## Use a different credit card for the Azure subscription

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. On the right side of the page, select **Manage payment methods**.
1. Click **Use Instead** next to the card that you want to use. This also updates any other subscriptions currently associated with this card. 

## Remove a credit or debit card from the account

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
3. On the right side of the page, select **Manage payment methods**.
4. Click **Delete** for the credit card that you want to delete.

If your credit card is associated with other active Microsoft subscriptions, you can't remove it from your Azure account. Remove the credit card from all active subscriptions that you have with Microsoft and try again. -->
## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Se la sottoscrizione è disabilitata, perché non è possibile rimuovere la carta di credito?

Dopo aver disabilitato o annullato una sottoscrizione, Microsoft attende 90 giorni prima di eliminare definitivamente la sottoscrizione. Durante questo periodo, il metodo di pagamento viene conservato, nel caso in cui il titolare della sottoscrizione voglia riattivarla. Successivamente, la sottoscrizione viene eliminata definitivamente.

Se è necessario rimuovere la carta di credito o di debito prima della fine del periodo di conservazione di 90 giorni, [riattivare la sottoscrizione](billing-subscription-become-disable.md). Se non è possibile riattivarla, [contattare il supporto tecnico di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Perché continua a essere visualizzato il messaggio "La sessione è scaduta. Per continuare, accedere di nuovo"?

Se si continua a ricevere questo messaggio anche dopo aver effettuato la disconnessione e un nuovo accesso, riprovare con una sessione anonima del browser.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Come si usa una carta diversa per ogni sottoscrizione?

Se le sottoscrizioni usano già la stessa carta, sfortunatamente non è possibile separarle in modo da usare carte diverse. Tuttavia, quando ci si iscrive per una nuova sottoscrizione, è possibile scegliere di usare un nuovo metodo di pagamento per la sottoscrizione.

### <a name="how-do-i-make-payments"></a>Come si effettuano i pagamenti?

Se il metodo di pagamento è configurato per l'uso di una carta di credito o una carta di debito, il pagamento viene addebitato automaticamente sulla carta al termine di ogni periodo di fatturazione. Non è necessario intervenire manualmente.

Se si usa l'opzione di [pagamento con fattura](billing-how-to-pay-by-invoice.md), inviare il pagamento al destinatario elencato in fondo alla fattura.

### <a name="how-do-i-make-a-one-time-payment"></a>Come si effettua un pagamento unico?

Sfortunatamente, al momento Azure non supporta pagamenti unici per carte di credito o di debito. 

### <a name="how-do-i-change-the-tax-id"></a>Come è possibile modificare l'ID imposta?

Per aggiungere o aggiornare l'ID imposta, passare a [ **Profilo** nel Centro account di Azure](https://account.azure.com/Profile) e quindi selezionare **Registrazione fiscale**. L'ID imposta viene usato per calcolare l'esenzione fiscale ed riportato sulla fattura.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

Se si hanno domande o assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).
