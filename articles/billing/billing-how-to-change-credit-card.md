---
title: Modificare la carta di credito usata per Azure | Documentazione Microsoft
description: Descrive come modificare la carta di credito usata per pagare una sottoscrizione di Azure
services: 
documentationcenter: 
author: genlin
manager: narmstr
editor: 
tags: billing
ms.assetid: 15252ced-1841-4a66-ae79-2e58af1d3370
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 09c18ca6a967c2930ddd2b16d37f0bc606712ea1
ms.openlocfilehash: 2a4751351561728024417d50aeefba0e5b14a438


---
# <a name="change-the-credit-card-used-to-pay-for-an-azure-subscription"></a>Modificare la carta di credito usata per pagare una sottoscrizione di Azure

Questo articolo descrive come aggiungere, modificare o rimuovere una carta di credito associata all'account Azure e come eseguire pagamenti.

<a id="addcard"></a>
## <a name="add-a-credit-card-as-a-payment-method"></a>Aggiungere una carta di credito come metodo di pagamento

Seguire questa procedura per aggiungere una carta di credito all'account.

1. Accedere al [Centro account di Azure](https://account.windowsazure.com/Subscriptions) come amministratore account.
2. In **Click a subscription to view details and usage** (Fare clic su una sottoscrizione per visualizzare i dettagli e l'utilizzo) selezionare la sottoscrizione per cui si desidera aggiungere un metodo di pagamento.
3. Sul lato destro della pagina selezionare **Manage payment methods**(Gestisci metodi di pagamento).
4. Nella pagina **Manage payment methods** (Gestisci metodi di pagamento) selezionare "+" per aggiungere una carta di credito.
5. Scegliere un tipo di carta di credito e quindi fornire tutte le informazioni necessarie.
6. Fare clic su **Avanti** per tornare alla pagina **Manage payment methods** (Gestisci metodi di pagamento). Se la carta di credito specificata è valida, verrà aggiunta all'elenco dei metodi di pagamento disponibili.

> [!NOTE]
>Se si riceve un messaggio di errore dopo aver inviato le informazioni sulla carta di credito, vedere l'articolo [La carta di debito o di credito è stata rifiutata o non è stata accettata](billing-credit-card-fails-during-azure-sign-up.md).
>
>

## <a name="edit-payment-information-for-an-existing-credit-card"></a>Modificare le informazioni di pagamento per una carta di credito esistente

Seguire questa procedura per verificare il metodo di pagamento e aggiornare i dettagli di una carta di credito esistente.

1. Accedere al [Centro account di Azure](https://account.windowsazure.com/Subscriptions) come amministratore account.

   > [!NOTE]
   > Le autorizzazioni per accedere al Centro account sono concesse solo all'amministratore account. Per altre informazioni sull'amministratore account, vedere [Come aggiungere o modificare i ruoli di amministratore di Azure](billing-add-change-azure-subscription-administrator.md).
   >
   >
2. In **Fare clic su una sottoscrizione per visualizzare i dettagli e l'utilizzo** selezionare la sottoscrizione collegata alla carta di credito.</br> ![selectsub](./media/billing-how-to-change-credit-card/selectsub.png)
3. Sul lato destro della pagina selezionare **Manage payment methods**(Gestisci metodi di pagamento).</br> ![changesub](./media/billing-how-to-change-credit-card/changesub_new.png)

  Verrà visualizzato il metodo di pagamento corrente elencato. Selezionarlo per verificare le informazioni necessarie.

  **Come aggiornare i dettagli di una carta di credito esistente**

  Se la carta di credito viene rinnovata e il numero rimane invariato, è possibile semplicemente aggiornare i dati della carta di credito esistente, ad esempio la data di scadenza. Se il numero di carta di credito viene modificato in seguito alla perdita, al furto o alla scadenza della carta, può essere tuttavia necessario [aggiungere una carta di credito all'account](#addcard). Questa operazione è necessaria perché il codice di sicurezza CVV cambia insieme al numero della carta di credito.

4. Nella pagina **Manage payment methods** (Gestisci metodi di pagamento) scegliere **Modifica** accanto al numero della carta di credito.</br> ![changesub](./media/billing-how-to-change-credit-card/editcard_new.png)
5. Nella pagina **Modifica** , controllare di aver selezionato il tipo di carta e il numero di carta di credito corretti.
6. Apportare le modifiche necessarie ai dettagli relativi alla carta e quindi selezionare **Avanti** nella pagina **Choose payment method** (Scegli il metodo di pagamento).

## <a name="change-the-credit-card-that-you-use-to-pay-an-azure-bill"></a>Modificare la carta di credito usata per pagare una fattura di Azure

1. Accedere al [Centro account di Azure](https://account.windowsazure.com/Subscriptions) come amministratore account.
2. In **Fare clic su una sottoscrizione per visualizzare i dettagli e l'utilizzo**selezionare la sottoscrizione per la quale si vuole aggiornare il metodo di pagamento.
3. Sul lato destro della pagina selezionare **Manage payment methods**(Gestisci metodi di pagamento).
4. Nella pagina **Manage payment methods** (Gestisci metodi di pagamento) fare clic su **Use Instead** (Usa questa carta) per selezionare la carta di credito da usare.

## <a name="remove-a-credit-card-from-the-account"></a>Rimuovere una carta di credito dall'account
1. Accedere al [Centro account di Azure](https://account.windowsazure.com/Subscriptions) come amministratore account.
2. In **Fare clic su una sottoscrizione per visualizzare i dettagli e l'utilizzo**selezionare la sottoscrizione per la quale si vuole aggiornare il metodo di pagamento.
3. Sul lato destro della pagina selezionare **Manage payment methods**(Gestisci metodi di pagamento).
4. Nella pagina **Manage payment methods** (Gestisci metodi di pagamento) fare clic su **Elimina** per selezionare la carta di credito da eliminare.

   > [!NOTE]
   > Se la carta di credito è associata ad altre sottoscrizioni di Microsoft attive, non sarà possibile eliminarla. Se si riceve un errore che specifica che la carta è in uso, assicurarsi dissociarla da tutte le sottoscrizioni attive con Microsoft.
   >
   >

##  <a name="how-to-make-payments"></a>Come eseguire pagamenti

Se il metodo di pagamento è impostato su carta di credito o carta di debito, il pagamento viene eseguito automaticamente.
Se si usa un [metodo di pagamento con fattura](https://azure.microsoft.com/pricing/invoicing/), inviare il pagamento al destinatario elencato nella parte inferiore della fattura.

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.



<!--HONumber=Feb17_HO2-->


