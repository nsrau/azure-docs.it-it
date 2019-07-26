---
title: Modificare la carta di credito per Azure
description: Viene descritto come modificare la carta di credito usata per pagare una sottoscrizione di Azure.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: banders
ms.openlocfilehash: 7719ae83525883a6d3f014dbb99877b7319f2ccd
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383670"
---
# <a name="add-update-or-remove-a-credit-card-for-azure"></a>Aggiungere, aggiornare o rimuovere una carta di credito per Azure

Nella portale di Azure è possibile aggiungere una nuova carta di credito, aggiornare una carta di credito esistente o eliminare una carta di credito che non si utilizza. Per apportare queste modifiche, è necessario essere un [amministratore account](billing-subscription-transfer.md#whoisaa).

Se si dispone di un contratto per i [clienti Microsoft](#check-access-to-a-microsoft-customer-agreement), i metodi di pagamento sono associati ai profili di fatturazione. Informazioni su come [modificare il metodo di pagamento predefinito per un profilo di fatturazione](#change-payment-method-for-a-billing-profile). Solo l'utente che ha effettuato l'iscrizione ad Azure può aggiornare il metodo di pagamento.

**Si vuole passare al pagamento tramite fattura (assegno/trasferimento tramite bonifico)?** vedere [Pagare le sottoscrizioni di Azure tramite fattura](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## <a name="add-a-new-credit-card-to-an-azure-subscription"></a>Aggiungere una nuova carta di credito a una sottoscrizione di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore account.
1. Cercare **Gestione costi e fatturazione**.

    ![Screenshot che mostra la ricerca](./media/billing-how-to-change-credit-card/search.png)

1. Selezionare la sottoscrizione a cui si vuole aggiungere la carta di credito.
1. Selezionare **Metodi di pagamento**.

    ![Screenshot che mostra l'opzione Gestisci i metodi di pagamento selezionata.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Nell'angolo in alto a sinistra selezionare "+" per aggiungere una carta. A destra verrà visualizzato un modulo per la carta di credito.
1. Immettere i dettagli della carta di credito.

    ![Screenshot che mostra l'aggiunta di una nuova scheda.](./media/billing-how-to-change-credit-card/sub-add-new-x.png)

1. Per rendere questa scheda il metodo di pagamento attivo, selezionare la casella accanto a **Rendi questo metodo di pagamento attivo** sopra il modulo. Questa carta diventerà lo strumento di pagamento attivo per tutte le sottoscrizioni che usano la stessa carta della sottoscrizione selezionata.

1. Selezionare **Avanti**.

Se si verifica un errore dopo aver aggiunto la carta di credito, vedere [La carta di credito viene rifiutata al momento dell'iscrizione ad Azure](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="update-existing-credit-card"></a>Aggiorna carta di credito esistente

Se la carta di credito viene rinnovata e il numero rimane invariato, aggiornare i dettagli della carta di credito esistente, ad esempio la data di scadenza. Se il numero di carta di credito viene modificato in seguito alla perdita, al furto o alla scadenza della carta, seguire la procedura nella sezione [Aggiungere una carta di credito come metodo di pagamento](#addcard). Non è necessario aggiornare il codice CVV.

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore account.
1. Cercare **Gestione costi e fatturazione**.

    ![Screenshot che mostra la ricerca](./media/billing-how-to-change-credit-card/search.png)

1. Selezionare **Metodi di pagamento**.

    ![Screenshot che mostra l'opzione Gestisci i metodi di pagamento selezionata.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Fare clic sulla carta di credito che si desidera modificare. A destra verrà visualizzato un modulo per la carta di credito.

    ![Screenshot che mostra la carta di credito selezionata.](./media/billing-how-to-change-credit-card/edit-card-x.png)

1. Aggiornare i dettagli della carta di credito.
1. Selezionare **Salva**.

## <a name="use-a-different-credit-card"></a>Usa una carta di credito diversa

Se più sottoscrizioni hanno lo stesso metodo di pagamento attivo, la modifica del metodo di pagamento attivo su una di queste sottoscrizioni aggiorna anche il metodo di pagamento attivo nelle altre sottoscrizioni.

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore account.
1. Cercare **Gestione costi e fatturazione**.

    ![Screenshot che mostra la ricerca](./media/billing-how-to-change-credit-card/search.png)

1. Selezionare la sottoscrizione a cui si vuole aggiungere la carta di credito.
1. Selezionare **Metodi di pagamento**.

    ![Screenshot che mostra l'opzione Gestisci i metodi di pagamento selezionata.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Selezionare la casella accanto alla scheda da impostare come metodo di pagamento attivo.
1. Fare clic su **Imposta attivo**.
    ![Screenshot che mostra la carta di credito selezionata e imposta attiva.](./media/billing-how-to-change-credit-card/sub-change-active-x.png)

## <a name="remove-a-credit-card-from-the-account"></a>Rimuovere una carta di credito dall'account

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore account.
1. Selezionare **Gestione costi e fatturazione** sul lato sinistro della pagina.

    ![Screenshot che mostra la ricerca](./media/billing-how-to-change-credit-card/search.png)

1. In **fatturazione**selezionare **metodi di pagamento**.

    ![Screenshot che mostra l'opzione Gestisci i metodi di pagamento selezionata.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Selezionare la casella accanto alla scheda che si desidera rimuovere.
1. Fare clic su **Elimina**.

Se la carta di credito è il metodo di pagamento attivo per qualsiasi sottoscrizione Microsoft, non è possibile rimuoverla dall'account Azure. Modificare il metodo di pagamento attivo per tutte le sottoscrizioni collegate a questa carta di credito e riprovare
<!-- # Add, update, or remove a credit card for Azure

In the Account Center, you can add a new credit card, update an existing credit card, or delete a credit card that you don't use. You must be an [Account Administrator](billing-subscription-transfer.md#whoisaa) to make these changes.

**Want to switch to pay by invoice?** See [Pay for Azure subscriptions by invoice](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## Add a new credit card

1. Sign in to the [Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select a subscription.
1. On the right side of the page, select **Manage payment methods**.

    ![Screenshot that shows Manage payment methods option selected.](./media/billing-how-to-change-credit-card/changesub_new.png)
1. Select “+” to add a card.

    ![Screenshot that shows the edit option next to the payment method.](./media/billing-how-to-change-credit-card/editcard_new.png)
1. Enter credit card details.
1. Select **Save**.

If you get an error after you add the credit card, see [Credit card declined at Azure sign-up](billing-credit-card-fails-during-azure-sign-up.md).

## Update existing credit card

If your credit card gets renewed and the number remains the same, update the existing credit card details like the expiration date. If your credit card number changes because the card is lost, stolen, or expired, follow the steps in the [Add a credit card as a payment method](#addcard) section. You don't need to update the CVV.

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. Select **Manage payment methods**.
1. Select **Edit** next to the card you want to update.
1. Update the credit card details.
1. Select **Save**.

## Use a different credit card for the Azure subscription

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. On the right side of the page, select **Manage payment methods**.
1. Click **Use Instead** next to the card that you want to use. This also updates any other subscriptions currently associated with this card.

## Remove a credit card from the account

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
3. On the right side of the page, select **Manage payment methods**.
4. Click **Delete** for the credit card that you want to delete.

If your credit card is associated with other active Microsoft subscriptions, you can't remove it from your Azure account. Remove the credit card from all active subscriptions that you have with Microsoft and try again. -->

## <a name="change-payment-method-for-a-billing-profile"></a>Modificare il metodo di pagamento per un profilo di fatturazione

Per modificare il metodo di pagamento per un profilo di fatturazione, è necessario essere la persona che ha effettuato l'iscrizione ad Azure.

Se si vuole cambiare il metodo di pagamento predefinito per il trasferimento tramite check/Wire, vedere la pagina relativa alla modalità di [commutazione di un profilo di fatturazione per il trasferimento in](billing-how-to-pay-by-invoice.md)transito.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Eseguire ricerche in **Gestione dei costi e fatturazione**.
1. Nel menu a sinistra fare clic su profili di **fatturazione**.

    ![screenshot che mostra il profilo di fatturazione nel menu](./media/billing-how-to-change-credit-card/billing-profile.png)

1. Selezionare un profilo di fatturazione.
1. Nel menu a sinistra selezionare **metodi di pagamento**.

   ![Screenshot che mostra i metodi di pagamento nel menu](./media/billing-how-to-change-credit-card/billing-profile-payment-methods.png)

1. Sopra il metodo di pagamento predefinito, fare clic su **Cambia**.

    ![Screenshot che mostra il pulsante cambia](./media/billing-how-to-change-credit-card/customer-led-switch-credit-card.png)

1. Selezionare una scheda esistente o aggiungerne una nuova.

## <a name="frequently-asked-questions"></a>Domande frequenti
Le sezioni seguenti rispondono alle domande più frequenti sulla modifica delle informazioni sulla carta di credito.

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Se la sottoscrizione è disabilitata, perché non è possibile rimuovere la carta di credito?

Dopo aver disabilitato o annullato una sottoscrizione, Microsoft attende 90 giorni prima di eliminare definitivamente la sottoscrizione. Durante questo periodo, il metodo di pagamento viene conservato, nel caso in cui il titolare della sottoscrizione voglia riattivarla. Successivamente, la sottoscrizione viene eliminata definitivamente.

Se è necessario rimuovere la carta di credito prima della scadenza del periodo di conservazione di 90 giorni, riattivare [la sottoscrizione](billing-subscription-become-disable.md). Se non è possibile riattivarla, [contattare il supporto tecnico di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Perché continua a essere visualizzato il messaggio "La sessione è scaduta. Per continuare, accedere di nuovo"?

Se si continua a ricevere questo messaggio anche dopo aver effettuato la disconnessione e un nuovo accesso, riprovare con una sessione anonima del browser.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Come si usa una carta diversa per ogni sottoscrizione?

Se le sottoscrizioni usano già la stessa carta, sfortunatamente non è possibile separarle in modo da usare carte diverse. Tuttavia, quando ci si iscrive per una nuova sottoscrizione, è possibile scegliere di usare un nuovo metodo di pagamento per la sottoscrizione.

### <a name="how-do-i-make-payments"></a>Come si effettuano i pagamenti?

Se si configura una carta di credito come metodo di pagamento, la carta viene addebitata automaticamente dopo ogni periodo di fatturazione. Non è necessario intervenire manualmente.

Se si usa l'opzione di [pagamento con fattura](billing-how-to-pay-by-invoice.md), inviare il pagamento al destinatario elencato in fondo alla fattura.

### <a name="how-do-i-change-the-tax-id"></a>Come è possibile modificare l'ID imposta?

Per aggiungere o aggiornare l'ID imposta, aggiornare il profilo nel [centro account di Azure](https://account.azure.com/Profile), quindi selezionare **record imposta**. L'ID imposta viene usato per calcolare l'esenzione fiscale ed riportato sulla fattura.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Controllare l'accesso a un contratto per i clienti Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

Per eventuali domande o per richiedere assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi
- Scopri le [prenotazioni di Azure](billing-save-compute-costs-reservations.md) per scoprire se puoi risparmiare denaro.
