---
title: Pagare le sottoscrizioni di Azure tramite fattura
description: Viene descritto come pagare le sottoscrizioni di Azure tramite fattura.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 90c7014e3c22ac7186854d5c01b911aa630d20dd
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774671"
---
# <a name="pay-for-your-azure-subscription-by-invoice"></a>Pagare la sottoscrizione di Azure tramite fattura

Se si passa a pagamento tramite fattura, il pagamento della fattura entro 30 giorni dalla data di fatturazione viene addebitato in base al trasferimento tramite assegno/bonifico. Per diventare idoneo a pagare per la sottoscrizione di Azure tramite fattura, inviare una richiesta al supporto tecnico di Azure. Una volta approvata la richiesta, è possibile passare alla fatturazione con pagamento (check/Wire Transfer) nell' [portale di Azure](https://portal.azure.com).

> [!IMPORTANT]
> * Il pagamento tramite fatturazione (check/Wire Transfer) è disponibile solo per gli account aziendali.
> * Prima di passare al pagamento con fattura, è necessario pagare tutti gli addebiti in attesa.

## <a name="request-to-pay-by-invoice"></a>Richiesta di pagamento tramite fattura

1. Accedere al [portale di Azure](https://portal.azure.com/). Selezionare **Guida e supporto** > **Nuova richiesta di supporto**.

    ![Collegamento Guida e supporto tecnico](./media/billing-how-to-pay-by-invoice/help-and-support.png)

2. Selezionare **fatturazione** come **tipo di problema**. Il *tipo di problema* è la categoria della richiesta di supporto. Selezionare la sottoscrizione per cui si desidera pagare tramite fattura, selezionare un piano di supporto e quindi fare clic su **Avanti**.

3. Selezionare **pagamento** come **tipo di problema**. Il *tipo di problema* è la sottocategoria della richiesta di supporto.

4. Selezionare l' **opzione per il pagamento tramite fattura** come sottotipo di **problema**

5. Immettere le informazioni seguenti nella casella **Dettagli** e quindi selezionare **Avanti**.

         New or existing customer:
         If existing, current payment method:
         Order ID (requesting for invoice option):
         Account Admins Live ID (or Org ID) (should be company domain):
         Commerce Account ID:
         Company Name (as registered under VAT or Government Website):
         Company Address (as registered under VAT or Government Website):
         Company Website:
         Country:
         TAX ID/ VAT ID:
         Company Established on (Year):
         Any prior business with Microsoft:
         Contact Name:
         Contact Phone:
         Contact Email:
         Justification on why you prefer Invoice option over credit card:

        For cores increase, provide the following additional information:

         (Old quota) Existing Cores:
         (New quota) Requested cores:
         Specific region & series of Subscription:

    - Il **nome** e l' **Indirizzo** della società devono corrispondere alle informazioni fornite per l'account Azure. Per visualizzare o aggiornare le informazioni, vedere [modificare le informazioni sul profilo dell'account Azure](billing-how-to-change-azure-account-profile.md).
    - È necessario aggiungere le informazioni di contatto per la fatturazione nel portale di Azure prima di poter approvare il limite di credito. I dettagli del contatto devono essere correlati alla contabilità fornitori o al reparto finanziario. Per aggiornare le informazioni di contatto per la fatturazione, passare a [centro account di Azure](https://account.azure.com/Profile).

6. Verificare le informazioni di contatto e il metodo di contatto preferito e quindi fare clic su **Crea**.

Se è necessario eseguire una verifica del credito a causa della quantità di credito necessaria, verrà inviata un'applicazione per la verifica del credito.

## <a name="switch-to-invoice-pay-checkwire-transfer"></a>Passa alla fatturazione a pagamento (check/Wire Transfer)

Una volta approvate le spese per fattura, è possibile passare alla fatturazione con pagamento (check/Wire Transfer) nell'portale di Azure.

Se si dispone di un account di programma di Microsoft Online Services, è possibile passare alla sottoscrizione di Azure per il trasferimento in transito. Se si dispone di un contratto per i clienti Microsoft, è possibile passare al profilo di fatturazione per il trasferimento in transito. [Informazioni su come controllare il tipo di account](#check-access-to-a-microsoft-customer-agreement).

### <a name="switch-azure-subscription-to-checkwire-transfer"></a>Passa alla sottoscrizione di Azure per il trasferimento di controllo/cablaggio

Seguire questa procedura per passare alla sottoscrizione di Azure per la fatturazione a pagamento (check/Wire Transfer). **Quando si passa alla fatturazione a pagamento (check/Wire Transfer), non è possibile tornare alla carta di credito**.

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore account.
1. Cercare **Gestione costi e fatturazione**.

    ![Screenshot che mostra la ricerca](./media/billing-how-to-pay-by-invoice/search.png)

1. Selezionare la sottoscrizione che si vuole passare al pagamento con fattura.
1. Selezionare **Metodi di pagamento**.
1. Sulla barra dei comandi, fare clic sul pulsante **paga per fattura** .

    ![Screenshot che mostra il pulsante paga per fattura](./media/billing-how-to-pay-by-invoice/pay-by-invoice.png)

### <a name="switch-billing-profile-to-checkwire-transfer"></a>Cambia profilo di fatturazione per il trasferimento di assegni/Wire

Attenersi alla procedura seguente per passare a un profilo di fatturazione per il trasferimento di assegni/bonifici. Si noti che solo la persona che ha effettuato l'iscrizione ad Azure può modificare il metodo di pagamento predefinito di un profilo di fatturazione.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Eseguire ricerche in **Gestione dei costi e fatturazione**.
1. Nel menu a sinistra fare clic su profili di **fatturazione**.

    ![screenshot che mostra il profilo di fatturazione nel menu](./media/billing-how-to-pay-by-invoice/billing-profile.png)

1. Selezionare un profilo di fatturazione.
1. Nel menu a sinistra selezionare **metodi di pagamento**.

   ![Screenshot che mostra i metodi di pagamento nel menu](./media/billing-how-to-pay-by-invoice/billing-profile-payment-methods.png)

1. Fare clic sul banner blu che indica che l'utente è idoneo per il pagamento tramite check/Wire Transfer.

    ![Screenshot che mostra il banner blu per passare a controllo/cablaggio](./media/billing-how-to-pay-by-invoice/customer-led-switch-to-invoice.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Controllare l'accesso a un contratto per i clienti Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

Per eventuali domande o per richiedere assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- Se necessario, aggiornare le informazioni di contatto per la fatturazione nel [centro account di Azure](https://account.azure.com/Profile).
