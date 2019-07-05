---
title: Addebito con fattura per le sottoscrizioni di Azure
description: Descrive come pagare le sottoscrizioni di Azure tramite fattura.
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
ms.openlocfilehash: 9ca726ef737ce4750018d2461bc4bcd6c7ebb5f5
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491253"
---
# <a name="pay-for-your-azure-subscription-by-invoice"></a>Addebito con fattura per la sottoscrizione di Azure

Se si passa al pagamento con fattura, significa che si paga la fattura entro 30 giorni dalla data della fattura dal controllo/bonifico. Per usufruire di addebito con fattura per la sottoscrizione di Azure, inviare una richiesta di supporto tecnico di Azure. Una volta approvata la richiesta, è possibile passare al pagamento con (controllo/bonifico) fattura nel [portale di Azure](https://portal.azure.com).

> [!IMPORTANT]
> * Il pagamento con fattura (controllo/bonifico bancario) è disponibile solo per gli account aziendali.
> * È necessario sostenere tutti gli addebiti in sospeso prima del passaggio al pagamento con fattura.

## <a name="request-to-pay-by-invoice"></a>Richiesta di addebito con fattura

1. Accedere al [portale di Azure](https://portal.azure.com/). Selezionare **Guida e supporto** > **Nuova richiesta di supporto**.

    ![Guida e supporto tecnico di collegamento](./media/billing-how-to-pay-by-invoice/help-and-support.png)

2. Selezionare **fatturazione** come la **tipo di problema**. Il *tipo di problema* è la categoria di richiesta di supporto. Selezionare la sottoscrizione per cui si vuole pagare tramite fattura, selezionare un piano di supporto e quindi selezionare **successivo**.

3. Selezionare **l'opzione di pagamento** con fattura nella casella **Tipo di problema**. Il *tipo di problema* è la sottocategoria di richiesta di supporto.

4. Immettere le informazioni seguenti nella casella **Dettagli** e quindi selezionare **Avanti**.

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

    - Il **nome società** e **aziendale indirizzo** deve corrispondere le informazioni fornite per l'account di Azure. Per visualizzare o aggiornare le informazioni, vedere [modificare le informazioni del profilo account di Azure](billing-how-to-change-azure-account-profile.md).
    - È necessario aggiungere le informazioni di contatto di fatturazione nel portale di Azure prima che il limite di credito potrà essere approvato. I dettagli di contatto devono essere correlati al reparto di contabilità fornitori o finanziari dell'azienda. Per aggiornare le informazioni di contatto di fatturazione, visitare il [centro Account Azure](https://account.azure.com/Profile).

5. Verificare le informazioni di contatto e il metodo di contatto preferito e quindi fare clic su **Crea**.

Se è necessario eseguire una verifica del credito considerata la quantità di credito necessario, ti invieremo che una carta di credito è controllare dell'applicazione.

## <a name="switch-to-invoice-pay-checkwire-transfer"></a>Opzione di fatturazione con pagamento (controllo/bonifico)

Una volta che sono approvate per pagare tramite fattura, è possibile passare alla fatturazione con pagamento (controllo/bonifico) nel portale di Azure.

Se hai un account del programma Microsoft Online Services, è possibile passare la sottoscrizione di Azure per archiviazione/rete trasferimento. Se hai un contratto di Microsoft dal cliente, è possibile passare il profilo di fatturazione per archiviazione/rete trasferimento. [Informazioni su come verificare il tipo di account](#check-access-to-a-microsoft-customer-agreement).

### <a name="switch-azure-subscription-to-checkwire-transfer"></a>Opzione di sottoscrizione di Azure per archiviazione/rete trasferimento

Attenersi alla procedura seguente per cambiare la sottoscrizione di Azure per (controllo/bonifico) pagamento con fattura. **Dopo il passaggio al pagamento con (controllo/bonifico) fattura non è possibile passare nuovamente alla carta di credito**.

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore account.
1. Cercare **costi di gestione e fatturazione**.

    ![Screenshot che mostra Cerca](./media/billing-how-to-pay-by-invoice/search.png)

1. Selezionare la sottoscrizione che si desidera passare per il pagamento della fattura.
1. Selezionare **Metodi di pagamento**.
1. Nella barra dei comandi, fare clic sui **pagare tramite fattura** pulsante.

    ![Screenshot che mostra il pagamento tramite pulsante fattura](./media/billing-how-to-pay-by-invoice/pay-by-invoice.png)

### <a name="switch-billing-profile-to-checkwire-transfer"></a>Commutatore profilo di fatturazione per archiviazione/rete trasferimento

Attenersi alla procedura seguente per passare un profilo di fatturazione per archiviazione/rete trasferimento. Si noti che solo la persona che ha effettuato l'iscrizione per Azure è possibile modificare il metodo di pagamento predefinito di un profilo di fatturazione.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Eseguire ricerche in **Gestione dei costi e fatturazione**.
1. Nel menu a sinistra, fare clic su **fatturazione profili**.

    ![screenshot che mostra il profilo di fatturazione nel menu di scelta](./media/billing-how-to-pay-by-invoice/billing-profile.png)

1. Selezionare un profilo di fatturazione.
1. Nel menu a sinistra, selezionare **metodi di pagamento**.

   ![Screenshot che mostra i metodi di pagamento nel menu di scelta](./media/billing-how-to-pay-by-invoice/billing-profile-payment-methods.png)

1. Selezionare il banner blu che indica che sei idoneo per il pagamento tramite controllo/bonifico.

    ![Screenshot che mostra un banner blu per passare alla verifica/transito](./media/billing-how-to-pay-by-invoice/customer-led-switch-to-invoice.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificare l'accesso a un contratto di Microsoft dal cliente
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

Se si hanno domande o assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- Se necessario, aggiornare le informazioni di contatto fatturazione per il [centro Account Azure](https://account.azure.com/Profile).
