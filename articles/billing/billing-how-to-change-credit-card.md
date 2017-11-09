---
title: Modificare la carta di credito per Azure | Microsoft Docs
description: Descrive come modificare la carta di credito usata per pagare una sottoscrizione di Azure
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: 15252ced-1841-4a66-ae79-2e58af1d3370
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2017
ms.author: genli
ms.openlocfilehash: 03764377b3ea0e17d4a192a7e05bb495ec56f331
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="add-update-or-remove-a-credit-or-debit-card-for-azure"></a>Aggiungere, aggiornare o rimuovere una carta di credito o di debito per Azure

Nel Centro account è possibile aggiungere una nuova carta di credito, aggiornarne una esistente o eliminare una carta di credito inutilizzata. Per apportare queste modifiche, è necessario essere un [amministratore account](billing-subscription-transfer.md#whoisaa).

**Per passare al modello di pagamento con fattura**, vedere [Pagare le sottoscrizioni di Azure tramite fattura](billing-how-to-pay-by-invoice.md).
 
<a id="addcard"></a>

## <a name="add-a-new-credit-or-debit-card"></a>Aggiungere una nuova carta di credito o di debito

1. Accedere al [Centro account](https://account.windowsazure.com/Subscriptions) come amministratore account.
1. Selezionare una sottoscrizione.
1. Sul lato destro della pagina selezionare **Manage payment methods**(Gestisci metodi di pagamento).

    ![Screenshot che mostra l'opzione Gestisci i metodi di pagamento selezionata.](./media/billing-how-to-change-credit-card/changesub_new.png)
1. Selezionare "+" per aggiungere una carta.

    ![Screenshot che mostra l'opzione Modifica accanto al metodo di pagamento.](./media/billing-how-to-change-credit-card/editcard_new.png)
1. Immettere i dettagli della carta di credito o di debito.
1. Selezionare **Salva**. 

Se si verifica un errore dopo aver aggiunto la carta di credito, vedere [La carta di credito viene rifiutata al momento dell'iscrizione ad Azure](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="update-existing-credit-or-debit-card"></a>Aggiornare una carta di credito o di debito esistente

Se la carta di credito viene rinnovata e il numero rimane invariato, aggiornare i dati della carta di credito esistente, ad esempio la data di scadenza. Se il numero di carta di credito viene modificato in seguito alla perdita, al furto o alla scadenza della carta, seguire la procedura nella sezione [Aggiungere una carta di credito come metodo di pagamento](#addcard). Non è necessario aggiornare il codice CVV.

1. Accedere al [Centro account di Azure](https://account.windowsazure.com/Subscriptions) come Amministratore account.
1. Selezionare la sottoscrizione collegata alla carta.
1. Selezionare **Gestisci i metodi di pagamento**.
1. Selezionare **Modifica** accanto alla carta che si vuole aggiornare.
1. Aggiornare i dettagli della carta di credito o di debito.
1. Selezionare **Salva**.

## <a name="use-a-different-credit-card-for-the-azure-subscription"></a>Usare una carta di credito diversa per la sottoscrizione di Azure

1. Accedere al [Centro account di Azure](https://account.windowsazure.com/Subscriptions) come Amministratore account.
1. Selezionare la sottoscrizione collegata alla carta.
1. Sul lato destro della pagina selezionare **Manage payment methods**(Gestisci metodi di pagamento).
1. Fare clic su **Usa questa carta** accanto alla carta che si vuole usare. Verranno aggiornate anche tutte le altre sottoscrizioni attualmente associate alla carta. 

## <a name="remove-a-credit-or-debit-card-from-the-account"></a>Rimuovere una carta di credito o di debito dall'account

1. Accedere al [Centro account di Azure](https://account.windowsazure.com/Subscriptions) come amministratore account.
1. Selezionare la sottoscrizione collegata alla carta.
3. Sul lato destro della pagina selezionare **Manage payment methods**(Gestisci metodi di pagamento).
4. Fare clic su **Elimina** per la carta di credito che si desidera eliminare.

Se la carta di credito è associata ad altre sottoscrizioni di Microsoft attive, è impossibile rimuoverla dal proprio account Azure. Rimuovere la carta di credito da tutte le sottoscrizioni Microsoft attive e riprovare.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Se la sottoscrizione è disabilitata, perché non è possibile rimuovere la carta di credito?

Dopo aver disabilitato o annullato una sottoscrizione, Microsoft attende 90 giorni prima di eliminare definitivamente la sottoscrizione. Durante questo periodo, il metodo di pagamento viene conservato, nel caso in cui il titolare della sottoscrizione voglia riattivarla. Dopo questo periodo di tempo, la sottoscrizione viene eliminata completamente.

Se è necessario rimuovere la carta di credito o di debito prima della fine del periodo di conservazione di 90 giorni, [contattare il supporto di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Perché continua a essere visualizzato il messaggio "La sessione è scaduta. Per continuare, accedere di nuovo"?

Se si continua a ricevere questo messaggio anche dopo aver effettuato la disconnessione e un nuovo accesso, riprovare con una sessione anonima del browser.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Come si usa una carta diversa per ogni sottoscrizione?

Se le sottoscrizioni usano già la stessa carta, sfortunatamente non è possibile separarle in modo da usare carte diverse. Tuttavia, quando ci si iscrive per una nuova sottoscrizione, è possibile scegliere di usare un nuovo metodo di pagamento per la sottoscrizione.

### <a name="how-do-i-make-payments"></a>Come si effettuano i pagamenti?

Se il metodo di pagamento è configurato per l'uso di una carta di credito o una carta di debito, il pagamento viene addebitato automaticamente sulla carta al termine di ogni periodo di fatturazione. Non è necessario intervenire manualmente.

Se si usa l'opzione di [pagamento con fattura](billing-how-to-pay-by-invoice.md), inviare il pagamento al destinatario elencato in fondo alla fattura.

### <a name="how-do-i-make-a-one-time-payment"></a>Come si effettua un pagamento unico?

Sfortunatamente, al momento Azure non supporta pagamenti unici per carte di credito o di debito. 

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
